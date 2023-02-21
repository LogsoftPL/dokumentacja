
# Logsoft - Lite WMS 

# Interface wymiany danych, komunikat Dokument-Stoki

Dokument zawiera ustandaryzowany format pozwalający uzyskać informacje związane ze stanami magazynowymi wraz z pełną informacją o produktach w WMS.

## Obiekt Document


| Pole | Opis | Typ danych| Pole WMS
|--|--|--|--|--|
|Type| zawsze Stocks | varchar(5)  
|Orderer|kod zleceniodawcy z WMS | nvarchar(25)  | `dord_code`
Logistics_Center|centrum logistyczne z WMS | nvarchar(25)  |`whc_code`
Products| Dane słownikowe produktów na stanach magazynowych wraz z ich ilością |Kolekcja|

Przykład JSON: 
```json
{
	"Document": [
		{
			"Type": "Stocks",
			"Orderer": "JTI Polska Sp. Z O.O.",
			"Logistics_Center": "CL Olechowska",
			"Products": []
		}
	]
}
```

## Kolekcja Products
| Pole | Opis | Typ danych| Pole WMS
|--|--|--|--|--|
|Code|kod porduktu jednoznacznie identyfikuje produkt musi byc unikatowy w obrębie jednego zleceniodawcy|nvarchar(50) |`prd_code`
|Name|nazwa produktu, nie musi byc unikatowa.|nvarchar(250) |`prd_name`
|Warehouse_group|Grupa magazynowa w sytemie WMS|varchar(250)|
|EANs|Kolekcja kodów kreskowych |kolekcja
|Packaging_structure|Struktura pakowania produktu.|kolekcja
|Attributes|Atrybuty produktu (patrz kolekcja Attributes, zastosowana również w kolekcji Stocks)|kolekcja
|Stocks|Kolekcja stanów magazynowych, w rozbicu na nośniki i lokacje (patrz kolekcja Attributes|kolekcja





## Kolekcja atrybuty


Kolekcja atrybuty może posiadać Max 20 obiektów. Atrybuty, których nazwa będzie niezgodna z nazwą w definicji atrybutów systemu WMS będą ignorowane. Niewymagane


| Pole | Wymagane | Opis | Typ danych| Pole WMS |
|--|--|--|--|--|
|name|T | kod atrybutu z definicji atrybutów systemu WMS | varchar(50) |`pdef_code`
|value|T |wartość wstawiana do odpowiedniego atrybutu nagłówka dokumentu|varchar(50) |`door_attribXX`




Przykład JSON: 
```json
  "document_attribute": {
    "attribute": [
      {
        "name":"customs_number",
        "value": "123456"
      },
      {
        "name": "order_number",
        "value": "hth/2020/829347"
      }
    ]
  }
```

Przykład XML:

```XML
        <document_attribute>
            <attribute>
                <name> customs_number</name>
                <value>123456</value>
            </attribute>
            <attribute>
                <name> order_number</name>
                <value>hth/2020/829347</value>
            </attribute>
        </document_attribute>
```
### Opakowania

Struktura pakowania ***nie aktualizuje sie*** zakładana jest przy pierwszym dodaniu produktu. 

| Pole | Wymagane | Opis | Typ danych| Pole WMS |Od wersji 
|--|--|--|--|--|--|
|unit_of_measure|N |podstawowa jednostka miary. Kod jednostki miary pownien byc zgodny ze słownikiem w systemie WMS. (jeśli pole puste przy zakładaniu nowego produktu jako nazwa zostań domyślna jednostka miary) |varchar(25) |`uom_code`
|weight|N |Waga brutto dla podstawowej jednostki miary wyrazona w ***kg***| decimal(18,6) |pplv_weight
|volume|N |Objętość podstawowej jednostki miary wyrazona w ***m3***| decimal(18,6) |pplv_volume
|units_in_package|N |ilość jednostek podatwowych w opakowaniu(kartonie). Tworzony jest nowy poziom struktury pakowania. Ten poziom opakowań oznaczany jest automatycznie jako opakowanie zbiorcze `pplv_calcAsOpa` | decimal(18,6) |
|unit_of_package|N |jednostka miary dla opakowania (kartonu) |varchar(25) |`uom_code`|1.1
|units_on_pallet|N |ilość jednostek podatwowych na palecie. Tworzony jest nowy poziom struktury pakowania.Ten poziom opakowań oznaczany jest automatycznie jako paleta `pplv_isLoadUnit` | decimal(18,6) |
|unit_of_pallet|N |jednostka miary dla palety |varchar(25) |`uom_code`|1.1
      

## Pozycja dokumentu
Reprezentuje pozycje dokumentu. 


| Pole | Wymagane | Opis | Typ danych| Pole WMS |Od wersji 
|--|--|--|--|--|--|
|LN|N | Numer linii - pole wykorzystywane w przypadku gdy systemy ERP w  komunikatach zwrotnych wymagają tej informacji np. SAP R3| int |`dori_lineNr`
|code|T |kod porduktu jednoznacznie identyfikuje produkt musi byc unikatowy w obrębie jednego zleceniodawcy|nvarchar(50) |`prd_code`
|ordered_quantity|T |Ilość zamówiona w podstawowych jednostkach miary|decimal(18,6) |`dori_basicQuantity`
|SSCC|N |Numer nośnika stosowany tylko w przypadku awiza dostawy **typ = IN** |varchar(25) |`dori_SSCC`
|pallet_type|N |typ nośnika stosowany tylko w przypadku awiza dostawy. Używany tylko w przypadku wypełniania pola SSCC|varchar(50) |`dori_luType`
|item_attribute|N |Atrybuty pozycji dokumentu Jeśli nie będzie zdefiniowanego atrybutu Status jakości wstawiona zostanie wartość domyślna dla statusu jakości|kolekcja


### Komunikat zwrotny
Zawiera to co komunikat wejściowy poszerzone o pola:


| Pole | Wymagane | Opis | Typ danych| Pole WMS |
|--|--|--|--|--|
|OUT_quantity_confirmed | N |[Tylko dla komunikatu zwrotnego] Zrealizowana ilość w jednostkach podstawowych |decimal(18,6)|`door_confirmedQuantity`


## Przykłady

Przykład dokumetu typu IN:
```json
{
  "document": {
    "header": {
      "type": "IN",
      "orderer": "Acme",
      "logistics_center": "CL 1",
      "completion_date": "2021-07-23",
      "priority": "1",
      "document_alternative_code": "PO/2021/62934",
      "description": "Sample description for the document",
      "firm": {
        "code": "Sunway",
        "name": "SunWay Europe GmbH",
        "street": "Wrangelstraße 100",
        "postal_code": "92-318",
        "city": "10997",
        "country": "DE"
      },
    "products": {
      "product": [
        {
          "code": "PM YOS9",
          "name": "Teddy bear",
          "ean": "5091234567890",
          "warehouse_group": "Toys",
          "packaging_structure": {
            "unit_of_measure": "szt",
            "weight": "12",
            "volume": "0.02",
            "units_in_package": "10",
            "unit_of_package": "KRT",
            "units_on_pallet": "100",
            "unit_of_pallet": "EP"
          },
          "product_attributes": {
            "attribute": [
              {
                "name": "Producer",
                "value": "LOBITO"
              },
              {
                "name": "Colour",
                "value": "White"
              }
            ]
          }
        },
        {
          "code": "GK A314",
          "name": "Hand cream 250ml",
          "ean": "5090987654321",
          "grupa_magazynowa": "Cosmetics",
          "packaging_structure": {
            "unit_of_measure": "szt"
          }
        }
      ]
    },
    "items": {
      "item": [
        {
          "LN": "1",
          "code": "PM YOS9",
          "ordered_quantity": "1000"
        },
        {
          "LN": "2",
          "code": "GK A314",
          "ordered_quantity": "1000"         
        }
      ]
    }
  }
}
```

Przykład dokumetu typu IN dla potwierdzenia:
```json
{
  "document": {
    "header": {
      "type": "IN_CONF",
      "orderer": "Acme",
      "logistics_center": "CL 1",
      "completion_date": "2021-07-23",
      "priority": "1",
      "document_alternative_code": "PO/2021/62934",
      "OUT_document_nr": "SWWZ000001",
      "OUT_date_creation": "2022-07-23",
      "OUT_date_closed": "022-07-23",
      "firm": {
        "code": "Sunway",
        "name": "SunWay Europe GmbH",
        "street": "Wrangelstraße 100",
        "postal_code": "92-318",
        "city": "10997",
        "country": "DE"      
        }
    },    
    "items": {
      "item": [
        {
          "LN": "1",
          "code": "PM YOS9",
          "ordered_quantity": "1000",
          "OUT_quantity_confirmed": "1000"
        },
        {
          "LN": "2",
          "code": "GK A314",
          "ordered_quantity": "1000",
          "OUT_quantity_confirmed": "300",
          "item_attributes": {
            "attribute": [
              {
                "name": "nr_LOT",
                "value": "ABCD826"
              }
            ]
          }
        },
        {
            "LN": "2",
            "code": "GK A314",
            "ordered_quantity": "1000",
            "OUT_quantity_confirmed": "700",
            "item_attributes": {
              "attribute": [
                {
                  "name": "nr_LOT",
                  "value": "ABCD827"
                }
              ]
            }
          }
      ]
    }
  }
}
```

Przykład dokumetu typu OUT:
```json
{
  "document": {
    "header": {
      "type": "OUT",
      "orderer": "Acme",
      "logistics_center": "CL 1",
      "completion_date": "2022-02-10",
      "priority": "1",
      "document_alternative_code": "ZAM/2021/62934",
      "description": "Sample description for the document",
      "firm": {
        "code": "7811903679",
        "name": "Logsoft",
        "street": "Papiernicza 7e",
        "postal_code": "92-318",
        "city": "Łódź",
        "country": "PL"
      },
      "courier": {
        "service": "DHL Standard",
        "COD": "156.23",
        "insurance_amount": "500",
        "telephone": "555-666-777",
        "email": "klient@kontakt.pl",
        "additional_info": "Sample additional info for courier"
      },
      "document_attributes": {
        "attribute": [
          {
            "name": "Alternative_seller",
            "value": "seller code"
          }
        ]
      }
    },
    "items": {
      "item": [
        {
          "LN": "1",
          "code": "PM YOS9",
          "ordered_quantity": "1"
        },
        {
          "LN": "2",
          "code": "GK A314",
          "ordered_quantity": "4",
          "item_attributes": {
            "attribute": [
              {
                "name": "nr_LOT",
                "value": "ABCD826"
              },
              {
                "name": "Quality_status",
                "value": "OK"
              }
            ]
          }
        }
      ]
    }
  }
}
```

Przykład dokumetu typu OUT dla potwierdzenia:
```json
{
  "document": {
    "header": {
      "type": "OUT_CONF",
      "orderer": "Acme",
      "logistics_center": "CL 1",
      "completion_date": "2022-02-10",
      "priority": "1",
      "document_alternative_code": "ZAM/2021/62934",
      "OUT_document_nr": "SWWZ000001",
      "OUT_date_creation": "2022-02-05",
      "OUT_date_closed": "2022-02-10",
      "firm": {
        "code": "7811903679",
        "name": "Logsoft",
        "street": "Papiernicza 7e",
        "postal_code": "92-318",
        "city": "Łódź",
        "country": "PL"
      },
      "courier": {
        "service": "DHL Standard",
        "COD": "156.23",
        "insurance_amount": "500",
        "telephone": "555-666-777",
        "email": "klient@kontakt.pl",
        "additional_info": "Sample additional info for courier",
        "OUT_tracking_number": "1F1928390128312908312903478"

      },
      "document_attributes": {
        "attribute": [
          {
            "name": "Alternative_seller",
            "value": "seller code"
          }
        ]
      }
    },
    "items": {
      "item": [
        {
          "LN": "1",
          "code": "PM YOS9",
          "ordered_quantity": "1",
          "OUT_quantity_confirmed": "1"
        },
        {
          "LN": "2",
          "code": "GK A314",
          "ordered_quantity": "4",
          "OUT_quantity_confirmed": "3",
          "item_attributes": {
            "attribute": [
              {
                "name": "nr_LOT",
                "value": "ABCD826"
              }           
            ]
          }
        }
      ]
    }
  }
}
```
