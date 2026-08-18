# Architektura aplikacji SparingiPRO

Aplikacja mobilna React Native / Expo (iOS, Android) dla trenerów sportowych, umożliwiająca wyszukiwanie, organizowanie i zarządzanie meczami sparingowymi.

## Stack technologiczny

Wersje zweryfikowane w `package.json` (wersja aplikacji: 2.0.3):

| Obszar | Technologia | Wersja |
|---|---|---|
| Framework | Expo | ~55.0.25 |
| Runtime | React Native | 0.83.6 |
| UI | React | 19.2.0 |
| Routing | Expo Router | ~55.0.15 |
| Stan serwera | TanStack React Query | ^5.91.2 |
| Stan globalny | Zustand | ^5.0.12 |
| Storage | react-native-mmkv | ^4.2.0 |
| Formularze | TanStack React Form | ^1.28.5 (+ `@tanstack/zod-form-adapter` ^0.42.1) |
| Walidacja | Zod | ^3.25.76 |
| Stylowanie | UniWind | ^1.6.0 (+ TailwindCSS 4.1.18) |
| HTTP | Axios | ^1.13.6 |
| Generator API | react-query-swagger | ^15.13.1 (+ react-query-kit ^3.3.2) |
| i18n | i18next ^25.8.20 / react-i18next ^16.5.8 |
| Monitoring | @sentry/react-native | ^7.11.0 |
| Czat | react-native-gifted-chat | ^3.3.2 |
| Listy | @shopify/flash-list | 2.0.2 |
| Animacje | react-native-reanimated ~4.2.1, moti ^0.30.0 |
| Mapy | expo-maps ~55.0.18, expo-location ~55.1.10 |
| Powiadomienia | expo-notifications ~55.0.23 |
| OTA | expo-updates ~55.0.23 |

Menedżer pakietów: **pnpm** (wymuszony przez `preinstall: npx only-allow pnpm`).

## Feature-Sliced Design (FSD)

Kod w `src/` podzielony jest na warstwy:

```
src/
├── app/           # Expo Router — routing plikowy (grupy tras w nawiasach)
├── features/      # Funkcje użytkownika: auth, location, onboarding, profile,
│                  #   sparring-listing, sparring-management, team-management
├── entities/      # Encje biznesowe: notification, offer, sparring, team, user
└── shared/        # Przekrojowe: api, config, i18n, lib, ui
```

Wewnątrz feature/entity obowiązuje podział na segmenty `model/` (logika, store) i `ui/` (komponenty).

### Reguły importów

Kierunek zależności: `shared → entities → features → app`. Warstwy niższe **nie mogą** importować z wyższych.

### Aliasy ścieżek (`tsconfig.json`)

| Alias | Ścieżka |
|---|---|
| `@/*` | `./src/*` |
| `@shared/*` | `./src/shared/*` |
| `@entities/*` | `./src/entities/*` |
| `@features/*` | `./src/features/*` |
| `@env` | `./src/shared/config/env.ts` |

## Zarządzanie stanem

- **Zustand** — stan globalny (np. auth: `src/features/auth/model/index.tsx`, onboarding). Store'y opakowane helperem `createSelectors` (`@shared/lib/utils`), co daje dostęp w stylu `useAuth.use.status()`.
- **React Query** — stan serwera. Wspólny `queryClient` w `src/shared/api/lib/query-client.ts` z globalnym `MutationCache.onError`, który pokazuje błędy API (poza 401) przez flash message.
- **MMKV** — trwały storage (`src/shared/lib/storage.tsx`): instancja `createMMKV()` + helpery `getItem/setItem/removeItem` (serializacja JSON). Przechowuje m.in. token auth i wybrany motyw.

## Warstwa API

### Klient generowany ze Swaggera

- `src/shared/api/axios-client.ts` oraz `src/shared/api/axios-client/` (`Client.ts`, `Query.ts`) — **auto-generowane** przez `react-query-swagger` (szablon Axios, styl NSwag) ze specyfikacji backendu. Nie edytować ręcznie.
- Hooki React Query dostępne przez `Query.*` (np. `Query.useCreateSparringMutation()`), eksportowane z `@shared/api`.
- Regeneracja: `pnpm generate:api` (szczegóły w `docs/03-konfiguracja-i-uruchomienie.md`).

### Instancja Axios i interceptory

Właściwa instancja z interceptorami znajduje się w **`src/shared/api/lib/client.tsx`** (moduł ładowany przez `APIProvider` w `provider.tsx` dla efektów ubocznych). Plik `src/shared/api/common/client.tsx` zawiera tylko bazową instancję bez interceptorów.

