---
title: Adatok másolása, illetve az Azure Cosmos DB (MongoDB API-t) a Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat a támogatott forrás adattárakból származó, vagy az Azure Cosmos DB (MongoDB API-t) támogatott fogadó-áruházak Data Factory használatával.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 2cffc99eade88d50e3de212b5680b640c14fa7a7
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812377"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-mongodb-api-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure Cosmos DB (MongoDB API-t) az Azure Data Factory használatával

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban másolhat adatokat, és az Azure Cosmos DB (MongoDB API-t). A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

>[!NOTE]
>Az összekötő podporují másolja az adatokat és a Cosmos DB MongoDB API. Az SQL API-t, tekintse meg a [Cosmos DB SQL API-összekötővel](connector-azure-cosmos-db.md). Más API-típusok most már nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok másolása az Azure Cosmos DB (MongoDB API-t) a bármely támogatott fogadó adattárba, vagy bármely támogatott forrásadattárból adatok másolása az Azure Cosmos DB (MongoDB API-t). Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Használhatja az Azure Cosmos DB (MongoDB API-t)-összekötő:

- Adatok másolása az Azure Cosmos DB és a [MongoDB API-val](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Írni az Azure Cosmos DB **beszúrása** vagy **upsert**.
- Importálás és exportálás JSON-dokumentumok,-, vagy másolja az adatokat, vagy egy táblázatos adatkészlethez. Ilyenek például egy SQL-adatbázis és a egy CSV-fájlt. Másolja ki a dokumentumok-, illetve a JSON-fájlok vagy az vagy egy másik Azure Cosmos DB-gyűjtemények, lásd: [importálás vagy exportálás JSON-dokumentumok](#importexport-json-documents).

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével konkrétan az Azure Cosmos DB (MongoDB API-t) a Data Factory-entitások definiálása-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Cosmos DB (MongoDB API-t) a társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **CosmosDbMongoDbApi**. | Igen |
| kapcsolati Sztringje |Adja meg az Azure Cosmos DB MongoDB API kapcsolati karakterláncát. Azt az Azure Portal -> annak a Cosmos DB panel elsődleges vagy másodlagos kapcsolati karakterlánc, a minta -> `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| adatbázis | Az elérni kívánt adatbázis nevét. | Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Használhatja az Azure integrációs modul és a egy saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

**Példa**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
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

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). Azure Cosmos DB (MongoDB API-t) adatkészlet a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **CosmosDbMongoDbApiCollection**. |Igen |
| collectionName |Az Azure Cosmos DB-gyűjtemény neve. |Igen |

**Példa**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB MongoDB API linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a tulajdonságok, amelyek támogatják az Azure Cosmos DB (MongoDB API-t) forrásaként és fogadó listáját tartalmazza.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-mongodb-api-as-source"></a>Az Azure Cosmos DB (MongoDB API-t) forrásként

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrása tulajdonságát **CosmosDbMongoDbApiSource**. |Igen |
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
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB MongoDB API input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
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

### <a name="azure-cosmos-db-mongodb-api-as-sink"></a>Az Azure Cosmos DB (MongoDB API-val) pedig a fogadó

A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát **CosmosDbMongoDbApiSink**. |Igen |
| WriteBehavior |Ismerteti, hogyan lehet adatokat írni az Azure Cosmos DB-hez. Megengedett értékek: **beszúrása** és **upsert**.<br/><br/>Viselkedését **upsert** , hogy cserélje le a dokumentumot, ha egy dokumentum ugyanazzal az azonosítóval már létezik; egyéb esetben helyezze be a dokumentum.<br /><br />**Megjegyzés**: A Data Factory automatikusan létrehozza egy dokumentumot egy Azonosítót, ha nem ad meg Azonosítót vagy az eredeti dokumentum vagy oszlop-hozzárendelés. Ez azt jelenti, hogy gondoskodnia kell arról, hogy a **upsert** a várt módon működik, a dokumentum rendelkezik azonosítóval. |Nem<br />(az alapértelmezett érték **beszúrása**) |
| WriteBatchSize | A **writeBatchSize** tulajdonság írása az egyes kötegekben lévő dokumentumok méretét szabályozza. Próbálja meg az értékét növelje **writeBatchSize** jobb teljesítmény és az érték csökkentésével, ha a dokumentumok méretétől, nagy folyamatban. |Nem<br />(az alapértelmezett érték **5**) |
| writeBatchTimeout | A várakozási idő a köteg beszúrási művelet befejezését, mielőtt azt az időkorlátot. Engedélyezett értéke időtartam. | Nem<br/>(az alapértelmezett érték **00:30:00** – 30 perc) |

**Példa**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Importálása JSON-dokumentumok,-van, tekintse meg [importálása és exportálása a JSON-dokumentumok](#import-or-export-json-documents) szakasz; táblázatos alakú adatokat másol, tekintse meg [séma-hozzárendelés](#schema-mapping).

## <a name="import-or-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Az Azure Cosmos DB-összekötő egyszerűen használható:

* JSON-dokumentumok importálása különböző forrásokból származó Azure Cosmos DB, beleértve az Azure Blob storage, Azure Data Lake Store és egyéb fájlalapú tárolók, amely az Azure Data Factory támogatja.
* JSON-dokumentumok exportálhat egy Azure Cosmos DB-gyűjtemények különböző fájlalapú tárolók.
* Másolja a között két Azure Cosmos DB-gyűjtemények, dokumentumok-van.

Az ilyen sémafüggetlen másolási eléréséhez hagyja ki a "struktúra" (más néven *séma*) szakaszban az adatkészlet és a séma hozzárendelése a másolási tevékenység.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Adatokat másol a Cosmos DB MongoDB API-t a fogadó táblázatos, vagy tekintse meg a fordított irányú, [séma-hozzárendelés](copy-activity-schema-and-type-mapping.md#schema-mapping).

Kifejezetten a összeállításakor az Cosmos DB-be, hogy a forrás adatokból, töltse ki a megfelelő Objektumazonosítóval rendelkező Cosmos DB például már rendelkezik egy "id" oszlop, SQL-adatbázistáblában és a MongoDB-ben a dokumentumazonosító értéke, amely a beszúrási/upsert használt , be kell állítania a MongoDB szigorú mód definíció szerint a megfelelő séma-hozzárendelés (`_id.$oid`) tegye a következőket:

![Képezze le a MongoDB-fogadó azonosítója](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Másolat után a tevékenység-végrehajtási alábbi BSON ObjectId fogadó létrehozása:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
