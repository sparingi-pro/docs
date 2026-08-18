# Przypadki użycia (Use Cases)

> Najważniejsze scenariusze korzystania z aplikacji. Aktorzy: **Trener** (aplikacja mobilna),
> **Administrator** (panel WWW), **System** (automaty serwerowe).

## UC-01: Rejestracja konta trenera

**Aktor:** Trener (nowy użytkownik)
**Cel:** Założenie aktywnego konta gotowego do organizacji sparingów.

1. Trener wypełnia formularz: imię, nazwisko, numer licencji trenerskiej, e-mail, telefon, hasło; akceptuje regulamin.
2. System tworzy konto z rolą Trener i wysyła e-mail z linkiem aktywacyjnym.
3. Trener klika link — konto zostaje potwierdzone.
4. Przy pierwszym logowaniu aplikacja wymaga dodania **pierwszej drużyny**
   (nazwa, klub, rocznik, poziom). Bez tego reszta aplikacji jest zablokowana.
5. Po zapisaniu drużyny trener trafia na ekran główny.

**Scenariusze alternatywne:**
- E-mail już zarejestrowany → komunikat o duplikacie.
- Brak potwierdzenia e-maila → logowanie niemożliwe; administrator może ponowić wysyłkę linku.

## UC-02: Publikacja ogłoszenia o sparingu

**Aktor:** Trener (organizator)
**Cel:** Znalezienie przeciwnika na wolny termin.

1. Trener klika „Dodaj +" na liście sparingów.
2. Wypełnia: tytuł, datę i godzinę, drużynę, lokalizację (województwo, miasto), uwagi;
   wybiera typ (sparing/turniej) i ewentualnie liczbę uczestników.
3. Opcjonalnie zaznacza cykliczność „co tydzień" i datę końca serii.
4. System publikuje ogłoszenie (lub całą serię — każde wydarzenie osobno, maks. 25).
5. Ogłoszenie natychmiast widoczne w wyszukiwarce innych trenerów.

## UC-03: Wyszukanie i zgłoszenie się na sparing

**Aktor:** Trener (poszukujący)
**Cel:** Umówienie meczu dla swojej drużyny.

1. Trener otwiera zakładkę Sparingi → „Wszystkie".
2. Filtruje ogłoszenia: rocznik, poziom, województwo, miasto, zakres dat.
3. Otwiera szczegóły interesującego sparingu; może kliknąć lokalizację (podgląd dojazdu w mapach)
   lub napisać do organizatora na czacie.
4. Klika „Zgłoś się" i wybiera drużynę, którą chce wystawić.
5. System zapisuje zgłoszenie jako oczekujące i wysyła push do organizatora.

**Scenariusze alternatywne:**
- Limit uczestników osiągnięty → oznaczenie „Rywal znaleziony!", zgłoszenie niemożliwe.
- Drużyna już zgłoszona → system blokuje duplikat.

## UC-04: Akceptacja lub odrzucenie zgłoszenia

**Aktor:** Trener (organizator)
**Cel:** Sfinalizowanie umówienia meczu.

1. Organizator dostaje push o nowym zgłoszeniu.
2. W widoku Moje sparingi (lub centrum powiadomień) widzi oczekujące zgłoszenie.
3. Klika **Akceptuj** → sparing potwierdzony, zgłaszający dostaje push z potwierdzeniem.
   Albo klika **Odrzuć** → zgłaszający dostaje informację o odrzuceniu.

**Scenariusz alternatywny — brak reakcji:**
- System co 2 godziny wysyła organizatorowi przypomnienie push (Nudge),
  aż do podjęcia decyzji lub upłynięcia terminu wydarzenia.

## UC-05: Ustalenie szczegółów na czacie

**Aktor:** Trener ↔ Trener
**Cel:** Dogranie szczegółów meczu bez wymiany numerów telefonów.

1. Trener otwiera czat z poziomu sparingu („Napisz do organizatora") lub z listy Wiadomości.
2. Trenerzy wymieniają wiadomości (do 2000 znaków); historia rozmowy zostaje w aplikacji.
3. Nowe wiadomości sygnalizowane pushem; kliknięcie otwiera rozmowę.

## UC-06: Edycja ogłoszenia przed pierwszym zgłoszeniem

**Aktor:** Trener (organizator)
**Warunek:** Nikt jeszcze nie zgłosił się na sparing (brak oczekujących/potwierdzonych).

1. Organizator otwiera własny sparing i klika „Edytuj" (przycisk widoczny tylko dla autora).
2. Zmienia dane (data, godzina, lokalizacja, uwagi, drużyna, limit uczestników).
3. System zapisuje zmiany.

**Scenariusz alternatywny:** Jeśli ktoś już się zgłosił — edycja zablokowana po stronie
serwera, aplikacja informuje o braku możliwości edycji.

## UC-07: Anulowanie sparingu / wycofanie się

**Aktor:** Trener
1. Trener anuluje potwierdzony sparing (lub usuwa ogłoszenie bez uczestników).
2. Druga strona dostaje push „X anulował sparing Y".
3. Sparing trafia do grupy anulowanych.

## UC-08: Usunięcie drużyny z aktywnymi sparingami

**Aktor:** Trener
1. Trener usuwa drużynę w Profil → Drużyny.
2. Jeśli drużyna ma aktywne sparingi — system pokazuje **ostrzeżenie**: powiązane sparingi
   zostaną usunięte/wycofane, uczestnicy dostaną powiadomienia.
3. Po potwierdzeniu: sparingi organizowane przez drużynę są usuwane wraz z czatami
   i powiadomieniami; zgłoszenia drużyny do cudzych sparingów są wycofywane;
   dotknięci trenerzy dostają powiadomienia.

## UC-09: Przegląd kalendarza i dojazd na mecz

**Aktor:** Trener
1. Trener otwiera Kalendarz; statusy sparingów rozróżnione kolorami; filtr po drużynie.
2. Klika dzień → szczegóły sparingu.
3. Klika lokalizację → otwiera się systemowa aplikacja map z celem podróży (nawigacja).

## UC-10: Przegląd ofert partnerów

**Aktor:** Trener
1. Trener otwiera zakładkę Oferty; widzi aktywne ogłoszenia partnerów (grafika, tytuł, opis).
2. Klika „Przejdź do oferty" → zewnętrzna strona otwiera się w przeglądarce.

## UC-11: Wysłanie komunikatu do wszystkich użytkowników

**Aktor:** Administrator
1. Administrator w panelu WWW otwiera Powiadomienia globalne.
2. Wpisuje tytuł i treść komunikatu, wybiera odbiorców.
3. System wysyła push na urządzenia użytkowników i zapisuje komunikat
   w centrum powiadomień w aplikacji (status przeczytane/nieprzeczytane).

## UC-12: Promowanie sparingu

**Aktor:** Administrator
1. Administrator na liście sparingów w panelu oznacza wydarzenie jako promowane (i ustala priorytet).
2. W aplikacji mobilnej sparing wyświetla się **na górze listy** wyszukiwania, przed
   pozostałymi wydarzeniami sortowanymi po dacie, z wyróżnieniem wizualnym.

## UC-13: Wymuszona aktualizacja aplikacji

**Aktor:** System
1. Przy starcie aplikacja pyta serwer o minimalną wspieraną wersję.
2. Jeśli zainstalowana wersja jest starsza — ekran blokady „Wymagana aktualizacja"
   z przyciskiem do App Store / Google Play; funkcje aplikacji niedostępne do czasu aktualizacji.
