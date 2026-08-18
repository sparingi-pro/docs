# Dokumentacja techniczna — SparingiPro (aplikacja mobilna)

Dokumentacja aplikacji mobilnej SparingiPro (React Native / Expo, architektura Feature-Sliced Design). Dokumentacja backendu znajduje się w osobnym projekcie: [`SparingiPRO/docs/`](../../SparingiPRO/docs/README.md).

## Spis treści

| # | Dokument | Zawartość |
|---|----------|-----------|
| 1 | [Architektura](01-architektura.md) | Stack technologiczny, warstwy FSD i reguły importów, zarządzanie stanem (Zustand, React Query, MMKV), warstwa API i odświeżanie tokenów, formularze, stylowanie (UniWind/Tailwind 4), i18n |
| 2 | [Ekrany i nawigacja](02-ekrany-i-nawigacja.md) | Mapa tras Expo Router, opis każdego ekranu, przepływy użytkownika (auth, onboarding, sparingi, czat), diagram nawigacji |
| 3 | [Konfiguracja i uruchomienie](03-konfiguracja-i-uruchomienie.md) | Wymagania, komendy, środowiska i zmienne środowiskowe, aktualizacje OTA, powiadomienia push, regeneracja klienta API |

## Szybki start

```bash
# Instalacja zależności (wymagany pnpm)
pnpm install

# Start dev serwera Expo
pnpm start

# Uruchomienie na symulatorze / emulatorze
pnpm ios
pnpm android

# Kontrola jakości
pnpm check-all   # lint + type-check + lint tłumaczeń
```

Szczegóły w [03-konfiguracja-i-uruchomienie.md](03-konfiguracja-i-uruchomienie.md).

## Dla kogo jest który dokument

- **Nowy członek zespołu** — zacznij od dokumentów 1 → 3.
- **Praca nad UI / nowym ekranem** — dokument 2 (nawigacja i ekrany) oraz sekcje stylowania w dokumencie 1.
- **Integracja z backendem** — sekcja "Warstwa API" w dokumencie 1 oraz [dokumentacja API backendu](../../SparingiPRO/docs/07-api.md).

> Dokumentacja wygenerowana na podstawie stanu kodu w repozytorium. W razie rozbieżności źródłem prawdy jest kod (uwaga: plik `CLAUDE.md` miejscami odbiega od stanu faktycznego — szczegóły w dokumencie 1).
