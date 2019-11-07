---
title: Adatok másolása Azure Cosmos DB (SQL API) vagy rendszerből a Data Factory használatával
description: Ismerje meg, hogy miként másolhatók adatok a támogatott forrás-adattárakból vagy Azure Cosmos DB (SQL API) rendszerről a Data Factory használatával támogatott fogadó tárolók számára.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 74bec61cbb69b17afddeb5abc1df16d8c1140234
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681306"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Adatok másolása Azure Cosmos DB (SQL API) vagy rendszerből a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuális verzió](connector-azure-cosmos-db.md)

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban adatok másolásához és átAzure Cosmos DBához (SQL API). A cikk a [másolási tevékenységre épül Azure Data Factoryban](copy-activity-overview.md), amely a másolási tevékenység általános áttekintését mutatja be.

>[!NOTE]
>Ez az összekötő csak az adatok másolását támogatja Cosmos DB SQL API-ba. A MongoDB API-hoz tekintse [meg a MONGODB API](connector-azure-cosmos-db-mongodb-api.md)-hoz készült összekötőjét a következő témakörben: Azure Cosmos db. Más API-típusok jelenleg nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Azure Cosmos DB (SQL API) összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Azure Cosmos DB (SQL API) adatait átmásolhatja bármely támogatott fogadó adattárba, vagy bármely támogatott forrásból származó adattárolóból átmásolhatja az adatok Azure Cosmos DB (SQL API) szolgáltatásba. A másolási tevékenység által a forrásként és a fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak és-formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

A következőhöz használhatja a Azure Cosmos DB (SQL API) összekötőt:

- Adatok másolása a és a rendszerből a Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction)-ba.
- Írás a Azure Cosmos DB **Insert** vagy **upsert**.
- JSON-dokumentumok importálása és exportálása, illetve adatok másolása táblázatos adatkészletbe vagy másolással. Ilyenek például az SQL Database és a CSV-fájlok. A dokumentumok fájlként való másolásához JSON-fájlokba vagy egy másik Azure Cosmos DB gyűjteményből vagy más-gyűjteményből: JSON-dokumentumok importálása vagy exportálása.

A Data Factory a [Azure Cosmos db tömeges végrehajtó függvénytárral](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) integrálódik, hogy a lehető legjobb teljesítményt nyújtsa a Azure Cosmos db írásakor.

> [!TIP]
> Az [adatáttelepítési videó](https://youtu.be/5-SRNiC_qOU) végigvezeti az adatok Azure Blob Storage-ból Azure Cosmos DBba való másolásának lépésein. A videó ismerteti a teljesítmény-hangolási megfontolásokat is, amelyek az adatfeldolgozást általában a Azure Cosmos DB.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel Azure Cosmos DB (SQL API) Data Factory entitásokat lehet meghatározni.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Cosmos DB (SQL API) társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
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

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB (SQL API) adatkészlet támogat. 

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatások](concepts-datasets-linked-services.md). 

Ha adatokat szeretne másolni a vagy a rendszerből a Azure Cosmos DBba (SQL API), állítsa az adatkészlet **Type (típus** ) tulajdonságát **DocumentDbCollection**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet **Type** tulajdonságát **DocumentDbCollection**értékre kell állítani. |Igen |
| collectionName |A Azure Cosmos DB dokumentum-gyűjtemény neve. |Igen |

**Példa**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Séma Data Factory szerint

A séma nélküli adattárakhoz (például Azure Cosmos DB) a másolási tevékenység a következő listában leírt módszerek egyikében vezeti be a sémát. Hacsak nem szeretne [JSON-dokumentumokat importálni vagy exportálni](#import-or-export-json-documents), az ajánlott eljárás az adatstruktúra meghatározása a **struktúra** szakaszban.

* Ha az adatszerkezetet az adatkészlet definíciójának **struktúra** tulajdonságának használatával határozza meg, Data Factory a struktúrát a séma szerint értékeli. 

    Ha egy sor nem tartalmaz értéket egy oszlophoz, a rendszer null értéket ad meg az oszlop értékéhez.
* Ha nem határozza meg az adatszerkezetet az adatkészlet definíciójának **struktúra** tulajdonságával, akkor a Data Factory szolgáltatás az adat első során kikövetkezteti a sémát. 

    Ha az első sor nem tartalmazza a teljes sémát, néhány oszlop hiányzik a másolási művelet eredményében.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB (SQL API) forrás és a fogadó támogat.

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) forrásként

Az adatok Azure Cosmos DBból (SQL API) történő másolásához állítsa a **forrás** típusát a másolási tevékenység **DocumentDbCollectionSource**. 

