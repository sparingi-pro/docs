# Sparingi.PRO — wersja 2.0: zmiany modernizacyjne

> Dokument porządkowy — spis funkcjonalności realizowanych w ramach modernizacji aplikacji.
> Część I: zgłoszenia właścicieli, część II: propozycje zespołu wykonawczego.

## Podsumowanie (dla osób decyzyjnych)

### Część I — Lista zmian modernizacyjnych (zgłoszenia właścicieli)

| ID   | Funkcjonalność                                      | Wycena                        | Status                   |
|------|-----------------------------------------------------|-------------------------------|--------------------------|
| Z-01 | Otwieranie lokalizacji sparingu w aplikacji mapowej | A: 100–200 zł / B: 400–500 zł | Zrealizowano — wariant A |
| Z-02 | Powiązanie konta trenera z klubami w panelu admina  | 2.3: 150 zł                   | Zrealizowano             |
| Z-03 | Obowiązkowa drużyna dla nowych trenerów             | 1100–1200 zł                  | Zrealizowano — wariant B |
| Z-04 | Moduł reklamowy (ogłoszenia i promocje)             | 5000–8000 zł                  | Zrealizowano             |
| Z-05 | Edycja sparingu do pierwszej akceptacji             | 550–650 zł                    | Zrealizowano             |
| Z-06 | Drobne zmiany wizualne                              | —                             | Zrealizowano             |

### Część II — Nasze propozycje zmian modernizacyjnych

| ID   | Funkcjonalność                                     | Wycena                                | Status                                |
|------|----------------------------------------------------|---------------------------------------|---------------------------------------|
| P-01 | Usunięcie drużyny z anulowaniem sparingów          | 1100–1300 zł                          | Zrealizowano                          |
| P-02 | E-maile HTML (potwierdzenie konta, reset hasła)    | 900–1100 zł                           | Zrealizowano                          |
| P-03 | Logowanie społecznościowe (OAuth)                  | —                                     | Na ten moment nie do realizacji       |
| P-04 | Powiadomienia globalne (broadcast) z panelu admina | A: 300–400 zł / B: 1200–1300 zł       | Zrealizowano — wariant B              |
| P-05 | Interaktywna mapa sparingów                        | 3600–5200 zł                          | Zrealizowano                          |
| P-06 | Dodatkowe sortowanie sparingów                     | 300–450 zł                            | Zrealizowano                          |
| P-07 | Synchronizacja z kalendarzem telefonu              | 3500–4000 zł                          | Na ten moment nie do realizacji       |
| P-08 | Weryfikacja rejestracji kodem e-mail               | 1200–1400 zł                          | Na ten moment nie do realizacji       |
| P-09 | Rozmiar bazy danych (koszty utrzymania)            | —                                     | Na ten moment nie do realizacji       |
| P-10 | Możliwość zmiany języka w aplikacji                | —                                     | Na ten moment nie do realizacji       |
| P-11 | Force Update (obowiązkowa aktualizacja)            | 600–800 zł                            | Zrealizowano                          |
| P-12 | Wygasanie sparingów po czasie                      | 250 zł                                | Zrealizowano                          |
| P-13 | Moduł sparingów cyklicznych                        | A/B: 3600–6000 zł / C: 6000–8400 zł   | Zrealizowano — wariant B              |
| P-14 | Kontakt w aplikacji                                | 150 zł                                | Zrealizowano                          |
| P-15 | System ogłoszeń administracyjnych                  | 3600–6000 zł                          | Na ten moment nie do realizacji       |
| P-16 | Priorytetyzacja wydarzeń                           | 1200–2000 zł (+ wyróżnienie wizualne) | Zrealizowano — wariant z wyróżnieniem |
| P-17 | System przypomnień (Nudge)                         | 2400–3600 zł                          | Zrealizowano                          |

---

# Część I — Lista zmian modernizacyjnych (zgłoszenia właścicieli)

## Z-01: Otwieranie lokalizacji sparingu w aplikacji mapowej (nawigacja)

