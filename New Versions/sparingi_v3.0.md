# Sparingi.PRO — wersja 3.0: propozycje nowych funkcjonalności

> Dokument roboczy — wsad do wyceny i planowania implementacji.
> Stan: propozycja do akceptacji przez finansujących.

## Podsumowanie

Wersja 3.0 rozszerza Sparingi.PRO z narzędzia dla trenerów do **platformy obsługującej
całą szkółkę piłkarską**. Pojawiają się dwa nowe typy kont — **rodzic** i **koordynator** —
oraz fundament pod **płatności** (składki i opłaty pobierane przez szkółkę od rodziców).

| Moduł | Co wnosi | Model przychodowy |
|---|---|---|
| [1. Zmiany ogólne](#moduł-1-zmiany-ogólne) | Wsparcie wielu typów kont, skalowanie | — |
| [2. Profil trenera](#moduł-2-profil-trenera-rozszerzenia) | Zarządzanie dziećmi w drużynie, powołania | — |
| [3. Profil rodzica](#moduł-3-profil-rodzica-nowy-typ-konta) | Rodzic widzi kalendarz meczów dziecka | **Subskrypcja 5 zł/mies.** (14 dni bezpłatnie) |
| [4. Profil koordynatora](#moduł-4-profil-koordynatora-nowy-typ-konta) | Zarządzanie trenerami i opłatami w szkółce | — |
| [5. Płatności](#moduł-5-płatności-do-szkółek) | Pobieranie składek od rodziców | prowizje / opłaty — do ustalenia |

### Nowe relacje w systemie

```
Koordynator (szkółka)
   └── Trenerzy (zaproszeni przez koordynatora)
          └── Drużyny
                 └── Dzieci  ◄── definiowane i przypisywane przez Rodziców
```

### Proponowana kolejność wdrożenia (zależności między modułami)

1. **Fundament:** moduł 1 (typy kont, OG-01/OG-06) + moduł 2 i 3 bez opłat i bez
   subskrypcji — rodzic może się zarejestrować, dodać dziecko do drużyny i widzieć
   kalendarz. To działa samodzielnie i od razu daje wartość.
2. **Ewidencja opłat:** moduł 4 + wariant 0 z modułu 5 (bez operatora płatności).
3. **Monetyzacja:** subskrypcja rodzica (RO-06–RO-09, wymaga OG-03) oraz płatności
   online (wariant A, wymaga ofert od operatorów i decyzji właścicieli).

> Uwaga: pkt 1–2 nie zależą od żadnej decyzji zewnętrznej (operator, sklepy) — można je
> wycenić i realizować od razu. Pkt 3 zależy od OG-03 i odpowiedzi operatorów.
> **Ryzyko modelu przychodowego do świadomej decyzji:** subskrypcja 5 zł/mies. jako
> warunek dodania dziecka (RO-07) to bariera adopcji w fazie budowania bazy użytkowników —
> do rozważenia start z trialem wydłużonym lub subskrypcją włączaną dopiero po osiągnięciu
> masy krytycznej rodziców.

---

## Moduł 1: Zmiany ogólne

**Tytuł:** Zmiany niezależne od typu konta

| ID | Wymaganie |
|---|---|
| OG-01 | W aplikacji musi być widoczna informacja, na jaki typ konta jesteśmy zalogowani (trener / rodzic / koordynator). |
| OG-02 | Obsługa większej ilości ruchu w aplikacji (skalowanie infrastruktury). |
| OG-03 | **Migracja aplikacji na konta deweloperskie spółki właścicieli.** Aplikacja jest obecnie opublikowana na prywatnych kontach dewelopera (Apple App Store i Google Play). Właściciele muszą założyć konta deweloperskie na swoją spółkę, a aplikacja zostanie przeniesiona (transfer aplikacji) na te konta. **Warunek konieczny przed uruchomieniem płatności** — subskrypcje i płatności w aplikacji nie mogą być rozliczane przez prywatne konto dewelopera. |
| OG-04 | **Rozszerzenie panelu administratora:** podgląd i wsparcie nowych bytów (rodzice, dzieci, koordynatorzy/szkółki, przypisania do drużyn, opłaty, statusy subskrypcji) — bez tego zespół nie obsłuży zgłoszeń użytkowników ani sporów o płatności. |
| OG-05 | **RODO i dokumenty prawne:** aplikacja zacznie przetwarzać **dane osobowe dzieci** (imię, nazwisko, data urodzenia) — wymagana aktualizacja polityki prywatności i regulaminu (zgody rodzica, zakres danych widoczny dla trenera/koordynatora, retencja po usunięciu konta), do weryfikacji z prawnikiem. Dochodzi też pozycjonowanie SaaS z zasady projektowej nr 6 (moduł 5). |
| OG-06 | **Kompatybilność wstecz / migracja istniejących kont:** obecni użytkownicy dostają rolę „trener" automatycznie; starsze wersje aplikacji nie znają nowych typów kont i opłat — wydanie v3.0 skorzysta z istniejącego mechanizmu **Force Update**. |

### Uwagi do OG-03 (migracja kont sklepowych)

- **Po stronie właścicieli (spółki):**
  - Google Play: konto deweloperskie firmowe (opłata jednorazowa 25 USD) + weryfikacja firmy.
  - Apple: Apple Developer Program dla organizacji (99 USD/rok) — wymaga numeru **D-U-N-S**
    spółki i osoby upoważnionej do zawierania umów; przy płatnościach dojdą umowy
    Paid Apps / dane bankowo-podatkowe w App Store Connect.
- **Po stronie techniczno-wykonawczej (transfer):**
  - Google Play: transfer aplikacji między kontami przez formularz Google (kilka dni).
  - Apple: wbudowany proces „Transfer App" w App Store Connect.
  - Do przepięcia po migracji: profile podpisywania / klucze, konfiguracja **Expo/EAS**
    (właściciel projektu, credentials), klucze push (FCM / APNs), wpisy w konfiguracji
    backendu i CI/CD.
- Transfer **zachowuje** użytkowników, oceny i historię instalacji — użytkownicy nie
  muszą nic robić.

**Wycena:** _(do uzupełnienia)_
**Status:** _(do uzupełnienia)_

---

## Moduł 2: Profil trenera (rozszerzenia)

**Tytuł:** Zmiany w typie konta: trener

### Zakres — etap 1

| ID | Wymaganie |
|---|---|
| TR-01 | Trener widzi listę dzieci przypisanych do **każdej ze swoich drużyn** (trener może mieć wiele drużyn — lista dzieci jest per drużyna). |
| TR-02 | Trener może w każdej chwili usunąć dziecko z drużyny; rodzic dostaje powiadomienie, że dziecko zostało usunięte. |
| TR-03 | Trener dostaje powiadomienie „dziecko X chce dołączyć do drużyny Y" i może zgłoszenie **zaakceptować lub odrzucić**; rodzic dostaje powiadomienie zwrotne z decyzją. |
| TR-04 | Trener widzi, do jakiego koordynatora jest przypisany. **Przypisanie do koordynatora jest opcjonalne** — trener bez koordynatora korzysta z aplikacji na dotychczasowych zasadach (sparingi, drużyny, dzieci), z tą różnicą, że funkcjonalności opłat są niedostępne, bo opłaty nakłada wyłącznie koordynator (moduł 4). |
| TR-06 | Trener dostaje **zaproszenie do szkółki** od koordynatora (lustro KO-02) i może je zaakceptować lub odrzucić; koordynator dostaje powiadomienie zwrotne z decyzją. Gdy koordynator usunie trenera ze szkółki (KO-03), trener dostaje o tym powiadomienie. |
| TR-07 | **Usunięcie drużyny lub konta trenera** odpina wszystkie dzieci od drużyny; rodzice tych dzieci dostają powiadomienie (analogicznie do TR-02). |

> Uwaga techniczna: w obecnym kodzie drużyna (`Team`) to wyłącznie metadane do sparingów
> (nazwa, klub, poziom, rocznik) — **nie ma pojęcia członków drużyny**. Członkostwo
> dziecko↔drużyna to nowy byt budowany od zera, nie rozszerzenie istniejącego.

### Do ustalenia
- !!!!!! może być więcej niż jeden kordynatorw szkółce, realny przypadek - jeden kordynator i tak będzie masterem
- TR-05: Czy trener widzi opłaty nakładane przez koordynatora? Wstępnie: **nie widzi nic** związanego z opłatami — do potwierdzenia. (zgdza się, tylko kordynator widzi płatnosci)
- Czy trener może być przypisany maksymalnie do **jednego** koordynatora (założenie robocze: tak)? (tak nawet między klubami!!!)
- Czy trener może **sam opuścić szkółkę** (odpiąć się od koordynatora), czy tylko koordynator może go usunąć? (informacja tylko dla trenera, że jak cos nie gra to mail do sparingów)
- **Zakres danych dziecka widocznych dla trenera** (dane osobowe małoletnich — RODO): tylko imię, nazwisko, rocznik, czy również dane kontaktowe rodzica? (praktycznie wszystko) RODO
- Czy po odrzuceniu zgłoszenia (TR-03) rodzic może ponawiać prośbę **bez limitu**, czy potrzebna blokada/limit ponowień (ochrona przed spamem)? ( bez limitu bo bęzi ełatwiej)
- Czy wszystkie drużyny trenera są **automatycznie widoczne w wyszukiwarce rodzica** (RO-04), czy trener decyduje, które drużyny są wyszukiwalne? Dziś drużyny są prywatnymi danymi trenera. -  nie ma sensu, wszystkie widoczne  dla rodziców

### Kolejny etap (poza wyceną etapu 1)

- Trener z poziomu widoku drużyny (z poziomu każdego dziecka) może rozpocząć **czat z rodzicem** danego dziecka.
- **Powołania:** trener podczas tworzenia sparingu wybiera z listy dzieci, które wezmą udział (domyślnie powołana cała drużyna, odznaczanie z listy). Tylko rodzice powołanych dzieci dostają powiadomienie, a wydarzenie trafia do ich kalendarza.
- Trener może **ręcznie dodać wydarzenie do kalendarza** (mecz, trening, turniej), które pojawi się rodzicom.

**Wycena:** _(do uzupełnienia)_
**Status:** _(do uzupełnienia)_


---

## Moduł 3: Profil rodzica (nowy typ konta)

**Tytuł:** Nowy typ konta: rodzic

Nowy typ konta przeznaczony dla rodziców dzieci, które są trenowane i należą do drużyn
trenerów obecnych w aplikacji.

### Rejestracja

| ID | Wymaganie |
|---|---|
| RO-01 | Podczas rejestracji jest wybór rodzaju konta: do obecnego domyślnego „trener" dochodzi typ „rodzic". Od wyboru zależy zakres danych w formularzu i późniejsze działanie aplikacji. |
| RO-02 | Rodzic podaje przy rejestracji: imię, nazwisko, e-mail, numer telefonu, hasło (bez numeru licencji trenerskiej). |

### Menu rodzica po zalogowaniu

| Sekcja | Działanie |
|---|---|
| **Kalendarz** | Analogicznie jak u trenera, ale rodzic widzi w nim **mecze swoich dzieci** — np. gdy trener zorganizował sparing dla drużyny, w której jest dziecko rodzica, wydarzenie pojawia się w kalendarzu rodzica z informacją „dziecko X ma sparing taki i taki". |
| **Oferty** | Analogicznie jak u trenera (ten sam panel ofert). |
| **Profil** | Podobnie jak u trenera. |
| **Wiadomości** | Na tym etapie pomijamy. |

> Doprecyzowanie do kalendarza: sparingi mają statusy (oczekujący / potwierdzony /
> odrzucony). Założenie robocze: rodzic widzi tylko sparingi **potwierdzone** dla drużyny
> dziecka. Rodzic dostaje też **powiadomienie push** o nowym sparingu oraz o jego
> **zmianie lub odwołaniu** (rozszerzenie istniejącego systemu powiadomień, który dziś
> informuje wyłącznie trenerów-uczestników).

### Dzieci i przypisanie do drużyn

| ID | Wymaganie |
|---|---|
| RO-03 | Rodzic definiuje dzieci w aplikacji; przy dodawaniu dziecka podaje: imię, nazwisko, datę urodzenia. |
| RO-04 | Rodzic wyszukuje klub / trenera / drużynę i wysyła prośbę o dodanie zdefiniowanego dziecka do wybranej drużyny. Trener dostaje powiadomienie i decyduje o akceptacji (patrz TR-03). |
| RO-05 | Dziecko może należeć do **wielu drużyn jednocześnie** (relacja wiele-do-wielu, np. własny rocznik + starszy rocznik, klub + dodatkowa akademia — także u różnych trenerów/szkółek). Rodzic widzi u siebie listę drużyn dziecka wraz ze statusem każdego przypisania (np. „czeka na potwierdzenie trenera" / „w drużynie"). Kalendarz rodzica agreguje wydarzenia ze **wszystkich** drużyn dziecka, a składki (RO-12) są naliczane **per szkółka** — bez podwójnej składki za dwie drużyny tej samej szkółki (patrz KO-06). |
| RO-10 | Rodzic może **edytować dane dziecka** oraz **usunąć dziecko**; usunięcie dziecka będącego w drużynie odpina je z drużyny, a trener dostaje powiadomienie. |
| RO-11 | Rodzic może **wycofać oczekującą prośbę** o dołączenie oraz **wypisać dziecko z drużyny** (lustro TR-02 — wtedy trener dostaje powiadomienie). |

### Opłaty od koordynatora (lustro KO-05 — jeśli szkółka ma koordynatora)

| ID | Wymaganie |
|---|---|
| RO-12 | Rodzic widzi **listę opłat** nałożonych na jego dziecko (składki cykliczne i jednorazowe) wraz ze statusem: do zapłaty / opłacona / zaległa. |
| RO-13 | Rodzic dostaje **powiadomienie push** o nowej opłacie oraz przypomnienia o zaległej (mechanizm Nudge). |
| RO-14 | Sposób opłacenia zależy od wariantu z modułu 5: wariant 0 — rodzic widzi kwotę, numer rachunku szkółki i tytuł przelewu; wariant A — płaci w aplikacji (BLIK/przelew przez operatora). |

> Rodzic dziecka w drużynie trenera **bez koordynatora** nie widzi sekcji opłat w ogóle
> (patrz TR-04 i moduł 4).

### Subskrypcja (model przychodowy)

| ID | Wymaganie |
|---|---|
| RO-06 | Aby dodać pierwsze dziecko, rodzic musi zaakceptować **subskrypcję**: pierwsza opłata po **14 dniach**, opłata miesięczna **5 zł**. |
| RO-07 | Bez aktywnej subskrypcji nie można dodać dziecka. |
| RO-08 | Rodzic może anulować subskrypcję w okresie próbnym (do 13. dnia) **bez opłat**. |
| RO-09 | Po anulowaniu subskrypcji rodzic nie może korzystać z funkcji aplikacji do czasu jej ponownej aktywacji — z wyjątkami wymaganymi przez sklepy: zarządzanie subskrypcją (paywall) i **usunięcie konta** muszą pozostać dostępne. |

> Uwagi wynikające z mechaniki Apple IAP / Google Play Billing (tor subskrypcji z modułu 5):
>
> - Okres próbny i anulowanie **obsługuje sklep**, nie nasza aplikacja — anulowanie
>   w trakcie trialu skutkuje brakiem opłaty (RO-08 realizuje się „za darmo", ale granicę
>   wyznacza mechanika sklepu, nie nasz „13. dzień").
> - Trial 14 dni przysługuje zwykle **raz na konto Apple/Google** — rodzic po anulowaniu
>   i powrocie nie dostanie drugiego okresu próbnego.
> - Backend musi znać status subskrypcji (webhooki App Store Server Notifications /
>   Google Real-Time Developer Notifications lub warstwa typu RevenueCat), bo od niego
>   zależą blokady RO-07/RO-09.
> - **Wygaśnięcie subskrypcji nie usuwa danych:** dzieci pozostają w drużynach (trener
>   widzi je nadal), rodzic traci jedynie dostęp do funkcji do czasu wznowienia —
>   do potwierdzenia z właścicielami.

### Pytania otwarte

- Czy e-mail potwierdzający rejestrację / przypomnienie hasła ma być taki sam jak obecnie dla trenera? Jeśli tak — trzeba go **uczynić bardziej generycznym**, bo obecna treść mówi o organizacji sparingów. - zostawić taki sam tylko zroibc bardziej ogólny
- (z TR-03) Czy prośba o dołączenie dziecka wymaga akceptacji trenera, czy dziecko od razu trafia na listę drużyny ze statusem „czeka na akceptację"? - tylko powiadomie i wtedy decyduje ale pasuej żeby widizał gdzies na liscie ze to dziecko czeka oczkeuje/odrzucone.
- **Dwoje rodziców jednego dziecka** (mama i tato, osobne konta): czy dziecko może być powiązane z dwoma kontami rodziców, czy jedno dziecko = jedno konto rodzica? Jeśli jedno — ryzyko duplikatów (każdy rodzic definiuje „swoje" dziecko niezależnie). Założenie robocze na etap 1: **jedno dziecko = jedno konto rodzica**. - udostępnienie dziecka drugiemu rodzicowi!!!
- Czy subskrypcja jest **per konto rodzica** (dowolna liczba dzieci w cenie 5 zł), czy per dziecko? Założenie robocze: per konto. - per konto!!!!
- **Usunięcie konta rodzica** (funkcja już istnieje dla trenera): co z dziećmi (odpięcie z drużyn + powiadomienie trenera) i z subskrypcją — subskrypcji w sklepie **nie da się anulować z backendu**, rodzic musi to zrobić sam; ekran usuwania konta musi o tym wyraźnie informować. 
- Czy rodzic i trener to **rozłączne typy kont**? Trener będący jednocześnie rodzicem musiałby mieć dwa konta (dwa e-maile). Założenie robocze na etap 1: rozłączne. zgadza się (obadać czy da się na jednym mailu to pomyslenia ale moze tryby profilu)

### Kolejny etap (poza wyceną etapu 1)

- **Komunikatory per drużyna:**
  - a) rodzice (grupa rodziców drużyny),
  - b) trener / rodzice (grupa całej drużyny),
  - c) trener / rodzic (rozmowa 1:1).

**Wycena:** _(do uzupełnienia)_
**Status:** _(do uzupełnienia)_

---

## Moduł 4: Profil koordynatora (nowy typ konta)

**Tytuł:** Nowy typ konta: koordynator

Profil koordynatora szkółki piłkarskiej — zarządza trenerami i opłatami w ramach szkółki.

> Opłaty istnieją **wyłącznie w kontekście koordynatora**. Trener nieprzypisany do żadnego
> koordynatora działa po staremu (patrz TR-04), a rodzice dzieci z jego drużyn nie widzą
> żadnych opłat w aplikacji.

### Zakres — etap 1

| ID | Wymaganie |
|---|---|
| KO-01 | Koordynator dodaje do siebie trenerów. |
| KO-02 | **Wyszukiwarka trenerów** z możliwością zaproszenia trenera do szkółki; trener dostaje powiadomienie i akceptuje lub odrzuca zaproszenie. |
| KO-03 | Koordynator może w każdej chwili usunąć trenera ze swojej listy. |
| KO-04 | Koordynator widzi wszystkie drużyny i wszystkie dzieci danego trenera. |
| KO-05 | Koordynator może nakładać opłaty: **cykliczną „składkę"** na okres „od–do" lub „od – bez daty końca", oraz **pojedynczą opłatę** z własnym tytułem (np. „składka turniej", „opłata obóz"). Może też samodzielnie tworzyć własne opłaty cykliczne. |
| KO-07 | Składka cykliczna jest definiowana **per drużyna** — różne drużyny tej samej szkółki mogą mieć różne kwoty (np. starszy rocznik droższy). |
| KO-06 | **Deduplikacja składek przy wielu drużynach (patrz RO-05):** dziecko należące do dwóch drużyn tej samej szkółki dostaje **jedną składkę miesięczną**, nie sumę składek obu drużyn. Ponieważ składki są per drużyna (KO-07) i mogą się różnić kwotą, potrzebna jest reguła wyboru składki — do ustalenia niżej. Składki od **różnych szkółek** (różnych koordynatorów) są niezależne — dziecko w dwóch szkółkach płaci składkę w każdej z nich. |
| KO-08 | **Modyfikacja kwot per dziecko przed akceptacją:** nałożenie opłaty jest dwukrokowe. Koordynator definiuje opłatę na drużynę (kwota bazowa XXX zł), po czym widzi listę dzieci drużyny z tą kwotą i **przed finalną akceptacją może zmodyfikować kwotę indywidualnie dla wybranych dzieci** (obniżka np. dla rodzeństwa, zwolnienie = 0 zł, sytuacja losowa). Dopiero akceptacja publikuje opłaty rodzicom (powiadomienia RO-13). Obejmuje to zarówno składki cykliczne, jak i opłaty jednorazowe. |

> ⚠️ KO-05 wiąże się bezpośrednio z tematem **zarządzania płatnościami** (moduł 5) —
> wycena zależna od decyzji o dostawcy płatności.

### Do ustalenia (opłaty a wiele drużyn)

- **Reguła wyboru składki dla dziecka w dwóch drużynach tej samej szkółki** (konsekwencja
  KO-06 + KO-07, gdy drużyny mają różne kwoty). Warianty do decyzji właścicieli:
  a) automatycznie **wyższa** kwota, b) składka drużyny **„macierzystej"** (pierwszej),
  c) koordynator **ręcznie wskazuje**, która składka obowiązuje. Nigdy suma.
  Rekomendacja: wariant c) z podpowiedzią wyższej kwoty — szkółki mają różne polityki,
  a ręczna decyzja koordynatora pokrywa wszystkie przypadki.
  - tylko, płaci tylko jedną składkę w klubie!! 99% te składki są takie same, satwka wyższa, decyzja kordynatora

