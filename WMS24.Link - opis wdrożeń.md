## Opis interfejsu WMS <-> WMS24.Link

1. **Gerlach (UPS i DPD):**
	
 a. Serwis insertujący na serwerze Gerlach - WMS24.CourierLink.Sync (C:\LOGSOFT\Import zleceń)
		Zasilany z procedury clink_expTransportOrders oraz widoku clink_expPackages.
		Eksport statusów z WMS do Clink - clink_updateStatusSend
		Update nr LP oraz pola door_tr_currentStatus - clink_updateWMSStatus

 b. Serwis drukujący WMS24.CourierLink.WorkerService na kompie lokalnym w magazynie (Renata, Wacław)

 c. Raport mailowy "UPS RAPORT LISTOW" - via LinkServer
	
2. **DKT (Integracja Baselinker oraz DPD-Witchen):**
	
 a. Insertacz via LinkServer na procedurze - dkt_InsertIntoCLinkNaszaDB
		Dla BL-BestStore: zamówienia dodawane do Clinka przy imporcie z BL (con_BLparseJSONOrderAndRead). Wstawiane ze statusem etykiety "Zablokowana", ustawiany status etykiety "Wygenerowana" na terminalu przy pakowaniu, po czym drukowana. Aktualizacja ilości paczek i nr LP w harmonogramie "BestStore-upd.Il.Paczek".
		Dla Witchen wywoływana z "...confirmPacking"

 b. Serwis drukujący na serwerze DKT (c:\LOGSOFT\CLINK_BL)

 c. Raporty via link server.

 d. Polecenia ppm z grida "Zamówienia od klienta" via link server.
	
3. **UNIQ na naszej, Owner - Uniq-BestService, tylko GLS:**

 a. Insertacz via LinkServer - bsv_InsertIntoCLinkTO_Database wywoływana z doc_generateDocumentPosition. Aktualizacje nr LP clink_updateTrackingNumber (z harmonogramu) 

 b. Serwis drukujący na kompie UNIQ - **!!!zminić ConncectionString!!!**
	
4. **HART (Integracja SUUS):**

 a. Insertacz via LinkServer - har_InsertIntoCLinkTO_Database - wywoływane ppm w "Zamówienia od klienta" -> "Ustaw wysyłkę RohligSuus dla zamówienia". Aktualizacja nr LP poprzez clink_updateTrackingNumber z harmonogramu jobem CLINK-UpdateTrackNum.

 b. Serwis drukujący na kompie HART - **!!!zminić ConncectionString!!!**
	
	
	

	



