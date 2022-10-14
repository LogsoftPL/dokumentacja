# Lite WMS Orange wersja 4.5


# Wysyłki informacje ogólne


Ogólne informacje o wysyłkach

### Typy wysyłek  
Typ wysłki przechowywany jest w tabeli `doc_order` w polu `doc_orderType`

|ID|KOD|Opis|		
|----------------|-------------------------------|-----------------------------|
|10|**Zbiórka automatyczna**  |Standardowa zbiórka z planowaniem
|24|**Zaokr. do pełnych palet**  |Podczas splanowania system zaokrągla ilości do pełnych palet przy planowaniu
|26|**Dowolne produkty**  |Wydanie bez planowania bez tworzenia pozycji zamówienia. System po zeskanowaniu palety sam dodaje odpowiednią pozycje zamówienia. Moze byc też wywołany na PC ze stanów magazynowych
|30|**Bez planowania**  |Wydanie bez planowania na terminalu. System bazując na pozycjach zamówienia podpowiada najlepszą paletę i tworzy zadanie. Moze byc też wywołany na PC ze stanów magazynowych
|31|**Bez planowania**  |Wydanie jak bez planowania pozwala jednak nie tworzenie zbiórek


## Priorytety wysyłek

> ***Priorytet 0 jest priorytetem najniższym wszelkie operacje powinny być sortowane rosnąco***

Ze względu na sposób obsługi priorytety dzielimy wg zakresu na:

| Priorytet| Planowanie | Uzupełnianie |Uzupełnianie|Grupowanie| Uwagi|
|--|--|--|--|--|--|
|0-89|+|+|+|+| standardowe priorytety 
|97|+|+|+| - |specjalny zlecenia o tym priorytecie nie są grupowane i wyswietlaja sie na terminalu na liście zleceń do wyboru
|99|-|-|-| - |specjalny zlecenia o tym priorytecie są pomijane podczas planowania i uzupełniania zbiórki i grupowania


#### Miejsca gdzie wykorzystane są priorytety

-   planowanie wysyłki
-   uzupełnianie zbiórki
-   wydruk kart zbiórki
-   kolejność wyświetlania zleceń na terminalu

## Planowanie wysyłek

### Automatyczne planowanie wysyłek 
Wysyłki planowane automatycznie poprzez wywołanie procedury ```doc_autoGeneratePickingPlan```  wielkość paczki okresla parametr systemowy ```doc_autoGeneratePickingPlan_QTY```
Kolejność przetawrzania zleceń wg:
- **prirytetu**
- **daty modyfikacji** (przy czym w celu unikanięcia planowania ciagle tych samych zleceń pomijane są zlecenia kórych data modyfikacji jest mniejsza niż 20 minut)
- **daty utworzenia**

Kazda próba planowania ustawia detę modyfikacji zlecenia na bieżącą 



### Zasady planowania wysyłek

tu diopisac


# Wydanie grupowe

## Słownik

**Zlecenie jednosztukowe** - zamówienie zawierajaca jedna sztuke towaru wysylkowego (uwaga zlecenie jednosztukowe może być realizowane jako grupowe jeśli w systemie jest   inne aktywne zamówienie dla tego samego klienta z tym samym adresem wysyłkowym)

**Zlecenie multiadres** -  zlecenia powazane w grupie na podstawie adresu dostawy

**Zlecenie pojedyncze** - zamówienie przekraczające jednym z parametrów graniczne wartości dla zlecenia grupowego lub należące do widoku `v_doorGroupSkipGrouping`

**Zlecenie grupowe** - zamówienie klienta zawierające więcej niż jedną sztukę towaru nie będące zleceniem pojedynczym

## Numeratory
WSPÓLNE DLA WSZYSTKICH ZLECENIODAWCÓW
- ZG______ - zbiórka grupowa wielosztukowa (standard)
- ZS______ - zbiórka grupowa zleceń jednosztukowych
- ZB______ - zbiórka batchowa

## Zasady grupowania

- wyszukujemy zlecenia o najwyższym priorytecie
- spośród zleceń o najwyższym priorytecie sprawdzamy czy jest jakieś do którego możemy dobrać inne pod ten sam adres 
- jeśli tak to tworzymy grupę ze wszystkich zleceń pod dany adres **Zlecenie multiadres**  (wliczając w to zlecenie jednosztukowe pod ww adres) pomijamy zlecnia z widoku `v_doorGroupSkipMultiAddress`
- jeśli wybrano zlecenie nie dające się powiązać z innym adresem dodajemy do niego inne grupowe wg standardowych zasad
- jeśli zlecenie sztukowe dobieramy inne zlecenia sztukowe
- jeśli zlecenie za duże żeby grupować lub zostaja spełnikoen warunki z widoku `v_doorGroupSkipGrouping` puszczamy pojedyncze zlecenie



## Parametry grupowanie

| Parametr| Opis | Użyty w |Od wersji|
|--|--|--|--|
|**groupPicking_autoGroup**|włącza wydruk grupowej listy zbiórki na formatce `doc_autoPrintTaskList`|`doc_autoPrintTaskList`||
|**groupPicking_maxOrderQty**|max ilość zleceń w grupie|||
|**groupPicking_maxWeight**|max waga zleceń w grupie|
|**groupPicking_maxVolume**|max objętość zleceń w grupie



## Parametry proces na terminalu