- Opłaty **jednorazowe** (turniej, obóz) są przypisane do konkretnej drużyny/wydarzenia —
  te mogą wystąpić równolegle z różnych drużyn i **nie podlegają deduplikacji**
  (dziecko jedzie na dwa obozy = dwie opłaty). TAK
  
- Moment naliczenia składki przy dołączeniu w trakcie miesiąca: pełna kwota, proporcjonalnie,
  czy od kolejnego okresu? Analogicznie: czy opuszczenie drużyny w trakcie okresu przerywa
  naliczanie od kolejnego cyklu? A przy zmianie drużyny na droższą/tańszą w trakcie okresu —
  od kiedy obowiązuje nowa kwota?
- kordynator steruje kiedy stawka rusza (wakcje od skłądek uwzględnić od kiedy do kiedy, wybór meisiecy dnia, możliwosc nałożenia ręcznego) 
  
- (z KO-08) Indywidualna kwota przy składce **cyklicznej**: obowiązuje we wszystkich
  kolejnych cyklach do odwołania (założenie robocze), czy tylko w bieżącym? Czy koordynator
  może zmienić indywidualną kwotę także **po** akceptacji (ze skutkiem od kolejnego cyklu)?
  Dziecko dołączające do drużyny w trakcie trwania składki dostaje kwotę bazową drużyny —
  koordynator może ją indywidualnie skorygować przy akceptacji zgłoszenia.

