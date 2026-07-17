# Testy integracji EDI: JMP ↔ Operator Logistyczny — etap SIT

**Dokument uniwersalny.** Jedna kopia obsługuje wszystkie wdrożenia JMP z operatorami
logistycznymi (Uniq, Hart, kolejne). Dane konkretnego wdrożenia wpisuje się w Kartę
integracji poniżej — reszta dokumentu jest wspólna.

> Zakres tej wersji: **wyłącznie SIT**. Testy biznesowe (BIT) — uzgodnienie sald, pełne cykle
> procesowe — zostaną opisane osobno, po zamknięciu SIT.

---

## Karta integracji

*Wypełnić przed rozpoczęciem testów. To jedyne miejsce z danymi konkretnego operatora.*

| | |
|---|---|
| **Operator logistyczny (LO)** | ☐ Uniq Logistic ☐ Hart ☐ inny: …………… |
| **GLN operatora — podmiot** (używany w kopercie komunikatu) | …………… |
| **GLN JMP** | `5900001300015` |
| **Lokalizacje LO objęte integracją** (GLN + nazwa) | …………… |
| **Kanał wymiany** | ☐ SFTP bezpośredni ☐ przez operatora EDI (jaki: ……………) |
| **Komunikaty w zakresie** | ☐ ORDERS ☐ RECADV ☐ DESADV (wydanie) ☐ INVRPT ☐ DESADV (zmiana zapasu) |
| **Wersje specyfikacji** | RECADV: …… DESADV: …… INVRPT: …… |
| **Osoba testująca po stronie JMP** | …………… |
| **Osoba kontaktowa po stronie LO** | …………… |
| **Data testów** | …………… |

---

## 1. Dla kogo jest ten dokument

Testy SIT wykonuje **logistyk po stronie JMP**, nie informatyk. **Nie musisz znać EDIFACT-u.**
Rozdział 3 to krótki przewodnik, który pozwoli Ci przeczytać plik na tyle, żeby odpowiedzieć
na jedno pytanie przy każdej pozycji: *czy operator przysłał wszystko, czego potrzebuję?*

Gdzie potrzebna jest wiedza techniczna, jest to wyraźnie oznaczone jako **[IT]** — takie
pozycje odznacza informatyk, nie Ty.

## 2. Czym jest SIT i po co go robimy

**SIT sprawdza, czy komunikat zawiera wszystko, co przewiduje specyfikacja — segment po
segmencie, pole po polu.** Nie sprawdza, czy proces biznesowy się domyka (od tego będzie BIT).
Sprawdza, czy **plik jest kompletny i poprawny formalnie**.

### Dlaczego to musi być zrobione nadmiarowo

Największym ryzykiem tej integracji jest **cicha zgodność**: system potrafi przyjąć komunikat
z brakującym polem i nie zgłosić żadnego błędu. Wszystko wygląda dobrze, dane wpadają,
nikt nic nie zauważa — a problem wychodzi po miesiącach jako rozbieżność zapasu, której nikt
nie umie wytłumaczyć.

Dlatego obowiązują trzy zasady:

1. **Punktem odniesienia jest specyfikacja, a nie reakcja systemu.** „Przeszło, więc jest
   dobrze" **nie jest** wynikiem testu. Test przechodzi, gdy **wszystkie wymagane pola zostały
   wysłane w wymaganym formacie** — nawet jeśli akurat ich nie używamy.
2. **Odstępstwo wymaga pisemnej zgody.** Jeśli operator czegoś nie wysyła, a specyfikacja tego
   wymaga, to **defekt** — dopóki JMP nie potwierdzi na piśmie, że tego pola nie oczekuje.
   Ustalenie mailem jest OK; ustalenie „przez telefon, chyba mówili, że nie trzeba" — nie.
3. **Specyfikacja to nie to samo co wdrożenie.** Dokumenty mają różne wersje i lata
   (2009, 2015, 2016). Trzeba potwierdzić, **która wersja obowiązuje po stronie JMP dzisiaj** —
   to pozycja U-06 w liście ustaleń.

