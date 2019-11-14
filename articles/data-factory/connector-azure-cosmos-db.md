---
title: Azure Cosmos DB (SQL API) adatainak másolása és átalakítása Data Factory használatával
description: Megtudhatja, hogyan másolhat adatok az Azure Cosmos DB (SQL API) és azokból, és hogyan alakíthatja át a Azure Cosmos DB (SQL API) adatait Data Factory használatával.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 5e9db7c63e1493e1de5593262515040f071186e8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076797"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Cosmos DB (SQL API) adatainak másolása és átalakítása Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuális verzió](connector-azure-cosmos-db.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok másolásához a és a rendszerbe Azure Cosmos DB (SQL API), valamint az adatáramlás használata az adatok átalakításához a Azure Cosmos DBban (SQL API). Azure Data Factory szolgáltatásról, olvassa el a [bevezető cikk](introduction.md).

>[!NOTE]
>Ez az összekötő csak Cosmos DB SQL API-t támogatja. A MongoDB API-hoz tekintse [meg a MONGODB API](connector-azure-cosmos-db-mongodb-api.md)-hoz készült összekötőjét a következő témakörben: Azure Cosmos db. Más API-típusok jelenleg nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Azure Cosmos DB (SQL API) összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Másolási tevékenység esetén ez a Azure Cosmos DB (SQL API) összekötő a következőket támogatja:

- Adatok másolása az Azure Cosmos DB és a [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Írni az Azure Cosmos DB **beszúrása** vagy **upsert**.
- Importálás és exportálás JSON-dokumentumok,-, vagy másolja az adatokat, vagy egy táblázatos adatkészlethez. Ilyenek például egy SQL-adatbázis és a egy CSV-fájlt. A dokumentumok fájlként való másolásához JSON-fájlokba vagy egy másik Azure Cosmos DB gyűjteményből vagy más-gyűjteményből: JSON-dokumentumok importálása vagy exportálása.

A Data Factory integrálható a [Azure Cosmos DB tömeges végrehajtó könyvtár](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) a legjobb teljesítményt biztosítja az Azure Cosmos DB írásakor.

> [!TIP]
> A [adatáttelepítés videó](https://youtu.be/5-SRNiC_qOU) végigvezeti a lépéseken, az adatok másolása az Azure Blob storage-ból az Azure Cosmos DB. A videó bemutatja a tölt be adatot az Azure Cosmos DB az általános teljesítmény-finomhangolási szempontok is.

## <a name="get-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel Azure Cosmos DB (SQL API) Data Factory entitásokat lehet meghatározni.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Cosmos DB (SQL API) társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **CosmosDb**. | Igen |
| connectionString |Adja meg az Azure Cosmos DB-adatbázishoz való csatlakozáshoz szükséges információk.<br />**Megjegyzés:** : Adjon meg adatbázis-információ a kapcsolati karakterlánc az alábbi példákban szemléltetett módon. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault is elhelyezheti a fiók kulcsát, és lekérheti a `accountKey` konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Használhatja az Azure integrációs modul és a egy saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md).

Azure Cosmos DB (SQL API) adatkészlet esetében a következő tulajdonságok támogatottak: 

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **CosmosDbSqlApiCollection**értékre kell állítani. |Igen |
| collectionName |Neve az Azure Cosmos DB-dokumentumgyűjteményt. |Igen |

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

### <a name="schema-by-data-factory"></a>Adat-előállítók által séma

A sémamentes adatokra tárolókat, mint az Azure Cosmos DB a másolási tevékenység kikövetkezteti a használandó sémát az egyik módja az alábbi lista ismerteti. Hacsak nem szeretné [importálása és exportálása a JSON-dokumentumok,-van](#import-or-export-json-documents), az ajánlott eljárás, hogy az adatok struktúráját adja meg a **struktúra** szakaszban.

Data Factory tiszteletben tartja a tevékenységhez megadott leképezést. Ha egy sor nem tartalmaz egy oszlop értékét, az oszlop értékének null értékű biztosítunk.

Ha nem ad meg leképezést, a Data Factory szolgáltatás a séma első sorát használja az adataiban. Ha az első sor nem tartalmazza a teljes sémát, néhány oszlop hiányzik a tevékenység műveletének eredményében.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB (SQL API) forrás és a fogadó támogat.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) forrásként

Az adatok Azure Cosmos DBból (SQL API) történő másolásához állítsa a **forrás** típusát a másolási tevékenység **DocumentDbCollectionSource**. 

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **CosmosDbSqlApiSource**értékre kell állítani. |Igen |
| lekérdezés |Adja meg az Azure Cosmos DB lekérdezéssel adatokat olvasni.<br/><br/>Példa:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítás végrehajtása: `select <columns defined in structure> from mycollection` |
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

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának **Type** tulajdonságát **CosmosDbSqlApiSink**értékre kell állítani. |Igen |
| writeBehavior |Ismerteti, hogyan lehet adatokat írni az Azure Cosmos DB-hez. Megengedett értékek: **beszúrása** és **upsert**.<br/><br/>Viselkedését **upsert** , hogy cserélje le a dokumentumot, ha egy dokumentum ugyanazzal az azonosítóval már létezik; egyéb esetben helyezze be a dokumentum.<br /><br />**Megjegyzés:** : Data Factory automatikusan létrehozza a dokumentum Azonosítóját, ha nem ad meg Azonosítót vagy az eredeti dokumentum vagy oszlop-hozzárendelés. Ez azt jelenti, hogy gondoskodnia kell arról, hogy a **upsert** a várt módon működik, a dokumentum rendelkezik azonosítóval. |Nem<br />(az alapértelmezett érték **beszúrása**) |
| writeBatchSize | Adat-előállító használ a [Azure Cosmos DB tömeges végrehajtó könyvtár](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) adatokat írni az Azure Cosmos DB-hez. A **writeBatchSize** tulajdonság az ADF által a könyvtárnak biztosított dokumentumok méretét határozza meg. Próbálja meg az értékét növelje **writeBatchSize** jobb teljesítmény és az érték csökkentésével, ha a dokumentumot a rendszer nagy méretezés – lásd az alábbi tippeket. |Nem<br />(az alapértelmezett érték **10 000**) |
| disableMetricsCollection | Data Factory olyan mérőszámokat gyűjt, mint például a Cosmos DB RUs a másolási teljesítmény optimalizálása és a javaslatok érdekében. Ha ezt a viselkedést érinti, a kikapcsolásához válassza a `true` lehetőséget. | Nem (az alapértelmezett érték `false`) |

>[!TIP]
>A cosmos DB korlátozza az egyetlen kérés mérete 2MB. A képlet egy kérés mérete = egyetlen dokumentum mérete * kötegméret írása. Ha eléri hiba üzenettel **"Request mérete túl nagy."** , **csökkentheti a `writeBatchSize` érték** másolási fogadó konfigurációban.

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

## <a name="import-or-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Ezt a Azure Cosmos DB (SQL API) összekötőt egyszerűen elvégezheti:

* JSON-dokumentumok importálása különböző forrásokból származó Azure Cosmos DB, beleértve az Azure Blob storage, Azure Data Lake Store és egyéb fájlalapú tárolók, amely az Azure Data Factory támogatja.
* JSON-dokumentumok exportálhat egy Azure Cosmos DB-gyűjtemények különböző fájlalapú tárolók.
* Másolja a között két Azure Cosmos DB-gyűjtemények, dokumentumok-van.

Sémafüggetlen másolási eléréséhez:

* Amikor az adatok másolása eszközzel, válassza ki a **exportálni – JSON-fájlokat vagy a Cosmos DB-gyűjtemények** lehetőséget.
* Ha tevékenység-létrehozást használ, válassza a JSON formátum elemet a forrás vagy a fogadó megfelelő fájljával.

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