> Zgłoszenie: „Jak wchodzisz na sparing, tam jest miejsce gdzie ma się odbyć. To musi być
> klikalne dla tego co przyjmuje sparing, tak żeby mógł sprawdzić gdzie to jest i żeby
> np. wyznaczyć drogę dojazdu z map systemowych."

### Wariant A — otwieranie lokalizacji w zewnętrznej aplikacji mapowej (nawigacja systemowa)

**Opis:** W widoku szczegółów sparingu pole zawierające lokalizację (adres / miasto)
będzie elementem interaktywnym. Po kliknięciu w lokalizację:

- aplikacja wywoła systemowy mechanizm otwierania aplikacji mapowej,
- użytkownik zostanie przekierowany do zewnętrznej aplikacji map (np. Google Maps / Apple Maps),
- w aplikacji mapowej zostanie ustawiony punkt docelowy zgodny z lokalizacją przypisaną do sparingu.

**Zakres obejmuje:**

- uczynienie pola adresu klikalnym,
- integrację z mechanizmem deep linków Android / iOS,
- przekazywanie adresu lub współrzędnych geograficznych do aplikacji mapowej.

**Funkcjonalność nie obejmuje:**

- wbudowanej mapy w aplikacji (np. osadzonego widoku Google Maps),
- wyznaczania trasy bezpośrednio w aplikacji,
- zapisywania trasy lub historii przejazdów,
- obsługi trybu offline.

### Wariant B — wyświetlanie wbudowanej mini-mapy w szczegółach sparingu

**Opis:** W widoku szczegółów sparingu, zamiast samego tekstowego adresu, wyświetlana
będzie wbudowana mapa z zaznaczoną lokalizacją wydarzenia. Mapa będzie:

- prezentowana w formie osadzonego widoku (np. Google Maps SDK),
- zawierać znacznik (marker) w miejscu sparingu,
- umożliwiać podstawową interakcję użytkownika (przesuwanie, przybliżanie).

**Zakres obejmuje:**

- integrację z SDK map (Android + iOS),
- renderowanie mapy w widoku szczegółów sparingu,
- wyświetlenie markera na podstawie współrzędnych,
- obsługę podstawowych gestów (zoom, pan).

**Wycena:** wariant A — 100–200 zł, wariant B — 400–500 zł
**Status:** Zrealizowano — wariant A

---

## Z-02: Powiązanie konta trenera z klubami w panelu administracyjnym

> Zgłoszenie: „W panelu administracyjnym powiązać konto trenera z klubami."

### Z-02.1 — Poprawa sortowania według statusu

**Opis:** W ramach zgłoszenia zweryfikowano działanie mechanizmu sortowania według statusu
oraz wprowadzono poprawki zapewniające prawidłowe sortowanie.

### Z-02.2 — Ukrycie wybranych kolumn w głównym widoku panelu administratora

**Opis:** Funkcjonalność polega na usunięciu (ukryciu) wybranych kolumn z tabeli
w głównym widoku panelu administratora.

### Z-02.3 — Wyświetlanie powiązanych drużyn w szczegółach konta trenera

**Opis:** W panelu administratora Drużyny i Kluby będą wyświetlane w szczegółach konta
trenera; dodatkowo ukrycie na głównym widoku użytkowników pól: „User ID", „Failed Logins".

**Wycena:** Z-02.3 — 150 zł
**Status:** Zrealizowano

---

## Z-03: Obowiązkowe zdefiniowanie minimum jednej drużyny dla nowych trenerów

> Zgłoszenie: „Domyślne dodawanie/wymuszenie dodania drużyny."

**Opis:** Funkcjonalność polega na wprowadzeniu mechanizmu wymuszającego dodanie
co najmniej jednej drużyny przez nowo rejestrowanego użytkownika z rolą trenera.
Nowe konto trenera nie będzie uznane za w pełni aktywne do momentu zdefiniowania minimum
jednej drużyny. Funkcjonalność wyłącznie dla nowych kont utworzonych po wdrożeniu zmiany.
Funkcjonalność obejmuje wdrożenie jednego z poniższych wariantów (UX do ustalenia przed
implementacją):