### Podział potwierdzeń

Każda pozycja ma dwie kolumny:

- **LO** — operator logistyczny potwierdza: *„wysyłamy to, zgodnie ze specyfikacją"*.
- **JMP** — JMP potwierdza: *„przyjmujemy to i tego potrzebujemy; tak wygląda nasze wdrożenie"*.

`☐` odznaczamy z **datą i osobą**. `—` oznacza, że dana strona nie potwierdza tej pozycji.
**Pozycja bez potwierdzenia JMP tam, gdzie jest wymagane, nie jest zamknięta** — nawet jeśli
u operatora działa.

---

## 3. Jak czytać plik EDI — minimum dla logistyka

Plik to zwykły tekst. Każda linia to **segment** zakończony apostrofem `'`. Pierwsze trzy
litery mówią, co to za linia. Pola rozdziela `+`, a wartości wewnątrz pola — dwukropek `:`.

Przykład z komunikatu wydania:

```
BGM+351::9+WZ1234/26+9'      ← nagłówek: to jest dokument WYDANIA nr WZ1234/26
DTM+137:20260717:102'        ← data dokumentu: 17.07.2026
NAD+SU+5905721779028::9'     ← kto wydaje (lokalizacja operatora)
NAD+BY+5900001300015::9'     ← kto kupuje (JMP)
LIN+1++5900112651355:EN'     ← pozycja nr 1, kod EAN towaru
PIA+1+663592:BP::92'         ← indeks tego towaru W SYSTEMIE JMP  ← NAJWAŻNIEJSZE
QTY+12:4224'                 ← ilość wydana: 4224
```

Segmenty, które musisz rozpoznawać:

| Kod | Co oznacza | Na co patrzysz |
|---|---|---|
| `UNB` | koperta — kto do kogo wysyła plik | dwa GLN-y: nadawca i odbiorca |
| `BGM` | nagłówek dokumentu | typ dokumentu i jego numer |
| `DTM` | data | **liczba po `+` mówi, jaka to data** (patrz niżej) |
| `NAD` | strona transakcji | **litery po `+`**: `SU` dostawca, `BY` kupujący, `SF` skąd, `DP` dokąd, `GY` lokalizacja zapasu |
| `LIN` | początek pozycji towarowej | numer pozycji i kod EAN |
| `PIA` | **indeks towaru w systemie JMP** | to po tym JMP rozpoznaje towar |
| `QTY` | ilość | **liczba po `+` mówi, jaka to ilość** (patrz niżej) |
| `RFF` | numer referencyjny | `ON` zamówienie, `AAU` list przewozowy, `AAK` WZ dostawcy, `BT` partia, `SE` seria leku |
| `UNT` / `UNZ` | stopki, liczniki | **[IT]** |

**Daty — liczba po `DTM+` mówi wszystko:**

| Kod | Znaczenie |
|---|---|
| `137` | data dokumentu |
| `11` | data wysłania towaru |
| `17` | planowana data dostawy |
| `50` | data przyjęcia towaru |
| `334` | data i czas utworzenia dokumentu u operatora |
| `366` | **data, na którą podany jest stan zapasu** |
| `36` / `361` | data ważności partii |

**Ilości — liczba po `QTY+`:**

| Kod | Znaczenie |
|---|---|
| `12` | ilość wydana |
| `194` | ilość przyjęta |
| `17` | zapas **dostępny** |
| `255` | zapas **zablokowany** |
| `73` / `195` / `269` / `61` | korekty i zwroty |

> **Zapamiętaj jedno:** `PIA` z literami `BP` to **indeks towaru w systemie JMP**. Jeśli jest
> zły, JMP nie rozpozna pozycji — bez względu na to, jak poprawna jest reszta pliku.

---

## 4. Ustalenia wspólne — **przed** rozpoczęciem testów

