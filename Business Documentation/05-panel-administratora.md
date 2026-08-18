# Panel administratora — możliwości biznesowe

> Co administrator może zrobić w panelu WWW (bez szczegółów technicznych).
> Panel jest dostępny w przeglądarce i wymaga osobnego konta administratora.

## Do czego służy panel?

Panel to „zaplecze" aplikacji: pozwala moderować treści, pomagać użytkownikom,
publikować oferty partnerów, promować wydarzenia i komunikować się ze wszystkimi
użytkownikami jednocześnie.

## Sekcje panelu

### 👥 Użytkownicy

- Przegląd wszystkich kont (trenerzy i administratorzy) z wyszukiwaniem i sortowaniem.
- Kolumny techniczne (np. identyfikator, liczba nieudanych logowań) są **ukryte
  w widoku głównym** — szczegóły dostępne po wejściu w konkretne konto.
- W szczegółach konta trenera widoczne są jego **drużyny i kluby** (powiązanie
  konta trenera z klubami).
- Edycja danych profilu: imię, nazwisko, telefon, **numer licencji trenerskiej**.
- Ponowne wysłanie e-maila potwierdzającego rejestrację.
- Blokowanie / odblokowanie konta.
- Usuwanie zawartości użytkownika (czyszczenie danych).

### 🛡️ Drużyny

- Przegląd wszystkich drużyn w systemie.
- Edycja danych drużyny: nazwa, klub, rocznik, poziom.
- Usuwanie drużyny (z potwierdzeniem).
- Podgląd sparingów danej drużyny.

### ⚽ Sparingi

- Przegląd wszystkich sparingów z filtrowaniem, sortowaniem (m.in. poprawionym
  sortowaniem po statusie) i stronicowaniem.
- Edycja danych wydarzenia (tytuł, data, lokalizacja).
- Usuwanie wydarzeń bez uczestników.
- Podgląd uczestników wraz z ich statusami (organizator / oczekujący / potwierdzony / odrzucony).
- **Promowanie wydarzeń**: włączenie/wyłączenie wyróżnienia i nadanie priorytetu.
  Promowane sparingi wyświetlają się w aplikacji mobilnej na samej górze listy,
  niezależnie od daty pozostałych wydarzeń.

### 💬 Czaty

- Wgląd w rozmowy między użytkownikami (moderacja).
- Usuwanie rozmów — uprawnienie zastrzeżone dla kont o podwyższonych uprawnieniach (SU).

### 🎁 Oferty (moduł reklamowy)

- Dodawanie i edycja ogłoszeń partnerów: grafika, tytuł, opis, link docelowy,
  kategoria (14 kategorii, m.in. obozy, sprzęt, transport, szkolenia),
  województwo (albo cała Polska).
- **Czas emisji**: data rozpoczęcia i zakończenia publikacji — oferta widoczna
  w aplikacji tylko w tym okresie.
- **Kolejność wyświetlania**: przeciąganie pozycji na liście ustala porządek ofert
  w aplikacji mobilnej.

### 🔔 Powiadomienia globalne (broadcast)

- Wysyłanie komunikatu push do użytkowników aplikacji (tytuł + treść).
- Możliwość filtrowania odbiorców (np. tylko z zarejestrowanym urządzeniem).
- Komunikat zapisuje się w **centrum powiadomień** w aplikacji — użytkownik widzi
  historię komunikatów i status przeczytane/nieprzeczytane.

### ⚙️ Zadania w tle (dla uprawnionych administratorów)

- Podgląd harmonogramu i historii automatycznych zadań serwera,
  m.in. systemu przypomnień **Nudge** (uruchamianego co 2 godziny).
- Dostęp ograniczony do wskazanych kont administratorów (polityka SU).

## Automaty serwerowe istotne biznesowo

| Automat | Częstotliwość | Efekt biznesowy |
|---|---|---|
| **Nudge — przypomnienia** | co 2 godziny | Organizatorzy, którzy nie odpowiedzieli na zgłoszenie, dostają push z przypomnieniem (losowa treść z 3 wariantów); zmniejsza liczbę zgłoszeń bez odpowiedzi |
| **Filtr terminów w wyszukiwarce** | przy każdym zapytaniu | Wydarzenia, których termin minął, nie pojawiają się na liście wyszukiwania |

## Uprawnienia — podsumowanie

| Operacja | Trener | Administrator | Administrator SU |
|---|---|---|---|
| Zarządzanie własnymi sparingami/drużynami | ✅ | — | — |
| Edycja kont i drużyn użytkowników | — | ✅ | ✅ |
| Promowanie sparingów | — | ✅ | ✅ |
| Oferty i powiadomienia globalne | — | ✅ | ✅ |
| Usuwanie czatów | — | — | ✅ |
| Panel zadań w tle | — | — | ✅ |