### Kolejny etap (poza wyceną etapu 1)

- **Raporty i eksporty:** zestawienia zaległości per drużyna/dziecko, eksport wpłat do
  księgowości (CSV/Excel), podsumowania miesięczne dla szkółki.
- **Wielu koordynatorów w jednej szkółce** (np. koordynator główny + pomocniczy
  z ograniczonymi uprawnieniami).

**Wycena:** _(do uzupełnienia)_
**Status:** _(do uzupełnienia)_

---

## Moduł 5: Płatności do szkółek

**Tytuł:** Płatności pobierane przez szkółkę piłkarską

**Cel:** zbieranie płatności od rodziców na rzecz szkółki piłkarskiej.

### Charakterystyka naszego przypadku (kluczowa dla wyboru rozwiązania)

- Odbiorcą płatności jest **szkółka (podmiot)**, nie osoba prywatna — model
  **konsument → podmiot**, a nie P2P. Wymaga weryfikacji podmiotu (KYB), nie osób fizycznych.
- Składka jest cykliczna, ale realny przepływ to **pojedyncze transakcje**:
  koordynator nakłada opłatę (KO-05) → system generuje żądanie płatności →
  rodzic dostaje push → płaci (np. BLIK). Nie potrzebujemy recurring billing u operatora —
  przypomnienia o zaległej składce obsłuży istniejąca infrastruktura powiadomień (Nudge).
