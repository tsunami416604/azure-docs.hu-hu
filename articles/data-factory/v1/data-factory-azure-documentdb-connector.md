---
title: Adatok áthelyezése az Azure Cosmos DB-re/onnan
description: Megtudhatja, hogyan helyezheti át az adatokat az Azure Cosmos DB-gyűjteménybe az Azure Data Factory használatával
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a638184d5232de916ebd25360147301a93309dd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260513"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Adatok áthelyezése az Azure Cosmos DB-ra és az Azure Cosmos DB-ből az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-documentdb-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [Azure Cosmos DB-összekötőt a V2-ben.](../connector-azure-cosmos-db.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése/ az Azure Cosmos DB (SQL API). Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

Bármely támogatott forrásadat-tárból adatokat másolhat az Azure Cosmos DB-be vagy az Azure Cosmos DB-ből bármely támogatott fogadó adattárba. A másolási tevékenység által forrásként vagy fogadóként támogatott adattárak listáját a [Támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblában található.

> [!IMPORTANT]
> Az Azure Cosmos DB-összekötő csak az SQL API-t támogatja.

Ha jsonfájlokba vagy más Cosmos DB-gyűjteményből szeretné másolni az adatokat, olvassa el [a JSON-dokumentumok importálása/exportálása című témakört.](#importexport-json-documents)

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely az adatokat az Azure Cosmos DB-be helyezi át/ az Azure Cosmos DB-ből különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására.
2. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására.
3. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. A Data Factory-entitások JSON-definícióival rendelkező mintákat, amelyek adatok másolására szolgálnak a Cosmos DB-ből, lásd: [JSON-példák](#json-examples) a cikk.

A következő szakaszok a Cosmos DB-re jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Az alábbi táblázat az Azure Cosmos DB-hez csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| type |A típustulajdonságnak a következőre kell állítania: **DocumentDb** |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg az Azure Cosmos DB adatbázishoz való csatlakozáshoz szükséges információkat. |Igen |

Példa:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához elérhető szakaszok & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben tájékozódhat. A JSON-adatkészlet ek struktúrája, rendelkezésre állása és szabályzata hasonló az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A typeProperties szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. A **DocumentDbCollection** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik.

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| Lekérdezés_neve |A Cosmos DB dokumentumgyűjtemény neve. |Igen |

Példa:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Séma adatgyár szerint
Sémanélküli adattárak, például az Azure Cosmos DB esetében a Data Factory szolgáltatás az alábbi módokon következtet a sémából:

1. Ha az adatkészlet-definícióban a **struktúra** tulajdonság használatával adja meg az adatok szerkezetét, a Data Factory szolgáltatás ezt a struktúrát sémaként tartja tiszteletben. Ebben az esetben, ha egy sor nem tartalmaz értéket egy oszlophoz, null értéket ad meg.
2. Ha nem adja meg az adatok szerkezetét az adatkészlet-definícióban a **struktúra** tulajdonság használatával, a Data Factory szolgáltatás az adatok első sorának használatával következtet a sémából. Ebben az esetben, ha az első sor nem tartalmazza a teljes sémát, néhány oszlop hiányozni fog a másolási művelet eredményében.

Ezért sémamentes adatforrások esetében ajánlott az adatok szerkezetének megadása a **struktúra** tulajdonság használatával.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a Folyamatok létrehozása című cikkben [tájékozódhat.](data-factory-create-pipelines.md) Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

> [!NOTE]
> A Másolási tevékenység csak egy bemenetet vesz igénybe, és csak egy kimenetet hoz létre.

A tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak, másolási tevékenység esetén pedig a források és a fogadók típusától függően változnak.

Ha a forrás típusú forrás másolása esetén a forrás **DocumentDbCollectionSource** típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| lekérdezés |Adja meg az adatokolvasáshoz a lekérdezést. |Az Azure Cosmos DB által támogatott lekérdezési karakterlánc. <br/><br/>Például: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, a végrehajtásra kerül az SQL utasítás:`select <columns defined in structure> from mycollection` |
| nestingSeparator |A dokumentum beágyazottként való megjelölésére utaló speciális karakter |Bármilyen karaktert. <br/><br/>Az Azure Cosmos DB egy NoSQL-áruház A JSON-dokumentumok, ahol beágyazott struktúrák megengedettek. Az Azure Data Factory lehetővé teszi a felhasználó számára, hogy a hierarchiát a beágyazássaljelölőss, azaz "" jelöli. a fenti példákban. Az elválasztóval a másolási tevékenység a tábladefinícióban szereplő "Name.First", "Name.Middle" és "Name.Last" (Név) objektumhárom gyermekelemmel hozza létre az Első, a Középső és az Utolsó elemet a tábladefinícióban szereplő "Name.First", "Name.Middle" és "Name.Last" szerint. |Nem |

**A DocumentDbCollectionSink** a következő tulajdonságokat támogatja:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| nestingSeparator |A forrásoszlop nevében egy speciális karakter jelzi, hogy beágyazott dokumentumra van szükség. <br/><br/>Például a `Name.First` fenti: a kimeneti táblázat ban a következő JSON-struktúra a Cosmos DB dokumentumban:<br/><br/>"Név": {<br/>    "Első": "John"<br/>}, |A beágyazási szinteket elválasztó karakter.<br/><br/>Az alapértelmezett `.` érték (pont). |A beágyazási szinteket elválasztó karakter. <br/><br/>Az alapértelmezett `.` érték (pont). |
| writeBatchSize |Az Azure Cosmos DB szolgáltatáshoz a dokumentumok létrehozásához szükséges párhuzamos kérelmek száma.<br/><br/>A teljesítmény finomhangolásával adatok másolása,/a Cosmos DB ezzel a tulajdonsággal. A writeBatchSize növelése kor jobb teljesítményre számíthat, mert a Rendszer további párhuzamos kéréseket küld a Cosmos DB-nek. Azonban el kell kerülnie a szabályozást, amely a következő hibaüzenetet dobhatja: "A kérelmek aránya nagy".<br/><br/>A szabályozást számos tényező határozza meg, beleértve a dokumentumok méretét, a dokumentumokban lévő kifejezések számát, a célgyűjtés indexelési politikáját stb. Másolási műveletekhez használhatja a jobb gyűjtemény (pl. S3), hogy a legtöbb átviteli rendelkezésre álló (2500 kérelem egység/másodperc). |Egész szám |Nem (alapértelmezett: 5) |
| writeBatchTimeout |Várjon időt a művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: "00:30:00" (30 perc). |Nem |

## <a name="importexport-json-documents"></a>JSON-dokumentumok importálása/exportálása
Ezzel a Cosmos DB csatlakozóval könnyedén

* Importálja a Különböző forrásokból származó JSON-dokumentumokat a Cosmos DB-ba, beleértve az Azure Blobot, az Azure Data Lake-et, a helyszíni fájlrendszert vagy az Azure Data Factory által támogatott más fájlalapú tárolókat.
* JSON-dokumentumokat exportálhat a Cosmos DB gyűjteményből különböző fájlalapú tárolókba.
* Adatok áttelepítése két Cosmos DB-gyűjtemény között, ahogy van.

Az ilyen séma-agnotikus másolat
* A Másolás varázsló használatakor jelölje be az **"Exportálás jsonfájlokba vagy a Cosmos DB gyűjteménybe"** lehetőséget.
* JSON-szerkesztés használatakor ne adja meg a Cosmos DB adatkészlet(ek) "struktúra" szakaszát, sem a Cosmos DB forrás/fogadó "nestingSeparator" tulajdonságát a Cosmos DB forrás/fogadó másolási tevékenységben. Importálni / export JSON fájlokat, a fájltároló adatkészlet adja formátum típusát "JsonFormat", config "filePattern", és hagyja ki a többi formátum beállításait, lásd [JSON formátum](data-factory-supported-file-and-compression-formats.md#json-format) szakasz a részleteket.

## <a name="json-examples"></a>Példák json-példák
Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatják, hogyan másolhat adatokat az Azure Cosmos DB és az Azure Blob Storage. Azonban az adatok **közvetlenül** a források bármelyikéből átmásolhatók az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott fogadókbármelyikébe az Azure Data Factory másolási tevékenység használatával.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Példa: Adatok másolása az Azure Cosmos DB-ről az Azure Blobba
Az alábbi minta a következőket mutatja:

1. [DocumentDb](#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [DocumentDbCollection](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [A DocumentDbCollectionSource](#copy-activity-properties) és [a BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta az Azure Cosmos DB-ben az Azure Blobba másolja az adatokat. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure Cosmos DB kapcsolt szolgáltatás:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob storage-hoz csatolt szolgáltatás:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Document DB bemeneti adatkészlet:**

A minta feltételezi, hogy egy **Személy** nevű gyűjtemény egy Azure Cosmos DB adatbázisban.

"külső": "true" beállítás és az Azure Data Factory szolgáltatás, amely szerint a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított externalData szabályzatadatainak megadása.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure Blob kimeneti adatkészlet:**

Az adatok at óránként egy új blobba másolja a blob elérési útja, amely az adott dátumidőt óra részletességgel tükrözi.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Minta JSON-dokumentum a Személyek gyűjteményében egy Cosmos DB adatbázisban:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
A Cosmos DB támogatja a dokumentumok lekérdezését egy SQL-hez hasonló szintaxissal a hierarchikus JSON-dokumentumokkal szemben.

Példa:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

A következő folyamat adatokat másol az Azure Cosmos DB-adatbázisban lévő személygyűjteményből egy Azure blobba. A másolási tevékenység részeként a bemeneti és kimeneti adatkészletek meg vannak adva.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Példa: Adatok másolása az Azure Blobból az Azure Cosmos DB-be
Az alábbi minta a következőket mutatja:

1. DocumentDb típusú csatolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. DocumentDbCollection típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és DocumentDbCollectionSink-t használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta adatokat másol az Azure Blobból az Azure Cosmos DB-be. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure Blob storage-hoz csatolt szolgáltatás:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Cosmos DB kapcsolt szolgáltatás:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob bemeneti adatkészlet:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB kimeneti adatkészlet:**

A minta adatokat másol egy "Személy" nevű gyűjteménybe.

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
A következő folyamat adatokat másol az Azure Blobból a Felelős kötet-ház a Személyek gyűjteményébe. A másolási tevékenység részeként a bemeneti és kimeneti adatkészletek meg vannak adva.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Ha a mintablob bemenete

```
1,John,,Doe
```
Ezután a kimeneti JSON a Cosmos DB lesz, mint:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Az Azure Cosmos DB egy NoSQL-áruház A JSON-dokumentumok, ahol beágyazott struktúrák megengedettek. Az Azure Data Factory lehetővé teszi a felhasználó számára, hogy a hierarchiát **a beágyazássaljelölőss**, azaz "" jelöli. ebben a példában. Az elválasztóval a másolási tevékenység a tábladefinícióban szereplő "Name.First", "Name.Middle" és "Name.Last" (Név) objektumhárom gyermekelemmel hozza létre az Első, a Középső és az Utolsó elemet a tábladefinícióban szereplő "Name.First", "Name.Middle" és "Name.Last" szerint.

## <a name="appendix"></a>Függelék
1. **Kérdés:** Támogatja a Másolási tevékenység a meglévő bejegyzések frissítését?

    **Válasz:** nem.
2. **Kérdés:** Hogyan egy újrapróbálkozás egy példányt az Azure Cosmos DB foglalkozik a már másolt rekordok?

    **Válasz:** Ha a rekordok "ID" mezővel rendelkeznek, és a másolási művelet ugyanazzal az azonosítóval próbál beszúrni egy rekordot, a másolási művelet hibát jelez.
3. **Kérdés:** Támogatja a Data Factory [a tartomány- vagy kivonatalapú adatparticionálást?](../../cosmos-db/sql-api-partition-data.md)

    **Válasz:** nem.
4. **Kérdés:** Megadhatok egynél több Azure Cosmos DB-gyűjteményt egy táblához?

    **Válasz:** nem. Jelenleg csak egy gyűjtemény adható meg.

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
