# Dokumentacja biznesowa — Sparingi.PRO

Dokumentacja funkcjonalna aplikacji **Sparingi.PRO** — opisuje, **co można zrobić
w aplikacji i jak ona działa**, językiem zrozumiałym dla osób nietechnicznych
(właściciel produktu, marketing, wsparcie użytkowników, nowi członkowie zespołu).

Dokumentacja opisuje **obecny stan aplikacji ustalony na podstawie kodu źródłowego**
(wersja 2.0.1). Materiały z folderu `informacje/` posłużyły jedynie jako punkt wyjścia —
tam gdzie były niekompletne lub rozbieżne, rozstrzygający był kod.

## Spis treści

| Dokument | Co zawiera | Dla kogo przede wszystkim |
|---|---|---|
| [01 — Opis produktu](01-opis-produktu.md) | Czym jest aplikacja, jaki problem rozwiązuje, aktorzy, kluczowe pojęcia, moduły | Każdy — zacznij tutaj |
| [02 — Funkcje użytkownika](02-funkcje-uzytkownika.md) | Co trener może zrobić — ekran po ekranie (rejestracja, kalendarz, sparingi, czat, oferty, profil, powiadomienia) | Wsparcie użytkowników, marketing |
| [03 — Cykl życia sparingu](03-cykl-zycia-sparingu.md) | Od ogłoszenia do meczu: statusy, zgłoszenia, akceptacje, przypomnienia, reguły edycji | Właściciel produktu, analitycy |
| [04 — Przypadki użycia](04-przypadki-uzycia.md) | 13 scenariuszy krok po kroku (UC-01…UC-13) z wariantami alternatywnymi | Analitycy, testerzy |
| [05 — Panel administratora](05-panel-administratora.md) | Możliwości administratora: użytkownicy, drużyny, promowanie, oferty, broadcast, uprawnienia | Administratorzy, właściciel |

## Najkrótsze wprowadzenie (elevator pitch)

> Sparingi.PRO to aplikacja mobilna dla trenerów drużyn sportowych. Trener publikuje
> ogłoszenie o wolnym terminie na mecz towarzyski, inni trenerzy zgłaszają swoje drużyny,
> a organizator akceptuje wybrane zgłoszenie. Szczegóły dogrywa się na wbudowanym czacie,
> a kalendarz i powiadomienia push pilnują, żeby nic nie umknęło.

## Powiązana dokumentacja techniczna

- Backend i panel administratora: [`SparingiPRO/docs/`](../../SparingiPRO/docs/README.md)
- Aplikacja mobilna: [`SparingiPro.Client/docs/`](../../SparingiPro.Client/docs/README.md)

## Materiały pomocnicze

- `informacje/GOOGLE_PLAY.txt` — opis marketingowy ze sklepu Google Play
- `informacje/sparingi_pro.txt` — zakres projektu (etap 1)
- `informacje/wersja2.0.txt` — lista zmian modernizacyjnych (etap 2.0)

> Uwaga: powyższe materiały mogą być niekompletne — źródłem prawdy dla tej dokumentacji jest kod.