- Kwoty rzędu **50–300 zł/mies.** (składki), nie mikropłatności — opłaty stałe operatora
  nie są problemem.
- Ewidencję „kto, kiedy, ile" w kontekście dziecka/rodzica/drużyny **budujemy sami**
  (własny rejestr płatności w bazie) — niezależnie od wyboru dostawcy, bo żaden operator
  nie zna tych relacji.
- Skala startowa: kilkanaście–kilkadziesiąt szkółek — onboarding szkółki może być
  półręczny, nie musi być w pełni automatyczny przez API od pierwszego dnia.
- **Subskrypcja rodzica (RO-06) to osobny tor:** odblokowuje funkcje cyfrowe, więc musi iść
  przez **Apple In-App Purchase / Google Play Billing** (prowizja sklepów 15–30%, czyli
  realnie ~3,50–4,25 zł z każdych 5 zł). Do rozważenia warstwa pośrednia (np. RevenueCat)
  zamiast własnej weryfikacji paragonów po obu stronach.
- **Składki za treningi NIE podlegają Apple IAP / Google Play Billing** — to płatność
  za usługę fizyczną konsumowaną poza aplikacją (treningi), a Apple i Google wprost
  wyłączają takie płatności ze swoich systemów billingowych (Google podaje wręcz przykład
  karnetów na siłownię). Możemy więc przyjmować składki przez PSP (BLIK/przelew) wewnątrz
  aplikacji mobilnej **bez ryzyka blokady w sklepach** i bez prowizji 15–30%. Warunek:
  nie mieszać tego toru z płatnościami za cyfrowe funkcje samej aplikacji (subskrypcja
  rodzica pozostaje w IAP).

