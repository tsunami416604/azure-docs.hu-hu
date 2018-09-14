---
title: Adatok másolása és- tárolókról az Azure Cosmos DB használatával a Data Factory |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat az Azure Cosmos DB támogatott forrás adattárakból származó (vagy) Cosmos DB-ből támogatott fogadó-áruházak a Data Factory segítségével.
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
ms.date: 09/11/2018
ms.author: jingwang
ms.openlocfilehash: db2f3086da41e990e6f87d8086d6e8defa447532
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543439"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Adatok másolása, vagy az Azure Cosmos DB az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuális verzió](connector-azure-cosmos-db.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban az adatok másolásához, és az Azure Cosmos DB (az SQL API-t). Épül a [másolási tevékenység áttekintése](copy-activity-overview.md) cikket, amely megadja a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok másolása az Azure Cosmos DB az összes támogatott fogadó adattárba, vagy bármely támogatott forrásadattárból adatokat másol az Azure Cosmos DB. A másolási tevékenység által források/fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) tábla.

Pontosabban az Azure Cosmos DB-összekötő támogatja:

- Adatok másolása Azure blobból vagy az, Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Írja be a Cosmos DB INSERT vagy UPSERT.
- Mint a JSON-dokumentumok importálása/exportálása –, vagy adatok másolása Azure blobból vagy az tabulált adatkészlet például az SQL database, a CSV-fájlok, stb. Másolja ki a dokumentumok-van és- tárolókról JSON-fájlokat, vagy egy másik Cosmos DB-gyűjtemények, lásd: [Import/Export JSON-dokumentumok](#importexport-json-documents).

A Data Factory integrálható [Cosmos DB tömeges végrehajtó könyvtár](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) írása az Cosmos DB-be a legjobb teljesítményt biztosítja.

>[!TIP]
>Tekintse meg [ebben a videóban](https://youtu.be/5-SRNiC_qOU) amely végigvezeti az adatok másolása az Azure Blob storage-ból a Cosmos DB-hez és teljesítmény-finomhangolási szempontjai általában tölt be adatot az Cosmos DB-be.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozhatók meg a Data Factory-entitások egyedi az Azure Cosmos DB-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Azure Cosmos DB-hez társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonság értékre kell állítani: **CosmosDb**. | Igen |
| kapcsolati Sztringje |Azure Cosmos DB-adatbázishoz való kapcsolódáshoz szükséges információkat adják meg. Vegye figyelembe, hogy meg kell adnia az adatbázis-információ a kapcsolati karakterláncban minta alatt. Ez a mező megjelölése tárolja biztonságos helyen a Data Factory, a SecureString vagy [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) az adattárban való kapcsolódáshoz használandó. Használhatja az Azure integrációs modul vagy a helyi integrációs modul (ha az adattár magánhálózaton található). Ha nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Szakaszok és adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg az adatkészletek a cikk. Ez a szakasz az Azure Cosmos DB-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Adatok másolása Azure blobból vagy az Azure Cosmos DB, állítsa be a type tulajdonság, az adatkészlet **DocumentDbCollection**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A type tulajdonságot az adatkészlet értékre kell állítani: **DocumentDbCollection** |Igen |
| collectionName |A Cosmos DB-dokumentumgyűjteményt neve. |Igen |

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

### <a name="schema-by-data-factory"></a>Adat-előállítók által séma

A séma nélküli adattárakban, mint az Azure Cosmos DB a másolási tevékenység kikövetkezteti a használandó sémát a következő módszerek valamelyikével. Ezért, ha nem szeretne [importálási/exportálási JSON-dokumentumok,-van](#importexport-json-documents), az ajánlott eljárás, hogy az adatok struktúráját adja meg a **struktúra** szakaszban.

*. Ha az adatok struktúráját használatával adja meg a **struktúra** tulajdonságot az adatkészlet-definícióban, a Data Factory szolgáltatás figyelembe veszi a sémát, ez a struktúra. Ebben az esetben ha egy sort tartalmaz egy oszlop értékét, null értékű biztosítjuk.
*. Ha nincs megadva az adatok struktúráját használatával a **struktúra** tulajdonságot az adatkészlet-definícióban, a Data Factory szolgáltatás kikövetkezteti a használandó sémát az első sor az adatok használatával. Ebben az esetben ha az első sor nem tartalmazza a teljes séma, egyes oszlopok nem érhető el a másolási művelet eredményét.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Szakaszok és tulajdonságok definiálását tevékenységek teljes listáját lásd: a [folyamatok](concepts-pipelines-activities.md) cikk. Ez a szakasz az Azure Cosmos DB-adatforrások és adatfogadók által támogatott tulajdonságok listáját tartalmazza.

### <a name="azure-cosmos-db-as-source"></a>Az Azure Cosmos DB forrásként

Adatok másolása az Azure Cosmos DB, állítsa be a forrás típusaként a másolási tevékenység **DocumentDbCollectionSource**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrása type tulajdonsága értékre kell állítani: **DocumentDbCollectionSource** |Igen |
| lekérdezés |Adja meg a Cosmos DB-lekérdezés adatokat olvasni.<br/><br/>Például: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítást, amely hajtja végre: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Speciális karakter jelzi, hogy a dokumentum van beágyazva, és hogyan flattern az eredményt állítsa.<br/><br/>Például, ha a Cosmos DB-lekérdezés beágyazott eredményt ad vissza `"Name": {"First": "John"}`, a másolási tevékenység azonosítsa oszlop neve "Name.First", "János" értékkel a nestedSeparator pont. |Nem (alapértelmezett érték a pont `.`) |

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

### <a name="azure-cosmos-db-as-sink"></a>Az Azure Cosmos DB pedig a fogadó

Adatok másolása az Azure Cosmos DB, állítsa a fogadó típusa a másolási tevékenység **DocumentDbCollectionSink**. A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A másolási tevékenység fogadó type tulajdonsága értékre kell állítani: **DocumentDbCollectionSink** |Igen |
| WriteBehavior |Írja az adatokat, a Cosmos DB ismertetik. Engedélyezett értékek a következők: `insert` és `upsert`.<br/>Viselkedését **upsert** , hogy cserélje le a dokumentumot, ha egy dokumentum annak ugyanazzal az azonosítóval már létezik; ellenkező esetben szúrja be. Megjegyzés: ADF automatikus létrehozása a dokumentum egy azonosítót, ha nincs megadva, vagy az eredeti dokumentum vagy oszlop-hozzárendelés), ami azt jelenti, hogy ellenőrizze, hogy a dokumentum kell rendelkezik egy "id", így upsert a várt módon működik. |Nem, alapértelmezett van beszúrása |
| WriteBatchSize | Data Factory használata [Cosmos DB tömeges végrehajtó könyvtár](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) az adatok írása az Cosmos DB-be. "writeBatchSize" a Microsoft biztosít a tár minden alkalommal, amikor dokumentumok méretét szabályozza. Megpróbálhatja a növekedés writeBatchSize teljesítmény javítása érdekében. |Nem, az alapértelmezett érték 10 000 |
| nestingSeparator |A forrás oszlop nevét jelzi, hogy a beágyazott dokumentum egy speciális karaktert van szükség. <br/><br/>Ha például `Name.First` a kimeneti adatkészlet struktúra hoz létre a Cosmos DB-dokumentumban a következő JSON-struktúrát:`"Name": {"First": "[value maps to this column from source]"}` pont a nestedSeparator esetén. |Nem (alapértelmezett érték a pont `.`) |

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
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Importálási/exportálási JSON-dokumentumok

A Cosmos DB-összekötő használatával egyszerűen

* Importálja a JSON-dokumentumok különböző forrásokból származó Cosmos DB, beleértve az Azure Blob, az Azure Data Lake Store és az egyéb fájlalapú tárolók az Azure Data Factory által támogatott.
* JSON-dokumentumok exportálása a Cosmos DB collecton különböző fájlalapú tárolók.
* Másolja a között két Cosmos DB-gyűjtemények, dokumentumok-van.

Az ilyen sémafüggetlen másolási eléréséhez:

* Másolási adatok eszközzel ellenőrizze a **"exportálása – JSON-fájlokat vagy a Cosmos DB-gyűjtemény"** lehetőséget.
* Ha használja a szerzői tevékenység, ne adja meg a "struktúra" (más néven séma) szakaszban Cosmos DB adatkészlet(ek) sem "nestingSeparator" tulajdonság a Cosmos DB forrás/fogadó a másolási tevékenység. Amikor importálása / exportálása a JSON-fájlokat a megfelelő fájl store adatkészletben, adja meg formátum típusa "JsonFormat" és a konfiguráció "filePattern" megfelelően (lásd: [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format) részleteket a következő szakaszban), majd ne adja meg a "szerkezet "(más néven séma) szakaszt, és hagyja ki a rest-fájlformátum beállításai.

## <a name="next-steps"></a>További lépések
A másolási tevékenység az Azure Data Factory által forrásként és fogadóként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
