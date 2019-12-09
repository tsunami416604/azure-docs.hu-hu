---
title: Adatmásolási és-átalakítási Azure Cosmos DB (SQL API)
description: Megtudhatja, hogyan másolhat adatok az Azure Cosmos DB (SQL API) és azokból, és hogyan alakíthatja át a Azure Cosmos DB (SQL API) adatait Data Factory használatával.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/13/2019
ms.openlocfilehash: bf24c12e8f1e5b7ee5c529ebffa6c15dd8acbcfc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913407"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Cosmos DB (SQL API) adatainak másolása és átalakítása Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuális verzió](connector-azure-cosmos-db.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához a és a rendszerbe Azure Cosmos DB (SQL API), valamint az adatáramlás használata az adatok átalakításához a Azure Cosmos DBban (SQL API). A Azure Data Factoryről a [bevezető cikkben](introduction.md)olvashat bővebben.

>[!NOTE]
>Ez az összekötő csak Cosmos DB SQL API-t támogatja. A MongoDB API-hoz tekintse [meg a MONGODB API](connector-azure-cosmos-db-mongodb-api.md)-hoz készült összekötőjét a következő témakörben: Azure Cosmos db. Más API-típusok jelenleg nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Azure Cosmos DB (SQL API) összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Másolási tevékenység esetén ez a Azure Cosmos DB (SQL API) összekötő a következőket támogatja:

- Adatok másolása a és a rendszerből a Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction)-ba.
- Írás a Azure Cosmos DB **Insert** vagy **upsert**.
- JSON-dokumentumok importálása és exportálása, illetve adatok másolása táblázatos adatkészletbe vagy másolással. Ilyenek például az SQL Database és a CSV-fájlok. A dokumentumok fájlként való másolásához JSON-fájlokba vagy egy másik Azure Cosmos DB gyűjteményből vagy más-gyűjteményből: JSON-dokumentumok importálása vagy exportálása.

A Data Factory a [Azure Cosmos db tömeges végrehajtó függvénytárral](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) integrálódik, hogy a lehető legjobb teljesítményt nyújtsa a Azure Cosmos db írásakor.

> [!TIP]
> Az [adatáttelepítési videó](https://youtu.be/5-SRNiC_qOU) végigvezeti az adatok Azure Blob Storage-ból Azure Cosmos DBba való másolásának lépésein. A videó ismerteti a teljesítmény-hangolási megfontolásokat is, amelyek az adatfeldolgozást általában a Azure Cosmos DB.

## <a name="get-started"></a>Az első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel Azure Cosmos DB (SQL API) Data Factory entitásokat lehet meghatározni.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Cosmos DB (SQL API) társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **Type** tulajdonságot **CosmosDb**értékre kell beállítani. | Igen |
| connectionString |Itt adhatja meg, hogy milyen információkra van szükség a Azure Cosmos DB-adatbázishoz való kapcsolódáshoz.<br />**Megjegyzés**: az alábbi példákban látható módon meg kell adnia az adatbázis adatait a kapcsolatok karakterláncában. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault is elhelyezheti a fiók kulcsát, és lekérheti a `accountKey` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . Használhatja a Azure Integration Runtime vagy a saját üzemeltetésű integrációs modult (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, a rendszer az alapértelmezett Azure Integration Runtime használja. |Nem |

**Példa**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: fiók kulcsának tárolása Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md).

Azure Cosmos DB (SQL API) adatkészlet esetében a következő tulajdonságok támogatottak: 

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **CosmosDbSqlApiCollection**értékre kell állítani. |Igen |
| collectionName |A Azure Cosmos DB dokumentum-gyűjtemény neve. |Igen |

Ha a "DocumentDbCollection" típusú adatkészletet használja, akkor továbbra is támogatott, ha visszamenőlegesen kompatibilis a másolási és keresési tevékenységekkel, az adatfolyam nem támogatott. Azt javasoljuk, hogy használja az új modellt a jövőre.

**Példa**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Séma Data Factory szerint

A séma nélküli adattárakhoz (például Azure Cosmos DB) a másolási tevékenység a következő listában leírt módszerek egyikében vezeti be a sémát. Hacsak nem szeretne [JSON-dokumentumokat importálni vagy exportálni](#import-or-export-json-documents), az ajánlott eljárás az adatstruktúra meghatározása a **struktúra** szakaszban.

Data Factory tiszteletben tartja a tevékenységhez megadott leképezést. Ha egy sor nem tartalmaz értéket egy oszlophoz, a rendszer null értéket ad meg az oszlop értékéhez.

Ha nem ad meg leképezést, a Data Factory szolgáltatás a séma első sorát használja az adataiban. Ha az első sor nem tartalmazza a teljes sémát, néhány oszlop hiányzik a tevékenység műveletének eredményében.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB (SQL API) forrás és a fogadó támogat.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) forrásként

Az adatok Azure Cosmos DBból (SQL API) történő másolásához állítsa a **forrás** típusát a másolási tevékenység **DocumentDbCollectionSource**. 

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **CosmosDbSqlApiSource**értékre kell állítani. |Igen |
| lekérdezés |Az adatolvasás Azure Cosmos DB lekérdezésének megadásához.<br/><br/>Példa:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, a rendszer a következő SQL-utasítást hajtja végre: `select <columns defined in structure> from mycollection` |
| preferredRegions | Azoknak a régióknak az előnyben részesített listája, amelyekhez csatlakozni szeretne, amikor Cosmos DB retriving adatait. | Nem |
| pageSize | A lekérdezési eredményben szereplő dokumentumok száma oldalanként. Az alapértelmezett érték a "-1", ami azt jelenti, hogy a szolgáltatás oldalsó dinamikus oldalának mérete legfeljebb 1000. | Nem |

Ha "DocumentDbCollectionSource" típusú forrást használ, továbbra is támogatja a-t a visszafelé való kompatibilitás érdekében. Azt javasoljuk, hogy használja az új modellt, amely gazdagabb képességeket biztosít az adatok Cosmos DBból való másolásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) fogadóként