### Rozważane warianty

#### Wariant 0 — MVP bez operatora płatności (ewidencja + zwykły przelew)

Koordynator definiuje opłaty (KO-05), rodzic widzi w aplikacji kwotę, numer rachunku szkółki
i tytuł przelewu, płaci zwykłym przelewem bankowym, a koordynator ręcznie oznacza wpłatę
jako opłaconą.

| Plusy | Minusy |
|---|---|
| Zero prowizji, umów z operatorem i ryzyka regulacyjnego | Ręczne księgowanie wpłat przez koordynatora |
| Najszybszy i najtańszy do wdrożenia | Brak płatności „jednym kliknięciem" (BLIK) |
| Cała ewidencja „kto, kiedy, ile" już działa i zostaje na kolejne etapy | Brak automatycznej informacji o wpłacie (statusy aktualizuje człowiek) |
| Naturalny etap 1 — płatności online dobudowujemy jako etap 2 na gotowym rejestrze | |

#### Wariant A — polski operator marketplace: Przelewy24 lub PayU (rekomendowany na etap 2)

Szkółka jako **submerchant** u operatora; rodzic płaci BLIK-iem/przelewem, środki trafiają
do szkółki przez mechanizm marketplace, aplikacja nie dotyka pieniędzy.

**A1 — Przelewy24 Marketplace:**

