---
title: Adatok másolása a Xero-ból az Azure Data Factory használatával
description: Megtudhatja, hogyan másolhatja az adatokat a Xero-ból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: bdb2dc283287bf83410f1846aca11f233e93d01b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990847"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Adatok másolása a Xero-ból az Azure Data Factory használatával

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a Xero. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Xero-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Xero-ról bármilyen támogatott fogadó adattárba másolhat adatokat. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban ez a Xero csatlakozó támogatja:

- Xero [magánalkalmazás,](https://developer.xero.com/documentation/getting-started/api-application-types) de nem nyilvános alkalmazás.
- Minden Xero tábla (API-végpont), kivéve a "Jelentések". 

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a Xero-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A Xero csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **Xero** | Igen |
| gazda | A Xero kiszolgáló (`api.xero.com`) végpontja .  | Igen |
| consumerKey (kulcs) | A Xero alkalmazáshoz társított fogyasztói kulcs. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| privát kulcs | A Xero privát alkalmazáshoz létrehozott .pem fájl személyes kulcsa, [lásd: Nyilvános/titkos kulcspár létrehozása](https://developer.xero.com/documentation/api-guides/create-publicprivate-key). Megjegyzés, hogy **létrehoz a privatekey.pem a numbits a 512** segítségével `openssl genrsa -out privatekey.pem 512`; Az 1024 nem támogatott. A .pem fájl összes szövegét tartalmazza, beleértve a Unix sorvégződéseket(\n), lásd az alábbi mintát.<br/><br/>Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| useEncryptedEndpoints | Itt adható meg, hogy az adatforrás végpontjai HTTPS protokoll használatával titkosítva legyenek-e. Az alapértelmezett érték az igaz.  | Nem |
| useHostVerification (useHostVerification) | Itt adható meg, hogy a kiszolgáló tanúsítványában szükség van-e az állomásnévre ahhoz, hogy megfeleljen a kiszolgáló állomásnevének, amikor TLS-en keresztül csatlakozik. Az alapértelmezett érték az igaz.  | Nem |
| usePeerVerification | Itt adható meg, hogy a kiszolgáló identitását ellenőrizze-e a TLS-en keresztüli csatlakozáskor. Az alapértelmezett érték az igaz.  | Nem |

**Példa:**

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

**Minta személyes kulcs értéke:**

A .pem fájl teljes szövegének belefoglalása, beleértve a Unix sorvégződéseket(\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a Xero adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Xero programból, állítsa az adatkészlet típustulajdonságát **XeroObject**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következőre kell állítania: **XeroObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

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

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a Xero-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="xero-as-source"></a>Xero mint forrás

Ha adatokat szeretne másolni a Xero-ból, állítsa be a forrástípust a másolási tevékenységben a **XeroSource elemre.** A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **XeroSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM Contacts"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

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

A Xero lekérdezés megadásakor vegye figyelembe a következőket:

- Az összetett elemeket tartalmazó táblák több táblára lesznek felosztva. A banki tranzakciók például összetett adatstruktúrával rendelkeznek: "Sorok", így `Bank_Transaction` a `Bank_Transaction_Line_Items`banki `Bank_Transaction_ID` tranzakciók adatai táblához vannak rendelve, és a , idegen kulcsként összekapcsolják őket.

- A Xero-adatok két sémán keresztül érhetők el: `Minimal` (alapértelmezett) és `Complete`. A Teljes séma olyan előfeltételként szolgáló hívástáblákat tartalmaz, amelyek további adatokat (például azonosító oszlopot) igényelnek a kívánt lekérdezés létrehozása előtt.

Az alábbi táblák ugyanazt az információt a Minimális és a Teljes séma. Az API-hívások számának csökkentéséhez használja a Minimális sémát (alapértelmezett).

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
- Invoices_ előlegek 
- Invoices_Overpayments 
- Manual_Journals 
- Túlfizetések 
- Overpayments_Allocations 
- Előlegek 
- Prepayments_Allocations 
- Bevételek 
- Receipt_Validation_Errors 
- Tracking_Categories

A következő táblák csak teljes sémával kérdezhetők le:

- Teljes,Bank_Transaction_Line_Items 
- Teljes.Bank_Transaction_Line_Item_Tracking 
- Teljes.Contact_Group_Contacts 
- Complete.Contacts_Contact_ személyek 
- Teljes.Credit_Note_Line_Items 
- Teljes.Credit_Notes_Line_Items_Tracking 
- Teljes.Expense_Claim_ kifizetések 
- Befejezés.Expense_Claim_Receipts 
- Teljes.Invoice_Line_Items 
- Befejezés.Invoices_Line_Items_Tracking
- Teljes.Manual_Journal_Lines 
- Teljes.Manual_Journal_Line_Tracking 
- Teljes,Overpayment_Line_Items 
- Teljes.Overpayment_Line_Items_Tracking 
- Teljes.Prepayment_Line_Items 
- Teljes.Prepayment_Line_Item_Tracking 
- Teljes.Receipt_Line_Items 
- Befejezés.Receipt_Line_Item_Tracking 
- Teljes.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A másolási tevékenység által támogatott adattárak listáját a [támogatott adattárak című témakörben tetszés szerint.](copy-activity-overview.md#supported-data-stores-and-formats)
