---
title: Adatok másolása a MongoDB
description: Megtudhatja, hogyan másolhat adatmásolási tevékenységet a Mongo DB-ből a Azure Data Factory-folyamat másolási tevékenységének használatával.
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
ms.openlocfilehash: a7bb74c09b45429a160a3ec481c23073575cfe3c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251712"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Adatok másolása a MongoDB a Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok MongoDB-adatbázisból történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

>[!IMPORTANT]
>Az ADF a MongoDB Connector új verzióját adja ki, amely jobb natív MongoDB-támogatást biztosít. Ha a korábbi MongoDB-összekötőt használja a megoldásban, amely támogatja a visszamenőleges kompatibilitást, tekintse meg a [MongoDB-összekötő (örökölt)](connector-mongodb-legacy.md) című cikket.

## <a name="supported-capabilities"></a>Támogatott képességek

A MongoDB-adatbázisból bármilyen támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Pontosabban, ez a MongoDB **-összekötő a 3,4-ig támogatja a verziókat**.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az MongoDB-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A MongoDB társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot a következőre kell beállítani: **MongoDbV2** |Igen |
| connectionString |A MongoDB-kapcsolatok karakterláncának (például `mongodb://[username:password@]host[:port][/[database][?options]]`) meghatározása. További részletekért tekintse [meg a MongoDB Manual on kapcsolati sztringet](https://docs.mongodb.com/manual/reference/connection-string/) . <br/><br /> A Azure Key Vaultban jelszót is beállíthat, és lekérheti a `password` konfigurációját a kapcsolatok karakterláncán kívülre. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md) című témakört. |Igen |
| database | Az elérni kívánt adatbázis neve. | Igen |
| connectVia | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . További tudnivalók az [Előfeltételek](#prerequisites) szakaszban olvashatók. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa**

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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). A MongoDB adatkészlet a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **MongoDbV2Collection** | Igen |
| collectionName |A gyűjtemény neve a MongoDB adatbázisban. |Igen |

**Példa**

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

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a MongoDB forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-as-source"></a>MongoDB forrásként

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **MongoDbV2Source** | Igen |
| szűrő | Meghatározza a kiválasztási szűrőt a lekérdezési operátorok használatával. Ha egy gyűjteményben lévő összes dokumentumot vissza szeretné adni, hagyja ki ezt a paramétert, vagy adjon át üres dokumentumot ({}). | Nem |
| cursorMethods.project | Meghatározza a dokumentumokban a kivetítéshez visszaadni kívánt mezőket. Ha a megfelelő dokumentumokban lévő összes mezőt vissza szeretné adni, hagyja ki ezt a paramétert. | Nem |
| cursorMethods. sort | Meghatározza, hogy a lekérdezés milyen sorrendben adja vissza a megfelelő dokumentumokat. Tekintse meg a [kurzor. sort ()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nem |
| cursorMethods. limit | A kiszolgáló által visszaadott dokumentumok maximális számát adja meg. Lásd: [kurzor. limit ()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nem |
| cursorMethods. skip | Meghatározza a kihagyni kívánt dokumentumok számát, valamint a MongoDB az eredmények visszaadásának helyét. Lásd: [kurzor. skip ()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nem |
| batchSize | Meghatározza a MongoDB-példány válaszának egyes kötegében visszaadni kívánt dokumentumok számát. A legtöbb esetben a Batch méretének módosítása nem érinti a felhasználót vagy az alkalmazást. Cosmos DB korlátozza, hogy az egyes kötegek ne lépjék túl a 40MB méretét, ami a dokumentumok méretének batchSize összege, ezért csökkentse ezt az értéket, ha a dokumentum mérete nagy. | Nem<br/>(az alapértelmezett érték **100**) |

>[!TIP]
>Az ADF támogatja a BSON-dokumentumok **szigorú módban**történő felhasználását. Győződjön meg arról, hogy a szűrő lekérdezése a rendszerhéj mód helyett szigorú módban van. További Leírás a következő helyen található: [MongoDB Manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Példa**

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

## <a name="export-json-documents-as-is"></a>JSON-dokumentumok exportálása a következőképpen:

Ezt a MongoDB-összekötőt használhatja a JSON-dokumentumok exportálására egy MongoDB-gyűjteményből különböző file-alapú áruházakba vagy a Azure Cosmos DBba. Az ilyen sémák és sémák közötti másoláshoz ugorjon a másolási tevékenységben a "Structure" (más néven *séma*) szakaszra.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Ha adatokat szeretne másolni a MongoDB-ből a táblázatos fogadóba, tekintse meg a [séma-hozzárendelést](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
