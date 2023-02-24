# Logsoft - Lite WMS 

# Interface wymiany danych z WMS, komunikaty Dokument

*Kolumna Wymagane - w nawiasie podano typy dokumnetu dla któych pole jest wymagane*

## Obiekt dokument - document

Dokument zawiera ustandaryzowany format pozwalający wymieniać informacje związane z awizacją dostaw i zamówieniami od klientów a także importować receptury i słownik produktów.

| Pole | Wymagane | Opis 
|--|--|--|
*header*|T| Nagłówek dokumentu |Obiekt|
*products*|T(PRODUCTS)| Dane słownikowe produktów |Kolekcja|
*recipes*|T(RECIPES)| Struktura dla receptur |Kolekcja|
*items*|T(IN,OUT)| Pozycje dokumentu |Kolekcja|


## Obiekt nagłówek - header

| Pole | Wymagane | Opis | Typ danych| Pole WMS
|--|--|--|--|--|
|type| T | określa typ dokumentu, przyjmowane wartości **OUT** dla dokumentów wyjściowych (Zamówienie od klienta), **IN** dla dokumentów wejściowych (awizo przyjęcia), **IN-PZ** dokument PZ automatycznie przyjmowany (np. do przenoszenia stanów mag. jako bilans otwarcia), **PRODUCTS** tylko słownik produktów (w trybie update/insert lub tylko insert),  **RECIPES** import receptur (w trybie update/insert lub tylko insert) | varchar(10)  
|orderer|T |kod zleceniodawcy z WMS Z punktu widzenia klienta (systemu ERP) mozna traktowac jako stałą)| nvarchar(25)  | `dord_code`
logistics_center|T(IN,OUT) |centrum logistyczne z WMS | nvarchar(25)  |`whc_code`
completion_date|N |żądana data realizacji|smalldatetime | `door_expectedCompletion`
priority|N |priorytet, wartości od 0 - 89. Priorytet zero jest najniższy. Priorytety 90-99 zarezerwowane jako specjalne do użytku wewnętrznego.| smallint |`door_expectedCompletion`
document_alternative_code|T(IN,OUT) |kod alternatywny zamówienia - kod zamówienia z systemu ERP klienta.| nvarchar(50) | `door_alternativeCode`
description|N|Opis do dokumentu|nvarchar(500) | `door_description`
baselinker_id|N|'order_id' w API baselinkera, nr zamówienia baselinker|varchar(10)|`door_tr_BaselinklerID`
baselinker_order_source_id|N|'order_source_id' w API baselinkera, identyfikator źródła zamówienia w baselinkerze|varchar(10)|`door_tr_BaselinkerOrderSourceID`
attachment|N|Link lub plik załącznika|varchar(500)|`satt_oryginalFileName`
OUT_document_nr| |[Tylko dla komunikatu zwrotnego] nr dokumentu w WMS|nvarchar(25)  | `ddoc_code`
OUT_date_creation| |[Tylko dla komunikatu zwrotnego] data utworzenia/importu dokumentu|Datetime | `door_dateCreated`
OUT_date_closed| |[Tylko dla komunikatu zwrotnego] data zamknięcia dokumentu|Datetime | `door_dateClosed`
*firm*|T(IN,OUT)| Obiekt zawiera dane kontrahenta (klienta/dostawcy w zależności od typu dokumentu|Obiekt|
*courier*|N| Obiekt zawiara dane potrzebne do wystawiania listu przewozowego z poziomu systemu WMS|Obiekt|
*document_attributes*|N| Atrybuty nagłówka dokumentu|Kolekcja|


## Kolekcja atrybuty - document_attributes, product_attributes, item_attributes

Kolekcja atrybuty może posiadać Max 20 obiektów. Atrybuty, których nazwa będzie niezgodna z nazwą w definicji atrybutów systemu WMS będą ignorowane. Niewymagane
#### Obiekt attribute:
| Pole | Wymagane | Opis | Typ danych| Pole WMS |
|--|--|--|--|--|
|name|T | kod atrybutu z definicji atrybutów systemu WMS | varchar(50) |`pdef_code`
|value|T |wartość wstawiana do odpowiedniego atrybutu nagłówka dokumentu|varchar(50) |`xxx_attribXX`

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


## Obiekt kontrahent

Obiekt zawiera dane kontrahenta (klienta/dostawcy) w zależności od typu dokumentu. Jeżeli dane kontrahenta nie istnieją w słowniku firm nowa firma zostanie automatycznie dodana do słownika. Możliwe jest podanie tylko kodu firmy, wówczas wstawiany do zamówienia jest ostatni adres dla danej firmy.

 Pole | Wymagane | Opis | Typ danych| Pole WMS |
--|--|--|--|--|
code|T |kod_firmy|nvarchar(50)|`frm_code`
name|T |nazwa firmy|nvarchar(100)|`frm_name`
address_code|N |kod_adresu|nvarchar(50)|`fadr_code`
street|T |nazwa ulicy z numerem domu|nvarchar(100)|`fadr_street` 
postal_code|T|kod pocztowy, format zależny od kraju |varchar(10)|`fadr_postalCode`
city|T |nazwa miejscowości|nvarchar(100)|`fadr_city`
country|N |kod kraju, dwuliterowy, jeśli puste wstawiany **PL**|varchar(2)|`dctr_code2`
NIP|N |NIP firmy|varchar(50)|`fadr_NIP`

Przykład JSON:
```json
"firm": {
                "code": "F00001",
                "name": "Logsoft",
                "street": "Papiernicza 7e",
                "postal_code": "93-400",
                "city": "Łódź",
                "country": "PL"
            }
```

Przykład XML:
```XML
<firm>
	<code>F00001</code>
	<name>Logsoft</name>
	<street>Papiernicza 7e</street>
	<postal_code>93-400</postal_code>
	<city>Lodz</city>
	<country>PL</country>
<firm>
```


## Obiekt kurier

Sekcja nie jest obowiązkowa, dotyczy tylko zleceń o typie ***OUT*** i jest używana tylko w przypadku gdy z systemu wystawiany jest list przewozowy. Niewymagane pola w tym obiekcie a wymagane przez API kuriera muszą być uzupełnione w procesie pakowania lub ręcznie w formatce zamówienia.

| Pole | Wymagane | Opis | Typ danych| Pole WMS |
|--|--|--|--|--|
|service|T |Nazwa usługi kurierskiej, np. DHL Standard|varchar(50) |`door_tr_service`
|COD|N | kwota pobrania |decimal(18,6)| `door_tr_COD`
|insurance_amount|N |deklarowana kwota do ubezpieczenia (jesli pobranie kwota musi być = COD), sprawdź wymogi konkrentnego kuriera| decimal(18,6)| `door_tr_packageValue`
|currency|N |waluta pobrania/ubezpieczenia| decimal(18,6)| `door_tr_packageValue`
|weight|N |waga przesyłki| decimal(18,6)| `door_tr_weight`
|telephone|N |numer telefonu kontaktowego odbiorcy|varchar(50) |`door_tr_contactPhone`
|email|N |mail kontaktowy|varchar(50) | `door_tr_contact`
|additional_info|N |informacje dodatkowe do wydruku na etykiecie|varchar(50) |`door_tr_description`
|parcel_size|N |gabaryt przesyłki|varchar(50) |`door_tr_parcelSize`
OUT_nr_log_tracking| |[Tylko dla komunikatu zwrotnego] nr listu przewozowego|Datetime | `door_tr_trackingNumber`

Przykład JSON:
```json
"courier": {
                "service": "DHL Standard",
                "COD": "156.23",
                "insurance_amount": "160",
                "phone": "555-666-777",
                "email": "klient@kontakt.pl",
                "additional_info": "additional_info"
             }
```

Przykład XML:
```XML
<courier>
	<service>DHL Standard</service>
	<CODE>156.23</COD>
	<insurance_amount>500</insurance_amount>
	<telefon>555-666-777</telefon>
	<email>klient@kontakt.pl</email>
	<additional_info>Note glass<additional_info>
</courier>
```


## Produkty - kolekcja products

Dane słownikowe produktów. Produkty identyfikowane są po polu code. Możliwa jest aktualizacja nazwy i wartości atrybutów. 

| Pole | Wymagane | Opis | Typ danych| Pole WMS
|--|--|--|--|--|
|warehouse_group|N |Powinna odpowiadac istniejącej grupie magazynowej w sytemie WMS, wartość dla całej kolekcji, nadpisywana wartością z obiektu *product*, nieobowiązkowa jeśli w WMS istnieje dla zleceniodawcy tylko jedna grupa magazynowa |varchar(250)|`prwg_code`

#### Obiekt product:
| Pole | Wymagane | Opis | Typ danych| Pole WMS
|--|--|--|--|--|
|code|T |kod produktu jednoznacznie identyfikuje produkt. Musi być unikatowy w obrębie jednego zleceniodawcy|nvarchar(50) |`prd_code`
|name|N |nazwa produktu (jeśli pole puste przy zakładaniu nowego produktu jako nazwa zostanie wykorzystany kod produktu). Nazwa nie musi byc unikatowa.|nvarchar(250) |`prd_name`
|EAN|N |kod kreskowy dla podstawowej jednostki miary (np EAN13) - kod nie musi byc unikatowy. W przypadku wystepienia innego kodu niż wczesniej dodany oryginalny wpis sie nie zaktulizuje, dodany zostanie nowy z bieżacym kodem |varchar(25) |`prdb_value`
|warehouse_group|N |Powinna odpowiadać istniejącej grupie magazynowej w sytemie WMS, wartość w obiekcie *product* nadpisuje wartość z kolekcji *products*! |varchar(250)|`prwg_code`
|*packaging_structure*|N |Struktura pakowania produktu, sekcja w zasadzie powinna być wstawiana głównie w przypadku awizacji dostaw.|kolekcja
|*product_attributes*|N |Atrybuty nagłówka dokumentu Jeśli nie będzie zdefiniowanego atrybutu Status jakości wstawiona zostanie wartość domyślna dla statusu jakości|kolekcja


## Receptury - kolekcja recipes

Struktura dla receptur, kolekcja receptur
#### Obiekt recipe
| Pole | Wymagane | Opis | Typ danych| Pole WMS 
|--|--|--|--|--|
|rec_code|T |kod receptury|varchar(50) |`prec_code`
|code|T |kod produktu głównego, wyjściowego.|varchar(50) |`prd_code`
|version|N |wersja receptury |varchar(25) |`prec_version`
|description|N |opis receptury |varchar(25) |`prec_description`
|*items*|N |Kolekcja elementów receptury|kolekcja
### Kolekcja elementów receptur - items
#### Obiekt item - element receptury
| Pole | Wymagane | Opis | Typ danych| Pole WMS 
|--|--|--|--|--|
|code|T |kod produktu składnika|varchar(50) |`prd_product`
|quantity|T |ilość składnika|decimal(18,6) |`prei_quantity`


## Opakowania - packaging_structure

Struktura pakowania ***nie aktualizuje sie*** zakładana jest przy pierwszym dodaniu produktu. 
Aktualizowane mogą być poszczególne wartości w struktrurze, pod warunkiem, że produkt nie został jescze wykorzystany w żadnym zadaniu WMS (nie istniał nigdy na stanie magazynowym)

| Pole | Wymagane | Opis | Typ danych| Pole WMS
|--|--|--|--|--|
|unit_of_measure|N |podstawowa jednostka miary. Kod jednostki miary pownien byc zgodny ze słownikiem w systemie WMS. (jeśli pole puste przy zakładaniu nowego produktu jako nazwa zostanie domyślna jednostka miary) |varchar(25) |`uom_code`
|weight|N |Waga brutto dla podstawowej jednostki miary wyrazona w ***kg***| decimal(18,6) |`pplv_packWeight`
|volume|N |Objętość podstawowej jednostki miary wyrazona w ***m3***| decimal(18,6) |`pplv_packVolume`
|height|N |Wysokość podstawowej jednostki miary wyrazona w ***mm***| decimal(18,6) |`pplv_packHeight`
|length|N |Długość podstawowej jednostki miary wyrazona w ***mm***| decimal(18,6) |`pplv_packLength`
|width|N |Szerokość podstawowej jednostki miary wyrazona w ***mm***| decimal(18,6) |`pplv_packWidth`
|units_in_package|N |ilość jednostek podatwowych w opakowaniu(kartonie). Tworzony jest nowy poziom struktury pakowania. Ten poziom opakowań oznaczany jest automatycznie jako opakowanie zbiorcze `pplv_calcAsOpa` | decimal(18,6) |
|unit_of_package|N |jednostka miary dla opakowania (kartonu) |varchar(25) |`uom_code`
|units_on_pallet|N |ilość jednostek podatwowych na palecie. Tworzony jest nowy poziom struktury pakowania.Ten poziom opakowań oznaczany jest automatycznie jako paleta `pplv_isLoadUnit` | decimal(18,6) |
|unit_of_pallet|N |jednostka miary dla palety |varchar(25) |`uom_code`
      

## Pozycje dokumentu - items
Reprezentuje pozycje dokumentu. 

#### obiekt item
| Pole | Wymagane | Opis | Typ danych| Pole WMS 
|--|--|--|--|--|--|
|LN|N | Numer linii - pole wykorzystywane w przypadku gdy systemy ERP w  komunikatach zwrotnych wymagają tej informacji np. SAP R3| int |`dori_lineNr`
|code|T |kod porduktu jednoznacznie identyfikuje produkt musi byc unikatowy w obrębie jednego zleceniodawcy|nvarchar(50) |`prd_code`
|ordered_quantity|T |Ilość zamówiona w podstawowych jednostkach miary|decimal(18,6) |`dori_basicQuantity`
|SSCC|N |Numer nośnika stosowany w przypadku awiza dostawy (IN), lub dokumentu PZ (IN-PZ) |varchar(25) |`dori_SSCC`
|pallet_type|N |typ nośnika stosowany przypadku awiza dostawy (IN), lub dokumentu PZ (IN-PZ). Używany tylko w przypadku wypełniania pola SSCC|varchar(50) |`dori_luType`
|OUT_quantity_confirmed | N |[Tylko dla komunikatu zwrotnego] Zrealizowana ilość w jednostkach podstawowych |decimal(18,6)|`dori_confirmedQuantity`
|*item_attributes*|N |Atrybuty pozycji dokumentu Jeśli nie będzie zdefiniowanego atrybutu Status jakości wstawiona zostanie wartość domyślna dla statusu jakości|kolekcja


# Przykłady

Przykład dokumetu JSON:
```json
{
  "document":{
    "header":{
      "type":"IN",
      "orderer":"Acme",
      "logistics_center":"CL 1",
      "completion_date":"2021-07-23",
      "priority":"1",
      "document_alternative_code":"PO/2021/62934",
      "description":"Sample description for the document",
      "firm":{
        "code":"Sunway",
        "name":"SunWay Europe GmbH",
        "street":"Wrangelstraße 100",
        "postal_code":"92-318",
        "city":"10997",
        "country":"DE"
      },
      "courier":{
        "service":"Paczkomaty Inpost",
        "COD":"156.23",
        "insurance_amount":"160",
        "phone":"555-666-777",
        "email":"klient@kontakt.pl",
        "additional_info":"additional_info",
        "parcel_size":"B"
      }
    },
    "products":{
      "warehouse_group":"Cosmetics",
      "product":[
        {
          "code":"PM YOS9",
          "name":"Teddy bear",
          "ean":"5091234567890",
          "warehouse_group":"Toys",
          "packaging_structure":{
            "unit_of_measure":"szt",
            "weight":12,
            "volume":0.02,
            "units_in_package":"10",
            "unit_of_package":"KRT",
            "units_on_pallet":"100",
            "unit_of_pallet":"EP"
          },
          "product_attributes":{
            "attribute":[
              {
                "name":"Producer",
                "value":"LOBITO"
              },
              {
                "name":"Colour",
                "value":"White"
              }
            ]
          }
        },
        {
          "code":"KDT",
          "name":"Face cream",
          "ean":"5091234561111",
          "packaging_structure":{
            "unit_of_measure":"szt",
            "weight":0.12,
            "volume":0.0002,
            "units_in_package":22,
            "unit_of_package":"KRT",
            "units_on_pallet":260,
            "unit_of_pallet":"EP"
          },
          "product_attributes":{
            "attribute":[
              {
                "name":"Producer",
                "value":"NIVEA"
              },
              {
                "name":"Type",
                "value":"Strong"
              }
            ]
          }
        }
      ]
    },
    "recipes":{
      "recipe":[
        {
          "rec_code":"SFC_Nivea_22",
          "code":"SFC22",
          "version":"2.0",
          "description":"gift box",
          "items":{
            "item":[
              {
                "code":"FC01",
                "quantity":1
              },
              {
                "code":"FC02",
                "quantity":4
              },
              {
                "code":"HC01",
                "quantity":3
              }
            ]
          }
        },
        {
          "rec_code":"SHC_Loreal_42",
          "code":"SHL_42",
          "version":"1.0",
          "description":"gift box extra",
          "items":{
            "item":[
              {
                "code":"HC03",
                "quantity":1
              },
              {
                "code":"SH02",
                "quantity":2
              },
              {
                "code":"HC01",
                "quantity":12
              }
            ]
          }
        }
      ]
    },
    "items":{
      "item":[
        {
          "LN":"1",
          "code":"PM YOS9",
          "ordered_quantity":1000
        },
        {
          "LN":"2",
          "code":"KDT",
          "ordered_quantity":1000,
          "item_attributes":{
            "attribute":[
              {
                "name":"Quality status",
                "value":"OK"
              },
              {
                "name":"Manufacture Date",
                "value":"2021-12-15"
              }
            ]
          }
        }
      ]
    }
  }
}
```
 
Ten sam przykład w wersji XML:
```
<?xml version="1.0" encoding="UTF-8" ?>
<document>
  <header>
    <type>IN</type>
    <orderer>Acme</orderer>
    <logistics_center>CL 1</logistics_center>
    <completion_date>2021-07-23</completion_date>
    <priority>1</priority>
    <document_alternative_code>PO/2021/62934</document_alternative_code>
    <description>Sample description for the document</description>
    <firm>
      <code>Sunway</code>
      <name>SunWay Europe GmbH</name>
      <street>Wrangelstraße 100</street>
      <postal_code>92-318</postal_code>
      <city>10997</city>
      <country>DE</country>
    </firm>
    <courier>
      <service>Paczkomaty Inpost</service>
      <COD>156.23</COD>
      <insurance_amount>160</insurance_amount>
      <phone>555-666-777</phone>
      <email>klient@kontakt.pl</email>
      <additional_info>additional_info</additional_info>
      <parcel_size>B</parcel_size>
    </courier>
  </header>
  <products>
    <warehouse_group>Cosmetics</warehouse_group>
    <product>
      <code>PM YOS9</code>
      <name>Teddy bear</name>
      <ean>5091234567890</ean>
      <warehouse_group>Toys</warehouse_group>
      <packaging_structure>
        <unit_of_measure>szt</unit_of_measure>
        <weight>12</weight>
        <volume>0.02</volume>
        <units_in_package>10</units_in_package>
        <unit_of_package>KRT</unit_of_package>
        <units_on_pallet>100</units_on_pallet>
        <unit_of_pallet>EP</unit_of_pallet>
      </packaging_structure>
      <product_attributes>
        <attribute>
          <name>Producer</name>
          <value>LOBITO</value>
        </attribute>
        <attribute>
          <name>Colour</name>
          <value>White</value>
        </attribute>
      </product_attributes>
    </product>
    <product>
      <code>KDT</code>
      <name>Face cream</name>
      <ean>5091234561111</ean>
      <packaging_structure>
        <unit_of_measure>szt</unit_of_measure>
        <weight>0.12</weight>
        <volume>0.0002</volume>
        <units_in_package>22</units_in_package>
        <unit_of_package>KRT</unit_of_package>
        <units_on_pallet>260</units_on_pallet>
        <unit_of_pallet>EP</unit_of_pallet>
      </packaging_structure>
      <product_attributes>
        <attribute>
          <name>Producer</name>
          <value>NIVEA</value>
        </attribute>
        <attribute>
          <name>Type</name>
          <value>Strong</value>
        </attribute>
      </product_attributes>
    </product>
  </products>
  <recipes>
    <recipe>
      <rec_code>SFC_Nivea_22</rec_code>
      <code>SFC22</code>
      <version>2.0</version>
      <description>gift box</description>
      <items>
        <item>
          <code>FC01</code>
          <quantity>1</quantity>
        </item>
        <item>
          <code>FC02</code>
          <quantity>4</quantity>
        </item>
        <item>
          <code>HC01</code>
          <quantity>3</quantity>
        </item>
      </items>
    </recipe>
    <recipe>
      <rec_code>SHC_Loreal_42</rec_code>
      <code>SHL_42</code>
      <version>1.0</version>
      <description>gift box extra</description>
      <items>
        <item>
          <code>HC03</code>
          <quantity>1</quantity>
        </item>
        <item>
          <code>SH02</code>
          <quantity>2</quantity>
        </item>
        <item>
          <code>HC01</code>
          <quantity>12</quantity>
        </item>
      </items>
    </recipe>
  </recipes>
  <items>
    <item>
      <LN>1</LN>
      <code>PM YOS9</code>
      <ordered_quantity>1000</ordered_quantity>
    </item>
    <item>
      <LN>2</LN>
      <code>KDT</code>
      <ordered_quantity>1000</ordered_quantity>
      <item_attributes>
        <attribute>
          <name>Quality status</name>
          <value>OK</value>
        </attribute>
        <attribute>
          <name>Manufacture Date</name>
          <value>2021-12-15</value>
        </attribute>
      </item_attributes>
    </item>
  </items>
</document>
```
 
