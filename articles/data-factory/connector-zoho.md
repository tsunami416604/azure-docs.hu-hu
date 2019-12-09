---
title: Adatok másolása a Zoho használatával Azure Data Factory (előzetes verzió)
description: Megtudhatja, hogyan másolhat adatokból a Zoho-ból egy Azure Data Factory-folyamat másolási tevékenységének használatával támogatott fogadó adattárakat.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 4a885f183dcec49ac857777ae552e97060c656f4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930871"
---
# <a name="copy-data-from-zoho-using-azure-data-factory-preview"></a>Adatok másolása a Zoho használatával Azure Data Factory (előzetes verzió)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Zoho-ból való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést küldhet nekünk. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Zoho-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)


Az adatok a Zoho-ból bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="getting-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a Zoho-összekötőhöz tartozó Data Factory-entitások definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Zoho társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **Zoho** | Igen |
| endpoint | A Zoho-kiszolgáló végpontja (`crm.zoho.com/crm/private`). | Igen |
| accessToken | A Zoho-hitelesítés hozzáférési jogkivonata. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| useEncryptedEndpoints | Meghatározza, hogy az adatforrás-végpontok HTTPS protokollal legyenek titkosítva. Az alapértelmezett érték az igaz.  | Nem |
| useHostVerification | Azt adja meg, hogy szükséges-e az állomásnév a kiszolgáló tanúsítványában, hogy egyezzen a kiszolgáló állomásneve, amikor SSL-kapcsolaton keresztül csatlakozik. Az alapértelmezett érték az igaz.  | Nem |
| usePeerVerification | Meghatározza, hogy az SSL protokollon keresztüli kapcsolódáskor ellenőrizni kell-e a kiszolgáló identitását. Az alapértelmezett érték az igaz.  | Nem |

**Példa**

```json
{
    "name": "ZohoLinkedService",
    "properties": {
        "type": "Zoho",
        "typeProperties": {
            "endpoint" : "crm.zoho.com/crm/private",
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Zoho-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Zoho-ból, állítsa az adatkészlet Type (típus) tulajdonságát **ZohoObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **ZohoObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "ZohoDataset",
    "properties": {
        "type": "ZohoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Zoho linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Zoho-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="zoho-as-source"></a>Zoho forrásként

Az adatok Zoho-ból való másolásához állítsa a forrás típusát a másolási tevékenység **ZohoSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **ZohoSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM Accounts"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromZoho",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Zoho input dataset name>",
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
                "type": "ZohoSource",
                "query": "SELECT * FROM Accounts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
