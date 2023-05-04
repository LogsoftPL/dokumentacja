# Lite WMS Changelog

## 2023.03.03
### Uwzglednienie wymagań BHP uwzględniających płeć przy tworzniu zbiórki grupowej
Z uwzgledenieniem: 
- całkowtej wagi grupy zleceń przypisanej do zbiórki dla uzytkownika, 
- maksymalnej wagi sztuki kóra moze uzytkownik danej płci podnieść.

## 2023.05.04
### Zrobiona i przerzucona na bazy do reszty użytkowników procedura do automatycznego wydawania jakiś stocków. _[doc_autoStockSendtoWZ]_
Przed każdym uruchomieniem tej procedury trzeba odpalić tę procedurę i sprawdzić wszytskie dane: 
-czy w danej bazie wszystkie procedury się zgadzają, 
-czy odpowiednie procedury do planowania oraz potwiewrdzania zadań są w procedurze, 
-należy zmienić zmienne dla konkretnego klienta/zleceniodawcy/centrum logistycznego, 
-sprecyzować jakie stocki ma wybierać. 
Dla primavery na naszej będzie puszczona ta procedura w harmonogramie do wydawania braków. 