### Wariant A — rejestracja dwuetapowa

- Etap 1 — utworzenie konta (dane użytkownika).
- Etap 2 — obowiązkowe dodanie minimum jednej drużyny; zakończenie rejestracji możliwe
  dopiero po poprawnym dodaniu drużyny.

### Wariant B — wymuszenie po pierwszym zalogowaniu

- Użytkownik kończy standardową rejestrację.
- Po pierwszym zalogowaniu aplikacja sprawdza, czy trener posiada przypisaną drużynę.
- Jeśli nie — wyświetlany jest obowiązkowy formularz dodania drużyny.
- Do momentu zapisania drużyny dostęp do pozostałych funkcji aplikacji jest zablokowany.

**Wycena:** 1100–1200 zł
**Status:** Zrealizowano — wariant B

---

## Z-04: System Wewnętrznych Ogłoszeń i Promocji (Moduł Reklamowy)

> Zgłoszenie: „Panel zarządzania reklamami."

**Opis:** Wprowadzenie nowej, dedykowanej sekcji w aplikacji oraz panelu zarządzania,
który umożliwi administratorom publikowanie graficznych ogłoszeń, banerów promocyjnych
lub ofert partnerów.

**Zmiany w aplikacji:**

- **Nowa zakładka:** dodanie nowej pozycji w menu aplikacji, prowadzącej do listy
  aktualnych ogłoszeń/promocji.
- **Format listy:** ogłoszenia prezentowane w formie przejrzystej listy (podobnie jak
  lista sparingów), z możliwością ikony/obrazka.