To są rzeczy, których **nie ma w żadnej specyfikacji**. Bez nich testy nie mają sensu.

| # | Do ustalenia | LO | JMP |
|---|---|:---:|:---:|
| U-01 | Kanał wymiany: SFTP bezpośredni czy przez operatora EDI. Kto odpowiada za retencję i retransmisję plików | ☐ | ☐ |
| U-02 | GLN operatora używany w kopercie | ☐ | ☐ |
| U-03 | GLN JMP (`5900001300015`) | ☐ | ☐ |
| U-04 | Lista lokalizacji operatora objętych integracją — **i potwierdzenie, że JMP je rozpoznaje** | ☐ | ☐ |
| U-05 | **Mapowanie kodów `BGM` z zamówień**: który kod oznacza wydanie, a który przyjęcie | ☐ | ☐ |
| U-06 | **Która wersja specyfikacji obowiązuje we wdrożeniu JMP dzisiaj** | ☐ | ☐ |
| U-07 | Czy JMP oczekuje **numerów partii i dat ważności** przy przyjęciu | ☐ | ☐ |
| U-08 | Czy JMP oczekuje **numerów SSCC palet** przy wydaniu | ☐ | ☐ |
| U-09 | Czy w zakresie są **korekty i zwroty** | ☐ | ☐ |
| U-10 | **Częstotliwość raportu zapasu** i czy `DTM+366` ma być dniem zamkniętym czy bieżącym | ☐ | ☐ |
| U-11 | Zakres i termin komunikatu **zmiany zapasu** (likwidacje, inwentury) | ☐ | ☐ |
| U-12 | Kto po stronie JMP decyduje o odstępstwach i jak je zgłaszać | ☐ | ☐ |

> **U-05 to najgroźniejsza pozycja na tej liście.** Jeśli kod `BGM` z zamówienia nie zostanie
> rozpoznany, system operatora **domyślnie** potraktuje je jako przyjęcie i odpowie
> potwierdzeniem przyjęcia zamiast awizem wydania. Nie pojawi się żaden błąd — komunikat
> po prostu pójdzie w złą stronę. Mapowanie musi być potwierdzone na piśmie i przetestowane
> w **obie** strony.

---

## 5. Poziomy GLN — skąd biorą się najczęstsze pomyłki

Ten sam operator ma **wiele GLN-ów o różnym przeznaczeniu** i nie są one wymienne:

| Poziom | Gdzie w pliku | Co to jest | Przykład |
|---|---|---|---|
| **Koperta** | `UNB` | GLN **podmiotu prawnego** operatora — adres, na który idzie plik | GLN spółki |
| **Komunikat** | `NAD+SU`, `NAD+SF`, `NAD+GY` | GLN **lokalizacji fizycznej** — konkretny magazyn | GLN magazynu w Gdańsku |

Oba są poprawne, oba należą do operatora — ale mylenie ich to **najczęstsze źródło błędów
w tej integracji**. Objawy, po których to poznasz:

- w kopercie nadawca i odbiorca to **ten sam GLN** → plik „od JMP do JMP", błąd konfiguracji;
- w `NAD+SU` widzisz GLN spółki zamiast magazynu → nie wiadomo, z której lokalizacji poszedł towar;
- w raporcie zapasu brakuje magazynu → prawdopodobnie **nie ma wpisanego GLN-u** po stronie
  operatora i został po cichu pominięty (patrz Z-04).

---

## 6. Scenariusze SIT

Legenda wyniku: **OK** = zgodne · **NIE OK** = brak lub odstępstwo → defekt lub pisemna zgoda JMP.

### SIT-A — Zamówienie (ORDERS): czy operator odczytał je poprawnie

**Po co:** zamówienie od JMP jest źródłem wszystkiego, co wróci. Błąd tutaj propaguje się dalej.

