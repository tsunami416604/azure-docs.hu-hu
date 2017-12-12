---
title: "Adatok másolása az Azure Cosmos DB használatával a Data Factory |} Microsoft Docs"
description: "Ismerje meg az adatok másolása az Azure Cosmos-Adatbázishoz a támogatott forráshierarchiából adatokat áruházakból (vagy) a Cosmos-Adatbázisból támogatott fogadó áruházak Data Factory használatával."
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: ab9e7b1b287be408f2d53ea005bad3815dc45f83
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Másolja az adatokat, vagy az Azure Data Factory használatához Azure Cosmos-Adatbázisból

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-azure-documentdb-connector.md)
> * [2. verzió – Előzetes verzió](connector-azure-cosmos-db.md)

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory másolja az adatokat, a kezdő és a Azure Cosmos DB (SQL API-t). Buildekről nyújtanak a [másolása tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Azure Cosmos DB connnector a V1](v1/data-factory-azure-documentdb-connector.md).

## <a name="supported-capabilities"></a>Támogatott képességei

Adatok másolása az Azure Cosmos DB az egyetlen támogatott fogadó adattár, vagy bármely támogatott forráshierarchiából adatokat tároló Azure Cosmos DB adatokat másolni. A másolási tevékenység által támogatott adatforrások/mosdók adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Konkrétan ez Azure Cosmos DB az összekötő támogatja:

- A cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Importálás/exportálás JSON-dokumentumokat,-, illetve az adatok másolásának/táblázatos dataset például az SQL-adatbázis, a CSV-fájlok, a stb.

