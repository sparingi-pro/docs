# Testy

Rozwiązanie zawiera cztery projekty testowe (katalog `tests/`), wszystkie na NUnit:

| Projekt | Typ | Narzędzia |
|---|---|---|
| `Domain.UnitTests` | testy jednostkowe domeny | NUnit, AwesomeAssertions |
| `Application.UnitTests` | testy jednostkowe warstwy aplikacji | NUnit, Moq, AwesomeAssertions |
| `Application.FunctionalTests` | testy funkcjonalne end-to-end (przez MediatR/HTTP) | NUnit, `WebApplicationFactory`, Testcontainers (PostgreSQL + MinIO), Respawn |
| `Infrastructure.IntegrationTests` | testy integracyjne infrastruktury | NUnit, Testcontainers (MinIO) |

## Uruchamianie

```bash
# z katalogu src/backend/
dotnet test                                   # wszystkie
dotnet test tests/Application.FunctionalTests # jeden projekt
dotnet test --filter "FullyQualifiedName~NazwaTestu"
```

Testy funkcjonalne i integracyjne wymagają **działającego Dockera** (Testcontainers).

## Application.UnitTests

Testy z mockami (Moq), bez bazy i sieci. Zakres widoczny w strukturze:

- `Common/Behaviours`, `Common/Exceptions` — testy zachowań pipeline i wyjątków,
- `Offers` — testy walidatorów (`CreateOfferCommandValidatorTests`, `UpdateOfferCommandValidatorTests`),
- `Imaging` — `SkiaImageProcessorTests` (przetwarzanie obrazów na plikach z `Resources/`),
- `Sparrings/Queries`.

## Application.FunctionalTests — architektura

Najważniejszy projekt testowy. Testy wysyłają prawdziwe komendy/zapytania MediatR do aplikacji uruchomionej w `CustomWebApplicationFactory`, z prawdziwą bazą PostgreSQL i storage MinIO w kontenerach.

### Cykl życia (`Testing.cs`, `[SetUpFixture]`)

1. `[OneTimeSetUp]`:
   - `TestDatabaseFactory.CreateAsync()` → `PostgreSQLTestcontainersTestDatabase`,
   - start kontenera `MinioTestContainer`,
   - utworzenie `CustomWebApplicationFactory` (połączenie do bazy testowej + opcje MinIO).
2. Testy korzystają ze statycznych helperów.
3. `[OneTimeTearDown]`: dispose bazy, MinIO i fabryki.

### Baza testowa (`PostgreSQLTestcontainersTestDatabase`)

- Kontener `postgres:16-alpine` (Testcontainers, `WithAutoRemove`),
- tworzy bazę `SparingiProTestDb` i wykonuje na niej pełne migracje EF Core (`Database.MigrateAsync()`),
- **Respawn** resetuje dane między testami (`DbAdapter.Postgres`, ignoruje tabelę `__EFMigrationsHistory`).

Alternatywa: `PostgreSQLTestDatabase` — lokalny PostgreSQL zamiast Testcontainers (wymaga zmiany w `TestDatabaseFactory` i `appsettings.json` projektu testowego).

### Mocki usług zewnętrznych (`Mocks/`)

- `MockPushNotificationBroadcaster` (push/Expo) — dostęp przez `GetMockBroadcaster()`,
- `FakeMailerSendClient` (e-mail) — dostęp przez `GetFakeMailerSend()`.

Oba są czyszczone w `ResetState()`.

### Helpery `Testing` (najważniejsze)

| Metoda | Rola |
|---|---|
| `SendAsync(request)` | wysyła komendę/zapytanie przez `ISender` w nowym scope DI |
| `PublishAsync(notification)` | publikuje zdarzenie przez `IPublisher` |
| `RunAsUser1()` / `RunAsUser2()` / `RunAsUser3()` | tworzy i „loguje" użytkownika testowego (User2 i User3 mają rolę `Trainer`) |
| `RunAsUserAsync(userName, password, firstName, lastName, roles)` | dowolny użytkownik z rolami |
| `EnsureRoleAsync(role)` / `CreateIdentityUserAsync(...)` | pomocnicze operacje Identity |
| `AddAsync(entity)` / `FindAsync<T>(keys)` / `CountAsync<T>()` / `GetAsync<T>()` | operacje na `ApplicationDbContext` |
| `ResetState()` | reset bazy (Respawn) + czyszczenie mocków i bieżącego użytkownika |
| `CreateClient()` | `HttpClient` do testów przez HTTP |

`BaseTestFixture` to klasa bazowa fixture'ów wywołująca `ResetState()` przed każdym testem.

### Organizacja testów

Testy odzwierciedlają strukturę feature'ów Application: `Calendar/`, `Chats/`, `Notifications/`, `Offers/`, `Push/`, `Sparrings/`, `SystemInfo/`, `Teams/`, `Users/` (+ pliki `*Constants.cs` ze stałymi testowymi i `Resources/` z obrazami testowymi).

## Infrastructure.IntegrationTests

Testy prawdziwych implementacji infrastruktury:

- `Email/` — `MailerSendClientTests`, `MailerSendClientResponseHandlingTests`, `IdentityEmailSenderTests`,
- `Storage/` — `R2FileStorageTests` z własnym `MinioTestContainer` (prawdziwy upload do S3-kompatybilnego storage),
- `Notifications/` — `PushNotificationBroadcasterTests` (+ `Push/`).

## Piramida testów — podsumowanie

```
        Infrastructure.IntegrationTests   (prawdziwe usługi w kontenerach)
      Application.FunctionalTests         (cała aplikacja + Postgres + MinIO)
    Application.UnitTests                 (mocki, walidatory, behaviours)
  Domain.UnitTests                        (czysta logika domenowa)
```
