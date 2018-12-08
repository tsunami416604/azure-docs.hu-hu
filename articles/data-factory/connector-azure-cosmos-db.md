---
title: Adatok másolása, illetve az Azure Cosmos DB (az SQL API-t) a Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat támogatott forrás adattárakból származó, vagy az Azure Cosmos DB támogatott fogadó-áruházak Data Factory használatával.
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
ms.date: 11/19/2018
ms.author: jingwang
ms.openlocfilehash: 16c02f1f47f556f550519feec78e7dd26b302e18
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103795"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Adatok másolása, vagy az Azure Cosmos DB (az SQL API-t) az Azure Data Factory használatával

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuális verzió](connector-azure-cosmos-db.md)

Ez a cikk ismerteti, hogyan használja a másolási tevékenység az Azure Data Factoryban másolhat adatokat, és az Azure Cosmos DB (az SQL API-t). A cikk számos tekintetben [másolási tevékenységgel az Azure Data Factoryban](copy-activity-overview.md), amely megadja, hogy a másolási tevékenység általános áttekintést.

## <a name="supported-capabilities"></a>Támogatott képességek

Adatok másolása az Azure Cosmos DB az összes támogatott fogadó adattárba, vagy bármely támogatott forrásadattárból adatokat másol az Azure Cosmos DB. Az adatok listáját tárolja, hogy a másolási tevékenység támogatja a forrásként és fogadóként, lásd: [támogatott adattárak és formátumok](copy-activity-overview.md#supported-data-stores-and-formats).

Használhatja az Azure Cosmos DB-összekötő:

- Adatok másolása az Azure Cosmos DB és a [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction).
- Írni az Azure Cosmos DB **beszúrása** vagy **upsert**.
- Importálás és exportálás JSON-dokumentumok,-, vagy másolja az adatokat, vagy egy táblázatos adatkészlethez. Ilyenek például egy SQL-adatbázis és a egy CSV-fájlt. Másolja ki a dokumentumok-, illetve a JSON-fájlok vagy az vagy egy másik Azure Cosmos DB-gyűjtemények, lásd: [importálás vagy exportálás JSON-dokumentumok](#importexport-json-documents).

A Data Factory integrálható a [Azure Cosmos DB tömeges végrehajtó könyvtár](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) a legjobb teljesítményt biztosítja az Azure Cosmos DB írásakor.

>[!NOTE]
>Az összekötő podporují másolja az adatokat és a Cosmos DB SQL API.

> [!TIP]
> A [adatáttelepítés videó](https://youtu.be/5-SRNiC_qOU) végigvezeti a lépéseken, az adatok másolása az Azure Blob storage-ból az Azure Cosmos DB. A videó bemutatja a tölt be adatot az Azure Cosmos DB az általános teljesítmény-finomhangolási szempontok is.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszok segítségével határozza meg a Data Factory-entitások, konkrétan az Azure Cosmos DB-tulajdonságokkal kapcsolatos részletekért.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Cosmos DB-hez társított szolgáltatás a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** tulajdonságot állítsa **CosmosDb**. | Igen |
| kapcsolati Sztringje |Adja meg az Azure Cosmos DB-adatbázishoz való csatlakozáshoz szükséges információk.<br /><br />**Megjegyzés:**: Adjon meg adatbázis-információ a kapcsolati karakterlánc az alábbi példákban szemléltetett módon. Jelölje meg a mező egy **SecureString** típus tárolja biztonságos helyen a Data Factoryban. Emellett [hivatkozik az Azure Key Vaultban tárolt titkos](store-credentials-in-key-vault.md). |Igen |
| connectVia | A [Integration Runtime](concepts-integration-runtime.md) kapcsolódni az adattárhoz. Használhatja az Azure integrációs modul és a egy saját üzemeltetésű integrációs modul (ha az adattár egy magánhálózaton található). Ha ez a tulajdonság nincs megadva, az alapértelmezett Azure integrációs modult használja. |Nem |

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

Ez a szakasz felsorolja, amely támogatja az Azure Cosmos DB-adatkészlet tulajdonságai. 

Szakaszok és adatkészletek definiálását tulajdonságok teljes listáját lásd: [adatkészletek és társított szolgáltatásokat](concepts-datasets-linked-services.md). 

Másolja az adatokat, vagy az Azure Cosmos DB, állítsa be a **típus** tulajdonság, az adatkészlet **DocumentDbCollection**. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a tulajdonságot az adatkészlet **DocumentDbCollection**. |Igen |
| collectionName |Neve az Azure Cosmos DB-dokumentumgyűjteményt. |Igen |

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

A sémamentes adatokra tárolókat, mint az Azure Cosmos DB a másolási tevékenység kikövetkezteti a használandó sémát az egyik módja az alábbi lista ismerteti. Hacsak nem szeretné [importálása és exportálása a JSON-dokumentumok,-van](#import-or-export-json-documents), az ajánlott eljárás, hogy az adatok struktúráját adja meg a **struktúra** szakaszban.

* Ha az adatok struktúráját használatával adja meg a **struktúra** tulajdonságot az adatkészlet-definícióban, adat-előállító figyelembe veszi a sémát, ez a struktúra. 

    Ha egy sor nem tartalmaz egy oszlop értékét, az oszlop értékének null értékű biztosítunk.
* Ha nem adja meg az adatok struktúráját használatával a **struktúra** tulajdonságot az adatkészlet-definícióban, a Data Factory szolgáltatás kikövetkezteti a használandó sémát az első sor az adatok használatával. 

    Ha az első sor a teljes séma nem tartalmaz, néhány oszlop hiányzik a másolási művelet eredménye lesz.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

Ez a szakasz a tulajdonságok, amelyek támogatják az Azure Cosmos DB-forrás- és fogadóadattárként listáját tartalmazza.

Szakaszok és a tevékenységek definiálását tulajdonságok teljes listáját lásd: [folyamatok](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-as-source"></a>Az Azure Cosmos DB forrásként

Adatok másolása az Azure Cosmos DB, állítsa be a **forrás** írja be a másolási tevékenység **DocumentDbCollectionSource**. 

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység forrása tulajdonságát **DocumentDbCollectionSource**. |Igen |
| lekérdezés |Adja meg az Azure Cosmos DB lekérdezéssel adatokat olvasni.<br/><br/>Példa:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítás végrehajtása: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Egy speciális karaktert, amely azt jelzi, hogy a dokumentum van beágyazva, és hogyan simítja egybe az eredményhalmaz.<br/><br/>Például, ha egy Azure Cosmos DB-lekérdezés beágyazott eredményét adja vissza. `"Name": {"First": "John"}`, a másolási tevékenység azonosítja az oszlop neve, mint `Name.First`, az értéke "János", ha a **nestedSeparator** érték **.** (pont). |Nem<br />(az alapértelmezett érték **.** (pont)) |

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

Adatok másolása az Azure Cosmos DB, állítsa be a **fogadó** írja be a másolási tevékenység **DocumentDbCollectionSink**. 

A következő tulajdonságok támogatottak a másolási tevékenység **forrás** szakaszban:

| Tulajdonság | Leírás | Szükséges |
|:--- |:--- |:--- |
| type | A **típus** értékre kell állítani a másolási tevékenység fogadó tulajdonságát **DocumentDbCollectionSink**. |Igen |
| WriteBehavior |Ismerteti, hogyan lehet adatokat írni az Azure Cosmos DB-hez. Megengedett értékek: **beszúrása** és **upsert**.<br/><br/>Viselkedését **upsert** , hogy cserélje le a dokumentumot, ha egy dokumentum ugyanazzal az azonosítóval már létezik; egyéb esetben helyezze be a dokumentum.<br /><br />**Megjegyzés:**: Data Factory automatikusan létrehozza a dokumentum Azonosítóját, ha nem ad meg Azonosítót vagy az eredeti dokumentum vagy oszlop-hozzárendelés. Ez azt jelenti, hogy gondoskodnia kell arról, hogy a **upsert** a várt módon működik, a dokumentum rendelkezik azonosítóval. |Nem<br />(az alapértelmezett érték **beszúrása**) |
| WriteBatchSize | Adat-előállító használ a [Azure Cosmos DB tömeges végrehajtó könyvtár](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) adatokat írni az Azure Cosmos DB-hez. A **writeBatchSize** tulajdonság, amely a kódtár a Microsoft biztosít dokumentumok méretét szabályozza. Próbálja meg az értékét növelje **writeBatchSize** jobb teljesítmény és az érték csökkentésével, ha a dokumentumot a rendszer nagy méretezés – lásd az alábbi tippeket. |Nem<br />(az alapértelmezett érték **10 000**) |
| nestingSeparator |Egy speciális karaktert a **forrás** oszlopnevet, amely azt jelzi, hogy a beágyazott dokumentum van szükség. <br/><br/>Ha például `Name.First` a kimeneti adatkészlet struktúra hoz létre az Azure Cosmos DB-ben a következő JSON-struktúrát mikor a dokumentum a **nestedSeparator** van **.** (pont): `"Name": {"First": "[value maps to this column from source]"}`  |Nem<br />(az alapértelmezett érték **.** (pont)) |

>[!TIP]
>A cosmos DB korlátozza az egyetlen kérés mérete 2MB. A képlet egy kérés mérete = egyetlen dokumentum mérete * kötegméret írása. Ha eléri hiba üzenettel **"Request mérete túl nagy."** , **csökkentheti a `writeBatchSize` érték** másolási fogadó konfigurációban.

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

## <a name="import-or-export-json-documents"></a>JSON-dokumentumok importálása és exportálása

Az Azure Cosmos DB-összekötő egyszerűen használható:

* JSON-dokumentumok importálása különböző forrásokból származó Azure Cosmos DB, beleértve az Azure Blob storage, Azure Data Lake Store és egyéb fájlalapú tárolók, amely az Azure Data Factory támogatja.
* JSON-dokumentumok exportálhat egy Azure Cosmos DB-gyűjtemények különböző fájlalapú tárolók.
* Másolja a között két Azure Cosmos DB-gyűjtemények, dokumentumok-van.

Sémafüggetlen másolási eléréséhez:

* Amikor az adatok másolása eszközzel, válassza ki a **exportálni – JSON-fájlokat vagy a Cosmos DB-gyűjtemények** lehetőséget.
* Szerzői tevékenység használatakor nem ad meg a **struktúra** (más néven *séma*) szakaszban az Azure Cosmos DB-adatkészletben. Ezenkívül nem adja meg a **nestingSeparator** tulajdonság frissítése az Azure Cosmos DB-forrás vagy a másolási tevékenység fogadó. Importálása vagy exportálása JSON-fájlok, a megfelelő fájlban tárolja az adatkészlet, adja meg a **formátum** típusa **JsonFormat** és konfigurálja a **filePattern** , ismertetett a [JSON formátumban](supported-file-formats-and-compression-codecs.md#json-format) szakaszban. Ezt követően nem adja meg a **struktúra** szakaszt, és hagyja ki a fájlformátum beállításai az.

## <a name="next-steps"></a>További lépések

Másolási tevékenységet támogatja az adatforrások és az Azure Data Factoryban fogadók adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md##supported-data-stores-and-formats).
