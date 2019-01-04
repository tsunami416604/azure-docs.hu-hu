---
title: Adatok másolása az Azure Data Factory használatával MongoDB |} A Microsoft Docs
description: Megtudhatja, hogyan másolhat adatokat a Mongo DB támogatott fogadó adattárakba az Azure Data Factory-folyamatot egy másolási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 1e0d6fe79d23f3d3d0679445f5120a3ec17ddd67
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013774"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Adatok másolása az Azure Data Factory használatával MongoDB

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység adatokat másol egy MongoDB-adatbázis használata. Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

>[!IMPORTANT]
>Az ADF kiadását az új hatékonyabb natív MongoDB-támogatást nyújt a MongoDB-összekötővel. Ha az előző MongoDB-összekötő használata a megoldás, amely is támogatott, a-van a visszamenőleges kompatibilitás érdekében tekintse meg [MongoDB-összekötővel (örökölt)](connector-mongodb-legacy.md) cikk.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok bármely támogatott fogadó adattárba másolhatja a MongoDB-adatbázist. A másolási tevékenység által, források és fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban, a MongoDB-összekötővel támogat **legfeljebb 3.4-es verziók**.

## <a name="prerequisites"></a>Előfeltételek

Adatokat másol egy MongoDB-adatbázis, amely nem érhető el nyilvánosan, akkor be kell állítania egy helyi Integration Runtime. Lásd: [helyi Integration Runtime](create-self-hosted-integration-runtime.md) további részleteket a cikkben.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok a tulajdonságok, amelyek meghatározzák az adott Data Factory-entitások MongoDB-összekötővel kapcsolatos adatokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Mongodb-hez társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type |A type tulajdonságot kell beállítani: **MongoDbV2** |Igen |
| kapcsolati Sztringje |Adja meg például a MongoDB kapcsolati sztring `mongodb://[username:password@]host[:port][/[database][?options]]`. Tekintse meg [kapcsolati karakterláncot a MongoDB manuális](https://docs.mongodb.com/manual/reference/connection-string/) további részletekért. <br/><br />Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| adatbázis | Az elérni kívánt adatbázis nevét. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. (Ha az adattár nyilvánosan hozzáférhető) használhatja a helyi Integration Runtime vagy az Azure integrációs modul. Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
            },
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

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). MongoDB-adatkészlet a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **MongoDbV2Collection** | Igen |
| collectionName |MongoDB-adatbázisban szereplő gyűjtemény neve. |Igen |

**Példa**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz a MongoDB-forráshoz által támogatott tulajdonságok listáját tartalmazza.

### <a name="mongodb-as-source"></a>Mongodb-hez mint forrás

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **MongoDbV2Source** | Igen |
| szűrő | Meghatározza a kijelölési szűrő lekérdezés operátorok használata. A gyűjteményben található dokumentumokat adja vissza, hagyja ki ezt a paramétert, vagy adja át egy üres dokumentumot ({}). | Nem |
| cursorMethods.project | Itt adhatja meg a dokumentumokat a leképezés a mezők. Minden mező az egyező dokumentumok visszaadandó, hagyja ki ezt a paramétert. | Nem |
| cursorMethods.sort | Meghatározza azt a sorrendet, amelyben a lekérdezés visszaadja az egyező dokumentumok. Tekintse meg [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nem |
| cursorMethods.limit | Meghatározza a kiszolgáló visszaadja dokumentumok maximális számát. Tekintse meg [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nem | 
| cursorMethods.skip | Megadja a számát, hagyja ki a dokumentumok és, ahol a MongoDB eredmények kezdődik. Tekintse meg [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nem |
| batchSize | Megadja a választ kapott a MongoDB-példányban minden kötegben visszaadandó dokumentumok számát. A legtöbb esetben a köteg méretének módosítása nem érinti a felhasználó vagy az alkalmazás. A cosmos DB-korlátok minden egyes köteg nem lehet hosszabb 40MB-nál, amely egyezik meg a dokumentumokat mérete batchSize számát, így csökkenti az értéket, ha a dokumentumok méretétől, nagy folyamatban. | Nem<br/>(az alapértelmezett érték **100**) |

>[!TIP]
>A BSON a dokumentum felhasználásához ADF támogatási **szigorú mód**. Ellenőrizze, hogy a szűrő lekérdezés helyett rendszerhéj mód szigorú módban van. További leírás található [MongoDB manuális](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

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

## <a name="export-json-documents-as-is"></a>Exportálja a JSON-dokumentumok,-van

A MongoDB-összekötő segítségével exportálhatja a JSON-dokumentumok,-különböző fájlalapú tárolók vagy az Azure Cosmos DB MongoDB-gyűjtemény származik. Az ilyen sémafüggetlen másolási eléréséhez hagyja ki a "struktúra" (más néven *séma*) szakaszban az adatkészlet és a séma hozzárendelése a másolási tevékenység.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Adatok másolása a MongoDB táblázatos fogadóba, tekintse meg [séma-hozzárendelés](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
