# Uruchomienie środowiska deweloperskiego

## Wymagania

- **.NET SDK 10.0.201** (patrz `src/backend/global.json`; `rollForward: latestFeature`)
- **Docker** — dla infrastruktury lokalnej (PostgreSQL, MinIO, Adminer) oraz Testcontainers w testach

Wszystkie komendy uruchamiaj z katalogu `SparingiPRO/src/backend/`.

## 1. Infrastruktura lokalna (Docker)

```bash
docker compose -f docker-compose.infra.yml up -d
```

Uruchamiane usługi:

| Usługa | Obraz | Porty | Dane dostępowe |
|---|---|---|---|
| PostgreSQL | `postgres:17` | 5432 | user: `main_acc`, hasło: `5m@KxX{qT795`, baza: `SparingiProDb` |
| Adminer | `adminer` | 8080 | przeglądarka bazy: http://localhost:8080 |
| MinIO | `minio/minio:latest` | 9000 (API), 9001 (konsola) | user: `minio`, hasło: `minio_password` |
| minio-init | `minio/mc:latest` | — | jednorazowo tworzy bucket `contents` i ustawia publiczny odczyt (`anonymous set download`) |

## 2. Budowanie i uruchamianie

```bash
# Budowanie całego rozwiązania
dotnet build

# API (Web)
dotnet run --project src/Web

# Panel administratora
dotnet run --project src/Administrator.Web
```

- API przekierowuje `/` na `/api` (Swagger UI, dostępny poza Production). Health check: `/health`.
- Przy starcie oba hosty wykonują `InitialiseDatabaseAsync()` (migracje/inicjalizacja bazy).

## 3. Konfiguracja

### Web (`src/Web/appsettings.json` + `appsettings.Development.json`)

| Sekcja/klucz | Znaczenie |
|---|---|
| `ConnectionStrings:SparingiProDb` | Baza główna (w Development: `Server=localhost;Port=5432;Database=SparingiProDb;Username=main_acc;...`) |
| `MailerSend` | `ApiUrl`, `ApiToken` (**do uzupełnienia**), `TemplateId`, `FromEmail`, `FromName` |
| `MediatrLicenseKey` | Klucz licencyjny MediatR (**do uzupełnienia**, https://luckypennysoftware.com) |
| `R2` | Storage S3: `Endpoint`, `AccessKey`, `SecretKey`, `Bucket`, `PublicUrl` — domyślnie wskazuje lokalne MinIO (`http://localhost:9000`, bucket `contents`) |
| `Expo` | `AccessToken` do wysyłki push (**do uzupełnienia**) |
| `AppSchema` | Schemat deep-linków aplikacji mobilnej: `exp+sparingipro` |
| `SystemInfo:MinimumSupportedAppVersion` | Minimalna wspierana wersja aplikacji mobilnej (np. `2.0.1`) |

Wartości sekretne (tokeny) można trzymać w User Secrets — pakiet `Microsoft.Extensions.Configuration.UserSecrets` jest podłączony.

### Administrator.Web (`src/Administrator.Web/appsettings.json`)

| Klucz | Znaczenie |
|---|---|
| `ConnectionStrings:DefaultConnection` | Baza panelu admina (`AdminDb`) — konta administratorów (ASP.NET Identity) |
| `ConnectionStrings:TickerQ` | Baza TickerQ (`AdminTickerQDb`) — stan zadań w tle |
| `ConnectionStrings:SparingiProDb` | Baza główna aplikacji (współdzielona z API) |
| `Nudge:CronSchedule` | Harmonogram zadania Nudge: `0 0 */2 * * *` (co 2 godziny) |
| `MailerSend`, `R2`, `Expo`, `MediatrLicenseKey`, `AppSchema` | Jak w Web (panel korzysta z tych samych warstw Application/Infrastructure) |

## 4. Migracje EF Core

Startup projectem jest `src/Web`, projektem z migracjami — `src/Infrastructure`:

```bash
dotnet ef migrations add NazwaMigracji --project src/Infrastructure --startup-project src/Web
dotnet ef database update --project src/Infrastructure --startup-project src/Web
```

Panel administratora ma osobny `AdministrationDbContext` z własnymi migracjami w `src/Administrator.Web/Data/Migrations/`.

## 5. Testy

```bash
# Wszystkie testy
dotnet test

# Pojedynczy projekt
dotnet test tests/Domain.UnitTests
dotnet test tests/Application.UnitTests
dotnet test tests/Application.FunctionalTests
dotnet test tests/Infrastructure.IntegrationTests

# Pojedynczy test po nazwie
dotnet test --filter "FullyQualifiedName~NazwaMetodyTestowej"
```

Testy funkcjonalne i integracyjne wymagają działającego Dockera (Testcontainers uruchamia własne kontenery PostgreSQL i MinIO) — szczegóły w `04-testy.md`.

## 6. Uwagi

- `TreatWarningsAsErrors` jest włączone — każde ostrzeżenie kompilacji przerywa build.
- Wersje pakietów NuGet zmieniaj wyłącznie w `Directory.Packages.props` (Central Package Management).
- Pliki `docker-compose.staging.yml`, `docker-compose.production.yml`, `docker-compose.admin-*.yml`, `docker-compose.proxy.yml` oraz skrypty `start*.sh` dotyczą wdrożeń — nie są potrzebne lokalnie.