A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának **Type** tulajdonságát **DocumentDbCollectionSource**értékre kell állítani. |Igen |
| lekérdezés |Az adatolvasás Azure Cosmos DB lekérdezésének megadásához.<br/><br/>Példa:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, a rendszer a következő SQL-utasítást hajtja végre: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Egy speciális karakter, amely azt jelzi, hogy a dokumentum beágyazva van, és az eredményhalmaz lelapulása.<br/><br/>Ha például egy Azure Cosmos DB lekérdezés a beágyazott eredményt adja vissza `"Name": {"First": "John"}`, a másolási tevékenység az oszlop nevét `Name.First`ként azonosítja, a "János" értékkel, ha a **nestedSeparator** értéke **.** (pont). |Nem<br />(az alapértelmezett érték a **.** (pont)) |

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
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
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

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadójának **Type** tulajdonságát **DocumentDbCollectionSink**értékre kell állítani. |Igen |
| WriteBehavior |Ismerteti, hogyan lehet az Azure Cosmos DBba írni az adatbevitelt. Megengedett értékek: **Insert** és **upsert**.<br/><br/>A **upsert** viselkedése a dokumentum cseréje, ha már létezik ilyen azonosítójú dokumentum. Ha nem, szúrja be a dokumentumot.<br /><br />**Megjegyzés**: Data Factory automatikusan létrehoz egy azonosítót egy dokumentumhoz, ha nincs megadva azonosító az eredeti dokumentumban vagy oszlop-hozzárendeléssel. Ez azt jelenti, hogy meg kell győződnie arról, hogy a **upsert** a várt módon működnek, a dokumentum azonosítója. |Nem<br />(az alapértelmezett érték a **Beszúrás**) |
| writeBatchSize | Data Factory az [Azure Cosmos db tömeges végrehajtó függvénytárat](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) használja az adatAzure Cosmos DBba való íráshoz. A **writeBatchSize** tulajdonság az ADF által a könyvtárnak biztosított dokumentumok méretét határozza meg. A **writeBatchSize** értékének növelésével növelheti a teljesítményt, és csökkentheti az értéket, ha a dokumentum mérete nagyméretű – lásd alább látható tippeket. |Nem<br />(az alapértelmezett érték **10 000**) |
| nestingSeparator |Egy speciális karakter a **forrás** oszlop nevében, amely azt jelzi, hogy egy beágyazott dokumentumra van szükség. <br/><br/>A kimeneti adatkészlet struktúrájának `Name.First` például a következő JSON-struktúrát hozza létre a Azure Cosmos DB dokumentumban, amikor a nestedSeparator **.** (pont): `"Name": {"First": "[value maps to this column from source]"}`  |Nem<br />(az alapértelmezett érték a **.** (pont)) |
| disableMetricsCollection | Data Factory olyan mérőszámokat gyűjt, mint például a Cosmos DB RUs a másolási teljesítmény optimalizálása és a javaslatok érdekében. Ha ezt a viselkedést érinti, a kikapcsolásához válassza a `true` lehetőséget. | Nem (az alapértelmezett érték `false`) |

>[!TIP]
>Cosmos DB korlátozza az egyszeri kérelmek méretét 2 MB-ra. A képlet a kérelem mérete = egyetlen dokumentum mérete * írási köteg mérete. Ha a **"kérés mérete túl nagy."** hibaüzenet jelenik meg, **csökkentse a `writeBatchSize` értéket** a másolási fogadó konfigurációjában.

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
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```
## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).

## <a name="import-or-export-json-documents"></a>JSON-dokumentumok importálása vagy exportálása

Ezt a Azure Cosmos DB (SQL API) összekötőt egyszerűen elvégezheti:

* A különböző forrásokból származó JSON-dokumentumokat importálhatja Azure Cosmos DBba, beleértve az Azure Blob Storage-ból, Azure Data Lake Storeból és más, a Azure Data Factory által támogatott fájl-alapú tárolókat.
* JSON-dokumentumok exportálása Azure Cosmos DB gyűjteményből különböző file-alapú áruházakba.
* Dokumentumok másolása két Azure Cosmos DB gyűjtemény között.

Séma – agnosztikus másolás:

* Az Adatok másolása eszköz használatakor válassza az **Exportálás másként lehetőséget a JSON-fájlok vagy a Cosmos db-gyűjtemény** lehetőségre.
* Ha tevékenység-létrehozást használ, ne határozza meg a **struktúra** (más néven *séma*) szakaszt a Azure Cosmos db adatkészletben. A másolási tevékenységben ne határozza meg a **nestingSeparator** tulajdonságot a Azure Cosmos db forrásában vagy a fogadóban. Ha JSON-fájlokból importál vagy exportál, a megfelelő file Store-adatkészletben adja meg a **Formátum** típusát **JsonFormat** , és konfigurálja a **filePattern** a JSON- [Formátum](supported-file-formats-and-compression-codecs.md#json-format) szakaszban leírtak szerint. Ezután ne adja meg a **struktúra** szakaszt, és hagyja ki a többi formátum beállításait.

## <a name="next-steps"></a>További lépések

A másolási tevékenység által támogatott adattárak listáját a Azure Data Factoryban található forrásként és nyelőként tekintheti meg. lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