| Plusy | Minusy |
|---|---|
| Model submerchanta = dokładnie nasz przypadek (konsument → podmiot) | Marketplace wymaga indywidualnej umowy — termin uruchomienia częściowo poza naszą kontrolą |
| BLIK i szybkie przelewy — standard na rynku PL | API onboardingu rachunku submerchanta **do potwierdzenia z P24** (warunek brzegowy) |
| Niski koszt (cennik bazowy ~1,29% + 0,30 zł; przy składce 150 zł to ~2,24 zł) | Mniej elastyczne API niż u konkurencji; część funkcji poza standardową dokumentacją |
| Mniejsza złożoność integracji (płatność + webhook + dispatch) | Słabsza opcja przy ewentualnej ekspansji zagranicznej |
| Środki u licencjonowanego operatora — niskie ryzyko regulacyjne po naszej stronie | |

**A2 — PayU Marketplace:**

| Plusy | Minusy |
|---|---|
| Dedykowany produkt marketplace: automatyczny podział środków do submerchantów, **platforma nie obsługuje środków szkółek** | Cennik marketplace niepubliczny — konieczne negocjacje |
| **Merchant Registration API** — onboarding szkółki (KYC/AML, umowa, aktywacja) zintegrowany z aplikacją; PayU zawiera umowę bezpośrednio ze szkółką | Bardziej sformalizowany/restrykcyjny proces weryfikacji szkółek |
| Duże zaufanie marki (Allegro), stabilność | Zwykle droższy od P24/Tpay dla mniejszych wolumenów |
| Możliwość pobierania prowizji platformowej z transakcji | |

