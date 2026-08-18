# Panel administratora (Administrator.Web)

Panel administracyjny to aplikacja **Blazor Server** (Interactive Server render mode) z UI opartym na **MudBlazor 9.4.0**. Korzysta z tych samych warstw `Application` i `Infrastructure` co API — operacje wykonuje przez MediatR bezpośrednio na bazie `SparingiProDb`.

## Uruchomienie

```bash
# z katalogu src/backend/
dotnet run --project src/Administrator.Web
```

Przy starcie wykonywana jest inicjalizacja bazy (`InitialiseDatabaseAsync()` przez `AdministrationDbContextInitializer`).

## Kompozycja aplikacji (`Program.cs`)

- Kultura domyślna: **pl-PL** (`CultureInfo.DefaultThreadCurrentCulture/UICulture`),
- `AddMudServices()` oraz rejestracja `MudLocalizer` → `PolishMudLocalizer` (polskie tłumaczenia komponentów MudBlazor),
- `AddApplicationServices()` + `AddInfrastructureServices()` — pełny stos aplikacyjny (MediatR, EF Core, e-mail, storage, push),
- `AddBackgroundJobs()` — konfiguracja TickerQ (patrz niżej),
- Razor Components z `AddInteractiveServerComponents()` i limitem wiadomości SignalR 10 MB (`MaximumReceiveMessageSize`),
- `HttpClient` o nazwie `SparingiAPI` z adresem `https://api.sparingi.pro/`.

## Bazy danych

Panel używa **trzech** connection stringów:

| Connection string | Baza | Zastosowanie |
|---|---|---|
| `DefaultConnection` | `AdminDb` | `AdministrationDbContext` — konta administratorów (ASP.NET Identity, encja `AdminUser`), własne migracje w `Data/Migrations/` |
| `TickerQ` | `AdminTickerQDb` | stan zadań TickerQ (operational store) |
| `SparingiProDb` | `SparingiProDb` | główna baza aplikacji (przez `ApplicationDbContext` z Infrastructure) |

## Uwierzytelnianie i autoryzacja

- **ASP.NET Identity** z cookies (`AddIdentityCookies`), `AddIdentityCore<AdminUser>` z `RequireConfirmedAccount = false`,
- `IdentityRevalidatingAuthenticationStateProvider` — rewalidacja stanu uwierzytelnienia,
- e-mail sender dla Identity to `IdentityNoOpEmailSender` (brak realnej wysyłki — konta zakładane ręcznie),
- `IUser` (interfejs z Application) implementowany przez `Admin.Web.Integration.AdminUser`,
- polityka **`SU`** (superuser): dostęp mają wyłącznie użytkownicy o nazwach `marcin` lub `lukasz` (lista `suUsers` zakodowana w `Program.cs`, porównanie bez rozróżniania wielkości liter).

## Strony (`Components/Pages/`)

| Strona | Plik |
|---|---|
| Strona główna | `Home.razor` |
| Użytkownicy | `Users.razor` |
| Drużyny | `Teams.razor` |
| Sparingi | `Sparrings.razor` |
| Czaty | `Chats.razor` |
| Oferty | `Offers.razor` |
| Powiadomienia globalne | `GlobalNotifications.razor` |
| Błąd | `Error.razor` |

Struktura komponentów: `Components/Account/` (logowanie/konta Identity), `Components/Dialogs/`, `Components/Layout/`, `Components/Localization/` (`PolishMudLocalizer`). Pomocnicze etykiety PL: `Extensions/CategoryLabels.cs`, `Extensions/VoivodeshipLabels.cs`.

## Zadania w tle — TickerQ

Konfiguracja w `BackgroundJobs/DependencyInjection.cs` (`AddBackgroundJobs`):

- **Scheduler**: `MaxConcurrency = 10`, `NodeIdentifier = "administration-server"`,
- **Persystencja**: operational store EF Core na `AdministrationDbContext` (`ConfigurationType.UseModelCustomizer`, pool size 34),
- **Dashboard**: dostępny pod ścieżką **`/background-jobs`**, autoryzacja hostowa polityką `SU`:
  - `PreDashboardMiddleware` sprawdza politykę `SU` i przekierowuje niezalogowanych na `/Account/Login?ReturnUrl=/background-jobs`,
  - middleware nadpisuje `/__tickerq-config.js`, aby SPA dashboardu automatycznie uwierzytelniała się z cookies (bez własnego formularza logowania TickerQ) oraz podmienia przycisk wylogowania na przycisk „Home",
- w pipeline: `app.UseTickerQ()` (w `Program.cs`).

### Zdefiniowane zadania

Jedyne zadanie w projekcie — `BackgroundJobs/Nudge/NudgeJobs.cs`:

```csharp
[TickerFunction("Nudge_CheckPendingNotifications", "%Nudge:CronSchedule%")]
public async Task CheckPendingNotificationsAsync(...)
    => await sender.Send(new CheckPendingNotificationsCommand(), cancellationToken);
```

- Nazwa funkcji: `Nudge_CheckPendingNotifications`,
- harmonogram z konfiguracji `Nudge:CronSchedule` — domyślnie `0 0 */2 * * *` (**co 2 godziny**),
- działanie: wysyła komendę `CheckPendingNotificationsCommand` (feature `Sparrings/Commands/SendNudge` w warstwie Application) — sprawdzenie i wysyłka zaległych powiadomień „nudge".

## Wdrożenie

Projekt ma własny `Dockerfile`; wdrożenia opisują pliki `docker-compose.admin-staging.yml` i `docker-compose.admin-production.yml` w katalogu `src/backend/` (oraz skrypt `start-admin.sh`).
