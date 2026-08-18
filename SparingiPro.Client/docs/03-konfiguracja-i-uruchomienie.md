# Konfiguracja i uruchomienie

## Wymagania

- **Node.js** LTS
- **pnpm 10.33.0** (wymuszony: `preinstall: npx only-allow pnpm`, wersja przypięta w `packageManager`)
- Środowisko React Native: Xcode (iOS) / Android Studio + SDK (Android)
- Konto EAS (buildy chmurowe); projekt EAS: `sparingipro` (owner `moapsemi`)
- `google-services.json` w katalogu głównym (Android, FCM) — nie wersjonowany, wskazywany zmienną `GOOGLE_SERVICES_JSON`

## Instalacja

```bash
cd SparingiPro.Client
pnpm install
```

Nowe pakiety instalować przez `npx expo install <pakiet>` (wersje zgodne z Expo), nie `pnpm add`.

## Komendy

| Komenda | Opis |
|---|---|
| `pnpm start` | Dev server Expo (środowisko development) |
| `pnpm ios` / `pnpm android` | Build i uruchomienie na symulatorze/emulatorze (android ustawia `GOOGLE_SERVICES_JSON=./google-services.json`) |
| `pnpm iphone` | Uruchomienie na fizycznym iPhonie (`--device`) |
| `pnpm web` | Uruchomienie w przeglądarce |
| `pnpm prebuild` | `expo prebuild --clean` — generuje katalogi `ios/` i `android/` |
| `pnpm lint` / `pnpm lint-fix` | ESLint (config antfu) |
| `pnpm type-check` | `tsc --noemit` z odfiltrowaniem błędów z `node_modules` |
| `pnpm lint:translations` | Lint plików tłumaczeń JSON |
| `pnpm check-all` | lint + type-check + lint tłumaczeń |
| `pnpm generate:api` | Regeneracja klienta API ze Swaggera (patrz niżej) |
| `pnpm doctor` | `expo-doctor` — diagnostyka projektu |
| `pnpm e2e-test` | Testy E2E Maestro (`.maestro/`); instalacja: `pnpm install-maestro` |

Warianty per środowisko (`cross-env EXPO_PUBLIC_APP_ENV=...`): `start:preview`, `start:production`, `prebuild:preview`, `prebuild:production`, `android:preview`, `ios:production` itd.

Buildy EAS: `build:{development|preview|production}:{ios|android}` → `eas build --profile <profil> --platform <platforma>`.

## Środowiska

Trzy środowiska aplikacji sterowane zmienną `EXPO_PUBLIC_APP_ENV`: **development**, **preview**, **production** (+ profil budowania `simulator` w `eas.json` używający środowiska development).

Źródła zmiennych:
- lokalnie: Expo CLI auto-ładuje `.env.development`,
- buildy i aktualizacje EAS: środowiska zdefiniowane w dashboardzie EAS (`environment` w `eas.json`, `eas update --environment <name>`).

### Pliki konfiguracyjne

- `env.ts` (root) — konfiguracja build-time dla `app.config.ts`; walidacja schematem Zod (twarda przy `STRICT_ENV_VALIDATION=1`, inaczej tylko warning). Zawiera stałą tożsamość aplikacji (bundle id `com.sparingipro`, scheme `sparingipro`, EAS project id) oraz sufiksy per środowisko: development → `.development`, preview → `.staging` (legacy), production → bez sufiksu.
- `src/shared/config/env.ts` — runtime env, importowany jako `@env`; czyta wyłącznie `EXPO_PUBLIC_*` i `Constants.expoConfig.version`.

### Zmienne środowiskowe (tylko nazwy, bez wartości)

