# Przegląd systemu SparingiPro

SparingiPro to platforma do umawiania sparingów sportowych (sports matchmaking). Repozytorium zawiera backend .NET oraz panel administratora. Klient mobilny (React Native/Expo) znajduje się w osobnym projekcie (`SparingiPro.Client`) i komunikuje się z backendem przez REST API.

## Komponenty systemu

| Komponent | Projekt | Technologia | Opis |
|---|---|---|---|
| API | `src/Web` (`SparingiPro.Web`) | ASP.NET Core Minimal API | Główne REST API dla aplikacji mobilnej |
| Panel administratora | `src/Administrator.Web` | Blazor Server + MudBlazor | Zarządzanie użytkownikami, drużynami, sparingami, czatami, powiadomieniami |
| Warstwa aplikacji | `src/Application` (`SparingiPro.Application`) | MediatR (CQRS) | Komendy, zapytania, walidacja, zachowania pipeline |
| Warstwa domeny | `src/Domain` (`SparingiPro.Domain`) | C# (bez zależności frameworkowych) | Encje, value objects, zdarzenia domenowe, reguły walidacji |
| Infrastruktura | `src/Infrastructure` (`SparingiPro.Infrastructure`) | EF Core + PostgreSQL, Identity, Serilog | Dostęp do danych, tożsamość, e-mail, storage, powiadomienia push |

## Stos technologiczny

- **.NET 10** — SDK `10.0.201` (zdefiniowane w `global.json`, `rollForward: latestFeature`), `TargetFramework: net10.0`
- **Baza danych**: PostgreSQL 17 (sterownik `Npgsql.EntityFrameworkCore.PostgreSQL` 10.0.1)
- **CQRS**: MediatR 14.1.0 (wymaga klucza licencyjnego — sekcja `MediatrLicenseKey` w konfiguracji)
- **Wynik operacji**: ErrorOr 2.0.1 (wzorzec Result zamiast wyjątków)
- **Walidacja**: FluentValidation 11.3.1 / 12.1.1
- **Logowanie**: Serilog 4.3.1 (sinki: Console, Seq)
- **Dokumentacja API**: NSwag 14.7.1 (OpenAPI/Swagger UI)
- **UI panelu admina**: MudBlazor 9.4.0 + `Extensions.MudBlazor.StaticInput`
- **Zadania w tle**: TickerQ 10.3.0 (+ Dashboard, + EntityFrameworkCore) — tylko w Administrator.Web
- **Przetwarzanie obrazów**: SkiaSharp 3.119.2
- **Storage plików**: S3-kompatybilny (AWSSDK.S3 4.0.22.1) — MinIO lokalnie, R2 w konfiguracji (sekcja `R2`)
- **E-mail**: MailerSend (klient HTTP, sekcja `MailerSend` w konfiguracji)
- **Push**: Expo (sekcja `Expo` z `AccessToken`)
- **Testy**: NUnit 4.5.1, Moq 4.20.72, AwesomeAssertions 9.4.0, Testcontainers (PostgreSql, Minio) 4.11.0, Respawn 7.0.0

## Struktura repozytorium (`src/backend/`)

```
src/backend/
├── global.json                      # SDK 10.0.201
├── Directory.Build.props            # net10.0, TreatWarningsAsErrors, Nullable, ImplicitUsings
├── Directory.Packages.props         # Centralne zarządzanie wersjami pakietów (CPM)
├── SparingiPro.sln
├── docker-compose.infra.yml         # Infrastruktura lokalna: PostgreSQL 17, Adminer, MinIO
├── docker-compose.{staging,production}.yml
├── docker-compose.admin-{staging,production}.yml
├── docker-compose.proxy.yml
├── azure.yaml
├── src/
│   ├── Domain/
│   ├── Application/
│   ├── Infrastructure/
│   ├── Web/
│   └── Administrator.Web/
└── tests/
    ├── Domain.UnitTests/
    ├── Application.UnitTests/
    ├── Application.FunctionalTests/
    └── Infrastructure.IntegrationTests/
```

## Moduły funkcjonalne (feature'y w warstwie Application)

Warstwa `Application` jest zorganizowana per funkcjonalność; każda ma podkatalogi `Commands/` i/lub `Queries/` (oraz miejscami `EventHandlers/`):

| Moduł | Zawartość |
|---|---|
| `Sparrings` | Commands, Queries, EventHandlers — sparingi (w tym uczestnictwo, nudge) |
| `Teams` | Commands, Queries, EventHandlers — drużyny |
| `Users` | Commands — użytkownicy |
| `Chats` | Commands, Queries, EventHandlers — czaty i wiadomości |
| `Calendar` | Queries — kalendarz użytkownika |
| `Notifications` | Commands, Queries, EventHandlers — powiadomienia |
| `Offers` | Commands, Queries — oferty (z obrazami) |
| `Push` | Events, Queries, `IPushNotificationBroadcaster`, `PushNotification` — powiadomienia push |
| `SystemInfo` | Queries — informacje o systemie (m.in. `MinimumSupportedAppVersion`) |

## Domena

Główne encje (`src/Domain/Entities/`): `Sparring`, `SparringTeam`, `PromotedSparring`, `Team`, `Chat`, `ChatMessage`, `Offer`, `SparringNotification`, `SystemNotification`, `RegisteredDevice`.

Value objects (`src/Domain/ValueObjects/`): `Location`, `UserMetadata`, `ChatParticipants`.

Enumy (`src/Domain/Enums/`): `SparringType`, `SparringTeamRelation`, `SparringNotificationType`, `TeamLevel`, `CycleType`, `OfferCategory`, `PolishVoivodeship`.

Stałe (`src/Domain/Constants/`): `Roles` (m.in. `Administrator`, `Trainer`), `Policies` (m.in. `CanPurge`).

## Usługi zewnętrzne i infrastrukturalne

| Usługa | Zastosowanie | Konfiguracja |
|---|---|---|
| PostgreSQL | Główna baza danych (`SparingiProDb`) | ConnectionString `SparingiProDb` |
| MinIO / R2 | Storage plików (bucket `contents`) | Sekcja `R2` (Endpoint, AccessKey, SecretKey, Bucket, PublicUrl) |
| MailerSend | Wysyłka e-maili transakcyjnych | Sekcja `MailerSend` (ApiUrl, ApiToken, TemplateId, FromEmail, FromName) |
| Expo | Powiadomienia push do aplikacji mobilnej | Sekcja `Expo` (AccessToken) |
| Adminer | Przeglądarka bazy danych (tylko dev) | Port 8080 (docker-compose.infra.yml) |
| Seq | Odbiornik logów Serilog | Sink `Serilog.Sinks.Seq` |

## Konwencje projektowe

- **Central Package Management** — wersje pakietów wyłącznie w `Directory.Packages.props`; pliki `.csproj` nie zawierają wersji.
- **TreatWarningsAsErrors** — włączone globalnie w `Directory.Build.props` (z wyjątkami NU1901–NU1904).
- **Nullable reference types** i **ImplicitUsings** — włączone globalnie.
- **Rejestracja DI per warstwa** — każda warstwa ma `DependencyInjection.cs` z metodą rozszerzającą: `AddApplicationServices`, `AddInfrastructureServices`, `AddWebServices`, `AddBackgroundJobs` (Administrator.Web).
