
## Załozenie i konfiguracja zleceniodawcy

 - - [ ] Załozenie zlecniodawcy
 - - [ ] Nadać uprawnienia zlecniodawcy


## Założenie centrum logistycznego

 - - [ ] Załozenie centrum
 - - [ ] Nadać uprawnienia do centrum
 - - [ ] Zalozyc strefę/strefy zbiórki 
 - - [ ] Zalozyc obszar/obszary zbiórki (wymaga strefy zbiórki) 
 - - [ ] Zalozyc Lokacje 
  > trzeba pamietac zeby była co najmniej jedna brama 
  
  > Zweryfikowac czy klasy lokacji odpowidaja wymaganiaom klienta

## Atrybuty

### Status jakosci 
Specjalny atrybut **stk_attrib9** ktróry zawsze pownnien byc zakładany(najlepiej skopiowac z istniejacego zlecniodawcy trzba pamietac zeby ustawic odpowiednie warości w sekcji zapytania:

- Lista wartości w ComboBox

```sql SELECT  dslr_code as ID , dslr_code as CODE  from dic_stockLockReason with (nolock) where isnull(dslr_OrdererID, <ORDERER_ID>) =  <ORDERER_ID> ```

- Domyślna wartość
```sql  select coalesce(nullif((select top 1 dori_attrib9  from doc_orderitem where dori_orderid = @door_id and dori_productid = @prd_ID order by dori_id   ),''),
(SELECT top 1 dslr_code FROM dic_stockLockReason where  dslr_isDefault = 1 
 and (dslr_OrdererID = <ORDERER_ID>
 or isnull(dslr_OrdererID,0) =0 ) order by dslr_OrdererID desc))
 ```
 - Wartość domyślna zamówienie
```sql select case when door_orderDocumentTypeID = 1 then null else
(SELECT top 1 dslr_code FROM dic_stockLockReason where  dslr_isDefault = 1 and isnull(dslr_OrdererID,door_ordererID) = door_ordererID order by dslr_OrdererID desc) end
from doc_order with (nolock)
where door_id = @dori_orderID
```
Kolejnym ważnym atrybutem jest stk_attrib1, czyli LOT(numer partii). Powinien być uwzględniany przy przyjęciach. W klasyfikacji GS1 ma numer IZ10.

## Sortowanie produktów do wydania 
 - - [ ] Wprowadzając atrybuty dotyczące dat produkcji('IZ11')/dat bestbefore('IZ15')/ dat ważności('IZ17') do grupy magazynowej ustawiamy im sortowanie czyli 'kolejność sortowania' oraz 'typ sortowania'
 - - [ ] Wchodzimy w Parametry systemowe. Tam szukamy 'stkGeneratePickingPlanMetod', który określa kolejność sortowania w systemie stocków przy wysyłce. Zmieniamy wartość parametru zgdnie z założeniami klienta sugerując się opisem.
# Metody kolejkowania wysyłanych produktów:
 -FIFO 'First In First Out', czyli pierwsze weszło, pierwsze wyszło
 -LIFO 'Last In First Out', czyli ostatnie weszło, pierwsze wyszło
 -FEFO 'First Expired First Out', czyli pierwsze wychodzą z magazynu produkty, których data ważności kończy się najwcześniej.
 


