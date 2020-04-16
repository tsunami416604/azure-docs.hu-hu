---
title: Adatok másolása a QuickBooks Online-ból az Azure Data Factory használatával (előzetes verzió)
description: Megtudhatja, hogyan másolhatja az adatokat a QuickBooks Online-ból a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: e2c9da9c1a37b087a31d1910094f51a39288c192
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416709"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Adatok másolása a QuickBooks Online-ból az Azure Data Factory használatával (előzetes verzió)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység et az Azure Data Factory adatok másolásához a QuickBooks Online-ból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést adhat nekünk. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a QuickBooks-összekötő a következő tevékenységek esetén támogatott:

- [Tevékenység másolása](copy-activity-overview.md) [támogatott forrás/fogadó mátrixcal](copy-activity-overview.md)
- [Keress tevékenységet](control-flow-lookup-activity.md)

A QuickBooks Online-ból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Az Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie egyetlen illesztőprogramot is ezzel az összekötővel.

Jelenleg ez az összekötő csak az 1.0a-t támogatja, ami azt jelenti, hogy fejlesztői fiókkal kell rendelkeznie a 2017.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a QuickBooks-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A QuickBooks csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A típustulajdonságot a következőre kell állítani: **QuickBooks** | Igen |
| endpoint | A QuickBooks Online kiszolgáló végpontja. (azaz quickbooks.api.intuit.com)  | Igen |
| companyId | A QuickBooks cég által engedélyezett cég azonosítója. A cégazonosító megkereséséről a Hogyan [találhatom meg a cégazonosítómat.](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551) | Igen |
| consumerKey (kulcs) | Az OAuth 1.0-s hitelesítés fogyasztói kulcsa. | Igen |
| consumerSecret | Az OAuth 1.0-s hitelesítés fogyasztói titka. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| accessToken | Az OAuth 1.0-s hitelesítés hozzáférési jogkivonata. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| accessTokenSecret | Az OAuth 1.0-s hitelesítés hozzáférési jogkivonatának titkos kulcsa. Jelölje meg ezt a mezőt SecureStringként a Data Factory biztonságos tárolásához, vagy [hivatkozzon az Azure Key Vaultban tárolt titkos fájlokra.](store-credentials-in-key-vault.md) | Igen |
| useEncryptedEndpoints | Itt adható meg, hogy az adatforrás végpontjai HTTPS protokoll használatával titkosítva legyenek-e. Az alapértelmezett érték az igaz.  | Nem |

**Példa:**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "endpoint" : "quickbooks.api.intuit.com",
            "companyId" : "<companyId>",
            "consumerKey": "<consumerKey>",
            "consumerSecret": {
                "type": "SecureString",
                "value": "<consumerSecret>"
            },
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            },
            "accessTokenSecret": {
                 "type": "SecureString",
                 "value": "<accessTokenSecret>"
            },
            "useEncryptedEndpoints" : true
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az [adatkészletekről](concepts-datasets-linked-services.md) szóló cikkben olvashatja. Ez a szakasz a QuickBooks adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a QuickBooks Online szolgáltatásból, állítsa az adatkészlet típustulajdonságát **QuickBooksObject**objektumra. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságát a következőre kell állítani: **QuickBooksObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" a tevékenységforrásban meg van adva) |

**Példa**

```json
{
    "name": "QuickBooksDataset",
    "properties": {
        "type": "QuickBooksObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<QuickBooks linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a QuickBooks forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="quickbooks-as-source"></a>QuickBooks forrásként

Ha adatokat szeretne másolni a QuickBooks Online szolgáltatásból, állítsa a másolási tevékenység forrástípusát a **QuickBooksSource**programba. A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **QuickBooksSource** | Igen |
| lekérdezés | Az adatok olvasásához használja az egyéni SQL-lekérdezést. Például: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Nem (ha az adatkészletben a "tableName" van megadva) |

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromQuickBooks",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<QuickBooks input dataset name>",
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
                "type": "QuickBooksSource",
                "query": "SELECT * FROM \"Bill\" WHERE Id = '123' "
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="copy-data-from-quickbooks-desktop"></a>Adatok másolása a Quickbooks Desktop programból

Az Azure Data Factory másolási tevékenysége nem tudja közvetlenül a Quickbooks Desktop ból adatokat másolni. Adatok másolásához a Quickbooks Desktopból exportálja a Quickbook-adatokat egy vesszővel tagolt értékű (CSV) fájlba, majd töltse fel a fájlt az Azure Blob Storage-ba. Innen a Data Factory segítségével másolhatja az adatokat a kiválasztott fogadóba.

## <a name="lookup-activity-properties"></a>A keresgaszíntevékenység tulajdonságai

A tulajdonságokrészleteinek megismeréséhez ellenőrizze a [Kereskövetési tevékenységet.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