| # | Co sprawdzasz | Dlaczego to ważne | LO | JMP |
|---|---|---|:---:|:---:|
| A-01 | Operator utworzył zlecenie z Twojego pliku | podstawa całego cyklu | ☐ | ☐ |
| A-02 | Kod `BGM` rozpoznany zgodnie z U-05 — wydanie poszło jako wydanie | zły kod = odpowiedź w złą stronę, bez błędu | ☐ | ☐ |
| A-03 | Zlecenie trafiło do **właściwego magazynu** | operator wyda z innej lokalizacji | ☐ | ☐ |
| A-04 | Ten sam plik wysłany dwa razy **nie tworzy dwóch zleceń** | podwójne wydanie towaru | ☐ | — |
| A-05 | **[IT]** Polskie znaki i składnia koperty obsłużone | uszkodzone nazwy i adresy | ☐ | ☐ |

### SIT-B — Potwierdzenie przyjęcia (RECADV)

**Po co ten komunikat:** operator mówi JMP „przyjąłem od dostawcy tyle a tyle towaru".

| # | Co musi być w komunikacie | W pliku | Dlaczego to ważne | LO | JMP |
|---|---|---|---|:---:|:---:|
| B-01 | Oznaczenie „przyjęcie towaru" + numer dokumentu | `BGM+632::9+<nr>+9` | bez tego JMP nie wie, co to za dokument | ☐ | ☐ |
| B-02 | Numer dokumentu **unikalny, maks. 25 znaków** | `BGM` | duplikaty numerów bywają odrzucane | ☐ | ☐ |
| B-03 | Data dokumentu | `DTM+137` | | ☐ | ☐ |
| B-04 | **Data przyjęcia towaru** | `DTM+50` | inna niż data dokumentu; ważna dla rozliczeń | ☐ | ☐ |
| B-05 | Numer zamówienia JMP | `RFF+ON` | powiązanie z zamówieniem | ☐ | ☐ |
| B-06 | **Numer WZ dostawcy** | `RFF+AAK` | powiązanie z dokumentem dostawcy | ☐ | ☐ |
| B-07 | Kupujący = JMP | `NAD+BY` | | ☐ | ☐ |
| B-08 | Sprzedający = **dostawca zewnętrzny**, nie operator | `NAD+SU` | inaczej nie wiadomo, od kogo przyszedł towar | ☐ | ☐ |
| B-09 | Miejsce dostawy = operator **wraz z nazwą i adresem** | `NAD+DP` | sam GLN to za mało wg specyfikacji | ☐ | ☐ |
| B-10 | Numer pozycji **taki sam jak w zamówieniu** | `LIN` | inaczej nie da się dopasować pozycji | ☐ | ☐ |
| B-11 | Kod EAN obecny (nawet jeśli JMP go ignoruje) | `LIN` | segment obowiązkowy wg specyfikacji | ☐ | ☐ |
| B-12 | **Indeks towaru w systemie JMP** | `PIA+…:BP::92` | **bez tego JMP nie rozpozna towaru** | ☐ | ☐ |
| B-13 | Ilość przyjęta, w jednostkach podstawowych | `QTY+194` | bez jednostki miary — tylko liczba | ☐ | ☐ |
| B-14 | **Numer partii + data ważności** — jeśli U-07 = tak | `RFF+BT` + `DTM+36`/`361` | każda partia musi być osobną pozycją | ☐ | ☐ |
| B-15 | Korekty przyjęcia — jeśli U-09 = tak | `QTY+61` | | ☐ | ☐ |
| B-16 | **[IT]** Liczniki i numery referencyjne zgodne | `UNT`, `UNZ` | | ☐ | — |

### SIT-C — Awizo wydania (DESADV)

**Po co ten komunikat:** operator mówi JMP „wydałem tyle a tyle towaru, jedzie do Was".

