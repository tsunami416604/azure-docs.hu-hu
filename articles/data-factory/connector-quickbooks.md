---
title: Adatok másolása a QuickBooks online-ból Azure Data Factory használatával (előzetes verzió)
description: Megtudhatja, hogyan másolhat adatokat a QuickBooks online-ból a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
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
ms.date: 08/03/2020
ms.openlocfilehash: e9c1651244eecb036ca18ad5dadfe23f48b2bce6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87529262"
---
# <a name="copy-data-from-quickbooks-online-using-azure-data-factory-preview"></a>Adatok másolása a QuickBooks online-ból Azure Data Factory használatával (előzetes verzió)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok QuickBooks online-ból való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést küldhet nekünk. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a QuickBooks-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a QuickBooks online-ból bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez az összekötő támogatja a QuickBooks OAuth 2,0 hitelesítést.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a QuickBooks-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A QuickBooks társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **QuickBooks** | Igen |
| connectionProperties | Olyan tulajdonságok csoportja, amelyek meghatározzák a QuickBooks-hez való kapcsolódás módját. | Igen |
| ***Alatt `connectionProperties` :*** | | |
| endpoint | A QuickBooks online-kiszolgáló végpontja. (quickbooks.api.intuit.com)  | Igen |
| companyId | Az engedélyezni kívánt QuickBooks-vállalat vállalati azonosítója. További információ a céges azonosító megkereséséről: [Hogyan céges azonosítójának megkeresése](https://quickbooks.intuit.com/community/Getting-Started/How-do-I-find-my-Company-ID/m-p/185551). | Igen |
| consumerKey | A OAuth 2,0-hitelesítés fogyasztói kulcsa. | Igen |
| consumerSecret | A OAuth 2,0-hitelesítéshez használt fogyasztói titok. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| refreshToken | A QuickBooks-alkalmazáshoz társított OAuth 2,0 frissítési jogkivonat. További információt [itt talál](https://developer.intuit.com/app/developer/qbo/docs/develop/authentication-and-authorization/oauth-2.0#obtain-oauth2-credentials-for-your-app). Megjegyzés: a frissítési token 180 nap után lejár. Az ügyfélnek rendszeresen frissítenie kell a frissítési tokent. <br/>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md).| Igen |
| useEncryptedEndpoints | Meghatározza, hogy az adatforrás-végpontok HTTPS protokollal legyenek titkosítva. Az alapértelmezett érték az igaz.  | Nem |

**Példa**

```json
{
    "name": "QuickBooksLinkedService",
    "properties": {
        "type": "QuickBooks",
        "typeProperties": {
            "connectionProperties": {
                "endpoint": "quickbooks.api.intuit.com",
                "companyId": "<company id>",
                "consumerKey": "<consumer key>", 
                "consumerSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "refreshToken": {
                     "type": "SecureString",
                     "value": "<refresh token>"
                },
                "useEncryptedEndpoints": true
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a QuickBooks-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok QuickBooks online-ból való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **QuickBooksObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **QuickBooksObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a QuickBooks-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="quickbooks-as-source"></a>QuickBooks forrásként

Az adatok QuickBooks online-ból való másolásához állítsa a forrás típusát a másolás tevékenység **QuickBooksSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **QuickBooksSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM "Bill" WHERE Id = '123'"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

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
## <a name="copy-data-from-quickbooks-desktop"></a>Adatok másolása a QuickBooks-asztalról

A Azure Data Factory másolási tevékenysége nem tud közvetlenül a QuickBooks Desktopból másolni az adatokból. Az adatok QuickBooks Desktopból való másolásához exportálja a QuickBooks-adatait egy vesszővel tagolt (CSV) fájlba, majd töltse fel a fájlt az Azure Blob Storageba. Innen a Data Factory segítségével másolhatók az adatok a választott fogadóba.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