- **Interakcja:** uproszczony model działania — kliknięcie w ogłoszenie powoduje
  natychmiastowe przekierowanie użytkownika do zewnętrznego adresu URL (np. strony
  partnera, sklepu lub regulaminu turnieju). Brak rozbudowanych podstron wewnątrz
  aplikacji (tzw. „szczegółów") na rzecz szybkich przekierowań.

**Zmiany w panelu administratora:**

- **Zarządzanie treścią:** możliwość dodawania/edycji ogłoszeń — wgranie grafiki,
  ustawienie tytułu oraz linku docelowego.
- **Sortowanie:** funkcja ustalania kolejności wyświetlania.
- **Czas emisji:** możliwość zdefiniowania, jak długo dane ogłoszenie ma być widoczne
  w aplikacji (data rozpoczęcia i zakończenia publikacji).

**Wycena:** 5000–8000 zł
**Status:** Zrealizowano

---

## Z-05: Możliwość edycji sparingu do momentu pierwszej akceptacji

> Zgłoszenie: „Edycja sparingu."

**Opis:** Po dodaniu nowego sparingu trener (autor ogłoszenia) ma możliwość edycji jego
danych wyłącznie do momentu, gdy żadna drużyna nie podejmie interakcji w postaci
akceptacji zgłoszenia.

Edycja jest możliwa tylko wtedy, gdy:

- nie została rozpoczęta formalna relacja sparingowa pomiędzy drużynami,
- żadna drużyna nie zgłosiła/zaakceptowała sparingu.

**Zakres obejmuje:**

- dodanie przycisku „Edytuj" w widoku szczegółów sparingu (widoczny tylko dla autora),
- walidację po stronie backendu uniemożliwiającą edycję po akceptacji,
- obsługę komunikatu informującego o braku możliwości edycji po zgłoszeniu/zaakceptowaniu.

**Wycena:** 550–650 zł
**Status:** Zrealizowano

---

## Z-06: Drobne zmiany wizualne

> Zgłoszenie: „Zmiany wizualne aplikacji."

**Opis:** Drobne zmiany wyświetlanego tekstu:

1. Zmiana tekstu dla niedostępnego wydarzenia, które znalazło przeciwnika, ale jest dalej
   widoczne na liście, bo termin jeszcze nie minął: z „Niedostępny" na „Rywal znaleziony!".
2. Zmiana pływającego przycisku na widoku listy sparingów: z „(+)" na „(Dodaj +)".
3. Klikalne linki w opisie wydarzenia.

**Wycena:** _(brak)_
**Status:** Zrealizowano

---

# Część II — Nasze propozycje zmian modernizacyjnych

## P-01: Usunięcie drużyny wraz z automatycznym anulowaniem powiązanych sparingów

> Kontekst: aktualnie nie da się usunąć drużyny, która ma/miała aktywne sparingi.

**Opis:** Wprowadzenie możliwości usunięcia drużyny przez trenera z poziomu panelu
zarządzania drużynami — również w sytuacji, gdy drużyna posiada aktywne lub oczekujące
sparingi.

System wyświetla ostrzeżenie informujące, że:

- wszystkie aktywne sparingi zostaną anulowane,
- pozostali uczestnicy otrzymają powiadomienie.

Po potwierdzeniu operacji:

- powiązane sparingi zmieniają status na „anulowany",
- drużyna zostaje usunięta z systemu,
- uczestnicy sparingów otrzymują powiadomienie o anulowaniu wydarzenia.

**Wycena:** 1100–1300 zł
**Status:** Zrealizowano

---

## P-02: Format wiadomości e-mail (HTML) — potwierdzenie konta i reset hasła

**Opis:** Przygotowanie i wdrożenie estetycznych, responsywnych wiadomości e-mail
w formacie HTML wysyłanych do użytkowników aplikacji.

**Zakres obejmuje wizualne szablony wiadomości dla:**

- potwierdzenia rejestracji konta (aktywacja konta),
- resetu hasła (przypomnienie hasła / ustawienie nowego hasła).

**Wiadomości będą:**

- zgodne z identyfikacją wizualną aplikacji,
- czytelne na urządzeniach mobilnych i desktopowych,
- poprawnie wyświetlane w popularnych klientach poczty (Gmail, Outlook, Apple Mail),
- zawierać przycisk CTA (np. „Aktywuj konto", „Ustaw nowe hasło").

**Wycena:** 900–1100 zł
**Status:** Zrealizowano

---

## P-03: Integracja logowania społecznościowego (OAuth) — Google, Facebook, Apple

**Opis:** Użytkownik otrzymuje możliwość rejestracji oraz logowania do aplikacji
za pomocą zewnętrznych dostawców tożsamości:

- Google,
- Facebook,
- Apple (Sign in with Apple — wymagane przy publikacji w App Store).

**Wycena:** na ten moment nie jest przewidziane do realizacji
**Status:** Na ten moment nie do realizacji

---

## P-04: System powiadomień globalnych (broadcast) z panelu administracyjnego

**Opis:** Administrator systemu otrzymuje możliwość wysłania jednorazowego powiadomienia
typu broadcast do wszystkich użytkowników aplikacji. Treść powiadomienia definiowana jest
w panelu administratora i wysyłana jako push notification na urządzenia mobilne
użytkowników. Funkcjonalność występuje w dwóch wariantach.

### Wariant A — proste powiadomienie push (bez zapisu w aplikacji)

**Logika działania:**

- administrator w panelu wpisuje treść komunikatu,
- system wysyła powiadomienie push do wszystkich użytkowników,
- kliknięcie w powiadomienie: otwiera aplikację, nie przekierowuje do konkretnego widoku,
  nie zapisuje komunikatu w systemie.

**Zakres obejmuje:**

- pole do wpisania treści komunikatu w panelu admina,
- integrację z istniejącym systemem push,
- wysłanie broadcastu do wszystkich aktywnych tokenów,
- otwarcie aplikacji po kliknięciu.

### Wariant B — powiadomienie push + zapis w centrum powiadomień w aplikacji

**Logika działania:**

- administrator wysyła komunikat z panelu,
- użytkownik otrzymuje push notification,
- powiadomienie zostaje zapisane w systemie,
- w aplikacji dostępna jest lista powiadomień (centrum powiadomień),
- użytkownik może: zobaczyć historię komunikatów, oznaczyć jako przeczytane.

**Zakres obejmuje:**

- panel wysyłki powiadomień w panelu admina,
- zapis powiadomienia w bazie danych,
- endpoint do pobierania listy powiadomień,
- widok listy powiadomień w aplikacji,
- obsługę statusu „przeczytane / nieprzeczytane",
- integrację push + deep link do listy powiadomień.

**Wycena:** wariant A — 300–400 zł, wariant B — 1200–1300 zł
**Status:** Zrealizowano — wariant B

---

## P-05: Interaktywna mapa sparingów z podglądem wydarzeń

**Opis:** Użytkownik otrzymuje możliwość przeglądania dostępnych sparingów w formie
interaktywnej mapy. W zakładce „Sparingi" dodana zostanie ikona umożliwiająca
przełączenie widoku z listy na widok mapy.

**Mapa wyświetla:**

- lokalizację użytkownika,
- wszystkie aktywne sparingi jako znaczniki (markery),
- podstawowe informacje po kliknięciu w marker.

**Po kliknięciu w znacznik:**

- wyświetlane jest krótkie podsumowanie (np. data, miasto, rocznik),
- po ponownym kliknięciu użytkownik zostaje przekierowany do szczegółów sparingu.

**Wycena:** 3600–5200 zł
**Status:** Zrealizowano

---

## P-06: Dodatkowe sortowanie sparingów

**Opis:** Możliwość sortowania sparingów o dodatkowe parametry:

- województwo (wybór z listy, wielokrotny wybór),
- zakres dat.

**Wycena:** 300–450 zł
**Status:** Zrealizowano

---

## P-07: Synchronizacja z kalendarzem telefonu (iOS/Android)

**Opis:** Funkcjonalność pozwala na automatyczne przeniesienie zaplanowanych sparingów
z aplikacji bezpośrednio do osobistego kalendarza w telefonie użytkownika
(np. Kalendarz Google, Apple).

- **Działanie:** w widoku kalendarza w aplikacji dodany zostanie przycisk, który
  po kliknięciu eksportuje wszystkie nadchodzące, zaakceptowane sparingi do pamięci telefonu.
- **Szczegóły:** każde wydarzenie w kalendarzu telefonu będzie zawierać kluczowe
  informacje: datę, godzinę, nazwę drużyny przeciwnika oraz lokalizację.

Dzięki temu użytkownik będzie otrzymywał systemowe powiadomienia i przypomnienia
o nadchodzących meczach bez konieczności otwierania aplikacji, a terminy sparingów będą
widoczne obok innych prywatnych planów trenera.

**Wycena:** 3500–4000 zł
**Status:** Na ten moment nie do realizacji

---

## P-08: Weryfikacja rejestracji kodem e-mail (zamiast linku)

**Opis:** Zmiana mechanizmu potwierdzania nowo założonego konta. Zamiast klikania w link
aktywacyjny przesyłany w wiadomości e-mail, proces zostanie uproszczony do wpisania
krótkiego kodu bezpośrednio w aplikacji.

**Przebieg procesu:** po wypełnieniu formularza rejestracji użytkownik nie zostaje
wylogowany, lecz trafia na nowy ekran w aplikacji z polem do wprowadzenia kodu.
Równocześnie na jego adres e-mail wysyłany jest unikalny kod.

**Zakres prac:**

- stworzenie nowego ekranu weryfikacji w aplikacji mobilnej,
- zmiana szablonu wiadomości e-mail (wysyłka czystego, czytelnego kodu),
- aktualizacja logiki po stronie serwera (generowanie kodu, sprawdzanie jego poprawności
  oraz obsługa czasu wygaśnięcia kodu),
- dodanie funkcji „Wyślij kod ponownie" na wypadek problemów z dostarczeniem maila.

**Wycena:** 1200–1400 zł
**Status:** Na ten moment nie do realizacji

---

## P-09: Rozmiar bazy danych

> Kontekst: baza danych się rozrasta, możliwe że dojdą koszty za utrzymanie.

**Opis:** Za jakiś czas będzie trzeba wejść na płatny poziom bazy danych, ale to
przyszłość — na ten moment zajętość ok. 15%.

**Wycena:** —
**Status:** Na ten moment nie do realizacji

---

## P-10: Możliwość zmiany języka w aplikacji

**Opis:** Nowe języki, możliwość zmiany języka w aplikacji.

**Wycena:** —
**Status:** Na ten moment nie do realizacji

---

## P-11: Mechanizm obowiązkowej aktualizacji (Force Update)

> Kontekst: powiadomienie o nowej wersji aplikacji.

**Opis:** Wprowadzenie systemowej kontroli wersji aplikacji, która gwarantuje, że wszyscy
użytkownicy korzystają z najnowszej i bezpiecznej wersji oprogramowania. Jest to
niezbędne przy wprowadzaniu dużych zmian w bazie danych lub funkcjonalnościach, które
nie są kompatybilne ze starymi wersjami aplikacji.

- **Działanie:** przy każdym uruchomieniu aplikacja sprawdza na serwerze, czy aktualnie
  zainstalowana wersja jest oznaczona jako wspierana.
- **Blokada dostępu:** jeśli dostępna jest nowa, krytyczna aktualizacja, użytkownik
  zobaczy ekran powitalny z informacją o konieczności pobrania nowej wersji. Dostęp
  do funkcji aplikacji zostanie zablokowany do momentu dokonania aktualizacji.
- **Przycisk akcji:** na ekranie blokady znajdzie się bezpośredni przycisk, który
  przeniesie użytkownika do odpowiedniego sklepu (App Store lub Google Play), aby jednym
  kliknięciem mógł zainstalować nową wersję.

**Wycena:** 600–800 zł
**Status:** Zrealizowano

---

## P-12: Automatyczne zarządzanie przedawnionymi ofertami (wygasanie sparingów)

**Opis:** System po stronie serwera będzie regularnie weryfikował wszystkie otwarte
zaproszenia na sparingi. Jeśli termin wydarzenia minął, a sparing nie został przez nikogo
zaakceptowany, system automatycznie usunie go z listy.

**Wycena:** 250 zł
**Status:** Zrealizowano

---

## P-13: Dodawanie cyklicznych sparingów

**Opis:** Wprowadzenie możliwości planowania powtarzalnych wydarzeń za pomocą jednego
formularza. Funkcja dedykowana trenerom, którzy chcą cyklicznie wystawiać ogłoszenia
o poszukiwaniu przeciwnika (np. w każdy wtorek do końca rundy).

**Proponowane warianty realizacji (do decyzji po analizie UX):**

### Wariant A

Na ogólnej liście wyświetla się tylko najbliższy termin z serii z oznaczeniem
„Sparing Cykliczny". Dopiero po jego wygaśnięciu lub zakończeniu system automatycznie
publikuje kolejne wydarzenie.

- Zaleta: brak zaśmiecania listy.
- Wada: inni użytkownicy nie mogą zgłosić się na terminy odległe o np. 3 tygodnie;
  implementacyjnie problematyczne.

### Wariant B

System generuje wszystkie sparingi z serii jako oddzielne wydarzenia od razu
po zatwierdzeniu formularza.

- Zaleta: pełna widoczność wszystkich terminów.
- Wada: duża liczba ogłoszeń od jednego trenera może zdominować listę (spam).

### Wariant C (sugerowany)

Na głównej liście widoczna jest jedna pozycja z oznaczeniem „Sparing Cykliczny".
Po kliknięciu w szczegóły użytkownik widzi listę dostępnych dat i wybiera konkretny
termin (lub kilka), w którym chce wziąć udział.

- Zaleta: najwyższy komfort użytkowania (UX) i brak spamu.
- Wada: najbardziej rozbudowany technicznie.

**Wycena:** wariant A — 3600–6000 zł (raczej odradzamy), wariant B — 3600–6000 zł,
wariant C — 6000–8400 zł
**Status:** Zrealizowano — wariant B

---

## P-14: Możliwość skontaktowania się z poziomu aplikacji

**Opis:** W zakładce profil nowa pozycja na liście „Kontakt" i przekierowanie do maila —
otworzy się klient pocztowy na telefonie.

**Wycena:** 150 zł
**Status:** Zrealizowano

---

## P-15: System ogłoszeń administracyjnych

**Opis:** Rozbudowa aplikacji o zupełnie nowy typ treści — „Ogłoszenia", które pozwolą
administratorowi na bezpośrednią komunikację z użytkownikami oraz promowanie ważnych
informacji (np. turniejów, obozów czy komunikatów) na górze listy wydarzeń.

**Nowe funkcje w panelu administratora:**

- dedykowany formularz dodawania ogłoszeń (tytuł, treść),
- zarządzanie czasem emisji: możliwość ustawienia daty „od–do", kiedy ogłoszenie
  ma być widoczne w aplikacji.

**Wycena:** 3600–6000 zł
**Status:** Na ten moment nie do realizacji

---

## P-16: System priorytetyzacji i promowania wydarzeń

**Opis:** Priorytetyzacja — opcja przypięcia ogłoszenia na samą górę listy, niezależnie
od daty pozostałych sparingów i turniejów. Wprowadzenie mechanizmu pozwalającego
administratorowi na ręczne zarządzanie kolejnością wyświetlania sparingów i turniejów
na liście głównej, niezależnie od ich daty.

**Zmiany w panelu administratora:**

- na liście wszystkich wydarzeń dodana zostanie opcja „Priorytet" (np. w formie
  numeru/przełącznika/checkboxa),