| Zmienna | Typ | Użycie |
|---|---|---|
| `EXPO_PUBLIC_APP_ENV` | build+runtime | Wybór środowiska (development/preview/production) |
| `EXPO_PUBLIC_API_URL` | runtime | Bazowy URL API |
| `EXPO_PUBLIC_SENTRY_DSN` | runtime | DSN Sentry |
| `EXPO_PUBLIC_SENTRY_ENABLED` | runtime | Włączenie Sentry (`'true'`) |
| `EXPO_PUBLIC_EXPO_UPDATES_ENABLED` | build | Włączenie OTA (domyślnie włączone) |
| `EXPO_PUBLIC_EXPO_UPDATES_CHECK_ON_STARTUP` | build+runtime | Tryb sprawdzania OTA: `ON_LOAD` / `ON_ERROR_RECOVERY` / `WIFI_ONLY` / `NEVER` |
| `EXPO_PUBLIC_NAME` | runtime | Nazwa aplikacji (runtime) |
| `GOOGLE_MAPS_API_KEY` | build-only | Klucz Google Maps (Android) |
| `GOOGLE_SERVICES_JSON` | build-only | Ścieżka do `google-services.json` |
| `NOTIFICATIONS_SERVICE_ACCOUNT` | build-only (opc.) | Konto serwisowe powiadomień |
| `SECRET_KEY` | build-only (opc.) | Klucz (EAS) |
| `STRICT_ENV_VALIDATION` | build-only | `1` = twarda walidacja env w `env.ts` |

Nie umieszczać wartości sekretów w repozytorium; buildy EAS pobierają je ze środowisk w dashboardzie EAS.

### Profile budowania (`eas.json`)

| Profil | Kanał | Dystrybucja | Uwagi |
|---|---|---|---|
| `production` | production | store | Android: app-bundle, autoIncrement, submit z `ascAppId` |
| `preview` | preview | internal | Android: APK |
| `development` | development | internal | `developmentClient: true` |
| `simulator` | simulator | — | iOS simulator build, env development |

## Aktualizacje OTA (Expo Updates)

- Konfiguracja w `app.config.ts`: `runtimeVersion: '2.0.0'`, URL `https://u.expo.dev/<EAS_PROJECT_ID>`, `fallbackToCacheTimeout: 0`; tryb sprawdzania domyślnie `WIFI_ONLY` na produkcji, `ON_LOAD` poza nią (nadpisywalny zmienną env).
- Runtime (`useBootstrap`): sprawdzenie OTA przy starcie i przy każdym powrocie aplikacji na pierwszy plan; dostępna aktualizacja jest pobierana i aplikacja przeładowuje się (`Updates.reloadAsync()`). W `__DEV__` pominięte.
- Publikacja: `pnpm update:preview` / `pnpm update:production` → `eas update --channel <kanał> --environment <środowisko>`.
- Dodatkowo mechanizm minimalnej wersji (`@shared/lib/store-version`): werdykt `force-update` blokuje aplikację overlayem `ForceUpdateOverlay`.

## Powiadomienia push (Expo Notifications)

- Plugin `expo-notifications` w `app.config.ts` (ikona, `mode: 'production'`, `enableBackgroundRemoteNotifications: true`); iOS: `UIBackgroundModes: ['remote-notification']`; Android: uprawnienia `POST_NOTIFICATIONS`, `RECEIVE_BOOT_COMPLETED` oraz `google-services.json` (FCM).
- Rejestracja: `registerForPushNotificationsAsync()` (`entities/notification/model/notifications.ts`) — kanał Android `default`, token Expo push pobierany z projectId EAS; wymaga fizycznego urządzenia.
- `bootstrapNotifications()` w root layout ustawia handler (alert + badge + dźwięk).
- Przy wylogowaniu token urządzenia jest wyrejestrowywany (`POST /api/Notifications/unregister`, czysta instancja Axios).

## Regeneracja klienta API ze Swaggera

```bash
pnpm generate:api
```

Skrypt `react-query-swagger` czyta spec **lokalnie z repozytorium backendu**: `../SparingiPRO/src/backend/src/Web/wwwroot/api/specification.json` i generuje `src/shared/api/axios-client.ts` (+ `axios-client/Client.ts`, `Query.ts`) — szablon Axios, hooki TanStack Query, klasy DTO (`/typeStyle:Class`), opcjonalne parametry i `null → undefined`.

Po generacji automatycznie (`postgenerate:api`): `scripts/patch-api-client.ts` (tsx) + `pnpm lint-fix`. Plików wygenerowanych nie edytować ręcznie; wymagany jest checkout backendu obok katalogu klienta.

## Release

- `pnpm app-release` — `np` (bump wersji bez publikacji npm); hook `version` odpala prebuild i `git add`.
- Konwencja commitów: conventional commits (commitlint + husky).
