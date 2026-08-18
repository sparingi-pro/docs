# Opis produktu — Sparingi.PRO

> Dokumentacja funkcjonalna (biznesowa). Opisuje, czym jest aplikacja i jaki problem rozwiązuje.
> Stan zgodny z wersją 2.0.1 (weryfikowane z kodem źródłowym).

## Czym jest Sparingi.PRO?

**Sparingi.PRO** to mobilna platforma do organizacji meczów towarzyskich (sparingów) i turniejów
dla trenerów drużyn sportowych. Zamiast dziesiątek telefonów i notatek — trener publikuje
ogłoszenie o wolnym terminie, a inni trenerzy zgłaszają się przez aplikację.

- **Platformy:** Android i iOS (aplikacja mobilna) + panel administracyjny w przeglądarce
- **Język:** polski
- **Grupa docelowa:** trenerzy drużyn (piłkarskich i innych), prowadzący jedną lub wiele grup

## Jaki problem rozwiązuje?

| Problem trenera | Rozwiązanie w aplikacji |
|---|---|
| Godziny spędzone na telefonie w poszukiwaniu rywala | Wyszukiwarka sparingów z filtrami (rocznik, poziom, lokalizacja, daty) |
| Chaos w ustaleniach i notatkach | Kalendarz ze statusami + wbudowany czat z historią rozmów |
| Brak odpowiedzi na zgłoszenia | Automatyczne przypomnienia push co 2 godziny (system „Nudge") |
| Prowadzenie kilku grup wiekowych | Wiele drużyn na jednym koncie, filtrowanie po drużynie |
| Dojazd na mecz | Klikalna lokalizacja sparingu — otwiera nawigację w mapach systemowych |

## Główni aktorzy

### Trener (użytkownik aplikacji mobilnej)
Rejestruje konto, definiuje swoje drużyny, publikuje ogłoszenia o sparingach, zgłasza się
na sparingi innych trenerów, akceptuje lub odrzuca zgłoszenia, komunikuje się przez czat.

### Administrator (użytkownik panelu WWW)
Zarządza użytkownikami, drużynami, sparingami i czatami. Publikuje oferty partnerów,
promuje wybrane sparingi (przypinanie na górze listy), wysyła powiadomienia globalne
do wszystkich użytkowników.

## Kluczowe pojęcia

| Pojęcie | Znaczenie |
|---|---|
| **Sparing** | Mecz towarzyski 1 na 1 — organizator + jedna drużyna przeciwna |
| **Turniej** | Wydarzenie dla wielu drużyn — organizator + konfigurowalna liczba uczestników |
| **Drużyna** | Zespół trenera: nazwa, klub, rocznik (np. U16), poziom (podstawowy / średnio zaawansowany / zaawansowany) |
| **Zgłoszenie** | Chęć udziału w cudzym sparingu — czeka na akceptację organizatora |
| **Sparing cykliczny** | Seria sparingów generowana automatycznie co tydzień (do 25 wystąpień) |
| **Oferta** | Ogłoszenie partnera (np. obozy, sprzęt) publikowane przez administratora, kieruje do zewnętrznej strony |
| **Sparing promowany** | Sparing przypięty przez administratora na górze listy wyszukiwania |

## Moduły aplikacji (widok z lotu ptaka)

```
┌────────────────────────  Aplikacja mobilna (trener)  ───────────────────────┐
│  Kalendarz   Sparingi (szukaj/moje)   Wiadomości   Oferty   Profil          │
│      │              │                     │           │        │            │
│      └── statusy    └── dodawanie,        └── czat    └── linki └── drużyny, │
│          kolorami       zgłoszenia,           1:1        partnerów  dane,   │
│                         filtry, mapa                             regulamin  │
└──────────────────────────────────┬───────────────────────────────────────────┘
                                   │ REST API + powiadomienia push
┌──────────────────────────────────┴───────────────────────────────────────────┐
│                          Backend (serwer)                                    │
│  konta, sparingi, drużyny, czaty, powiadomienia, oferty, przypomnienia Nudge │
└──────────────────────────────────┬───────────────────────────────────────────┘
                                   │
┌──────────────────────────────────┴───────────────────────────────────────────┐
│                 Panel administratora (przeglądarka WWW)                      │
│  Użytkownicy   Drużyny   Sparingi (+promowanie)   Czaty   Oferty   Broadcast │
└───────────────────────────────────────────────────────────────────────────────┘
```

## Kanały komunikacji z użytkownikiem

1. **Powiadomienia push** — zgłoszenia, akceptacje, odrzucenia, anulowania, nowe wiadomości,
   przypomnienia Nudge, komunikaty globalne od administratora.
2. **Centrum powiadomień w aplikacji** — historia powiadomień ze statusem przeczytane/nieprzeczytane.
3. **E-maile** — potwierdzenie rejestracji, reset hasła (szablony HTML).
4. **Czat w aplikacji** — rozmowy 1:1 między trenerami w kontekście sparingu.

## Dalsza lektura

- [02 — Funkcje aplikacji z perspektywy trenera](02-funkcje-uzytkownika.md)
- [03 — Cykl życia sparingu](03-cykl-zycia-sparingu.md)
- [04 — Przypadki użycia](04-przypadki-uzycia.md)
- [05 — Panel administratora](05-panel-administratora.md)