| # | Co musi być w komunikacie | W pliku | Dlaczego to ważne | LO | JMP |
|---|---|---|---|:---:|:---:|
| C-01 | Oznaczenie „wydanie towaru" + numer dokumentu | `BGM+351::9+<nr>+9` | | ☐ | ☐ |
| C-02 | Numer dokumentu **unikalny, maks. 25 znaków** | `BGM` | limit wprost w specyfikacji | ☐ | ☐ |
| C-03 | Data dokumentu wydania | `DTM+137` | | ☐ | ☐ |
| C-04 | **Data wysłania towaru** | `DTM+11` | kiedy towar faktycznie wyjechał | ☐ | ☐ |
| C-05 | **Planowana data dostawy** | `DTM+17` | gdy operator jej nie zna — ma być równa dacie wysłania | ☐ | ☐ |
| C-06 | **Data i czas utworzenia dokumentu u operatora** | `DTM+334` | rozstrzyga kolejność zdarzeń | ☐ | ☐ |
| C-07 | Numer zlecenia JMP (STO) | `RFF+ON` | | ☐ | ☐ |
| C-08 | **Numer listu przewozowego** | `RFF+AAU` | powiązanie z transportem | ☐ | ☐ |
| C-09 | Dostawca / kupujący / skąd / dokąd | `NAD+SU`, `BY`, `SF`, `DP` | `SF` musi wskazywać **lokalizację**, nie spółkę | ☐ | ☐ |
| C-10 | Kod EAN obecny | `LIN` | obowiązkowy, choć ignorowany | ☐ | ☐ |
| C-11 | **Indeks towaru w systemie JMP** — dla **wszystkich** pozycji, także palet | `PIA+…:BP::92` | palety są normalnym towarem i muszą być rozliczone | ☐ | ☐ |
| C-12 | Ilość wydana | `QTY+12` | | ☐ | ☐ |
| C-13 | **Numer pozycji ze zlecenia JMP** | `RFF+LI` | dopasowanie pozycji do zamówienia | ☐ | ☐ |
| C-14 | **Numery SSCC palet** — jeśli U-08 = tak | `PCI+33E` + `GIN+BJ` | jeden SSCC na pozycję | ☐ | ☐ |
| C-15 | Korekty i zwroty — jeśli U-09 = tak | `QTY+73`/`195`/`269` | | ☐ | ☐ |
| C-16 | **[IT]** Liczniki, `CPS` dokładnie raz | `UNT`, `UNZ`, `CPS` | | ☐ | — |

### SIT-D — Raport zapasu (INVRPT)

**Po co ten komunikat:** operator pokazuje JMP stan magazynu na dany dzień — do uzgodnienia sald.

| # | Co musi być w komunikacie | W pliku | Dlaczego to ważne | LO | JMP |
|---|---|---|---|:---:|:---:|
| D-01 | W kopercie nadawca = operator, odbiorca = JMP, **i są to różne GLN-y** | `UNB` | ten sam GLN po obu stronach = plik „od JMP do JMP" | ☐ | ☐ |
| D-02 | Numer raportu | `BGM+35+<nr>+9` | | ☐ | ☐ |
| D-03 | Data sporządzenia raportu | `DTM+137` | | ☐ | ☐ |
| D-04 | **Data, na którą podany jest stan** | `DTM+366` | zgodna z U-10; **może być inna niż data raportu** | ☐ | ☐ |
| D-05 | **Osobny raport na każdy magazyn**, każdy ze swoim GLN-em lokalizacji | `NAD+GY` | inaczej nie wiadomo, gdzie leży towar | ☐ | ☐ |
| D-06 | **Wszystkie Twoje magazyny są w pliku** | — | magazyn bez GLN-u u operatora **znika po cichu**, bez błędu | ☐ | ☐ |
| D-07 | Kod EAN obecny | `LIN` | obowiązkowy, choć ignorowany | ☐ | ☐ |
| D-08 | **Indeks towaru w systemie JMP** | `PIA+…:BP::92` | | ☐ | ☐ |
| D-09 | Zapas **dostępny** i **zablokowany** rozdzielone | `QTY+17` / `QTY+255` | mylenie ich zafałszuje dostępność | ☐ | ☐ |
| D-10 | **Zapas ujemny** ze znakiem minus, kod ilości bez zmian | `QTY+17:-5` | | ☐ | ☐ |
| D-11 | **Seria produktu leczniczego** — jeśli dotyczy | `RFF+SE` | każda seria osobną pozycją | ☐ | ☐ |
| D-12 | Raport zawiera **pełny stan**, nie próbkę | — | patrz Z-03 | ☐ | ☐ |
| D-13 | **[IT]** Liczniki | `UNT`, `UNZ` | | ☐ | — |