> Wybór A1 vs A2 nastąpi po zebraniu ofert — kluczowym kryterium jest odpowiedź na pytanie
> o API onboardingu submerchanta (pyt. 3) i koszty. **Tpay** zbieramy jako trzecią ofertę:
> potencjalnie najtańszy (taryfa Business ~0,99% + 99 zł/mies., BLIK Płatności Powtarzalne),
> ale jego model marketplace/submerchant wymaga **pisemnego potwierdzenia**, że obsłuży
> nasz przypadek.

#### Wariant B — Mangopay (plan B)

Infrastruktura portfelowa: rodzic → PayIn → wallet → transfer → wallet szkółki → payout
na rachunek bankowy.

| Plusy | Minusy |
|---|---|
| Pełne API do onboardingu odbiorcy (Recipients, IBAN, KYB, SCA) — konfiguracja rachunku w całości z poziomu aplikacji | Prawdopodobnie droższy dla prostych płatności PLN (wycena indywidualna) |
| Natywna historia transakcji per wallet | Większa złożoność wdrożenia (users / wallets / transfers / payouts) |
| Portfele, opóźnione wypłaty, escrow — gdyby model się rozbudował | Przechowywanie środków i escrow — funkcje, których **obecnie nie potrzebujemy** |
| Lepszy przy ekspansji na inne rynki/waluty | BLIK nie jest rdzeniem oferty; do weryfikacji warunki dla rynku PL |

> Stripe Connect — **odrzucony** decyzją właściciela (dodatkowo: najwyższe prowizje
> ~1,5–2,5% + 1 zł oraz opłata za każde aktywne subkonto szkółki ~2 USD/mies.).
> Mollie Connect — odnotowany, bez przewagi kosztowej dla rynku PL; nie zbieramy oferty.
>
> Wskazówka negocjacyjna: u polskich operatorów pytać wprost o **„ofertę dla platformy
> typu marketplace"** — dedykowane cenniki często nie są publikowane na stronach.

### Zasady projektowe (niezależne od wyboru operatora)

1. **Nigdy nie przyjmujemy pełnej kwoty płatności na rachunek naszej firmy.** Środki mają
   trafiać bezpośrednio od rodzica do szkółki (submerchant / rozliczenie bezpośrednie
   u operatora). Model „rodzic płaci nam → my przelewamy szkółce" generuje obowiązki
   VAT, AML, reklamacyjne i odpowiedzialność za środki klientów — jest wykluczony.
2. **Ewentualnym przychodem aplikacji jest wyłącznie prowizja platformowa** (jeśli
   właściciele zdecydują o jej pobieraniu) — nigdy cała kwota składki.
3. **Dokument sprzedaży dla rodzica wystawia szkółka** (zgodnie ze swoim modelem
   księgowym) — nie operator i nie my. Aplikacja przechowuje tylko identyfikator
   transakcji, status i kwotę.
4. **Model cykliczny bez automatycznego obciążania karty:** jednorazowa płatność
   miesięczna inicjowana przez rodzica (BLIK / szybki przelew) + przypomnienie push
   o terminie i zaległości (istniejąca infrastruktura Nudge) + automatyczne oznaczanie
   zaległości w ewidencji. Opcjonalne zapisanie karty / BLIK One Click — jako
   rozszerzenie, nie fundament.
5. **Nie budujemy własnych sald ani portfeli** z realnymi środkami użytkowników.
6. **Pozycjonowanie prawne — dostawca oprogramowania (SaaS):** regulamin aplikacji ma
   jasno stwierdzać, że jesteśmy wyłącznie dostawcą oprogramowania, a **stronami
   transakcji płatniczej są Rodzic i Szkółka**. Fakturę/paragon za zajęcia wystawia
   rodzicowi szkółka; my wystawiamy szkółce fakturę za korzystanie z systemu
   (abonament lub % od obrotu — do decyzji właścicieli).

### Rekomendacja

1. **Etap 1: Wariant 0** — ewidencja opłat i wpłat bez operatora (najmniejsze ryzyko,
   wartość dla szkółek od razu, fundament pod płatności online).
