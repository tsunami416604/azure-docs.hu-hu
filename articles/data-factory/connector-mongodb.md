---
title: Adatok másolása a MongoDB-ból
description: Megtudhatja, hogyan másolhatja az adatokat a Mongo DB-ből a támogatott fogadó adattárakba egy Azure Data Factory-folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: 3b6476b794d2e1b2e9a36aa26f35c247641d44e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418150"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Adatok másolása a MongoDB-ból az Azure Data Factory használatával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk ismerteti, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok másolása a MongoDB-adatbázisból. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

>[!IMPORTANT]
>ADF mentesít ez új változat -ból MongoDB összeköt melyik szolgáltat jobb bennszülött MongoDB támogat. Ha az előző MongoDB-összekötőt használja a megoldásban, amely a visszamenőleges kompatibilitás érdekében támogatott, olvassa el a [MongoDB-összekötő (örökölt)](connector-mongodb-legacy.md) cikket.

## <a name="supported-capabilities"></a>Támogatott képességek

A MongoDB adatbázisból adatokat másolhat bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként/fogadóként támogatott adattárak listáját a [Támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblában található.

Pontosabban, ez a MongoDB csatlakozó támogatja **a 3.4-ig tartó verziókat.**

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a MongoDB-összekötőre jellemző Data Factory-entitások definiálásához használt tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

A MongoDB csatolt szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type |A típustulajdonságot a következőre kell állítani: **MongoDbV2** |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg a MongoDB kapcsolati karakterláncot, pl. . `mongodb://[username:password@]host[:port][/[database][?options]]` További részletekért olvassa el [a MongoDB csatlakozási karakterláncra vonatkozó kézikönyvét.](https://docs.mongodb.com/manual/reference/connection-string/) <br/><br /> Az Azure Key Vaultban is elhelyezhet `password` egy jelszót, és kihúzhatja a konfigurációt a kapcsolati karakterláncból. További részleteket az [Azure Key Vault áruházi hitelesítő adataiban](store-credentials-in-key-vault.md) talál. |Igen |
| adatbázis | Az elérni kívánt adatbázis neve. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használandó [integrációs futásidő.](concepts-integration-runtime.md) További információ az [Előfeltételek](#prerequisites) szakaszból. Ha nincs megadva, az alapértelmezett Azure-integrációs runtime-ot használja. |Nem |

**Példa:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": "mongodb://[username:password@]host[:port][/[database][?options]]",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek és csatolt szolgáltatások című [témakörben található.](concepts-datasets-linked-services.md) A MongoDB adatkészlet a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet típustulajdonságának a következőre kell állítania: **MongoDbV2Collection** | Igen |
| Lekérdezés_neve |A gyűjtemény neve a MongoDB adatbázisban. |Igen |

**Példa:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok](concepts-pipelines-activities.md) című cikkben olvashat. Ez a szakasz a MongoDB-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-as-source"></a>MongoDB mint forrás

A másolási tevékenység **forrásszakaszában** a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának típustulajdonságát a következőre kell állítani: **MongoDbV2Source** | Igen |
| filter | A kijelölési szűrőt lekérdezésoperátorokkal adja meg. A gyűjtemény összes dokumentumának visszaadására hagyja ki{}ezt a paramétert, vagy adja át az üres dokumentumot ( ). | Nem |
| kurzormódszerek.projekt | Megadja azokat a mezőket, amelyeket a dokumentumokban vetítésre ad vissza. Ha az egyező bizonylatok összes mezőjét vissza szeretné adni, hagyja ki ezt a paramétert. | Nem |
| kurzormetódusok.rendezés | Megadja, hogy a lekérdezés milyen sorrendben adja vissza az egyező dokumentumokat. Lásd: [kurzor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nem |
| kurzormódszerek.limit | Megadja a kiszolgáló által visszaadott dokumentumok maximális számát. Lásd: [kurzor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nem |
| kurzormetódusok.skip | Megadja az átugrani a dokumentumok számát, és ahonnan a MongoDB elkezdi visszaadni az eredményeket. Lásd: [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nem |
| batchSize | Megadja a MongoDB-példány válaszának minden kötegében visszaadandó dokumentumok számát. A legtöbb esetben a kötegméret módosítása nincs hatással a felhasználóra vagy az alkalmazásra. Cosmos DB korlátok minden köteg nem haladhatja meg a 40 MB-os méretű, amely a kötegméret dokumentumok száma, így csökkentse ezt az értéket, ha a dokumentum mérete nagy. | Nem<br/>(az alapértelmezett **érték 100**) |

>[!TIP]
>Az ADF támogatja a BSON-dokumentum szigorú módban történő **fogyasztását.** Győződjön meg arról, hogy a szűrőlekérdezés Shell mód helyett Szigorú módban van. További leírás megtalálható [mongoDB kézikönyv](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Példa:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>JSON-dokumentumok exportálása

Ezzel a MongoDB-összekötővel exportálhatja a JSON-dokumentumokat a MongoDB-gyűjteményből különböző fájlalapú tárolókba vagy az Azure Cosmos DB-be. Az ilyen séma-független másolás eléréséhez hagyja ki az adatkészlet és a séma leképezésének "struktúra" (más néven *séma)* szakaszát a másolási tevékenységben.

## <a name="schema-mapping"></a>Sémaleképezés

Ha adatokat szeretne másolni a MongoDB-ból a táblázatos fogadóba, olvassa el a [sémaleképezést.](copy-activity-schema-and-type-mapping.md#schema-mapping)

## <a name="next-steps"></a>További lépések
A forrásként támogatott és fogadóként az Azure Data Factory másolási tevékenysége által támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
