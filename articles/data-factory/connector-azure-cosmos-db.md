---
title: Adatok másolása, illetve az Azure Cosmos DB (az SQL API-t) a Data Factory használatával |} A Microsoft Docs
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
ms.openlocfilehash: 561f383327738c9a2ab29f2907f00ace1eec6def
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010273"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure Cosmos DB (az SQL API-t) az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuális verzió](connector-azure-cosmos-db.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban másolhat adatokat, és az Azure Cosmos DB (az SQL API-t). A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

>[!NOTE]
>Az összekötő podporují másolja az adatokat és a Cosmos DB SQL API. A MongoDB API-hoz tekintse [meg a MONGODB API](connector-azure-cosmos-db-mongodb-api.md)-hoz készült összekötőjét a következő témakörben: Azure Cosmos db. Más API-típusok jelenleg nem támogatottak.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Azure Cosmos DB (SQL API) összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

A Azure Cosmos DB (SQL API) adatait átmásolhatja bármely támogatott fogadó adattárba, vagy bármely támogatott forrásból származó adattárolóból átmásolhatja az adatok Azure Cosmos DB (SQL API) szolgáltatásba. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

A következőhöz használhatja a Azure Cosmos DB (SQL API) összekötőt:

- Adatok másolása az Azure Cosmos DB és a [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Írni az Azure Cosmos DB **beszúrása** vagy **upsert**.
- Importálás és exportálás JSON-dokumentumok,-, vagy másolja az adatokat, vagy egy táblázatos adatkészlethez. Ilyenek például egy SQL-adatbázis és a egy CSV-fájlt. A dokumentumok fájlként való másolásához JSON-fájlokba vagy egy másik Azure Cosmos DB gyűjteményből vagy más-gyűjteményből: JSON-dokumentumok importálása vagy exportálása.

A Data Factory integrálható a [Azure Cosmos DB tömeges végrehajtó könyvtár](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) a legjobb teljesítményt biztosítja az Azure Cosmos DB írásakor.

> [!TIP]
> A [adatáttelepítés videó](https://youtu.be/5-SRNiC_qOU) végigvezeti a lépéseken, az adatok másolása az Azure Blob storage-ból az Azure Cosmos DB. A videó bemutatja a tölt be adatot az Azure Cosmos DB az általános teljesítmény-finomhangolási szempontok is.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyekkel Azure Cosmos DB (SQL API) Data Factory entitásokat lehet meghatározni.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Azure Cosmos DB (SQL API) társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **CosmosDb**. | Igen |
| connectionString |Adja meg az Azure Cosmos DB-adatbázishoz való csatlakozáshoz szükséges információk.<br />**Megjegyzés**: A következő példákban látható módon meg kell adnia az adatbázis adatait a kapcsolatok karakterláncában. <br/>A mező megjelölése SecureString, hogy biztonságosan tárolja Data Factoryban. A Azure Key Vault is elhelyezheti a fiók kulcsát, és `accountKey` lekérheti a konfigurációt a kapcsolatok sztringből. További részletekért tekintse meg a következő mintákat, és [tárolja a hitelesítő adatokat Azure Key Vault](store-credentials-in-key-vault.md) cikkben. |Igen |
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

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB (SQL API) adatkészlet támogat. 

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Ha adatokat szeretne másolni a vagy a rendszerből a Azure Cosmos DBba (SQL API), állítsa az adatkészlet **Type (típus** ) tulajdonságát **DocumentDbCollection**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **DocumentDbCollection**. |Igen |
| collectionName |Neve az Azure Cosmos DB-dokumentumgyűjteményt. |Igen |

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

### <a name="schema-by-data-factory"></a>Adat-előállítók által séma

A sémamentes adatokra tárolókat, mint az Azure Cosmos DB a másolási tevékenység kikövetkezteti a használandó sémát az egyik módja az alábbi lista ismerteti. Hacsak nem szeretné [importálása és exportálása a JSON-dokumentumok,-van](#import-or-export-json-documents), az ajánlott eljárás, hogy az adatok struktúráját adja meg a **struktúra** szakaszban.

* Ha az adatok struktúráját használatával adja meg a **struktúra** tulajdonságot az adatkészlet-definícióban, adat-előállító figyelembe veszi a sémát, ez a struktúra. 

    Ha egy sor nem tartalmaz egy oszlop értékét, az oszlop értékének null értékű biztosítunk.
* Ha nem adja meg az adatok struktúráját használatával a **struktúra** tulajdonságot az adatkészlet-definícióban, a Data Factory szolgáltatás kikövetkezteti a használandó sémát az első sor az adatok használatával. 

    Ha az első sor a teljes séma nem tartalmaz, néhány oszlop hiányzik a másolási művelet eredménye lesz.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket a Azure Cosmos DB (SQL API) forrás és a fogadó támogat.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) forrásként

Az adatok Azure Cosmos DBból (SQL API) történő másolásához állítsa a **forrás** típusát a másolási tevékenység **DocumentDbCollectionSource**. 

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrása tulajdonságát **DocumentDbCollectionSource**. |Igen |
| query |Adja meg az Azure Cosmos DB lekérdezéssel adatokat olvasni.<br/><br/>Példa:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítás végrehajtása: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Egy speciális karaktert, amely azt jelzi, hogy a dokumentum van beágyazva, és hogyan simítja egybe az eredményhalmaz.<br/><br/>Például, ha egy Azure Cosmos DB-lekérdezés beágyazott eredményét adja vissza. `"Name": {"First": "John"}`, a másolási tevékenység azonosítja az oszlop neve, mint `Name.First`, az értéke "János", ha a **nestedSeparator** érték **.** (pont). |Nem<br />(az alapértelmezett érték **.** (pont)) |

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

Az adatAzure Cosmos DB (SQL API) másolásához állítsa a fogadó típust a másolás tevékenység **DocumentDbCollectionSink**. 

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát **DocumentDbCollectionSink**. |Igen |
| writeBehavior |Ismerteti, hogyan lehet adatokat írni az Azure Cosmos DB-hez. Megengedett értékek: **beszúrása** és **upsert**.<br/><br/>Viselkedését **upsert** , hogy cserélje le a dokumentumot, ha egy dokumentum ugyanazzal az azonosítóval már létezik; egyéb esetben helyezze be a dokumentum.<br /><br />**Megjegyzés**: Data Factory automatikusan létrehoz egy azonosítót egy dokumentumhoz, ha nincs megadva azonosító az eredeti dokumentumban vagy oszlop-hozzárendeléssel. Ez azt jelenti, hogy gondoskodnia kell arról, hogy a **upsert** a várt módon működik, a dokumentum rendelkezik azonosítóval. |Nem<br />(az alapértelmezett érték **beszúrása**) |
| writeBatchSize | Adat-előállító használ a [Azure Cosmos DB tömeges végrehajtó könyvtár](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) adatokat írni az Azure Cosmos DB-hez. A **writeBatchSize** tulajdonság az ADF által a könyvtárnak biztosított dokumentumok méretét határozza meg. Próbálja meg az értékét növelje **writeBatchSize** jobb teljesítmény és az érték csökkentésével, ha a dokumentumot a rendszer nagy méretezés – lásd az alábbi tippeket. |Nem<br />(az alapértelmezett érték **10 000**) |
| nestingSeparator |Egy speciális karaktert a **forrás** oszlopnevet, amely azt jelzi, hogy a beágyazott dokumentum van szükség. <br/><br/>Ha például `Name.First` a kimeneti adatkészlet struktúra hoz létre az Azure Cosmos DB-ben a következő JSON-struktúrát mikor a dokumentum a **nestedSeparator** van **.** (pont): `"Name": {"First": "[value maps to this column from source]"}`  |Nem<br />(az alapértelmezett érték **.** (pont)) |
| disableMetricsCollection | Data Factory olyan mérőszámokat gyűjt, mint például a Cosmos DB RUs a másolási teljesítmény optimalizálása és a javaslatok érdekében. Ha ezt a viselkedést érinti, a kikapcsolásához válassza `true` a következőt:. | Nem (alapértelmezett `false`érték) |

>[!TIP]
>A cosmos DB korlátozza az egyetlen kérés mérete 2MB. A képlet egy kérés mérete = egyetlen dokumentum mérete * kötegméret írása. Ha eléri hiba üzenettel **"Request mérete túl nagy."** , **csökkentheti a `writeBatchSize` érték** másolási fogadó konfigurációban.

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

## <a name="import-or-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Ezt a Azure Cosmos DB (SQL API) összekötőt egyszerűen elvégezheti:

* JSON-dokumentumok importálása különböző forrásokból származó Azure Cosmos DB, beleértve az Azure Blob storage, Azure Data Lake Store és egyéb fájlalapú tárolók, amely az Azure Data Factory támogatja.
* JSON-dokumentumok exportálhat egy Azure Cosmos DB-gyűjtemények különböző fájlalapú tárolók.
* Másolja a között két Azure Cosmos DB-gyűjtemények, dokumentumok-van.

Sémafüggetlen másolási eléréséhez:

* Amikor az adatok másolása eszközzel, válassza ki a **exportálni – JSON-fájlokat vagy a Cosmos DB-gyűjtemények** lehetőséget.
* Szerzői tevékenység használatakor nem ad meg a **struktúra** (más néven *séma*) szakaszban az Azure Cosmos DB-adatkészletben. Ezenkívül nem adja meg a **nestingSeparator** tulajdonság frissítése az Azure Cosmos DB-forrás vagy a másolási tevékenység fogadó. Importálása vagy exportálása JSON-fájlok, a megfelelő fájlban tárolja az adatkészlet, adja meg a **formátum** típusa **JsonFormat** és konfigurálja a **filePattern** , ismertetett a [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format) szakaszban. Ezt követően nem adja meg a **struktúra** szakaszt, és hagyja ki a fájlformátum beállításai az.

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