**Request interceptor:**
- dokleja nagłówek `Authorization: Bearer <token>` gdy w MMKV jest token typu `bearer`,
- w trybie DEV dodaje nagłówek `skip_zrok_interstitial`.

**Response interceptor (auto-refresh po 401):**
1. Błąd 401 dla URL spoza `/api/Users` uruchamia rotację tokenów.
2. `rotateTokens()` wykonuje `POST /api/Users/refresh` z refresh tokenem (na osobnej, „czystej" instancji Axios bez interceptorów) i zapisuje nową parę tokenów przez `signIn()`.
3. Współbieżne 401 trafiają do kolejki (`pendingQueue`) — w locie jest tylko jedno żądanie odświeżenia (`isRefreshing`); po sukcesie kolejka jest opróżniana i żądania ponawiane.
4. Ponowione żądanie oznaczane jest flagą `_isRetry` — kolejne 401 po ponowieniu kończy się `signOut()`.
5. Brak refresh tokena lub błąd rotacji → `signOut()`.

Dodatkowo: brak odpowiedzi sieciowej (poza `/health`) wyzwala refetch query `['health']` (wskaźnik `ApiHealthIndicator`).

Na końcu modułu generowany klient NSwag jest spinany z tą instancją: `setBaseUrl(Env.API_URL)` + `setAxiosFactory(() => client)`.

## Formularze

- **TanStack Form** + walidacja **Zod** (`@tanstack/zod-form-adapter`).
- Wrapper `useAppForm` (`src/shared/lib/use-app-form.ts`): łapie błędy API i mapuje je na błędy pól (`applyApiErrors`), czyści błędy serwerowe przed ponownym submitem (`clearServerErrors`), a błędy niepolowe przekazuje do `onSubmitError`.
- Helpery formularzy: `src/shared/ui/form-utils.ts` (`getFieldError`), wspólne schematy: `password-schema.ts`, `phone-schema.ts` w `@shared/lib`.

## Stylowanie

- **UniWind** (następca NativeWind) z **TailwindCSS 4** — klasy w propie `className`.
- Motyw zdefiniowany w `global.css` w bloku `@theme {}` (brak `tailwind.config.js`): palety `gray-*`, `main-green-*`, `danger-*`, kolory tagów, font `Inter`.
- Kolory dostępne też z JS: `src/shared/ui/colors.js`.
- Warianty komponentów: `tailwind-variants` + `tailwind-merge`.
- Tryb ciemny/jasny/systemowy: `useSelectedTheme` (`@shared/lib/use-selected-theme`, persystencja w MMKV) + `useThemeConfig` (`@shared/config/use-theme-config`) dla React Navigation.

## Drzewo providerów (root layout `src/app/_layout.tsx`)

```
GestureHandlerRootView
└── LocationProvider              (features/location)
    └── KeyboardProvider          (react-native-keyboard-controller)
        └── ThemeProvider         (@react-navigation/native)
            └── APIProvider       (QueryClientProvider + interceptory)
                ├── ApiHealthIndicator
                └── UserProvider  (entities/user)
                    └── NotificationProvider  (entities/notification)
                        └── BottomSheetModalProvider
                            ├── <Stack> (ekrany)
                            └── FlashMessage
```

Przed renderem (side-effects na poziomie modułu): `hydrateAuth()`, `loadSelectedTheme()`, `SplashScreen.preventAutoHideAsync()`, inicjalizacja **Sentry** (DSN i włączenie z env, mobile replay + integracja nawigacji), `bootstrapNotifications()`.

Bootstrap aplikacji (`useBootstrap`, `@shared/lib/use-bootstrap.ts`) steruje fazami `splash` → `force-update` → `ready`: sprawdza aktualizacje OTA (expo-updates), minimalną wymaganą wersję (`store-version.ts`, overlay `ForceUpdateOverlay`) i czeka na hydratację auth; JS-owy splash (`JsSplashScreen`) znika po zakończeniu.

## Internacjonalizacja (i18n)

- Konfiguracja: `src/shared/i18n/` (i18next + react-i18next).
- Wspierane języki: **polski (`pl`)** i **angielski (`en`)** — pliki `translations/pl.json`, `translations/en.json` (rejestrowane w `resources.ts`).
- Język domyślny i fallback: `pl`. Wybrany język zapisywany w MMKV i ładowany asynchronicznie przy starcie (`getLanguage()`).
- RTL wyłączony na stałe (`isRTL = false`).
- Helpery: `translate()` / `safeTranslate()` / `useSelectedLanguage` (`@shared/i18n/utils`); typowanie kluczy przez `react-i18next.d.ts`.
- Lint tłumaczeń: `pnpm lint:translations` (eslint-plugin-i18n-json).
