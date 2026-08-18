# Dokumentacja techniczna — SparingiPro (backend)

Dokumentacja backendu platformy SparingiPro: API .NET 10 (Clean Architecture) oraz panelu administratora Blazor Server. Dokumentacja aplikacji mobilnej znajduje się w osobnym projekcie: [`SparingiPro.Client/docs/`](../../SparingiPro.Client/docs/README.md).

## Spis treści

| # | Dokument | Zawartość |
|---|----------|-----------|
| 1 | [Przegląd systemu](01-przeglad-systemu.md) | Czym jest SparingiPro, komponenty systemu, stos technologiczny, moduły funkcjonalne, usługi zewnętrzne |
| 2 | [Architektura](02-architektura.md) | Clean Architecture, warstwy i kierunek zależności, CQRS (MediatR), pipeline behaviours, ErrorOr, rejestracja endpointów |
| 3 | [Uruchomienie środowiska dev](03-uruchomienie-dev.md) | Wymagania, Docker (PostgreSQL, Adminer, MinIO), build, uruchomienie, migracje EF Core, konfiguracja |
| 4 | [Testy](04-testy.md) | Projekty testowe (unit, functional, integration), Testcontainers, Respawn, uruchamianie testów |
| 5 | [Panel administratora](05-panel-administratora.md) | Blazor Server + MudBlazor, strony, autoryzacja (polityka SU), zadania w tle (TickerQ) |
| 6 | [Model domeny](06-model-domeny.md) | Encje, value objects, enumy, zdarzenia domenowe, reguły biznesowe, diagram ERD |
| 7 | [API](07-api.md) | Wszystkie endpointy REST (42), uwierzytelnianie JWT, format błędów, OpenAPI/NSwag |

## Szybki start

```bash
cd src/backend

# 1. Infrastruktura (PostgreSQL, Adminer, MinIO)
docker compose -f docker-compose.infra.yml up -d

# 2. Build i uruchomienie API
dotnet build
dotnet run --project src/Web

# 3. Panel administratora
dotnet run --project src/Administrator.Web

# 4. Testy
dotnet test
```

Szczegóły w [03-uruchomienie-dev.md](03-uruchomienie-dev.md).

## Dla kogo jest który dokument

- **Nowy członek zespołu** — zacznij od dokumentów 1 → 2 → 3.
- **Programista frontend / mobile** — dokumenty 7 (API) i 6 (model domeny).
- **Programista backend** — dokumenty 2, 6, 7 oraz 4 (testy).
- **Administrator / operacje** — dokumenty 3 i 5.

> Dokumentacja wygenerowana na podstawie stanu kodu w repozytorium. W razie rozbieżności źródłem prawdy jest kod.