Az adatAzure Cosmos DB (SQL API) másolásához állítsa a fogadó **típust** a másolás tevékenység **DocumentDbCollectionSink**. 

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának **Type** tulajdonságát **CosmosDbSqlApiSink**értékre kell állítani. |Igen |
| writeBehavior |Ismerteti, hogyan lehet az Azure Cosmos DBba írni az adatbevitelt. Megengedett értékek: **Insert** és **upsert**.<br/><br/>A **upsert** viselkedése a dokumentum cseréje, ha már létezik ilyen azonosítójú dokumentum. Ha nem, szúrja be a dokumentumot.<br /><br />**Megjegyzés**: Data Factory automatikusan létrehoz egy azonosítót egy dokumentumhoz, ha nincs megadva azonosító az eredeti dokumentumban vagy oszlop-hozzárendeléssel. Ez azt jelenti, hogy meg kell győződnie arról, hogy a **upsert** a várt módon működnek, a dokumentum azonosítója. |Nem<br />(az alapértelmezett érték a **Beszúrás**) |
| writeBatchSize | Data Factory az [Azure Cosmos db tömeges végrehajtó függvénytárat](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) használja az adatAzure Cosmos DBba való íráshoz. A **writeBatchSize** tulajdonság az ADF által a könyvtárnak biztosított dokumentumok méretét határozza meg. A **writeBatchSize** értékének növelésével növelheti a teljesítményt, és csökkentheti az értéket, ha a dokumentum mérete nagyméretű – lásd alább látható tippeket. |Nem<br />(az alapértelmezett érték **10 000**) |
| disableMetricsCollection | Data Factory olyan mérőszámokat gyűjt, mint például a Cosmos DB RUs a másolási teljesítmény optimalizálása és a javaslatok érdekében. Ha ezt a viselkedést érinti, a kikapcsolásához válassza a `true` lehetőséget. | Nem (az alapértelmezett érték `false`) |

>[!TIP]
>Cosmos DB korlátozza az egyszeri kérelmek méretét 2 MB-ra. A képlet a kérelem mérete = egyetlen dokumentum mérete * írási köteg mérete. Ha a **"kérés mérete túl nagy."** hibaüzenet jelenik meg, **csökkentse a `writeBatchSize` értéket** a másolási fogadó konfigurációjában.

Ha "DocumentDbCollectionSink" típusú forrást használ, továbbra is támogatja a-t a visszafelé való kompatibilitás érdekében. Azt javasoljuk, hogy használja az új modellt, amely gazdagabb képességeket biztosít az adatok Cosmos DBból való másolásához.

**Példa**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

A forrás- [átalakítás](data-flow-source.md) és a fogadó [átalakítás](data-flow-sink.md) részleteinek megismerése a leképezési folyamatokban.

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="import-or-export-json-documents"></a>JSON-dokumentumok importálása vagy exportálása

Ezt a Azure Cosmos DB (SQL API) összekötőt egyszerűen elvégezheti:

* A különböző forrásokból származó JSON-dokumentumokat importálhatja Azure Cosmos DBba, beleértve az Azure Blob Storage-ból, Azure Data Lake Storeból és más, a Azure Data Factory által támogatott fájl-alapú tárolókat.
* JSON-dokumentumok exportálása Azure Cosmos DB gyűjteményből különböző file-alapú áruházakba.
* Dokumentumok másolása két Azure Cosmos DB gyűjtemény között.

Séma – agnosztikus másolás:

* Az Adatok másolása eszköz használatakor válassza az **Exportálás másként lehetőséget a JSON-fájlok vagy a Cosmos db-gyűjtemény** lehetőségre.
* Ha tevékenység-létrehozást használ, válassza a JSON formátum elemet a forrás vagy a fogadó megfelelő fájljával.

## <a name="next-steps"></a>Következő lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintheti meg. lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