### SIT-E — Sytuacje wyjątkowe

| # | Co sprawdzasz | Dlaczego to ważne | LO | JMP |
|---|---|---|:---:|:---:|
| E-01 | Gdy wysyłka pliku się nie uda, w systemie operatora **zostaje ślad z przyczyną** | inaczej nikt się nie dowie, że plik nie doszedł | ☐ | — |
| E-02 | Treść komunikatu jest zapisana i możliwa do podejrzenia po fakcie | bez tego nie da się zdiagnozować rozbieżności | ☐ | — |
| E-03 | Błąd jednej integracji nie zatrzymuje pozostałych | | ☐ | — |
| E-04 | **[IT]** Numer kontrolny koperty jest unikalny dla każdego pliku | powtarzalny numer bywa odrzucany jako duplikat | ☐ | ☐ |

---

## 7. Znane pułapki — sprawdź je świadomie

| # | Pułapka | Jak się objawia | LO | JMP |
|---|---|---|:---:|:---:|
| Z-01 | **Nierozpoznany kod `BGM`** → domyślnie przyjęcie | na wydanie przychodzi potwierdzenie przyjęcia; **zero błędów** | ☐ | ☐ |
| Z-02 | **GLN spółki zamiast lokalizacji** w `NAD+SU`/`SF` | nie wiadomo, z którego magazynu poszedł towar | ☐ | ☐ |
| Z-03 | **Tryb demo raportu zapasu** zostawiony włączony | raport pokazuje kilkanaście pozycji zamiast pełnego stanu — wygląda jak **załamanie magazynu** | ☐ | ☐ |
| Z-04 | **Magazyn bez GLN-u** u operatora | znika z raportu zapasu po cichu, bez ostrzeżenia | ☐ | ☐ |
| Z-05 | **Zmiana ścieżki wysyłki nie wywołuje błędu** | nieistniejący katalog jest tworzony; test „zepsutej ścieżki" fałszywie przechodzi | ☐ | — |
| Z-06 | **Stały numer kontrolny** w konfiguracji | wszystkie pliki z tym samym numerem → odrzucenia | ☐ | — |
| Z-07 | **Palety potraktowane jako śmieć** | palety **są** normalnymi indeksami JMP; filtrowanie ich = braki w rozliczeniu | ☐ | ☐ |

---

## 8. Zamknięcie SIT

| # | Warunek | LO | JMP |
|---|---|:---:|:---:|
| K-01 | Wszystkie ustalenia wspólne (rozdz. 4) potwierdzone | ☐ | ☐ |
| K-02 | Wszystkie scenariusze SIT-A…E zaliczone | ☐ | ☐ |
| K-03 | Pułapki z rozdz. 7 sprawdzone świadomie | ☐ | ☐ |
| K-04 | **Rejestr odstępstw** zamknięty — każda pozycja „NIE OK" ma pisemną zgodę JMP albo poprawkę | ☐ | ☐ |
| K-05 | Zgoda na przejście do testów biznesowych (BIT) | ☐ | ☐ |

### Rejestr odstępstw

| # | Pozycja | Czego brakuje / co odbiega | Decyzja JMP | Dowód (mail, data, osoba) |
|---|---|---|---|---|
| | | | ☐ akceptujemy ☐ do poprawy | |
| | | | ☐ akceptujemy ☐ do poprawy | |

> **Brak dowodu = defekt.** Pozycja „NIE OK" bez pisemnej zgody JMP blokuje K-04.