2. **Etap 2: Wariant A (polski operator marketplace)** — zebrać oferty od **P24, PayU
   i Tpay** z identycznym opisem modelu i tą samą listą pytań; wybór A1 (P24) vs A2 (PayU)
   po odpowiedziach, z kluczowym kryterium: API onboardingu submerchanta + koszty.
   **Wariant B (Mangopay)** tylko jeśli żaden polski operator nie zapewni akceptowalnego
   onboardingu przez API lub warunków marketplace.
3. **Subskrypcja rodzica** — równolegle, wyłącznie przez Apple IAP / Google Play Billing.
   Składki za treningi — poza IAP (usługa fizyczna), przez wybranego operatora PSP.

### Zapytanie ofertowe do operatorów (P24 / PayU / Tpay)

Ten sam opis modelu do każdego operatora:

> Budujemy platformę SaaS dla szkółek piłkarskich. Rodzic dokonuje w aplikacji płatności
> za zajęcia (składki 50–300 zł/mies., głównie BLIK), a środki mają być rozliczane
> bezpośrednio na rzecz zweryfikowanej szkółki (submerchant). Nasza spółka jest wyłącznie
> operatorem platformy i nie przyjmuje na własny rachunek środków należnych szkółkom.
> Czy możecie obsłużyć taki model marketplace/submerchant, w którym szkółka przechodzi
> KYC/KYB u operatora i jest stroną otrzymującą środki? Czy możliwe jest również pobranie
> prowizji platformy z każdej transakcji? Prosimy o dedykowaną ofertę dla platformy
> typu marketplace.

### Pytania do operatorów (przed decyzją o wariancie A)

Onboarding i przepływ środków:
1. Czy szkółka może być osobnym submerchantem/sprzedawcą, a środki trafiać **bezpośrednio na jej rachunek**?
2. Czy nasza firma staje się **stroną transakcji**, czy wyłącznie operatorem platformy? (kluczowe podatkowo)
3. Czy rejestracja i zmiana rachunku bankowego submerchanta ma API, czy wymaga procesu ręcznego / panelu operatora?
4. Jakie wymagania KYC/KYB stawiane są szkółkom — czy obsługiwane są **fundacje, stowarzyszenia i JDG** — i ile trwa weryfikacja?
5. Czy akceptowane są płatności za usługi sportowe świadczone dzieciom?

Koszty i rozliczenia:
6. Jakie są koszty dispatchu (przekazania środków do submerchanta) i wypłat? Jak często realizowane są wypłaty i czy wypłaty na polskie rachunki są dodatkowo płatne?
7. Czy platforma może pobierać prowizję platformową i czy jest liczona od pełnej kwoty, czy od części szkółki?
8. Kto odpowiada za chargebacki, fraud i refundacje (np. rezygnacja rodzica z zajęć)?

Integracja:
9. Czy platforma może pobierać historię transakcji konkretnego submerchanta przez API?
10. Czy operator wysyła webhooki po płatności, refundacji i wypłacie? Czy dostępne są raporty / eksport księgowy?
11. Czy środki są przekazywane automatycznie, czy platforma inicjuje dispatch?
12. Czy dostępne są płatności cykliczne / zapisanie karty (jako opcja na przyszłość)?

### Pytania otwarte (decyzje właścicieli)

- Kto ponosi koszty prowizji od transakcji: doliczana rodzicowi, potrącana szkółce,
  czy pokrywana przez aplikację?
- Czy prowizja aplikacji od składek ma być pobierana (model przychodowy), czy 100% trafia do szkółki?

### Ryzyka

| Ryzyko | Komentarz |
|---|---|
| **DAC7** — obowiązki raportowe platformy pośredniczącej w transakcjach | Do potwierdzenia z doradcą podatkowym; dotyczy wariantów A i B, nie dotyczy wariantu 0 |
| Status platformy mimo rozliczeń bezpośrednich | Nawet gdy środki idą wprost do szkółki, operator/urząd może uznać nas za platformę z określonymi obowiązkami — rozstrzyga umowa i regulamin operatora; do weryfikacji z prawnikiem |
| Negocjacje umowy marketplace | Termin uruchomienia częściowo zależny od operatora; mitygacja: równoległe oferty P24 / PayU / Tpay |
| Zależność od migracji kont sklepowych (OG-03) | Uruchomienie subskrypcji i płatności wymaga wcześniejszego przeniesienia aplikacji na konta deweloperskie spółki właścicieli — założenie kont (D-U-N-S dla Apple) i transfer trzeba rozpocząć odpowiednio wcześnie |
| Prowizja sklepów od subskrypcji rodzica | 15–30% z 5 zł/mies. — wpływ na model przychodowy do zaakceptowania przez właścicieli |
| Chargebacki i refundacje | Do ustalenia w umowie z operatorem: kto obsługuje i ponosi koszt |

**Wycena:** _(do uzupełnienia — osobno: wariant 0, wariant A, subskrypcja IAP)_
**Status:** _(do uzupełnienia)_