Másolja a dokumentumokat-van/JSON-fájlokat vagy egy másik Cosmos DB gyűjteményhez, lásd: [Import/Export JSON-dokumentumok](#importexport-json-documents).

## <a name="getting-started"></a>Bevezetés
A másolási tevékenység során .NET SDK, a Python SDK, az Azure PowerShell, a REST API-t vagy a Azure Resource Manager-sablon használatával hozhat létre egy folyamatot. Lásd: [másolási tevékenység oktatóanyag](quickstart-create-data-factory-dot-net.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját.

A következő szakaszok részletesen bemutatják való Azure Cosmos DB Data Factory tartozó entitások meghatározásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok

Azure Cosmos DB kapcsolódó szolgáltatás támogatott a következő tulajdonságokkal:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot kell beállítani: **CosmosDb**. | Igen |
| connectionString |Adja meg Azure Cosmos DB adatbázishoz való kapcsolódáshoz szükséges adatokat. Vegye figyelembe, hogy meg kell adni az adatbázis-információ a kapcsolódási karakterláncban minta alatt. Ez a mező megjelölése a SecureString. |Igen |
| connectVia | A [integrációs futásidejű](concepts-integration-runtime.md) csatlakozni az adattárolóhoz használandó. Használhat Azure integrációs futásidejű vagy Self-hosted integrációs futásidejű (amennyiben az adattároló magánhálózaton található). Ha nincs megadva, akkor használja az alapértelmezett Azure integrációs futásidejű. |Nem |

**Példa**

```json
{
    "name": "CosmosDbLinkedService",
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

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Szakaszok és meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg az adatkészletek cikket. Ez a témakör az Azure Cosmos DB dataset által támogatott tulajdonságokról.

Adatok másolása Azure Cosmos DB vagy állítsa be a adatkészlet type tulajdonsága **DocumentDbCollection**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adathalmaz értékre kell állítani: **DocumentDbCollection** |Igen |
| CollectionName |A Cosmos DB dokumentum gyűjtemény nevét. |Igen |

**Példa**

```json
{
    "name": "CosmosDbDataset",
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

### <a name="schema-by-data-factory"></a>Adat-előállító sémája

Sémamentesadat-tárolókhoz, például az Azure Cosmos DB a másolási tevékenység során a következő módszerek valamelyikével kikövetkezteti a séma. Ezért hacsak nem szeretné [importálási/exportálási JSON-dokumentumokat,-van](#importexport-json-documents), a bevált gyakorlat az, hogy adja meg az adatok szerkezete a **struktúra** szakasz.

1. Ha az adatok szerkezete használatával adja meg a **struktúra** tulajdonsághoz a DataSet adatkészlet-definícióban a Data Factory szolgáltatásnak eleget tegyen a séma szerint ez a struktúra. Ebben az esetben ha egy sort tartalmaz egy olyan oszlop értékét, null értékű nyújtanak az.
2. Ha nincs megadva az adatok szerkezete használatával a **struktúra** tulajdonság az adatkészlet-definícióban, a Data Factory szolgáltatásnak kikövetkezteti a séma az adatok első sora használatával. Ebben az esetben ha az első sort tartalmazza a teljes séma, néhány oszlop nem érhető el a másolási művelet eredménye.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és a rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a témakör az Azure Cosmos DB forrás és a fogadó által támogatott tulajdonságokról.

### <a name="azure-cosmos-db-as-source"></a>Az Azure Cosmos DB forrásaként

Adatok másolása az Azure Cosmos DB, állítsa be a forrás típusa a másolási tevékenység **DocumentDbCollectionSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot a másolási tevékenység forrás értékre kell állítani: **DocumentDbCollectionSource** |Igen |
| lekérdezés |Adja meg a Cosmos adatbázis-lekérdezés adatokat olvasni.<br/><br/>Példa:`SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítást, amely végrehajtja a rendszer:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Jelzi, hogy a dokumentum van beágyazva speciális karaktert, és hogyan flattern az eredményt állítsa be.<br/><br/>Ha egy Cosmos-adatbázis-lekérdezés egy beágyazott eredményt adja vissza, például `"Name": {"First": "John"}`, másolási tevékenység során azonosítja oszlopnév "Name.First", "John" értékkel a nestedSeparator pont esetén. |Nem (alapértelmezett érték a pont `.`) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
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

### <a name="azure-cosmos-db-as-sink"></a>Mint fogadó Azure Cosmos-adatbázis

Adatok másolása az Azure Cosmos DB, állítsa a fogadó típusa a másolási tevékenység **DocumentDbCollectionSink**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakasz:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **DocumentDbCollectionSink** |Igen |
| nestingSeparator |A forrás oszlop nevét jelzi, hogy a beágyazott dokumentum egy különleges karakterek van szükség. <br/><br/>Például `Name.First` kimeneti adatkészlet struktúra hoz létre a következő JSON struktúrában a Cosmos DB dokumentumban:`"Name": {"First": "[value maps to this column from source]"}` pont a nestedSeparator esetén. |Nem (alapértelmezett érték a pont `.`) |
| writeBatchTimeout |Várakozási idő a művelet befejezését, mielőtt azt az időkorlátot.<br/><br/>Két érték engedélyezett: timespan. Példa: "00: 30:00" (30 perc). |Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
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
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Importálási/exportálási JSON-dokumentumok

A Cosmos DB összekötő használatával egyszerűen

* Importálja a JSON-dokumentumok különböző forrásokból Cosmos DB, beleértve az Azure Blob, az Azure Data Lake Store és az egyéb fájlalapú tárolók Azure Data Factory által támogatott.
* A Cosmos DB collecton JSON-dokumentumok exportálása különböző fájlalapú tárolók.
* Másolja a dokumentumok között két Cosmos DB gyűjteményeket-van.

A séma-független másolat eléréséhez:

- A Cosmos DB adatkészlet(ek), ne adja meg a "structure" szakasz; és a másolási tevékenység Cosmos DB forrás/fogadó, nem adja meg a "nestingSeparator".
- Amikor importálja / exportálása a JSON-fájlokat a megfelelő fájlt tároló adatkészlet, adja meg formázási típusa "JsonFormat" és a konfiguráció "filePattern" megfelelően (lásd: [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format) szakaszát), majd nem adja meg a "structure "szakaszt, és hagyja ki a többi formátum beállításait.

## <a name="next-steps"></a>Következő lépések
Támogatott források és mosdók által a másolási tevékenység során az Azure Data Factory adattárolókhoz listájáért lásd: [adattárolókhoz támogatott](copy-activity-overview.md##supported-data-stores-and-formats).