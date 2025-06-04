# Mapa pól JSON: Baselinker → Link (WMS24.Link)

### Opracowano na podstawie:

* Przykładów JSON z obu systemów
* Dokumentacji [xOrder](#xorder) i [xOrderItem](#xorderitem) (\[pełna dokumentacja]\(link dokumetaacja.md))

---

## Tabela mapowania głównych pól zamówienia

| **Baselinker**            | **Link (WMS24.Link)**                   | **Typ docelowy (z dokumentacji)** | **Opis / Uwagi**                                                     |
| ------------------------- | --------------------------------------- | --------------------------------- | -------------------------------------------------------------------- |
| `order_id`                | `id`                                    | int (readonly)                    | Identyfikator zamówienia                                             |
| `external_order_id`       | `marketPlaceDocNr`                      | string                            | Numer zamówienia marketplace/allegro                                 |
| `order_source`            | `sourceInfo`                            | string                            | Źródło (np. 'allegro', 'allegro-pl')                                 |
| `order_source_id`         | `sourceConfigId`                        | int                               | Identyfikator źródła/konfiguracji marketplace                        |
| `order_status_id`         | `orderStatus.id`                        | int                               | Id statusu (w Link jest strukturą xStatus)                           |
|                           | `orderStatus.name`                      | string                            | Nazwa statusu                                                        |
| `confirmed`               | `confirmed`                             | bool                              | Informacja czy zamówienie potwierdzone (nie zawsze występuje w Link) |
| `date_confirmed`          | `orderStatusDate`                       | datetime                          | Data potwierdzenia, konwersja timestamp → ISO                        |
| `date_add`                | `creationDate`                          | datetime                          | Data utworzenia zamówienia                                           |
| `date_in_status`          | brak/jest w historii                    |                                   | Data zmiany statusu, nie jest mapowana 1:1, w Link `orderStatusDate` |
| `user_login`              | `marketplaceUserLogin`                  | string                            | Login użytkownika w marketplace                                      |
| `phone`                   | `receiverAddress.contactPhone`          | string                            | Telefon odbiorcy                                                     |
| `email`                   | `receiverAddress.contactEmail`          | string                            | Email odbiorcy                                                       |
| `user_comments`           | `userComments`                          | string                            | Komentarz klienta                                                    |
| `admin_comments`          | `adminComments`                         | string                            | Komentarz administratora                                             |
| `currency`                | `currency`                              | string                            | Kod waluty (np. PLN)                                                 |
| `payment_method`          | `paymentMethod`                         | string                            | Nazwa metody płatności                                               |
| `payment_done`            | `paymentValue`                          | double                            | Wartość zapłacona                                                    |
|                           | `paid`                                  | bool                              | Flaga zapłaty (Baselinker nie posiada - wynika z wartości)           |
| `delivery_method`         | `shippingServiceSourceExternalName`     | string                            | Nazwa metody dostawy                                                 |
| `delivery_price`          | `shippingPrice`                         | float                             | Koszt dostawy                                                        |
| `delivery_fullname`       | `receiverAddress.name`                  | string                            | Imię i nazwisko odbiorcy                                             |
| `delivery_address`        | `receiverAddress.street`                | string                            | Ulica i numer domu odbiorcy                                          |
| `delivery_city`           | `receiverAddress.city`                  | string                            | Miasto odbiorcy                                                      |
| `delivery_postcode`       | `receiverAddress.zipCode`               | string                            | Kod pocztowy odbiorcy                                                |
| `delivery_country_code`   | `receiverAddress.country`               | string                            | Kod kraju odbiorcy (np. PL)                                          |
| `delivery_point_id`       | `parcelLocker`                          | string                            | Id punktu odbioru (np. paczkomat, automat One Box)                   |
| `delivery_point_name`     | brak (można mapować do extraField)      | string                            | Nazwa punktu odbioru                                                 |
| `delivery_point_address`  | brak (można mapować do extraField)      | string                            | Adres punktu odbioru                                                 |
| `delivery_point_postcode` | brak                                    | string                            | Kod pocztowy punktu                                                  |
| `delivery_point_city`     | brak                                    | string                            | Miasto punktu                                                        |
| `invoice_fullname`        | `invoiceAddress.name`/`invoiceFullName` | string                            | Imię i nazwisko na fakturze                                          |
| `invoice_company`         | `invoiceCompany`/`invoiceAddress.name`  | string                            | Firma na fakturze (jeśli występuje)                                  |
| `invoice_nip`             | `invoiceNip`                            | string                            | NIP firmy (jeśli występuje)                                          |
| `invoice_address`         | `invoiceAddress.street`                 | string                            | Ulica i nr domu (faktura)                                            |
| `invoice_city`            | `invoiceAddress.city`                   | string                            | Miasto (faktura)                                                     |
| `invoice_postcode`        | `invoiceAddress.zipCode`                | string                            | Kod pocztowy (faktura)                                               |
| `invoice_country_code`    | `invoiceAddress.country`                | string                            | Kraj (faktura)                                                       |
| `want_invoice`            | `wantInvoice`                           | bool                              | Czy klient chce fakturę (Baselinker: string '1'/'0', Link: bool)     |
| `order_page`              | `orderPage`                             | string                            | Link do zamówienia w panelu                                          |
| `products[]`              | `items[]`                               | List<xOrderItem>                  | Lista produktów (patrz szczegóły niżej)                              |
| `delivery_company`        | brak, ew. extraField                    | string                            | Nazwa firmy odbiorczej, zwykle puste                                 |
| `delivery_state`          | brak                                    | string                            | Województwo odbiorcy (w Link nieużywane)                             |
| `invoice_state`           | brak                                    | string                            | Województwo faktura (w Link nieużywane)                              |
| `extra_field_1/2`         | `extraField1/2`                         | string                            | Pola dodatkowe                                                       |

---

## Mapa produktów (pojedynczy element listy zamówienia)

| **Baselinker `products[]`** | **Link `items[]`**       | **Typ docelowy** | **Opis / Uwagi**                                      |
| --------------------------- | ------------------------ | ---------------- | ----------------------------------------------------- |
| `order_product_id`          | `id`                     | int (readonly)   | Id pozycji zamówienia (techniczne)                    |
| `name`                      | `productName`            | string           | Nazwa produktu                                        |
| `quantity`                  | `orderedQuantity`        | double           | Ilość zamówiona                                       |
| `product_id`                | `productId`              | int              | Id produktu (jeśli istnieje w Link)                   |
| `sku`                       | `productCode`            | string           | Kod produktu                                          |
| `ean`                       | `productEAN`             | string           | EAN                                                   |
| `price_brutto`              | `priceBrutto`            | double           | Cena brutto za sztukę                                 |
| `tax_rate`                  | `taxRate`                | double           | Stawka VAT                                            |
| `variant_id`                | `productAlternativeCode` | string           | Kod wariantu (jeśli dotyczy)                          |
| `auction_id`                | `offer`                  | string           | Id aukcji / oferty allegro                            |
| `weight`                    | `productWeight`          | double           | Waga produktu (jeśli dotyczy)                         |
| `location`                  | `productWarehouseGroup`  | string           | Lokalizacja/magazyn                                   |
| `attributes`                | brak dedykowanego        | string           | Atrybuty - można zmapować do custom fields            |
| `bundle_id`                 | brak                     | int              | Identyfikator zestawu (jeśli dotyczy)                 |
| brak w Baselinker           | `confirmedQuantity`      | double           | Potwierdzona ilość (przepisujemy z ordered)           |
| brak w Baselinker           | `priceNetto`             | double           | Cena netto (można wyliczyć na podstawie brutto i VAT) |
| brak w Baselinker           | `set`                    | string           | Oznaczenie zestawu, jeżeli dotyczy                    |
| brak w Baselinker           | `boughtDate`             | datetime         | Data zakupu (pobierana z zamówienia głównego)         |

---

## Pola specjalne i różnice

* **Daty**:  Baselinker trzyma daty jako timestampy, a Link oczekuje ISO8601 (`"2025-03-31T13:17:30.013"`).
* **Adresy**:  W Link wszystkie dane adresowe lądują w obiekcie `receiverAddress` i `invoiceAddress` (`xAddress`).  Mapujemy odpowiednio pola z Baselinker (delivery\_\* → receiverAddress, invoice\_\* → invoiceAddress).
* **Statusy**:  Link posiada rozbudowane statusy (`orderStatus`, `erpStatus`, `wmsStatus`) jako obiekty xStatus, natomiast Baselinker podaje status tylko jako id.
* **ExtraFields**:  Pola dodatkowe, które nie mają dedykowanych odpowiedników, można wprowadzać do `extraField1/2` lub zostawić puste.

---

## Przykładowa mapa pól na realnych danych

| **Baselinker (wartość)**                    | **Link (wartość)**                 | **Komentarz**                                  |
| ------------------------------------------- | ---------------------------------- | ---------------------------------------------- |
| `"order_id": 747473295`                     | `"id": 986943`                     | Id w Baselinker nie zawsze równa się id w Link |
| `"external_order_id": "...c82575"`          | `"marketPlaceDocNr": "...c82575"`  | Zachowujemy identyczną wartość                 |
| `"order_source": "allegro"`                 | `"sourceInfo": "allegro-pl"`       | Link rozróżnia np. 'allegro-pl'                |
| `"order_status_id": 307557`                 | `"orderStatus": { "id": 130, ...}` | Tłumaczymy id na statusy systemowe w Link      |
| `"phone": "+48790004010"`                   | `receiverAddress.contactPhone`     | Przepisujemy 1:1                               |
| `"email": "au5lffjx4f+...@..."`             | `receiverAddress.contactEmail`     | Przepisujemy 1:1                               |
| `"delivery_fullname": "Iwona Markiewicz"`   | `receiverAddress.name`             | Przepisujemy 1:1                               |
| `"delivery_address": "Przebiśniegów 4 m.6"` | `receiverAddress.street`           | Przepisujemy 1:1                               |
| `"delivery_city": "Nowa Iwiczna"`           | `receiverAddress.city`             | Przepisujemy 1:1                               |
| `"delivery_postcode": "05-500"`             | `receiverAddress.zipCode`          | Przepisujemy 1:1                               |
| `"delivery_country_code": "PL"`             | `receiverAddress.country`          | Przepisujemy 1:1                               |
| `"delivery_point_id": "AL041WPI"`           | `parcelLocker: "AL041WPI"`         | Przepisujemy 1:1                               |
| `"invoice_fullname": "Iwona Markiewicz"`    | `invoiceFullName`                  | Przepisujemy 1:1                               |
| `"invoice_company": ""`                     | `invoiceCompany`                   | Przepisujemy 1:1 (jeśli puste - null)          |
| `"invoice_nip": ""`                         | `invoiceNip`                       | Przepisujemy 1:1 (jeśli puste - null)          |
| `"want_invoice": "0"`                       | `wantInvoice: false`               | Konwersja string na bool                       |
| `"user_comments": ""`                       | `userComments: null`               | Przepisujemy 1:1                               |

---

## Mapowanie statusów

Baselinker: `order_status_id` → Link: `orderStatus.id`
**Statusy należy mapować wg logiki systemu docelowego (np. 'Wysłane', 'Finished', 'ReadyForProcessing').**

---

## Produkty – przykład mapowania jednej pozycji

### Baselinker

```json
{
  "order_product_id": 1325442144,
  "product_id": "1194826636",
  "name": "Fitomed Krem...",
  "sku": "8033573HURT",
  "ean": "5907504400020",
  "quantity": 3,
  "price_brutto": 20.29,
  "tax_rate": 23,
  "auction_id": "16654190002"
}
```

### Link (WMS24.Link)

```json
{
  "id": 1556644,
  "productName": "Fitomed Krem...",
  "orderedQuantity": 3,
  "productId": 2000179,
  "productCode": "8033573HURT",
  "productEAN": "5907504400020",
  "priceBrutto": 20.29,
  "taxRate": 23,
  "offer": "16654190002"
}
```

**Komentarz:**
Identyfikatory produktu (`product_id` w Baselinker) i (`productId` w Link) mogą się różnić – należy je mapować na podstawie systemu docelowego lub ustalonego słownika.

---

## Typy specjalne i konwersje

* **Flagi boolowskie:**  Baselinker potrafi mieć wartości typu string (`"1"`, `"0"`), Link oczekuje typu bool (`true`/`false`).
* **Liczby:**  Jeśli pole nie istnieje w Baselinker – pole docelowe zostaje puste/null.
* **EAN, SKU, itp.:**  Jeśli nie występują w Baselinker – można wypełnić tylko częściowo.

---

## Załączniki/Linki

* **order\_page**: `orderPage` – Link do zamówienia w systemie, jeśli jest dostępny.

---

## Braki / pola niemapowane

Niektóre pola w Link są specyficzne dla WMS/ERP, np.:

* `erpStatus`, `wmsStatus` – w Baselinker brak odpowiednika.
* `warehouse`, `warehouseId` – jeśli nie zarządzane przez Baselinker.

---

## Źródła

* \[xOrder, xOrderItem – dokumentacja API WMS24.Link]\(link dokumetaacja.md)
* Przykład JSON z Baselinker i Link (2025-03-31)

---

# Podsumowanie

**Proces mapowania** jest w dużej mierze 1:1 dla podstawowych pól (adresy, dane płatności, produkty, wartości liczbowe), a różnice pojawiają się w strukturze statusów, typach danych i obecności niektórych pól (dodatkowe, zaawansowane pola WMS/ERP w Link).
Produkty i adresy są najbardziej bezpośrednie do mapowania – reszta wymaga ew. transformacji typów i formatów dat.

---
