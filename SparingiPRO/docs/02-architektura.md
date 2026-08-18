# Architektura backendu

Backend jest zbudowany zgodnie z Clean Architecture. Zależności płyną do środka:

```
Web / Administrator.Web  →  Infrastructure  →  Application  →  Domain
```

- **Domain** — nie zależy od żadnej innej warstwy; zawiera encje, value objects, zdarzenia i stałe. Używa `ErrorOr` (typy wyników) i `MediatR` (zdarzenia domenowe jako `INotification`).
- **Application** — zależy tylko od Domain; definiuje interfejsy (`Common/Interfaces/`), które implementuje Infrastructure.
- **Infrastructure** — implementacje techniczne: EF Core, Identity, e-mail, storage, push.
- **Web / Administrator.Web** — hosty; rejestrują wszystkie warstwy przez metody rozszerzające DI.

## CQRS z MediatR

Każda operacja to komenda lub zapytanie MediatR. Handlery zwracają `ErrorOr<T>`. Struktura per feature:

```
Application/<Feature>/
├── Commands/<Operacja>/     # komenda + handler + walidator
├── Queries/<Operacja>/      # zapytanie + handler + DTO
└── EventHandlers/           # handlery zdarzeń domenowych
```

Rejestracja w `Application/DependencyInjection.cs` (`AddApplicationServices`):

- `AddValidatorsFromAssembly` — automatyczna rejestracja walidatorów FluentValidation,
- `AddMediatR` z kluczem licencyjnym z konfiguracji (`MediatrLicenseKey`) i rejestracją zachowań pipeline,
- rejestracja maperów `IGenericMapper<TSource, TDest>` (ręczne mapowanie, bez AutoMappera), np. `TeamToTeamDtoMapper`, `SparringToSparringDtoMapper`, `OfferToOfferDtoMapper`,
- bindowanie `SystemInfoConfiguration` z sekcji konfiguracji.

## Pipeline behaviours (kolejność rejestracji)

Zachowania z `Application/Common/Behaviours/`, rejestrowane w tej kolejności:

| # | Behaviour | Rola |
|---|---|---|
| 1 | `UnhandledExceptionBehaviour` | Łapie i loguje nieobsłużone wyjątki żądań |
| 2 | `AuthorizationBehaviour` | Sprawdza atrybut `[Authorize]` na żądaniu: wymaga zalogowanego użytkownika (`IUser.Id`), odrzuca zablokowanych (`IIdentityService.IsLockedAsync`), weryfikuje role i polityki (`ForbiddenAccessException` / `UnauthorizedAccessException`) |
| 3 | `ValidationBehaviour` | Uruchamia walidatory FluentValidation przed handlerem |
| 4 | `PerformanceBehaviour` | Mierzy czas wykonania żądań |

Dodatkowo `LoggingBehaviour` jest pre-procesorem (`IRequestPreProcessor`) — loguje nazwę żądania, `UserId`, `UserName` i treść żądania przed obsługą.

## Warstwa Web — Minimal API

`Web/Program.cs` składa aplikację z trzech wywołań: `AddApplicationServices()`, `AddInfrastructureServices()`, `AddWebServices()`. Pipeline HTTP: `UseHealthChecks("/health")`, `UseHttpsRedirection`, `UseStaticFiles`, Swagger UI pod `/api` (poza Production), `UseExceptionHandler`, `MapEndpoints()`.

### Rejestracja endpointów

Klasy endpointów w `Web/Endpoints/` dziedziczą po `EndpointGroupBase` (`Web/Infrastructure/`):

- nadpisują `Map(WebApplication app)` i używają `app.MapGroup(this)` — prefiks trasy wywodzi się z nazwy klasy,
- są wykrywane automatycznie (auto-discovery przez `WebApplicationExtensions.MapEndpoints`),
- metoda pomocnicza `Problem(List<Error>)` mapuje błędy `ErrorOr` na kody HTTP:

| ErrorType | Kod HTTP |
|---|---|
| `Validation` | 400 (ValidationProblem) |
| `Unauthorized` | 401 |
| `Forbidden` | 403 |
| `NotFound` | 404 |
| `Conflict` | 409 |
| pozostałe | 500 |

Istniejące grupy endpointów: `Calendar`, `Chats`, `Notifications`, `Offers`, `Sparrings`, `SystemInfo`, `Teams`, `Users` (+ katalog `User/`).

### Pozostałe usługi Web (`AddWebServices`)

- `IUser` → `CurrentUser` (dostęp do bieżącego użytkownika z `HttpContext`),
- health checks z `AddDbContextCheck<ApplicationDbContext>`,
- `CustomExceptionHandler` jako `IExceptionHandler`,
- NSwag `AddOpenApiDocument` z definicją zabezpieczenia JWT (nagłówek `Authorization: Bearer {token}`) — poza środowiskiem Production.

## Warstwa Infrastructure

`Infrastructure/DependencyInjection.cs` (`AddInfrastructureServices`):

- **Serilog** — `AddSerilogLogging()` (moduł `Infrastructure/Logging`),
- **EF Core** — `ApplicationDbContext` z `UseNpgsql` (connection string `SparingiProDb`) i interceptorami:
  - `AuditableEntityInterceptor` — automatyczne pola audytu,
  - `DispatchDomainEventsInterceptor` — publikacja zdarzeń domenowych przy `SaveChanges`,
- **Uwierzytelnianie** — `AddBearerToken(IdentityConstants.BearerScheme)`: token dostępu ważny **14 dni**, refresh token **365 dni**,
- **Identity** — `AddIdentityCore<ApplicationUser>` z `RequireConfirmedAccount = true`, rolami (`IdentityRole`), `AddApiEndpoints()` i `CustomUserClaimsPrincipalFactory`,
- **E-mail** — `MailerSendOptions` + `IMailerSendClient`/`MailerSendClient` (typowany HttpClient), `IEmailSender<ApplicationUser>` → `IdentityEmailSender`,
- **Storage** — `R2Options`, `IFileStorage` → `R2FileStorage` (S3/MinIO), `IImageProcessor` → `SkiaImageProcessor`,
- **Push** — `ExpoOptions` + `AddNotifications()`,
- **Autoryzacja** — polityka `Policies.CanPurge` wymagająca roli `Roles.Administrator`,
- `TimeProvider.System`, `IIdentityService` → `IdentityService`, `IUserContentPurger` → `UserContentPurger`.

## Zdarzenia domenowe

Encje dziedziczą po `BaseEntity<TId>` / `BaseAuditableEntity` (`Domain/Common/`). Zdarzenia (`Domain/Events/`: Chats, Sparrings, Teams) dziedziczą po `BaseEvent` i są publikowane przez `DispatchDomainEventsInterceptor` podczas zapisu do bazy, a obsługiwane przez `EventHandlers/` w warstwie Application (np. wysyłka powiadomień push po zdarzeniu).