| Parametr| Opis | Użyty w |Od wersji|
|--|--|--|--|
|**groupPickingTer_listShowAllGroups**|Wyswietla wszytkie aktywne grupy/listy zbiórki lub ogranicza tylko do konkretnego uzytkownika|||
|**groupPickingTer_allowChangeToMixWithoutPlanning**, **groupPickingTer_mixOrderTypeID**|W przypadku zamówień bez planowania typu sztukowego lub paletowego z poziomu menu można zmienić taką grupę na mix paletowo-sztukowy. groupPickingTer_mixOrderTypeID to docelowe ID typu zamówienia (raczej stała, ale niech już będzie elastycznie)
|**groupPickingTer_skipProductScanWhenLuIsHomogenous**|Jeśli na nośniku znajduje się tylko jeden produkt oraz nie ma on unikalnego atrybutu (seriala) to możemy pomijać skan produktu
|**groupPickingTer_useFullLuSSCC**(!!!)|Czy przy skanie docelowego boxa wykorzystujemy cały numer (wersja z drukowaniem etykiety na każdy box wysyłkowy już w momencie pickingu - ZGxxxx_01, ZGxxxx_02,... - parametr na **1**) czy operujemy na 01, 02, 03 (oklejony wózek, a nie kartony - parametr na **0**). Niezależnie od ustawienia stlu_sscc zawsze przyjmuje pod spodem format ZGxxxxx_YY |
|**groupPickingTer_allowCreateSingleOrderGroup, groupPickingTer_allowCreateOrderGroup**|**PRZYNAJMNIEJ JEDNO MUSI BYĆ NA 1** - steruje tym, co user może zrobić z poziomu skanera. Mogą być klienci, którzy nie chcą grupować, więc wtedy tylko SingleOrderGroup
|**groupPickingTer_showProposedValuesInInputBox**|W kilku miejscach pokazuje proponowaną wartość jako [wartość] + '_'. Ogólnie WB to chwali, tekst w InputBoxie jest nieco większy i szybciej ludzie ogarniają.
| **TwoStepShippingPicking**| Wprowadzony aby nie kolidować z całopaletowymi zamówieniami|tsk_confirmPickingNEW_grouped
|**allowEditOrderAttribsOnTerminal, returnablePackagesOnShipment, checklistOnShipment** | Wykorzystane już w starych procesach, odpowiada za opcje w menu
|**stkGeneratePickingPlan**|Też już istniejący parametr, ale bardzo ważny. Ustawiony na 0 oznacza, że w przypadku braku zamówienie jest automatycznie wstrzymane.  W innym wypadku user ma możliwość zdecydowania czy zbiera do końca czy anuluje
|**allowOverloadPickingWithoutPlanning**| wykorzystywane przy całopaletowych bez planowania, określa czy można ładować ponad ilość zamówioną
||


## Zmiany w strukturze bazy
|tabela|zmiany|
|--|--|
|doc_orderType|kolumny: dort_isPlanned(bit), dort_hasOrderItems(bit), dort_allowFullLu(bit), dort_allowPicking(bit) + nowy typ zamówienia Mieszane bez planowania (same wartości już trzeba podejrzeć w bazie)

## Zmiany w obecnych obiekach (lepiej na spokojnie porównać, mogą być dostosowywane)
**vdoc_orderRecalculateHelper** - zmiany w sposobie wyliczania ilości zaplanowanej - w przypadku częściowego braku tworzą się nowe zadania i ilość ta wychodziła niepoprawnie
**proces pakowania (u nas PACKING)** - wszędzie gdzie zliczamy ilość zamówioną trzeba podmienić SUM(task_assignedbasicquantity) 
na 
sum(case  when  task_status=103 or (task_basicQuantity>0 and task_pickingstatus in (10,13)) then task_basicquantity else task_assignedbasicquantity end)
(u nas to kroki 15, 24)
**tsk_confirmPacking** - zmiana sposobu wyliczania ilości zamówionej (kolumna ZAMOWIONO - 2 razy występuje)
**proces TRANSFERSHIPMENT** - wykorzystywał task_assignedDestinationXXX... W przypadku zamówień bez planowania tworzą się zadania od razu z task_destinationXXX... Trzeba podmienić na isnull(task_destinationXXX, task_assignedDestinationXXX)
**tsk_confirmPickingNEW** - poprawiona obsługa braku przy zbiórce

TO DO


- [x] jesli potwerdzmy zbiórke **0** info dla wykonawcy ze potwerdzono z brakien
- [x] jesli potwerdzmy zbiórke **0** blokujemy cały stok tego porduktu

## Obiekty do przerzucenia
###### Widoki:
- vstk_availableStockFullLU_strict
###### Funkcje:
- dbo.ter_getOrderlistText(door_id) - Buduje wyswietlany tekst przy liscie zlceń. Uwaga na terminalu z CE wyswietla sie uproszczona wersja w prównaniu z Windows lub Androidem| 
###### Procedury:
- door_groupInfo
- door_groupPlan
- door_groupNextOrderPlanNEW
- tsk_fastShippingFindStock_FAST - może już gdzieś być (wykorzystany w FAST_SHIPMENT_1PCS)
- tsk_fastShippingFindStock_fullLuOnly
- gp_pickingGetGroupList
- gp_generatePickingPathWithoutPlanningGrouped
- gp_getNextFullLuPickingTask
- gp_getNextPickingTaskGrouped - **UWAGA NIE POMYLIC Z ter_getNextPickingTaskGrouped**
- tsk_replanOrderPartly
- tsk_replanOrderPartlyGroup
- gp_getPickingWoPlanningMenuList
- gp_shipPalletNEW_validation
- tsk_generatePickingPathWithoutPlanningGroup
- tsk_confirmPickingNEW_1pcs_grouped
- tsk_confirmPickingNEW_1pcs_grouped_woControl
- tsk_confirmPickingNEW_grouped

## Nieoczywiste zależności
- Jeśli występują produkty z numerami seryjnymi to wymagany jest TwoStepShippingPicking = 1, aby nie stracić informacji przy pakowaniu jednosztukowej zbiórki

