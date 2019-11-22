---
title: Adatok másolása Azure Cosmos DB API-MongoDB az Data Factory használatával
description: Ismerje meg, hogy miként másolhatók adatok a támogatott forrás-adattárakból vagy a Azure Cosmos DB API-MongoDB a támogatott fogadó üzletekbe Data Factory használatával.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 0da3452964a7c4bb7d2a22ce4cd5164ad8c1e3fb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280707"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Adatok másolása Azure Cosmos DB API-MongoDB az Azure Data Factory használatával

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure Cosmos DB API-MongoDB való másolásához. A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

>[!NOTE]
>Ez az összekötő csak a Azure Cosmos DB API-MongoDB való Adatmásolást támogatja. Az SQL API esetében tekintse meg az [Cosmos db SQL API-összekötőt](connector-azure-cosmos-db.md). Más API-típusok jelenleg nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

A Azure Cosmos DB API-MongoDB bármely támogatott fogadó adattárba másolhatja az adatait, vagy bármely támogatott forrás adattárból átmásolhatja az adatAzure Cosmos DB API-ját a MongoDB. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

A MongoDB-összekötő Azure Cosmos DB API-ját a következőre használhatja:

- Adatok másolása a és a rendszerből a [Azure Cosmos db API-MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Írni az Azure Cosmos DB **beszúrása** vagy **upsert**.
- Importálás és exportálás JSON-dokumentumok,-, vagy másolja az adatokat, vagy egy táblázatos adatkészlethez. Ilyenek például egy SQL-adatbázis és a egy CSV-fájlt. A dokumentumok fájlként való másolásához JSON-fájlokba vagy egy másik Azure Cosmos DB gyűjteményből vagy más-gyűjteményből: JSON-dokumentumok importálása vagy exportálása.

## <a name="get-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyeket a Azure Cosmos DB API-MongoDB tartozó entitások definiálásához használhat Data Factory.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A MongoDB társított szolgáltatáshoz tartozó Azure Cosmos DB API-k esetében a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **CosmosDbMongoDbApi**értékre kell beállítani. | Igen |
| connectionString |Adja meg a Azure Cosmos DB API-MongoDB tartozó kapcsolatok karakterláncát. A Azure Portal > a Cosmos DB > elsődleges vagy másodlagos kapcsolatok karakterláncát, `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`mintázatával. <br/><br />Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| database | Az elérni kívánt adatbázis neve. | Igen |
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

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). A MongoDB-adatkészlet Azure Cosmos DB API-jával a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **CosmosDbMongoDbApiCollection**értékre kell állítani. |Igen |
| collectionName |A Azure Cosmos DB gyűjtemény neve. |Igen |

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

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB API-ját a MongoDB forrás-és fogadó támogatásához.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB MongoDB API-ját forrásként

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **CosmosDbMongoDbApiSource**értékre kell állítani. |Igen |
| filter | Meghatározza a kiválasztási szűrőt a lekérdezési operátorok használatával. Ha egy gyűjteményben lévő összes dokumentumot vissza szeretné adni, hagyja ki ezt a paramétert, vagy adjon át üres dokumentumot ({}). | Nem |
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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB API a MongoDB as fogadóként

A következő tulajdonságok támogatottak a másolási tevékenység **fogadó** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának **Type** tulajdonságát **CosmosDbMongoDbApiSink**értékre kell állítani. |Igen |
| writeBehavior |Ismerteti, hogyan lehet adatokat írni az Azure Cosmos DB-hez. Megengedett értékek: **beszúrása** és **upsert**.<br/><br/>A **upsert** viselkedése a dokumentum cseréje, ha már létezik azonos `_id`ú dokumentum. Ha nem, szúrja be a dokumentumot.<br /><br />**Megjegyzés**: a Data Factory automatikusan létrehoz egy `_id` a dokumentumhoz, ha az eredeti dokumentumban vagy az oszlop-hozzárendelésben nincs megadva `_id`. Ez azt jelenti, hogy gondoskodnia kell arról, hogy a **upsert** a várt módon működik, a dokumentum rendelkezik azonosítóval. |Nem<br />(az alapértelmezett érték **beszúrása**) |
| writeBatchSize | A **writeBatchSize** tulajdonság az egyes kötegekben írandó dokumentumok méretét határozza meg. A **writeBatchSize** értékének növelésével növelheti a teljesítményt és csökkentheti az értéket, ha a dokumentum mérete nagy. |Nem<br />(az alapértelmezett érték **10 000**) |
| writeBatchTimeout | Az a várakozási idő, ameddig a kötegelt beszúrási művelet befejezi az időtúllépést. Az engedélyezett érték a TimeSpan. | Nem<br/>(az alapértelmezett érték **00:30:00** – 30 perc) |

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
>Ha JSON-dokumentumokat szeretne importálni, tekintse meg a [JSON-dokumentumok importálása vagy exportálása](#import-or-export-json-documents) szakaszt; a táblázatos adatokból történő másoláshoz tekintse meg a [séma-hozzárendelést](#schema-mapping).

## <a name="import-or-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Az Azure Cosmos DB-összekötő egyszerűen használható:

* JSON-dokumentumok importálása különböző forrásokból származó Azure Cosmos DB, beleértve az Azure Blob storage, Azure Data Lake Store és egyéb fájlalapú tárolók, amely az Azure Data Factory támogatja.
* JSON-dokumentumok exportálhat egy Azure Cosmos DB-gyűjtemények különböző fájlalapú tárolók.
* Másolja a között két Azure Cosmos DB-gyűjtemények, dokumentumok-van.

Az ilyen sémák és sémák közötti másoláshoz ugorjon a másolási tevékenységben a "Structure" (más néven *séma*) szakaszra.

## <a name="schema-mapping"></a>Séma-hozzárendelés

Ha Azure Cosmos DB API-ból szeretne adatokat másolni táblázatos MongoDB vagy fordított értékre, tekintse meg a [séma-hozzárendelést](copy-activity-schema-and-type-mapping.md#schema-mapping).

Különösen a Cosmos DBba való íráshoz, hogy a forrásadatok közül a jobb oldali AZONOSÍTÓJÚ Cosmos DB töltse fel, például az SQL Database tábla "id" oszlopát, és azt szeretné használni, hogy a MongoDB a INSERT/upsert a megfelelő séma-hozzárendelést a következő módon kell beállítania: MongoDB Strict Mode Definition (`_id.$oid`).

![Térkép azonosítója a MongoDB-fogadóban](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

A másolási tevékenység végrehajtása után a BSON ObjectId a fogadóban jön létre:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Következő lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
