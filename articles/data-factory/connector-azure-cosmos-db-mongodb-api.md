---
title: Adatok másolása az Azure Cosmos DB MongoDB-hoz készült API-jából
description: Megtudhatja, hogy miként másolhat adatokat a támogatott forrásadat-tárolókból az Azure Cosmos DB MongoDB-hoz készült API-jából a Data Factory használatával támogatott tárolókba.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 9b23f46a418f2663531cc121f00b83d00d84e48d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415441"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Adatok másolása az Azure Cosmos DB API-jába és onnan máshová az Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használhatja az Azure Data Factory másolási tevékenységét, amellyel adatokat másolhat az MongoDB-hez készült Azure Cosmos DB API-ba és onnan ki. A cikk az [Azure Data Factory másolási tevékenységére](copy-activity-overview.md)épül, amely általános áttekintést nyújt a másolási tevékenységről.

>[!NOTE]
>Ez az összekötő csak az Azure Cosmos DB MongoDB-hoz készült API-jába/ az Azure Cosmos DB-ből történő másolási adatokat támogatja. Az SQL API-hoz tekintse meg a [Cosmos DB SQL API-összekötőt.](connector-azure-cosmos-db.md) Más API-típusok most már nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatokat másolhat az Azure Cosmos DB MongoDB-hoz készült API-jából bármely támogatott fogadóadattárba, vagy adatokat másolhat bármely támogatott forrásadattárból az Azure Cosmos DB MongoDB-hoz készült API-ba. A Másolási tevékenység által forrásként és fogadóként támogatott adattárak listáját a [Támogatott adattárak és -formátumok című témakörben tetszhet.](copy-activity-overview.md#supported-data-stores-and-formats)

Az Azure Cosmos DB MongoDB-összekötőhöz szükséges API-ját a következőkre használhatja:

- Adatok másolása az [Azure Cosmos DB MongoDB-hoz készült API-jából.](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)
- Írjon az Azure Cosmos **DB-nek beszúrásként** vagy **upsert**ként.
- JSON-dokumentumok importálása és exportálása adott állapotban, illetve adatok másolása táblázatos adatkészletből vagy táblázatos adatkészletbe. Ilyen például egy SQL-adatbázis és egy CSV-fájl. Dokumentumok másolása a JSON-fájlokba vagy onnan, illetve egy másik Azure Cosmos DB-gyűjteménybe, lásd: JSON-dokumentumok importálása vagy exportálása.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Az alábbi szakaszok az Azure Cosmos DB-api-jára jellemző Data Factory-entitások definiálásaként használható tulajdonságok részleteit ismertetik.

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai

Az alábbi tulajdonságok at az Azure Cosmos DB API-ja a MongoDB-hoz csatolt szolgáltatás:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típustulajdonságot** **CosmosDbMongoDbApi (Típustulajdonság) tulajdonságnak CosmosDbMongoDbApi**-ra kell állítania. | Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg az Azure Cosmos DB MongoDB-hoz való API-jának kapcsolati karakterláncát. Megtalálhatja azt az Azure Portalon -> a Cosmos DB panel -> `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`elsődleges vagy másodlagos kapcsolati karakterlánc, a minta. <br/><br />Az Azure Key Vaultban is elhelyezhet `password` egy jelszót, és kihúzhatja a konfigurációt a kapcsolati karakterláncból.További részleteket az Azure Key  [Vault áruházi hitelesítő adataiban](store-credentials-in-key-vault.md)talál.|Igen |
| adatbázis | Az elérni kívánt adatbázis neve. | Igen |
| connectVia | Az adattárhoz való csatlakozáshoz használt [integrációs futásidő.](concepts-integration-runtime.md) Használhatja az Azure-integrációs runtime vagy egy saját üzemeltetésű integrációs runtime (ha az adattár található egy magánhálózat). Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure-integrációs runtime lesz használva. |Nem |

**Példa**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
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

Az adatkészletek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját az Adatkészletek és csatolt szolgáltatások című [témakörben található.](concepts-datasets-linked-services.md) Az alábbi tulajdonságok at az Azure Cosmos DB MongoDB-adatkészlethez való API-ja támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **típustulajdonságát** **CosmosDbMongoDbApiCollection**tulajdonságra kell állítani. |Igen |
| Lekérdezés_neve |Az Azure Cosmos DB-gyűjtemény neve. |Igen |

**Példa**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Tevékenység tulajdonságainak másolása

Ez a szakasz az Azure Cosmos DB MongoDB-forrás- és -fogadótámogatáshoz nyújtott API-ja által biztosított tulajdonságok listáját tartalmazza.

A tevékenységek definiálására rendelkezésre álló szakaszok és tulajdonságok teljes listáját a [Folyamatok című témakörben található.](concepts-pipelines-activities.md)

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Az Azure Cosmos DB MongoDB API-ja forrásként

A következő tulajdonságokat támogatja a Másolási tevékenység **forrásszakasz:**

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenységforrás **típustulajdonságát** **CosmosDbMongoDbApiSource (CosmosDbMongoDbApiSource)** típusú tulajdonságra kell állítani. |Igen |
| filter | A kijelölési szűrőt lekérdezésoperátorokkal adja meg. A gyűjtemény összes dokumentumának visszaadására hagyja ki{}ezt a paramétert, vagy adja át az üres dokumentumot ( ). | Nem |
| kurzormódszerek.projekt | Megadja azokat a mezőket, amelyeket a dokumentumokban vetítésre ad vissza. Ha az egyező bizonylatok összes mezőjét vissza szeretné adni, hagyja ki ezt a paramétert. | Nem |
| kurzormetódusok.rendezés | Megadja, hogy a lekérdezés milyen sorrendben adja vissza az egyező dokumentumokat. Lásd: [kurzor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nem |
| kurzormódszerek.limit | Megadja a kiszolgáló által visszaadott dokumentumok maximális számát. Lásd: [kurzor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nem | 
| kurzormetódusok.skip | Megadja az átugrani a dokumentumok számát, és ahonnan a MongoDB elkezdi visszaadni az eredményeket. Lásd: [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nem |
| batchSize | Megadja a MongoDB-példány válaszának minden kötegében visszaadandó dokumentumok számát. A legtöbb esetben a kötegméret módosítása nincs hatással a felhasználóra vagy az alkalmazásra. Cosmos DB korlátok minden köteg nem haladhatja meg a 40 MB-os méretű, amely a kötegméret dokumentumok száma, így csökkentse ezt az értéket, ha a dokumentum mérete nagy. | Nem<br/>(az alapértelmezett **érték 100**) |

>[!TIP]
>Az ADF támogatja a BSON-dokumentum szigorú módban történő **fogyasztását.** Győződjön meg arról, hogy a szűrőlekérdezés Shell mód helyett Szigorú módban van. További leírás megtalálható [mongoDB kézikönyv](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Példa**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Az Azure Cosmos DB MongoDB-hoz való API-ja fogadóként

A következő tulajdonságokat támogatja a Másolási tevékenység **fogadó** ja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Másolási tevékenység fogadójának **típustulajdonságát** **CosmosDbMongoDbApiSink (CosmosDbMongoDbApiSink)** típusú tulajdonságra kell állítani. |Igen |
| writeBehavior (írási viselkedés) |Az Azure Cosmos DB-re való adatok írása. Megengedett értékek: **insert** és **upsert**.<br/><br/>A **upsert** viselkedése az, hogy cserélje ki `_id` a dokumentumot, ha a dokumentum azonos már létezik; ellenkező esetben szúrja be a dokumentumot.<br /><br />**Megjegyzés:** A Data Factory `_id` automatikusan létrehoz `_id` egy dokumentumot, ha nincs megadva sem az eredeti dokumentumban, sem oszlopleképezésben. Ez azt jelenti, hogy biztosítania kell, hogy a **upsert** a várt módon működjön, a dokumentum azonosítóval rendelkezik. |Nem<br />(az alapértelmezett beállítás **beszúrása**) |
| writeBatchSize | A **writeBatchSize** tulajdonság az egyes kötegekbe írandó dokumentumok méretét szabályozza. Megpróbálhatja növelni a **writeBatchSize** értékét a teljesítmény javítása és az érték csökkentése érdekében, ha a dokumentum mérete nagy. |Nem<br />(az alapértelmezett **érték 10 000**) |
| writeBatchTimeout | A kötegbehelyezési művelet befejezéséhez, mielőtt időtúljárna. Az engedélyezett érték az időtartomány. | Nem<br/>(az alapértelmezett érték **00:30:00** - 30 perc) |

>[!TIP]
>A JSON-dokumentumok importjának a következőképpen történő importálásához tekintse meg [a JSON-dokumentumok importálása vagy exportálása](#import-and-export-json-documents) című szakaszt; táblázatos alakú adatokból másoláshoz olvassa el a [Sémaleképezés című](#schema-mapping)területet.

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

## <a name="import-and-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Az Azure Cosmos DB-összekötő segítségével egyszerűen:

* Dokumentumok másolása két Azure Cosmos DB-gyűjtemény között, ahogy van.
* Importálja a különböző forrásokból származó JSON-dokumentumokat az Azure Cosmos DB-ba, többek között a MongoDB-ból, az Azure Blob storage-ból, az Azure Data Lake Store-ból és az Azure Data Factory által támogatott egyéb fájlalapú áruházakból.
* JSON-dokumentumokat exportálhat egy Azure Cosmos DB-gyűjteményből különböző fájlalapú tárolókba.

Séma-agnosztikus másolat elérése:

* Az Adatok másolása eszköz használatakor válassza az **Exportálás jsonfájlokba vagy a Cosmos DB gyűjteménybe lehetőséget.**
* Ha tevékenységszerzői tevékenységet használ, válassza a JSON formátumot a megfelelő fájltárolóval a forráshoz vagy a fogadóhoz.

## <a name="schema-mapping"></a>Sémaleképezés

Ha adatokat szeretne másolni az Azure Cosmos DB MongoDB-beli API-jából táblázatos fogadóba vagy fordított, olvassa el [a sémaleképezést.](copy-activity-schema-and-type-mapping.md#schema-mapping)

Kifejezetten a Cosmos DB-be való íráshoz, hogy biztosan feltöltse a Cosmos DB-t a forrásadatokból származó megfelelő objektumazonosítóval, például van egy "id" oszlop az SQL adatbázistáblában, és a dokumentum azonosítójaként a MongoDB-ban a beszúráshoz / upserthez a megfelelő sémaleképezést kell beállítania a MongoDB szigorú definíciója (`_id.$oid`) szerint a következőként:

![Térképazonosító a MongoDB mosogatóban](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

A másolási tevékenység végrehajtása után a BSON ObjectId alatt jön létre a fogadó:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>További lépések

Az Azure Data Factory ban a Másolási tevékenység által adatforrásként és fogadóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats)című témakörben tetszhet.
