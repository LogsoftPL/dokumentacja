# WMS24 API DOCUMENTATION

Doc version / date: **v1.35 - 30.08.2024**
Available API versions: **v0.9**

# Table of contents

1. [Table of contents](#table-of-contents)

2. [Changes](#changes)

3. [Introduction](#introduction)

4. [Objects](#objects)

    - [xAddress](#xaddress)

    - [xApiConfig](#xapiConfig)

    - [xApiConfigParameter](#xapiconfigparameter)

    - [xAttachment](#xattachment)

    - [xAttachmentFile](#xattachmentfile)
      
    - [xAttachmentBody](#xattachmentbody)

    - [xLog](#xlog)

    - [xLoginBody](#xloginbody)

    - [xLogJson](#xlogjson)

    - [xOrder](#xorder)

    - [xOrderItem](#xorderitem)

    - [xOwner](#xowner)

    - [xPackage](#xpackage)

    - [xPackageLabel](#xpackagelabel)

    - [xParameter](#xparameter)

    - [xRegisterBody](#xregisterbody)

    - [xShippingService](#xshippingservice)

    - [xStatus](#xstatus)

    - [xTracking](#xtracking)

    - [xTransportOrder](#xtransportprder)

    - [xTransportOrderBody](#xtransportorderbody)

    - [xWarehouse](#xwarehouse)

    - [xResLogin](#xreslogin)
  
    - [xResNewEntries](#xresnewentries)
  
    - [xOrderBody](#xorderbody)

    - [xProduct](#xproduct)
    
    - [xProductApiConfig](#xproductapiconfig)
  
    - [xProductDescription](#xproductdescription)
  
    - [xProductParameter](#xproductparameter)
  
    - [xProductSet](#xproductset)
  
    - [xProductStock](#xproductstock)
      
    - [xOrderPatchStatusBody](#xorderpatchstatusbody)

5. [Actions](#actions)

    - [Auth actions](#auth-actions)

    - [ApiConfigs actions](#apiconfigs-actions)

    - [Attachments actions](#attachments-actions)

    - [Logs actions](#logs-actions)

    - [Orders actions](#orders-actions)

    - [Owners actions](#owners-actions)

    - [Packages actions](#packages-actions)

    - [Services actions](#services-actions)

    - [Tracking actions](#tracking-actions)

    - [TransportOrders actions](#transportorders-actions)

    - [Warehouses actions](#warehouses-actions)
      
    - [Products actions](#products-actions)

# Changes

| **Description** | **Doc version** | **Date** | **Author** |
| --- | --- | --- | --- |
| Creation of document. | 1.0 | 17.05.2024 | Artur Masłowski |
| Added the feature to add batch transport orders and orders. Changed xResNewEntry to xResNewEntries. Added POST method for xOrder. | 1.05 | 22.05.2024 | Artur Masłowski |
| Changed xTransportOrderBody. ApiConfigId => ApiConfigName, ShippingServiceId => ShippingServiceName. | 1.06 | 28.05.2024 | Artur Masłowski |
| Changed xTransportOrderBody. Added ShippingServiceCourierService field. | 1.07 | 31.05.2024 | Artur Masłowski |
| Added ExternalPackageNumber field for xPackage. | 1.08 | 03.06.2024 | Artur Masłowski |
| ExternalStatus in xTransportOrder from now is not required | 1.09 | 04.06.2024 | Artur Masłowski |
| Added (recommended) flag to MarketPlaceDocNr | 1.1 | 05.06.2024 | Artur Masłowski |
| Removed SourceInfo field from xOrderBody | 1.11 | 12.06.2024 | Artur Masłowski |
| Added Product models. | 1.2 | 28.06.2024 | Artur Masłowski |
| Added endpoint to add attachment. Added new parameter in xOrder GETs | 1.3 | 07.08.2024 | Artur Masłowski |
| Added page parameter in xOrder and xTransportOrder GET | 1.31 | 13.08.2024 | Artur Masłowski |
| Added new endpoint PATCH to update xOrder statuses. Added new parameters for xOrders GET. Updated xOrder response. | 1.35 | 30.08.2024 | Artur Masłowski |

# Introduction

The document contains the specification of the data exchange interface between the client system and WMS24 (clink). It is based on the REST API architecture.

Address email of IT support: [logsoft@logsoft.com.pl](mailto:logsoft@logsoft.com.pl)

Endpoint example:

<https://x.x.x.x:PORT/api/v0.9/transport-orders?limit=50>

Under _x.x.x.x:PORT_ is the address of the host with port on which the API service is issued. The host address along with the port can be obtained from the IT support.

Every endpoint starts with _/api_ route.

The second parameter is the version of the API for which the called method is available.

The third parameter is the name of the controller that provides REST methods.

The application uses Bearer Token as a form of authorization. It is valid for **24 hours**. It can be obtained by calling the appropriate anonymous endpoint.

A maximum of **15 requests** can be sent within **10 seconds**.

# Objects

#### xAddress
An object containing fields representing an address.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Name | string | Main name of address ex. name of company or full name of person | x   |
| Street | string | Street (can be with numbers or not) | x   |
| City | string | City | x   |
| ZipCode | string | Zip code (00-000 format) | x   |
| Country | string | Country code (PL, EN etc.) | x   |
| Name2 | string | Additional address name |     |
| Name3 | string | Additional address name |     |
| BuildingNumber | string | Number of building |     |
| ApartmentNumber | string | Number of apartment |     |
| ContactPerson | string | Full name of address name person |     |
| ContactPhone | string | Phone number of ContactPerson |     |
| ContactEmail | string | Email of ContactPerson |     |

#### xApiConfig
An object containing fields representing integrations (e.g., courier, marketplace).

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Integration | string | General name of integration (available from pool of integrations) | x   |
| IntegrationName | string | Name of integration | x   |
| OwnerToken | guid | Token of owner related to config | x   |
| IsProductionConfig | bool | Is configuration available for production? |     |
| IsMarketplaceConfig | bool | Is configuration of marketplace? |     |
| IsCourierConfig | bool | Is configuration of courier? |     |
| Status | enum (0 – Disabled 1 - Active) | Status |     |
| Parameters | List&lt;xApiConfigParameter&gt; | List of additional parameters |     |
| OwnerBank | string | Bank name of owner |     |
| OwnerBankAccount | string | Account number of owner |     |
| Login | string | Login to external service |     |
| Password | string | Password to external service |     |
| Token | string | Token to external service |     |
| TokenDate | datetime | Date of Token |     |
| AdditionalId | string | Additional id |     |
| SenderAddress | xAddress | Sender address |     |
| Language | string | Language code (PL, EN etc.) |     |
| LabelFormat | string | Format of label (PDF, ZPL etc.) |     |
| LabelPath | string | Path to save labels |     |
| PageFormat | string | Page format |     |
| BaseUrl | string | Url of external service |     |
| TokenUrl | string | Token url |     |
| TrackingStatus | enum (0 – Disabled 1 - Active) | Is tracking enabled? |     |
| FristTrackingHour | int | First tracking hour |     |
| PrintScaleMode | string | (Fit, ActualSize, CustomScale) |     |
| PrintPageOrientation | string | (Fit, Portrait, Landscape) |     |
| ExternalTrackingLink | string | Link to tracking page ( {tracking} is variable to replace with actual tracking number ) |     |

#### xApiConfigParameter
Object describing the parameters for xApiConfig.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Name | string | Name of parameter | x   |
| Value | string | Value of parameter |     |
| Description | string | Description |     |

#### xAttachment
Object describing fields for attachments.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| CreationDate | datetime | Date of creation |     |
| OwnerToken | guid | Token of owner related to attachment |     |
| Type | string | Type of attachment |     |
| DocumentNr | string | Number of document |     |
| Description | string | Description |     |
| AutoPrintOn | string | Is autoprint on? |     |
| Status | xStatus | Status of attachment |     |
| Printer | string | Name of printer |     |
| PrinterHost | string | Host of printer |     |
| PrintDate | datetime | Date of print |     |

#### xAttachmentFile
Object describing the fields related to the attachment file.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| AttachmentData | byte\[\] | File data encoded in base64 |     |
| Extension | string | File extension |     |
| OriginalFileName | string | Original file name |     |
| OriginalFilePath | string | Original file path |     |

#### xLog 
Object describing fields for logs.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| LogTime | datetime | Time of event |     |
| Status | enum (0 – OK, 1 – Error, 2 – ErrorSend, 3 – Warning) | Status of log |     |
| Message | string | Message |     |
| ErrorTrace | string | Error trace |     |
| Host | string | Host of event |     |
| Type | string | Type |     |
| Source | enum (0 – Auto, 1 – Manual, 2 - API) | Source |     |

#### xLoginBody
Login request body object.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Email | string | Email | x   |
| Password | string | Password | x   |

#### xLogJson
Object describing Request and Response fields for xLog.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Request | string | JSON string containing request |     |
| Response | string | JSON string containing response |     |

#### xOrder
Object describing order.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| OwnerToken | guid | Token of owner related to order | x   |
| Priority | int | Priority | x   |
| CreationDate | datetime | Date of creation (in system) |     |
| WantInvoice | bool | Does a customer want an invoice? |     |
| Items | List&lt;xOrderItem&gt; | List of order items (products) |     |
| InvoiceStatus | xStatus | Status of invoice |     |
| ERPStatus | xStatus | Status of ERP |     |
| WMSStatus | xStatus | Status of WMS |     |
| OrderStatus | xStatus | Status of order |     |
| ReceiverAddress | xAddress | Address of receiver |     |
| InvoiceAddress | xAddress | Address of invoice |     |
| Warehouse | xWarehouse | Warehouse |     |
| SourceInfo | string | Source info |     |
| SourceConfigId | int | Source config id |     |
| MarketPlaceDocNr | string | Marketplace document number |     |
| MarketPlaceDocId | int | Marketplace document id |     |
| ERPDocNr | string | ERP document number |     |
| ERPDocId | int | Id of document ERP |     |
| ERPConfigId | int | ERP config id |     |
| ERPStatusDate | datetime | Date of ERPStatus |     |
| InvoiceDocNr | string | Invoice document number |     |
| InvoiceDocId | int | Invoice document id |     |
| InvoiceConfigId | int | Invoice config id |     |
| InvoiceStatusDate | datetime | Invoice status date |     |
| WMSDocNr | string | WMS document number |     |
| WMSDocId | int | WMS document id |     |
| WMSConfigId | int | WMS config id |     |
| WMSStatusDate | datetime | WMS status date |     |
| Currency | string | Currency code (ex. PLN, USD etc.) |     |
| PaymentMethod | string | Payment method |     |
| PaymentValue | double | Payment value |     |
| PaymentDate | datetime | Payment date |     |
| Paid | bool | Did customer paid for order? |     |
| UserComments | string | User comments |     |
| AdminComments | string | Admin comments |     |
| COD | double | Cash on delivery value |     |
| CODPaymentMethod | string | Cash on delivery payment method |     |
| CODCurrency | string | Cash on delivery currency code (ex. PLN, USD etc.) |     |
| BankAccountNr | string | Customer bank account |     |
| Insurance | double | Insurance value |     |
| InsuranceCurrency | string | Insurance currency code (ex. PLN, USD etc.) |     |
| DeclaredValue | double | Declared value |     |
| DeclaredValueCurrency | string | Declared value currency code (ex. PLN, USD etc.) |     |
| ParcelLocker | string | Parcel locker code |     |
| CustomSourceId | int | Custom source id |     |
| MarketplaceUserLogin | string | Marketplace user login |     |
| ShippingServiceExternalSourceID | string | Id of external service |     |
| ShippingServiceSourceExternalName | string | Name of external service |     |
| IsFulfillmentOrder | bool | Is fulfillment order? |     |
| FulfillmentStatus | string | Fulfillment status |     |
| ShippingPrice | float | Shipping price |     |
| InvoiceFullName | string | Invoice person full name |     |
| InvoiceCompany | string | Company name |     |
| InvoiceNip | string | NIP of company |     |
| ExtraField1 | string | Extra field |     |
| ExtraField2 | string | Extra field |     |
| OrderStatusDate | datetime | Order status date |     |
| ImportDate | datetime | Import date |     |
| OrderCloseDate | datetime | Close date |     |
| OrderPage | string | Order page |     |
| TransportOrderId | int | Id of related transport order |     |
| TrackingNumbers | Array<string> | Tracking numbers |     |

#### xOrderItem
Object describing order items for xOrder.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| ProductName | string | Product name | x   |
| OrderedQuantity | double | Quantity | x   |
| LineNumber | int | Line number |     |
| ProductId | int | Product id |     |
| ProductExternalId | int | Product external id |     |
| ProductCode | string | Product code |     |
| ProductAlternativeCode | string | Product alternative code |     |
| ProductWarehouseGroup | string | Product warehouse group |     |
| ProductEAN | string | Product EAN |     |
| ProductUnit | string | Product unit |     |
| ProductWeight | double | Product weight |     |
| ProductVolume | double | Product volume |     |
| ProductHeight | double | Product height |     |
| ProductLength | double | Product length |     |
| ProductWidth | double | Product width |     |
| ProductBoxUnit | string | Product box unit |     |
| ProductBoxEAN | string | Product box EAN |     |
| ProductBoxQuantity | double | Product box quantity |     |
| ProductPalletUnit | string | Product pallet unit |     |
| ProductPalletEAN | string | Product pallet EAN |     |
| ProductPalletQuantity | double | Product pallet quantity |     |
| ProductIsService | bool | Is product service? |     |
| ConfirmedQuantity | double | Confirmed quantity |     |
| PriceNetto | double | Net price |     |
| TaxRate | double | Tax rate |     |
| PriceBrutto | double | Gross price |     |
| Offer | string | Offer |     |
| Set | string | Set |     |
| BoughtDate | datetime | Bought date |     |

#### xOwner
Object describing fields of owner.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Token | guid | Token of owner |     |
| Name | string | Name |     |
| Status | enum (0 – Disabled, 1 - Active) | Status |     |
| Address | xAddress | Address |     |
| Description | string | Description |     |
| WmsId | string | Id in WMS |     |

#### xPackage 
Object describing fields for package.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| TrackingNumber | string | Tracking number (waybill) |     |
| Type | string | Type |     |
| SizeType | string | Size type |     |
| Length | int | Length |     |
| Width | int | Width |     |
| Height | int | Height |     |
| Weight | double | Weight |     |
| Description | string | Description |     |
| InternalPackageNumber | string | Internal package number (name) | x   |
| ExternalPackageNumber | string | External package number |     |

#### xPackageLabel
Object describing label file for xPackage.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Label | byte\[\] | Label file encoded in base64 |     |
| LabelType | string | Label type (format ex. PDF, ZPL etc.) |     |

#### xParameter
Object describing additional parameters for xTransportOrder.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Name | string | Name of parameter |     |
| Value | string | Value of parameter |     |

#### xRegisterBody
Object describing request body for register.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Token | string | Special token for registration | x   |
| Email | string | Email | x   |
| UserName | string | Username | x   |
| Password | string | Password | x   |
| Description | string | Description | x   |
| PhoneNumber | string | Phone number |     |

#### xShippingService
Object describing shipping services.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Name | string | Name | x   |
| CourierName | string | Courier name (integration) | x   |
| Description | string | Description |     |
| SubCourierName | string | Additional courier name |     |
| Marketplace | string | Marketplace name |     |
| WMSService | string | WMS service name |     |
| CourierService | string | Courier service name |     |
| Status | enum (0 – Disabled, 1 - Enabled) | Status |     |
| OnePackageService | bool | Is one package service? |     |
| DefaultPackageType | string | Default package type |     |
| DefaultPackageSizeType | string | Default package size type |     |
| DefaultPackageLength | int | Default package length |     |
| DefaultPackageWidth | int | Default package width |     |
| DefaultPackageHeight | int | Default package height |     |
| DefaultPackageWeight | double | Default package weight |     |
| DefaultInsurance | double | Default insurance value |     |
| DefaultInsuranceCurrency | string | Default insurance currency code (ex. PLN, USD etc.) |     |

#### xStatus
Object describing status fields.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Name | string | Name | x   |
| EnglishName | string | English name | x   |
| Dependency | string | Dependency from pool of available dependencies | x   |
| Code | string | Code | x   |
| Description | string | Description |     |
| BackColor | string | Background color (HEX) |     |
| TextColor | string | Text color (HEX) |     |

#### xTracking
Object describing tracking fields.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Source | enum (0 – Internal, 1 - External) | Source |     |
| StatusTime | datetime | Status date |     |
| UpdateTime | datetime | Update date |     |
| TrackingNumber | string | Tracking number (waybill) |     |
| Status | string | Text status |     |
| StatusDescription | string | Description of Status |     |
| AdditionalDescription | string | More information about Status |     |
| Location | string | Event location |     |
| ExternalId | string | External id |     |

#### xTransportOrder
Object describing fields for transport order.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Integration | string | Integration general name (from xApiConfig) |     |
| IntegrationName | string | Integration name (from xApiConfig) |     |
| OwnerToken | guid | Token of related owner |     |
| CreationDate | datetime | Creation date |     |
| OrderNr | string | Transport order number |     |
| ReferenceNr | string | Reference number |     |
| ReceiverAddress | xAddress | Receiver address |     |
| Warehouse | xWarehouse | Warehouse |     |
| Service | xShippingService | Shipping service |     |
| TransportOrderStatus | xStatus | Status of transport order |     |
| TrackingStatus | xStatus | Status of tracking |     |
| LabelStatus | xStatus | Status of label |     |
| ExternalId | string | External id |     |
| Description | string | Descripton |     |
| MainTrackingNumber | string | Main tracking number (for parcel) |     |
| COD | double | Cash on delivery value |     |
| CODCurrency | string | Cash on delivery currency (ex. PLN, USD etc.) |     |
| BankAccountNr | string | Customer bank account number |     |
| Insurance | double | Insurance value |     |
| InsuranceCurrency | string | Insurance currency (ex. PLN, USD etc.) |     |
| DeclaredValue | double | Declared value |     |
| DeclaredValueCurrency | string | Declared value currency (ex. PLN, USD etc.) |     |
| ParcelLocker | string | Parcel locker code |     |
| ExternalStatus | string | External status |     |
| LabelPrinter | string | Name of label printer |     |
| LabelHost | string | Label host |     |
| GenerationDate | datetime | Date of generation |     |
| PrintDate | datetime | Date of label print |     |
| TrackingDate | datetime | Date of latest tracking |     |
| TrackingGuid | guid | Tracking guid for external use |     |
| WMSDocId | int | Id of related document in WMS |     |

#### xTransportOrderBody
Object describing request body for transport order.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| ApiConfigName | string | Name of xApiConfig (IntegrationName) | x   |
| OwnerToken | guid | Token of owner | x   |
| ReceiverAddress | xAddress | Receiver address | x   |
| OrderNr | string | Transport order number | x   |
| ReferenceNr | string | Reference number | x   |
| Packages | List&lt;xPackage&gt; | Packages | x   |
| ShippingServiceName | string | Name of xShippingService | x   |
| ShippingServiceCourierService | string | CourierService of xShippingService | x - if CourierService in xShippingService is NOT null   |
| ExternalStatus | string | External status |     |
| WarehouseId | int | Id of xWarehouse |     |
| ExternalId | string | External id |     |
| Description | string | Description |     |
| COD | double | Cash on delivery value |     |
| CODCurrency | string | Cash on delivery currency (ex. PLN, USD etc.) |     |
| BankAccountNr | string | Customer bank account number |     |
| Insurance | double | Insurance value |     |
| InsuranceCurrency | string | Insurance currency (ex. PLN, USD etc.) |     |
| DeclaredValue | double | Declared value |     |
| DeclaredValueCurrency | string | Declared value currency (ex. PLN, USD etc.) |     |
| ParcelLocker | string | Parcel locker code |     |
| LabelPrinter | string | Label printer name |     |
| LabelHost | string | Label host |     |

#### xWarehouse
Object describing warehouse fields.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| Code | string | Warehouse code |     |
| Address | xAddress | Address |     |
| Description | string | Description |     |

#### xResponse
Object describing default response field.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Success | bool | Was the request successful? |     |
| Code | int | Http code |     |
| Message | string | Response message |     |

#### xResLogin
Object describing response for login action.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Success | bool | Was the request successful? |     |
| Code | int | Http code |     |
| Message | string | Response message |     |
| Token | string | Auth token |     |

#### xResNewEntries
Object describing response for creation entry action.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Success | bool | Was the request successful? |     |
| Code | int | Http code |     |
| Message | string | Response message |     |
| EntryIds | Array<object> | Ids of new created entries |     |

#### xOrderBody
Object describing request body for order.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| OwnerToken | guid | Token of owner related to order | x   |
| Priority | int | Priority | x   |
| WantInvoice | bool | Does a customer want an invoice? | x   |
| CreationDate | datetime | Order creation date | x   |
| Items | List&lt;xOrderItem&gt; | List of order items (products) | x   |
| MarketPlaceDocNr | string | Marketplace document number | (recommended)   |
| MarketPlaceDocId | int | Marketplace document id |     |
| InvoiceStatus | xStatus | Status of invoice |     |
| ReceiverAddress | xAddress | Address of receiver |     |
| InvoiceAddress | xAddress | Address of invoice |     |
| WarehouseId | int | Id of warehouse |     |
| SourceConfigId | int | Source config id |     |
| ERPDocNr | string | ERP document number |     |
| ERPDocId | int | Id of document ERP |     |
| ERPConfigId | int | ERP config id |     |
| InvoiceDocNr | string | Invoice document number |     |
| InvoiceDocId | int | Invoice document id |     |
| InvoiceConfigId | int | Invoice config id |     |
| InvoiceStatusDate | datetime | Invoice status date |     |
| WMSDocNr | string | WMS document number |     |
| WMSDocId | int | WMS document id |     |
| WMSConfigId | int | WMS config id |     |
| Currency | string | Currency code (ex. PLN, USD etc.) |     |
| PaymentMethod | string | Payment method |     |
| PaymentValue | double | Payment value |     |
| PaymentDate | datetime | Payment date |     |
| Paid | bool | Did customer paid for order? |     |
| UserComments | string | User comments |     |
| AdminComments | string | Admin comments |     |
| COD | double | Cash on delivery value |     |
| CODPaymentMethod | string | Cash on delivery payment method |     |
| CODCurrency | string | Cash on delivery currency code (ex. PLN, USD etc.) |     |
| BankAccountNr | string | Customer bank account |     |
| Insurance | double | Insurance value |     |
| InsuranceCurrency | string | Insurance currency code (ex. PLN, USD etc.) |     |
| DeclaredValue | double | Declared value |     |
| DeclaredValueCurrency | string | Declared value currency code (ex. PLN, USD etc.) |     |
| ParcelLocker | string | Parcel locker code |     |
| CustomSourceId | int | Custom source id |     |
| MarketplaceUserLogin | string | Marketplace user login |     |
| ShippingServiceExternalSourceID | string | CourierService of xShippingService | x - if CourierService in xShippingService is NOT null and ShippingServiceSourceExternalName is not null   |
| ShippingServiceSourceExternalName | string | Name of xShippingService |     |
| IsFulfillmentOrder | bool | Is fulfillment order? |     |
| FulfillmentStatus | string | Fulfillment status |     |
| ShippingPrice | float | Shipping price |     |
| InvoiceFullName | string | Invoice person full name |     |
| InvoiceCompany | string | Company name |     |
| InvoiceNip | string | NIP of company |     |
| ExtraField1 | string | Extra field |     |
| ExtraField2 | string | Extra field |     |

#### xProduct
Object describing product model

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| IsSet | bool | Is product set |     |
| Name | string | Name |     |
| ImageUrls | Array<string> | Image urls |     |
| OwnerToken | guid | Owner token |     |
| Code | string | Code |     |
| ManufacturerName | string | Manufacturer name |     |
| SourceId | string | Source id (id of product from a source) |     |
| SourceName | string | Source name |     |
| AlternativeCode | string | Alternative code |     |
| CategoryId | string | Category id |     |
| WarehouseGroup | string | Warehouse group |     |
| Ean | string | EAN |     |
| Unit | string | Unit |     |
| Weight | double | Weight |     |
| Volume | double | Volume |     |
| Height | double | Height |     |
| Length | double | Length |     |
| Width | double | Width |     |
| BoxUnit | string | Box unit |     |
| BoxEAN | string | Box EAN |     |
| BoxQuantity | double | Box quantity |     |
| PalletUnit | string | Pallet unit |     |
| PalletEAN | string | Pallet EAN |     |
| PalletQuantity | double | Pallet quantity |     |
| PriceNetto | double | Net price |     |
| TaxRate | double | Tax rate |     |
| PriceBrutto | double | Gross price |     |
| StatusDate | datetime | Status date |     |
| Status | xStatus | Status |     |

#### xProductApiConfig
Object describing product api config model

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| ProductId  | int | Id of xProduct |     |
| ApiConfigId  | int | Id of xApiConfig |     |
| ExportSetDetails  | bool | Export set details? |     |
| Code  | string | Product code |     |
| Name  | string | Product name |     |
| AlternativeCode  | string | Product alternative code |     |
| Ean  | string | Product EAN |     |

#### xProductDescription
Object describing product descriptions

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| ProductId  | int | Id of xProduct |     |
| CountryCode  | string | Country code (ex. PL, EN, US) |     |
| Description  | string | Description |     |
| DescriptionExtra1  | string | Extra field 1 |     |
| DescriptionExtra2  | string | Extra field 2 |     |
| DescriptionExtra3  | string | Extra field 3 |     |
| DescriptionExtra4  | string | Extra field 4 |     |

#### xProductParameter
Object describing product parameters

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| ProductId  | int | Id of xProduct |     |
| CountryCode  | string | Country code (ex. PL, EN, US) |     |
| Name  | string | Parameter name |     |
| Value  | string | Parameter value |     |

#### xProductSet
Object describing product sets

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| SetId  | int | Id of xProduct (which is a set) |     |
| ProductId  | int | Id of xProduct (belongs to a set) |     |
| Quantity  | int | Quantity |     |

#### xProductStock
Object describing product stocks

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| Id  | int (readonly) | Identifier |     |
| ProductId  | int | Id of xProduct |     |
| ModificationDate  | datetime | Modification date time |     |
| Quantity  | double | Quantity |     |
| WarehouseId  | int | Id of xWarehouse |     |
| ReservedQuantity  | double | Reserved quantity |     |
| AvailableQuantity  | double | Available quantity |     |
| BlockedQuantity  | double | Blocked quantity |     |
| OrderedQuantity  | double | Ordered quantity |     |
| Attribute1  | string | Attribute 1 |     |
| Attribute2  | string | Attribute 2 |     |
| Attribute3  | string | Attribute 3 |     |
| ModificationSource  | string | Modification Source |     |

#### xAttachmentBody
Object describing request body for attachment.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| OwnerToken  | Guid  | Owner token | x   |
| TransportOrderId  | int | Id of xTransportOrder | x - or OrderId   |
| OrderId  | int | Id of xOrder | x - or TransportOrderId   |
| Type  | string | Type of attachment (faktura) | x   |
| DocumentNr  | string | Document number | x   |
| OriginalFileName  | string | File name |     |
| OriginalFilePath  | string | File path |     |
| AttachmentData  | string | Attachment data (file encoded in base64) |     |
| Extension  | string | Extension (ex. pdf, zpl, gif) |     |
| Description  | string | Description |     |
| ExternalId  | string | External id |     |
| LabelPrinter  | string | Label printer |     |
| LabelHost  | string | Label host |     |

#### xOrderPatchStatusBody
Object describing request body for update statuses.

| **Property** | **Type** | **Description** | **Required? (x - true)** |
| --- | --- | --- | --- |
| OrderId  | int | Identifier | x   |
| OrderStatus  | int | Id of xStatus |     |
| WMSStatus  | int | Id of xStatus |     |
| ERPStatus  | int | Id of xStatus |     |
| InvoiceStatus  | int | Id of xStatus |     |
| ERPDocStatus  | int | Id of xStatus |     |
| WMSDocStatus  | int | Id of xStatus |     |

# Actions

## Auth actions

\[v0.9\]
\[POST\]
\[ANONYMOUS\]
\[REQUEST BODY: **xLoginBody**\]
\[RESPONSE: **xResLogin**\]

- **api/v0.9/auth/login**

Obtain a token that is used for authorization when using other secured endpoints. It is valid for 24 hours.

_Request:_
```
curl -X 'POST'  \
'https://localhost:7072/api/v0.9/auth/login' \  
\-H 'accept: */*'   \
\-H 'Content-Type: application/json'   \
\-d '{  
"email": "user@example.com", 
"password": "string"
}'
```
_Response body:_
```
{
"success": true, 
"code": 200,  
"message": "Login successfully.", 
"token": “eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c”  
}
```
\[v0.9\]
\[POST\]
\[ANONYMOUS\]
\[REQUEST BODY: **xRegisterBody**\]
\[RESPONSE: **xResNewEntries**\]

- **api/v0.9/auth/register**

Create an API access account. You can use endpoint if you have a special token designed for registration and registration is enabled.

_Request:_
```
curl -X 'POST' \  
'https://localhost:7072/api/v0.9/auth/register' \  
\-H 'accept: */*' \  
\-H 'Content-Type: application/json' \  
\-d '{  
"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9kc2UiLCJpYXQiOjE1MTYyMzkwMj.1nEepUs0tnzht21KQPjtiemxxhRSAbw-ORzorBgaLHw",  
"email": "user@example.com",  
"userName": "string",  
"password": "stringABC123!",  
"description": "string",  
"phoneNumber": "string"  
}'
```
_Response:_
```
{
"success": true,  
"code": 200, 
"message": "Successfully created user.",  
"entryIds": [2]  
}
```
## ApiConfigs actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xApiConfig**\]

- **api/v0.9/api-configs**

Get api configs.

_Request:_
```
curl -X 'GET'  \
'https://localhost:7072/api/v0.9/api-configs' \  
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{  
"id": 123
"integration": "DPD",
"integrationName": "DPD - Sandbox",
"ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
"isProductionConfig": false,
"isMarketplaceConfig": false,
"isCourierConfig": true,
"status": 1,
"parameters": [],
"ownerBank": "",
"ownerBankAccount": "",
"login": "test",
"password": "test",
"token": "",
"tokenDate": null,
"additionalID": "1",
"senderAddress": {
"id": 1,
"name": "Logsoft",
"street": "Wiejska",
"city": "Łódź",
"zipCode": "90-001",
"country": "PL",
"name2": "",
"name3": null,
"buildingNumber": "12",
"apartmentNumber": "",
"contactPerson": "KK",
"contactPhone": "123123123",
"contactEmail": ""
},
"language": null,
"labelFormat": "PDF",
"labelPath": null,
"pageFormat": null,
"baseUrl": "",
"tokenUrl": null,
"trackingStatus": 0,
"fristTrackingHour": null,
"printScaleMode": null,
"printPageOrientation": null,
"externalTrackingLink": null
  }
]
```
## Attachments actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xAttachment**\]

- **api/v0.9/attachments/all/transport-order/{transportOrderId}**
- Path: \[ _transportOrderId_ (int, required) \]

Get attachments for transport order.

_Request:_
```
curl -X 'GET' \  
'https://localhost:7072/api/v0.9/attachments/all/transport-order/123' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[
{
"id": 12,
"creationDate": "2024-05-17T14:17:29.2265027",
"ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
"type": "shippingOrder",
"documentNr": "shippingOrder_XXX",
"description": "List przewozowy.",
"autoPrintOn": null,
"status": null,
"printer": null,
"printerHost": null,
"printDate": null
}
]
```
\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **xAttachmentFile**\]

- **api/v0.9/attachments/file/transport-order/{transportOrderId}/{attachmentId}**
- Path: \[ _transportOrderId_ (int, required), _attachmentId_ (int, required) \]

Get attachment file for attachment.

_Request:_
```
curl -X 'GET' \  
'https://localhost:7072/api/v0.9/attachments/file/transport-order/123/12' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
{
"attachmentData": "[base64 encoded]",
"extension": "PDF",
"originalFileName": "shippingOrder_XXX ",
"originalFilePath": null
}
```
\[v0.9\]
\[POST\]
\[SECURED\]
\[REQUEST BODY: **xAttachmentBody**\]
\[RESPONSE: **xResNewEntries**\]

- **api/v0.9/attachments**

Create attachment.

_Request:_
```
curl -X 'POST' \  
'https://localhost:7072/api/v0.9/attachments' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
\-H 'Content-Type: application/json' \
  -d '{
  "ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
  "orderId": 1,
  "type": "faktura",
  "documentNr": "FV-1",
  "originalFileName": "faktura",
  "originalFilePath": null,
  "attachmentData": "[base64 encoded file]",
  "extension": "PDF",
  "description": "Faktura VAT",
  "externalId": "5432",
  "labelPrinter": "PRINTER-23",
  "labelHost": "DESKTOP-43548"
}'
```
_Response:_
```
{  
"success": true, 
"code": 200,
"message": "Successfully created.",
"entryIds": [1]
}
```

## Logs actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xLog for xTransportOrder**\]

- **api/v0.9/logs/all/transport-order/{transportOrderId}**
- Path: \[ _transportOrderId_ (int, required) \]

Get logs for transport order.

_Request:_
```
curl -X 'GET' \ 
'https://localhost:7072/api/v0.9/logs/all/transport-order/134' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{  
"id": 123,
"logTime": "2024-05-17T14:17:26.48976",
"status": 0,
"message": "Generowanie listu, status: OK ",
"errorTrace": null,
"host": "DESKTOP-123",
"type": "Generowanie listu",
"source": 0
}]
```
\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **xLogJson**\]

- **api/v0.9/logs/json/transport-order/{transportOrderId}/{logId}**
- Path: \[ _transportOrderId_ (required), _logId_ (required)\]

Get log JSON object (response and request) for log.

_Request:_
```
curl -X 'GET' \
'https://localhost:7072/api/v0.9/logs/json/transport-order/134/123' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
{
"request": "[string json]",
"response": "[string json]"
}
```
## Orders actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xOrder**\]

- **api/v0.9/orders**
- Parameters: \[ _limit_ (int, optional – max 100), _page_ (int, optional), _creationDateFrom_ (datetime, optional), _creationDateTo_ (datetime, optional), _getTrackingNumbers_ (bool, optional), _orderStatus_ (int, optional), _wmsStatus_ (int, optional), _erpStatus_ (int, optional)\]

Get list of orders. Max 100 orders per request.

To get orders with a specific status, use the parameters: 

_orderStatus_ - order status
_erpStatus_ - ERP status
_wmsStatus_ - WMS status

The status identifier is given as the parameter value.

_Request:_
```
curl -X 'GET' \
'https://localhost:7072/api/v0.9/orders?limit=1&creationDateFrom=2024-05-17' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{  
"id": 123,
"ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
"priority": 0,
"creationDate": "2024-05-17T00:01:51.58",
"wantInvoice": false,
"items": [{  
"id": 1,
"productName": "Test Test",
"orderedQuantity": 5,
"lineNumber": null,
"productId": 0,
"productExternalId": "ACC3",
"productCode": "ADSC43",
"productAlternativeCode": null,
"productWarehouseGroup": null,
"productEAN": null,
"productUnit": null,
"productWeight": null,
"productVolume": null,
"productHeight": null,
"productLength": null,
"productWidth": null,
"productBoxUnit": null,
"productBoxEAN": null,
"productBoxQuantity": null,
"productPalletUnit": null,
"productPalletEAN": null,
"productPalletQuantity": null,
"productIsService": false,
"confirmedQuantity": null,
"priceNetto": 9.6179,
"taxRate": 23,
"priceBrutto": 11.83,
"offer": "13457673795",
"set": null,
"boughtDate": "2024-05-17T12:03:51.58"
}],  
"invoiceStatus": null,
"erpStatus": null,
"wmsStatus": null,
"orderStatus": {
"id": 110,
"name": "Do realizacji",
"englishName": "Ready for processing",
"dependency": "OrderStatus",
"code": "ReadyForProcessing",
"description": null,
"backColor": "#FAFA43",
"textColor": "#000000"
}, 
"receiverAddress": {
"id": 1,
"name": "Logsoft",
"street": "Wiejska",
"city": "Łódź",
"zipCode": "90-001",
"country": "PL",
"name2": "",
"name3": null,
"buildingNumber": "12",
"apartmentNumber": "",
"contactPerson": "KK",
"contactPhone": "123123123",
"contactEmail": ""
},  
"invoiceAddress": null,
"warehouse": null,
"sourceInfo": "allegro-pl",
"sourceConfigId": 34,
"marketPlaceDocNr": "TEST",
"marketPlaceDocId": null,
"erpDocNr": null,
"erpDocId": null,
"erpConfigId": null,
"erpStatusDate": null,
"invoiceDocNr": null,
"invoiceDocId": null,
"invoiceConfigId": null,
"invoiceStatusDate": null,
"wmsDocNr": null,
"wmsDocId": null,
"wmsConfigId": null,
"wmsStatusDate": null,
"currency": "PLN",
"paymentMethod": "ONLINE",
"paymentValue": 59.15,
"paymentDate": "2024-05-16T22:04:00.51",
"paid": true,
"userComments": null,
"adminComments": null,
"cod": null,
"codPaymentMethod": null,
"codCurrency": null,
"bankAccountNr": null,
"insurance": null,
"insuranceCurrency": null,
"declaredValue": 59.15,
"declaredValueCurrency": "PLN",
"parcelLocker": "ACXA34",
"customSourceId": null,
"marketplaceUserLogin": "TEST",
"shippingServiceExternalSourceID": "ASC342543554ASD",
"shippingServiceSourceExternalName": "Allegro Paczkomaty InPost",
"isFulfillmentOrder": false,
"fulfillmentStatus": null,
"shippingPrice": null,
"invoiceFullName": null,
"invoiceCompany": null,
"invoiceNip": null,
"extraField1": null,
"extraField2": null,
"orderStatusDate": "2024-05-16T03:01:23.23423423",
"importDate": "2024-05-16T03:01:23.4234235",
"orderCloseDate": null,
"orderPage": null,
"transportOrderId": null,
"trackingNumbers": []
}]
```
\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **xOrder or null**\]

- **api/v0.9/orders/{orderId}**
- Path: \[ _orderId_ (int, required), _getTrackingNumbers_ (bool, optional) \]

Get order.

_Request:_
```
curl -X 'GET' \
'https://localhost:7072/api/v0.9/orders/123' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
{ 
"id": 123,
"ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
"priority": 0,
"creationDate": "2024-05-17T00:01:51.58",
"wantInvoice": false,
"items": [{
"id": 1,
"productName": "Test Test",
"orderedQuantity": 5,
"lineNumber": null,
"productId": 0,
"productExternalId": "ACC3",
"productCode": "ADSC43",
"productAlternativeCode": null,
"productWarehouseGroup": null,
"productEAN": null,
"productUnit": null,
"productWeight": null,
"productVolume": null,
"productHeight": null,
"productLength": null,
"productWidth": null,
"productBoxUnit": null,
"productBoxEAN": null,
"productBoxQuantity": null,
"productPalletUnit": null,
"productPalletEAN": null,
"productPalletQuantity": null,
"productIsService": false,
"confirmedQuantity": null,
"priceNetto": 9.6179,
"taxRate": 23,
"priceBrutto": 11.83,
"offer": "13457673795",
"set": null,
"boughtDate": "2024-05-17T12:03:51.58"
}],
"invoiceStatus": null,
"erpStatus": null,
"wmsStatus": null,
"orderStatus": {
"id": 110,
"name": "Do realizacji",
"englishName": "Ready for processing",
"dependency": "OrderStatus",
"code": "ReadyForProcessing",
"description": null,
"backColor": "#FAFA43",
"textColor": "#000000"
},  
"receiverAddress": {
"id": 1,
"name": "Logsoft",
"street": "Wiejska",
"city": "Łódź",
"zipCode": "90-001",
"country": "PL",
"name2": "",
"name3": null,
"buildingNumber": "12",
"apartmentNumber": "",
"contactPerson": "KK",
"contactPhone": "123123123",
"contactEmail": ""
},
"invoiceAddress": null,
"warehouse": null,
"sourceInfo": "allegro-pl",
"sourceConfigId": 34,
"marketPlaceDocNr": "TEST",
"marketPlaceDocId": null,
"erpDocNr": null,
"erpDocId": null,
"erpConfigId": null,
"erpStatusDate": null,
"invoiceDocNr": null,
"invoiceDocId": null,
"invoiceConfigId": null,
"invoiceStatusDate": null,
"wmsDocNr": null,
"wmsDocId": null,
"wmsConfigId": null,
"wmsStatusDate": null,
"currency": "PLN",
"paymentMethod": "ONLINE",
"paymentValue": 59.15,
"paymentDate": "2024-05-16T22:04:00.51",
"paid": true,
"userComments": null,
"adminComments": null,
"cod": null,
"codPaymentMethod": null,
"codCurrency": null,
"bankAccountNr": null,
"insurance": null,
"insuranceCurrency": null,
"declaredValue": 59.15,
"declaredValueCurrency": "PLN",
"parcelLocker": "ACXA34",
"customSourceId": null,
"marketplaceUserLogin": "TEST",
"shippingServiceExternalSourceID": "ASC342543554ASD",
"shippingServiceSourceExternalName": "Allegro UPS",
"isFulfillmentOrder": false,
"fulfillmentStatus": null,
"shippingPrice": null,
"invoiceFullName": null,
"invoiceCompany": null,
"invoiceNip": null,
"extraField1": null,
"extraField2": null,
"orderStatusDate": "2024-05-16T03:01:23.23423423",
"importDate": "2024-05-16T03:01:23.4234235",
"orderCloseDate": null,
"orderPage": null,
"transportOrderId": null,
"trackingNumbers": []
}
```

\[v0.9\]
\[POST\]
\[SECURED\]
\[REQUEST BODY: **Array of xOrderBody**\]
\[RESPONSE: **xResNewEntries**\]

- **api/v0.9/orders**

Create orders.

_Request:_
```
curl -X 'POST' \
'https://localhost:7072/api/v0.9/orders' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
\-H 'Content-Type: application/json' \
  -d '[
  {
    "ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
    "priority": 0,
    "wantInvoice": true,
    "creationDate": "2024-05-22T09:19:12.901Z"
    "items": [
      {
        "productName": "string",
        "orderedQuantity": 0,
        "lineNumber": 0,
        "productId": 0,
        "productExternalId": "string",
        "productCode": "string",
        "productAlternativeCode": "string",
        "productWarehouseGroup": "string",
        "productEAN": "string",
        "productUnit": "string",
        "productWeight": 0,
        "productVolume": 0,
        "productHeight": 0,
        "productLength": 0,
        "productWidth": 0,
        "productBoxUnit": "string",
        "productBoxEAN": "string",
        "productBoxQuantity": 0,
        "productPalletUnit": "string",
        "productPalletEAN": "string",
        "productPalletQuantity": 0,
        "productIsService": true,
        "confirmedQuantity": 0,
        "priceNetto": 0,
        "taxRate": 0,
        "priceBrutto": 0,
        "offer": "string",
        "set": "string",
        "boughtDate": "2024-05-22T09:19:12.901Z"
      }
    ],
    "receiverAddress": {
      "name": "string",
      "street": "string",
      "city": "string",
      "zipCode": "string",
      "country": "string",
      "name2": "string",
      "name3": "string",
      "buildingNumber": "string",
      "apartmentNumber": "string",
      "contactPerson": "string",
      "contactPhone": "string",
      "contactEmail": "string"
    },
    "invoiceAddress": {
      "name": "string",
      "street": "string",
      "city": "string",
      "zipCode": "string",
      "country": "string",
      "name2": "string",
      "name3": "string",
      "buildingNumber": "string",
      "apartmentNumber": "string",
      "contactPerson": "string",
      "contactPhone": "string",
      "contactEmail": "string"
    },
    "warehouseId": null,
    "sourceConfigId": 0,
    "marketPlaceDocNr": "string",
    "marketPlaceDocId": 0,
    "erpDocNr": "string",
    "erpDocId": 0,
    "erpConfigId": 0,
    "invoiceDocNr": "string",
    "invoiceDocId": 0,
    "invoiceConfigId": 0,
    "wmsDocNr": "string",
    "wmsDocId": 0,
    "wmsConfigId": 0,
    "currency": "string",
    "paymentMethod": "string",
    "paymentValue": 0,
    "paymentDate": "2024-05-22T09:19:12.901Z",
    "paid": true,
    "userComments": "string",
    "adminComments": "string",
    "cod": 0,
    "codPaymentMethod": "string",
    "codCurrency": "string",
    "bankAccountNr": "string",
    "insurance": 0,
    "insuranceCurrency": "string",
    "declaredValue": 0,
    "declaredValueCurrency": "string",
    "parcelLocker": "string",
    "customSourceId": 0,
    "marketplaceUserLogin": "string",
    "shippingServiceExternalSourceID": "string",
    "shippingServiceSourceExternalName": "string",
    "isFulfillmentOrder": true,
    "fulfillmentStatus": "string",
    "shippingPrice": 0,
    "invoiceFullName": "string",
    "invoiceCompany": "string",
    "invoiceNip": "string",
    "extraField1": "string",
    "extraField2": "string"
  }
]'
```
_Response:_
```
{  
"success": true, 
"code": 200,
"message": "Successfully created.",
"entryIds": [1]
}
```

\[v0.9\]
\[PATCH\]
\[SECURED\]
\[REQUEST BODY: **xOrderPatchStatusBody**\]
\[RESPONSE: **xResponse**\]

- **api/v0.9/orders/status**

Update statuses of xOrder.

_Request:_
```
curl -X 'PATCH' \
  'https://localhost:7072/api/v0.9/orders/status' \
  -H 'accept: */*' \
  -H 'Content-Type: application/json' \
  -d '{
  "orderId": 3415,
  "orderStatus": null,
  "wmsStatus": 1004,
  "erpStatus": 1005,
  "invoiceStatus": null,
  "erpDocStatus": null,
  "wmsDocStatus": null
}'
```
_Response:_
```
{
	"success": true,
	"code": 200,
	"message": "ERP status updated. WMS status updated."
}
```

## Owners actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xOwner**\]

- **api/v0.9/owners**

Get list of owners.

_Request:_
```
curl -X 'GET' \
'https://localhost:7072/api/v0.9/owners' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{
"token": "2db7e5cf-b84c-42a1-aea4-533f660c534f ",
"name": "Test",
"status": 1,
"address": null,
"description": "Test",
"wmsId": "32"
}]
```
Packages actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **List of xPackage**\]

- **api/v0.9/packages/all/{transportOrderId}**
- Path: \[ _transportOrderId_ (int, required) \]

Get packages for transport order.

_Request:_
```
curl -X 'GET' \
'https://localhost:7072/api/v0.9/packages/all/1234' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{
"id": 33,
"trackingNumber": "TEST123123123",
"type": "PALET",
"sizeType": null,
"length": 120,
"width": 80,
"height": 200,
"weight": 200,
"description": "AVC/01",
"internalPackageNumber": "AVC/01",
"externalPackageNumber": "ABC123"
}]
```
\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **xPackageLabel**\]

- **api/v0.9/packages/label/{transportOrderId}/{packageId}**
- Path: \[ _transportOrderId_ (required), _packageId_ (required)\]

Get label for package.

_Request:_
```
curl -X 'GET' \
'https://localhost:7072/api/v0.9/packages/label/1234/33' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
{
"label": "[base64 encoded]", 
"labelType": "PDF"
}
```
## Services actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **List of xShippingService**\]

- **api/v0.9/services**

Get list of available shipping services.

_Request:_
```
curl -X 'GET' \
'https://localhost:7072/api/v0.9/services' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{
"id": 1,
"name": "DHL Dostawa w sobotę",
"courierName": "DHL",
"description": "Moze nie być dostępna dla wszystkich adresów",
"subCourierName": null,
"marketplace": null,
"wmsService": null,
"courierService": null,
"status": 1,
"onePackageService": null,
"defaultPackageType": null,
"defaultPackageSizeType": null,
"defaultPackageLength": null,
"defaultPackageWidth": null,
"defaultPackageHeight": null,
"defaultPackageWeight": null,
"defaultInsurance": null,
"defaultInsuranceCurrency": null
}]
```

## Tracking actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xTracking**\]

- **api/v0.9/tracking/all/{transportOrderId}**
- Path: \[ _transportOrderId_ (int, required) \]

Get tracking for transport order.

_Request:_
```
curl -X 'GET' \  
'https://localhost:7072/api/v0.9/tracking/all/1234' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{ 
"id": 1241,
"source": 0,
"statusTime": "2024-05-17T14:12:12",
"updateTime": "2024-05-17T14:12:54.5432",
"trackingNumber": "TESTETST213",
"status": "Czeka na kuriera",
"statusDescription": "Nadawca utworzył etykietę, firma UPS jeszcze nie odebrała paczki. ",
"additionalStatus": "M",
"additionalDescription": null,
"location": null,
"externalId": null
}  ]
```
## TransportOrders actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xTransportOrder**\]

- **api/v0.9/transport-orders**
- Parameters: \[ _limit_ (int, optional – max 100), _page_ (int, optional), _creationDateFrom_ (datetime, optional), _creationDateTo_ (datetime, optional)\]

Get list of transport orders. Max 100 transport orders per request.

_Request:_
```
curl -X 'GET' \ 
'https://localhost:7072/api/v0.9/transport-orders?limit=1&creationDateFrom=2024-05-17' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{ 
"id": 1234,
"integration": "Test",
"integrationName": "Test UPS",
"ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f ",
"creationDate": "2024-05-17T10:20:11.5434",
"orderNr": "TEST123123",
"referenceNr": "TEST123123",
"receiverAddress": {
"id": 1,
"name": "Logsoft",
"street": "Wiejska",
"city": "Łódź",
"zipCode": "90-001",
"country": "PL",
"name2": "",
"name3": null,
"buildingNumber": "12",
"apartmentNumber": "",
"contactPerson": "KK",
"contactPhone": "123123123",
"contactEmail": ""
},
"externalStatus": "",
"warehouse": null,
"service": {
"id": 321,
"name": "UPS",
"courierName": "UPS",
"description": null,
"subCourierName": " UPS ",
"marketplace": null,
"wmsService": null,
"courierService": null,
"status": 1,
"onePackageService": false,
"defaultPackageType": null,
"defaultPackageSizeType": null,
"defaultPackageLength": null,
"defaultPackageWidth": null,
"defaultPackageHeight": null,
"defaultPackageWeight": null,
"defaultInsurance": null,
"defaultInsuranceCurrency": null
},
"transportOrderStatus": {
"id": 2,
"name": "Wygenerowane",
"englishName": "Generated",
"dependency": "TransportOrderStatus",
"code": "Generated",
"description": "Wygenerowane poprawnie",
"backColor": "#99CC00",
"textColor": "#FFFFFF"
},
"trackingStatus": null,
"labelStatus": {
"id": 25,
"name": "Wydrukowana",
"englishName": "Printed",
"dependency": "PrintStatus",
"code": "Printed",
"description": "Wydrukowana poprawnie ",
"backColor": "#99CC00",
"textColor": "#FFFFFF"
},
"externalId": "12356",
"description": null,
"mainTrackingNumber": null,
"cod": 0,
"codCurrency": "PLN",
"bankAccountNr": null,
"insurance": 120.99,
"insuranceCurrency": "PLN",
"declaredValue": null,
"declaredValueCurrency": "PLN",
"parcelLocker": null,
"labelPrinter": "PDF",
"labelHost": "",
"generationDate": null,
"printDate": "2024-05-17T18:18:34.5553",
"trackingDate": null,
"trackingGuid": null,
"wmsDocId": 11353
}]

```
\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **xTransportOrder or null**\]

- **api/v0.9/transport-orders/{transportOrderId}**
- Path: \[ _transportOrderId_ (int, required) \]

Get transport order.

_Request:_
```
curl -X 'GET' \ 
'https://localhost:7072/api/v0.9/transport-orders/1234' \ 
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_  
```
{
"id": 1234,
"integration": "Test",
"integrationName": "Test UPS",
"ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
"creationDate": "2024-05-17T10:20:11.5434",
"orderNr": "TEST123123",
"referenceNr": "TEST123123",
"receiverAddress": {
"id": 1,
"name": "Logsoft",
"street": "Wiejska",
"city": "Łódź",
"zipCode": "90-001",
"country": "PL",
"name2": "",
"name3": null,
"buildingNumber": "12",
"apartmentNumber": "",
"contactPerson": "KK",
"contactPhone": "123123123",
"contactEmail": ""
},
"externalStatus": "",
"warehouse": null,
"service": {
"id": 321,
"name": "UPS",
"courierName": "UPS",
"description": null,
"subCourierName": " UPS ",
"marketplace": null,
"wmsService": null,
"courierService": null,
"status": 1,
"onePackageService": false,
"defaultPackageType": null,
"defaultPackageSizeType": null,
"defaultPackageLength": null,
"defaultPackageWidth": null,
"defaultPackageHeight": null,
"defaultPackageWeight": null,
"defaultInsurance": null,
"defaultInsuranceCurrency": null
},
"transportOrderStatus": {
"id": 2,
"name": "Wygenerowane",
"englishName": "Generated",
"dependency": "TransportOrderStatus",
"code": "Generated",
"description": "Wygenerowane poprawnie",
"backColor": "#99CC00",
"textColor": "#FFFFFF"
},
"trackingStatus": null,
"labelStatus": {
"id": 25,
"name": "Wydrukowana",
"englishName": "Printed",
"dependency": "PrintStatus",
"code": "Printed",
"description": "Wydrukowana poprawnie ",
"backColor": "#99CC00",
"textColor": "#FFFFFF"
},
"externalId": "12356",
"description": null,
"mainTrackingNumber": null,
"cod": 0,
"codCurrency": "PLN",
"bankAccountNr": null,
"insurance": 120.99,
"insuranceCurrency": "PLN",
"declaredValue": null,
"declaredValueCurrency": "PLN",
"parcelLocker": null,
"labelPrinter": "PDF",
"labelHost": "",
"generationDate": null,
"printDate": "2024-05-17T18:18:34.5553",
"trackingDate": null,
"trackingGuid": null,
"wmsDocId": 11353
}  
```
\[v0.9\]
\[POST\]
\[SECURED\]
\[REQUEST BODY: **Array of xTransportOrderBody**\]
\[RESPONSE: **xResNewEntries**\]

- **api/v0.9/transport-orders**

Create transport orders.

_Request:_
```
curl -X 'POST' \
'https://localhost:7072/api/v0.9/transport-orders' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
\-H 'Content-Type: application/json' \
\-d '[{  
"apiConfigName": "string",
"ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
"receiverAddress": {
"name": "string",
"street": "string",
"city": "string",
"zipCode": "string",
"country": "string",
"name2": "string",
"name3": "string",
"buildingNumber": "string",
"apartmentNumber": "string",
"contactPerson": "string",
"contactPhone": "string",
"contactEmail": "string"
},
"orderNr": "string",
"referenceNr": "string",
"externalStatus": "string",
"packages": [{
"trackingNumber": "string",
"type": "string",
"sizeType": "string",
"length": 0,
"width": 0,
"height": 0,
"weight": 0,
"description": "string",
"internalPackageNumber": "string"
}],
"warehouseId": 0,
"externalId": "string",
"description": "string",
"shippingServiceName": "string",
"shippingServiceCourierService": "string",
"cod": 0,
"codCurrency": "string",
"bankAccountNr": "string",
"insurance": 0,
"insuranceCurrency": "string",
"declaredValue": 0,
"declaredValueCurrency": "string",
"parcelLocker": "string",
"labelPrinter": "string",
"labelHost": "string"
}]
```
_Response:_
```
{  
"success": true, 
"code": 200,
"message": "Successfully created.",
"entryIds": [1]
}
```
## Warehouses actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xWarehouse**\]

- **api/v0.9/warehouses**

Get list of warehouses.

_Request:_
```
curl -X 'GET' \
'https://localhost:7072/api/v0.9/warehouses' \
\-H 'accept: */*' \
\-H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[{
"id": 14,
"code": "Centrum testowe",
"address": {
"id": 312,
"name": "Test/Test",
"street": "Kowalska",
"city": "Łódź",
"zipCode": "00-001",
"country": "PL",
"name2": null,
"name3": null,
"buildingNumber": "34",
"apartmentNumber": "54",
"contactPerson": null,
"contactPhone": null,
"contactEmail": null
},
"description": null
}]
```
## Products actions

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xProduct**\]

- **api/v0.9/products**
- Parameters: \[ _limit_ (int, optional – max 100) \]
  
Get list of products. Max 100 per request.

_Request:_
```
curl -X 'GET' \
  'https://localhost:7072/api/v0.9/products?limit=1' \
  -H 'accept: */*' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[
  {
    "id": 1,
    "isSet": true,
    "name": "PATELNIA_TEST",
    "imageUrls": [
      "https://encrypted-tbn0.gstatic.com/shopping?q=tbn:ANd9GcQjc7XyEsisAb99GTvTUBpp4NSjBId2Exo1Io3iJ7KUOgoJpPico_NjJGxKhSqs23jANGT1LMRzEkQq9nVfte59mAyWf1Xmq8dviWtwcplSU74xuMfaE2SV"
    ],
    "ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
    "code": "123123",
    "manufacturerName": null,
    "sourceId": null,
    "sourceName": null,
    "alternativeCode": "PATELNIA_TEST",
    "categoryId": "123123",
    "warehouseGroup": null,
    "ean": "",
    "unit": "szt",
    "weight": null,
    "volume": null,
    "height": null,
    "length": null,
    "width": null,
    "boxUnit": null,
    "boxEAN": null,
    "boxQuantity": null,
    "palletUnit": null,
    "palletEAN": null,
    "palletQuantity": null,
    "priceNetto": 18.2764,
    "taxRate": 23,
    "priceBrutto": 22.48,
    "statusDate": null,
    "status": {
      "id": 1013,
      "name": "Aktywny",
      "englishName": "Active",
      "dependency": "ProductStatus",
      "code": "Active",
      "description": null,
      "backColor": "#339966",
      "textColor": "#FFFFFF"
    }
  }
]
```

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **xProduct**\]

- **api/v0.9/products/{productId}**
- Path: \[ _productId_ (int, required) \]
  
Get product by id.

_Request:_
```
curl -X 'GET' \
  'https://localhost:7072/api/v0.9/products/1' \
  -H 'accept: */*' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
{
  "id": 1,
  "isSet": true,
  "name": "PATELNIA_TEST",
  "imageUrls": [
    "https://encrypted-tbn0.gstatic.com/shopping?q=tbn:ANd9GcQjc7XyEsisAb99GTvTUBpp4NSjBId2Exo1Io3iJ7KUOgoJpPico_NjJGxKhSqs23jANGT1LMRzEkQq9nVfte59mAyWf1Xmq8dviWtwcplSU74xuMfaE2SV"
  ],
  "ownerToken": "2db7e5cf-b84c-42a1-aea4-533f660c534f",
  "code": "123123",
  "manufacturerName": null,
  "sourceId": null,
  "sourceName": null,
  "alternativeCode": "PATELNIA_TEST",
  "categoryId": "123123",
  "warehouseGroup": null,
  "ean": "",
  "unit": "szt",
  "weight": null,
  "volume": null,
  "height": null,
  "length": null,
  "width": null,
  "boxUnit": null,
  "boxEAN": null,
  "boxQuantity": null,
  "palletUnit": null,
  "palletEAN": null,
  "palletQuantity": null,
  "priceNetto": 18.2764,
  "taxRate": 23,
  "priceBrutto": 22.48,
  "statusDate": null,
  "status": {
    "id": 1013,
    "name": "Aktywny",
    "englishName": "Active",
    "dependency": "ProductStatus",
    "code": "Active",
    "description": null,
    "backColor": "#339966",
    "textColor": "#FFFFFF"
  }
}
```

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xProductSock**\]

- **api/v0.9/products/stocks/all/{productId}**
- Path: \[ _productId_ (int, required) \]
  
Get product stocks by product id.

_Request:_
```
curl -X 'GET' \
  'https://localhost:7072/api/v0.9/products/stocks/all/1' \
  -H 'accept: */*' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[
  {
    "id": 23,
    "productId": 1,
    "modificationDate": "2024-06-06T13:26:42.9593019",
    "quantity": 43,
    "warehouseId": null,
    "reservedQuantity": 0,
    "availableQuantity": 43,
    "blockedQuantity": 0,
    "orderedQuantity": null,
    "attribute1": null,
    "attribute2": null,
    "attribute3": null,
    "modificationSource": "wms"
  }
]
```

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xProductApiConfig**\]

- **api/v0.9/products/api-configs/all/{productId}**
- Path: \[ _productId_ (int, required) \]
  
Get product api configs by product id.

_Request:_
```
curl -X 'GET' \
  'https://localhost:7072/api/v0.9/products/api-configs/all/1' \
  -H 'accept: */*' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[
  {
    "id": 2,
    "productId": 1,
    "apiConfigId": 3,
    "exportSetDetails": false,
    "code": "Test",
    "name": null,
    "alternativeCode": null,
    "ean": null
  }
]
```

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xProductSet**\]

- **api/v0.9/products/sets/all/{productId}**
- Path: \[ _productId_ (int, required) \]
  
Get product sets by product id.

_Request:_
```
curl -X 'GET' \
  'https://localhost:7072/api/v0.9/products/sets/all/1' \
  -H 'accept: */*' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[
  {
    "id": 1,
    "setId": 1,
    "productId": 2,
    "quantity": 3
  },
  {
    "id": 2,
    "setId": 1,
    "productId": 3,
    "quantity": 2
  }
]
```

\[v0.9\]
\[GET\]
\[SECURED\]
\[RESPONSE: **Empty list or list of xProductParameter**\]

- **api/v0.9/products/parameters/all/{productId}**
- Path: \[ _productId_ (int, required) \]
  
Get product parameters by product id.

_Request:_
```
curl -X 'GET' \
  'https://localhost:7072/api/v0.9/products/parameters/all/1' \
  -H 'accept: */*' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```
_Response:_
```
[
  {
    "id": 1,
    "productId": 1,
    "countryCode": "PL",
    "name": "Test",
    "value": null
  },
  {
    "id": 2,
    "productId": 1,
    "countryCode": "EN",
    "name": "Test2",
    "value": "Test"
  }
]
```
