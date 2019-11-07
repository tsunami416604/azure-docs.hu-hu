---
title: Adatok másolása a Xero a Azure Data Factory használatával
description: Bemutatjuk, hogy miként másolhatók adatok a Xero-ből a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: d52e536170c649cbc84b6c6dce92afb76ffe3125
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680005"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Adatok másolása a Xero a Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Xero való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Xero-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Xero bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a Xero-összekötő a következőket támogatja:

- Xero [privát alkalmazás](https://developer.xero.com/documentation/getting-started/api-application-types) , de nem nyilvános alkalmazás.
- Minden Xero-tábla (API-végpont) a "jelentések" kivételével. 

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Xero-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Xero társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Xero** | Igen |
| gazdagép | A Xero-kiszolgáló végpontja (`api.xero.com`).  | Igen |
| consumerKey | A Xero alkalmazáshoz társított fogyasztói kulcs. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| privateKey | A Xero privát alkalmazásához létrehozott. PEM fájl titkos kulcsa. a [nyilvános/titkos kulcspár létrehozása](https://developer.xero.com/documentation/api-guides/create-publicprivate-key)című témakörben talál további információt. Vegye figyelembe, hogy **a privatekey. PEM előállításához a 512-es numbits-t** használja `openssl genrsa -out privatekey.pem 512`; a 1024 nem támogatott. Adja meg a. PEM fájl összes szövegét, beleértve a UNIX-sorok végződését (\n), lásd az alábbi mintát.<br/><br/>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Meghatározza, hogy az adatforrás-végpontok HTTPS protokollal legyenek titkosítva. Az alapértelmezett érték TRUE (igaz).  | Nem |
| useHostVerification | Megadja, hogy az állomásnév kötelező-e a kiszolgáló tanúsítványában, hogy az megfeleljen a kiszolgáló állomásneve, amikor SSL-kapcsolaton keresztül csatlakozik. Az alapértelmezett érték TRUE (igaz).  | Nem |
| usePeerVerification | Meghatározza, hogy az SSL protokollon keresztüli kapcsolódáskor ellenőrizni kell-e a kiszolgáló identitását. Az alapértelmezett érték TRUE (igaz).  | Nem |

**Példa**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Minta titkos kulcsának értéke:**

Adja meg a. PEM fájl összes szövegét, beleértve a UNIX-sorok végződését (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Xero adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Xero való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **XeroObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **XeroObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Xero forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="xero-as-source"></a>Xero forrásként

Az adatok Xero való másolásához állítsa a forrás típusát a másolás tevékenység **XeroSource**értékére. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **XeroSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM Contacts"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

A Xero-lekérdezés megadásakor vegye figyelembe a következőket:

- Az összetett elemeket tartalmazó táblák több táblára lesznek felosztva. A banki tranzakciók például a "listaelemek" nevű összetett adatstruktúrával rendelkeznek, így a banki tranzakciós adat a táblázat `Bank_Transaction` és `Bank_Transaction_Line_Items`, a `Bank_Transaction_ID` mint idegen kulcs használatával együtt.

- A Xero-adatkészletek két sémán keresztül érhetők el: `Minimal` (alapértelmezett) és `Complete`. A teljes séma olyan előfeltételként szükséges hívási táblákat tartalmaz, amelyek további adatértékeket igényelnek (például azonosító oszlop) a kívánt lekérdezés végrehajtása előtt.

Az alábbi táblázatokban ugyanazok az adatok szerepelnek a minimális és a teljes sémában. Az API-hívások számának csökkentéséhez használja a minimális sémát (alapértelmezett).

- Bank_Transactions
- Contact_Groups 
- Kapcsolatok 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Számlák 
- Invoices_Credit_Notes
- Invoices_ előfizetések 
- Invoices_Overpayments 
- Manual_Journals 
- Túlfizetések 
- Overpayments_Allocations 
- Előlegek 
- Prepayments_Allocations 
- Bevételek 
- Receipt_Validation_Errors 
- Tracking_Categories

A következő táblázatok csak a teljes sémával kérhetők le:

- Befejezve. Bank_Transaction_Line_Items 
- Befejezve. Bank_Transaction_Line_Item_Tracking 
- Befejezve. Contact_Group_Contacts 
- Kész. Contacts_Contact_ személyek 
- Befejezve. Credit_Note_Line_Items 
- Befejezve. Credit_Notes_Line_Items_Tracking 
- Teljesített. Expense_Claim_ kifizetések 
- Befejezve. Expense_Claim_Receipts 
- Befejezve. Invoice_Line_Items 
- Befejezve. Invoices_Line_Items_Tracking
- Befejezve. Manual_Journal_Lines 
- Befejezve. Manual_Journal_Line_Tracking 
- Befejezve. Overpayment_Line_Items 
- Befejezve. Overpayment_Line_Items_Tracking 
- Befejezve. Prepayment_Line_Items 
- Befejezve. Prepayment_Line_Item_Tracking 
- Befejezve. Receipt_Line_Items 
- Befejezve. Receipt_Line_Item_Tracking 
- Befejezve. Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>További lépések
A másolási tevékenység által támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
