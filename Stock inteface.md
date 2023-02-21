
# Logsoft - Lite WMS 

# Interface wymiany danych, komunikat Dokument-Stoki

Dokument zawiera ustandaryzowany format pozwalający uzyskać informacje związane ze stanami magazynowymi wraz z pełną informacją o produktach w WMS.

## Obiekt Document

| Pole | Opis | Typ danych| Pole WMS
|--|--|--|--|
|Type| zawsze Stocks | varchar(5)  
|Orderer|kod zleceniodawcy z WMS | nvarchar(25)  | `dord_code`
Logistics_Center|centrum logistyczne z WMS | nvarchar(25)  |`whc_code`
Products| Dane słownikowe produktów na stanach magazynowych wraz z ich ilością |Kolekcja|


## Kolekcja Products

| Pole | Opis | Typ danych| Pole WMS
|--|--|--|--|
|Code|kod porduktu jednoznacznie identyfikuje produkt musi byc unikatowy w obrębie jednego zleceniodawcy|nvarchar(50) |`prd_code`
|Name|nazwa produktu, nie musi byc unikatowa.|nvarchar(250) |`prd_name`
|Warehouse_group|Grupa magazynowa w sytemie WMS|varchar(250)|
|EANs|Kolekcja kodów kreskowych |kolekcja
|Packaging_structure|Struktura pakowania produktu.|kolekcja
|Attributes|Atrybuty produktu (patrz kolekcja Attributes, zastosowana również w kolekcji Stocks)|kolekcja
|Stocks|Kolekcja stanów magazynowych, w rozbicu na nośniki i lokacje (patrz kolekcja Attributes|kolekcja

### Opakowania - kolekcja Packaging_structure

Struktura pakowania.

| Pole | Opis | Typ danych| Pole WMS
|--|--|--|--|
|Unit_of_measure|podstawowa jednostka miary. |varchar(25) |`uom_code`
|Weight|Waga brutto dla podstawowej jednostki miary wyrazona w ***kg***| decimal(18,6) |pplv_weight
|Volume|Objętość podstawowej jednostki miary wyrazona w ***m3***| decimal(18,6) |pplv_volume
|Units_in_package|ilość jednostek podatwowych w opakowaniu(kartonie).| decimal(18,6) |
|Unit_of_package|jednostka miary dla opakowania (kartonu) |varchar(25) |`uom_code`
|Units_on_pallet|ilość jednostek podatwowych na palecie. | decimal(18,6) |
|Unit_of_pallet|jednostka miary dla palety |varchar(25) |`uom_code`


## Kolekcja atrybuty

Kolekcja atrybuty może posiadać Max 20 obiektów. 

| Pole | Wymagane | Opis | Typ danych| Pole WMS |
|--|--|--|--|--|
|Name|T | kod atrybutu z definicji atrybutów systemu WMS | varchar(50) |`pdef_code`
|Value|T |wartość wstawiana do odpowiedniego atrybutu | varchar(50) |`prd_attribXX` lub 'stk_attribX'


## Kolekcja stocks

| Pole | Opis | Typ danych| Pole WMS 
|--|--|--|--|
|SSCC|Numer nośnika, palety| varchar(25) |`stlu_SSCC`
|Quantity|Ilość produktu na nośniku |decimal(18,6) |`stk_basicQuantity`
|Location|Kod lokacji na której znajduje się nośnik/produkt|varchar(25) |`whlo_code`
|item_attribute|Atrybuty pozycji dokumentu Jeśli nie będzie zdefiniowanego atrybutu Status jakości wstawiona zostanie wartość domyślna dla statusu jakości|kolekcja
|Attributes|Atrybuty stoku (patrz kolekcja Attributes, zastosowana również w kolekcji Products)|kolekcja


## Przykłady

Przykład dokumetu typu IN:
```json
{
	"Document": [
		{
			"Type": "Stocks",
			"Orderer": "JTI Polska Sp. Z O.O.",
			"Logistics_Center": "CL Olechowska",
			"Products": [
				{
					"Code": "11311736",
					"Name": "PG RF-0443",
					"Quantity": 1000.0,
					"Warehouse_group": "Magazyn główny",
					"EANs": [
						{
							"EAN": "5904479051219"
						}
					],
					"Packaging_structure": [
						{
							"Unit_of_measure": "szt",
							"Weight": 0.0,
							"Volume": 0.0,
							"Units_in_package": 12.0,
							"Unit_of_package": "KRT",
							"Units_on_pallet": 70.0,
							"Unit_of_pallet": "EURO-PAL"
						}
					],
					"Attributes": [
						{
							"Name": "Producent",
							"Value": "producent xbc"
						}
					],
					"Stocks": [
						{
							"SSCC": "PA1089808",
							"Location": "BC-01-45",
							"Quantity": 400.0,
							"Attributes": [
								{
									"Name": "Status jakości",
									"Value": "GOOD"
								},
								{
									"Name": "Partia ",
									"Value": "13760698"
								},
								{
									"Name": "Data produkcji",
									"Value": "2022-02-28"
								},
								{
									"Name": "Lokalizacja",
									"Value": "BRAK"
								}
							]
						},
						{
							"SSCC": "PA1089809",
							"Location": "BC-02-45",
							"Quantity": 600.0,
							"Attributes": [
								{
									"Name": "Status jakości",
									"Value": "GOOD"
								},
								{
									"Name": "Partia ",
									"Value": "13760698"
								},
								{
									"Name": "Data produkcji",
									"Value": "2022-02-28"
								},
								{
									"Name": "Lokalizacja",
									"Value": "BRAK"
								}
							]
						}
					]
				}
			]
		}
	]
}```