- administrator będzie mógł w dowolnym momencie nadać lub odebrać status priorytetu
  wybranemu sparingowi.

**Logika w aplikacji mobilnej:**

- zaktualizowany algorytm sortowania: w pierwszej kolejności na górze listy wyświetlane
  będą wszystkie wydarzenia oznaczone jako priorytetowe, a pod nimi pozostałe wydarzenia
  według standardowej daty.

**Wariant z wyróżnieniem (\*):** możliwość dodania delikatnego wyróżnienia wizualnego dla
wydarzeń priorytetowych (np. inny kolor ramki lub ikona „Polecane"), aby użytkownik
wiedział, dlaczego znajdują się na górze.

**Wycena:** wariant zwykły — 1200–2000 zł, wariant z wyróżnieniem — 1200–2000 zł +
**Status:** Zrealizowano — wariant z wyróżnieniem

---

## P-17: System Inteligentnych Przypomnień (Nudge System)

**Opis:** Wprowadzenie mechanizmu automatycznego monitorowania interakcji między
trenerami. Celem funkcji jest wyeliminowanie sytuacji, w których zgłoszenia na sparing
pozostają bez odpowiedzi, co zniechęca użytkowników do korzystania z aplikacji.

- **Logika działania:** system będzie monitorował nowo powstałe zgłoszenia. Jeśli
  organizator sparingu nie zareaguje na ofertę w ciągu ustalonego czasu — 2 godziny —
  system wyśle automatyczne powiadomienie push z przypomnieniem.
- **Harmonogram powiadomień:** cyklicznie co 2 h, aby skutecznie, ale nienatarczywie,
  zachęcić trenera do podjęcia decyzji (akceptacja/odrzucenie). Przykład: trener Y klika,
  że chce zagrać sparing z trenerem X o 10:00 — jeśli trener X nie reaguje, dostanie
  powiadomienia o 12:00, 14:00, 16:00… o treści losowej z trzech poniższych.

**Propozycje komunikatów:**

1. „Trener [Imię_Nazwisko] czeka na Twoją odpowiedź w sprawie [sparingu/turnieju]!
   Nie daj mu czekać — daj znać, czy termin Ci odpowiada."
2. „Masz nowe zgłoszenie na [sparing/turniej], na które jeszcze nie odpowiedziałeś.
   Sprawdź szczegóły, zanim inny trener zajmie Twój termin."
3. „Pamiętaj o swoich ogłoszeniach! Ktoś chce zagrać z Twoją drużyną — odpowiedz teraz,
   aby sfinalizować spotkanie."

**Wycena:** 2400–3600 zł
**Status:** Zrealizowano

