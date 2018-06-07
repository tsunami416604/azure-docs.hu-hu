---
title: Adatok áthelyezése az Azure Cosmos DB |} Microsoft Docs
description: Megtudhatja, hogyan helyezi át az adatokat az Azure Data Factory használatához Azure Cosmos DB gyűjtemény
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d0897f73ed1a321c8287729eaba775a625f51e4d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620986"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Adatok áthelyezése, és az Azure Cosmos Adatbázisba az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-azure-documentdb-connector.md)
> * [2. verzió – Előzetes verzió](../connector-azure-cosmos-db.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [Azure Cosmos DB-összekötőt, a V2](../connector-azure-cosmos-db.md).

Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factory áthelyezni az adatokat és a Azure Cosmos DB (SQL API-t). Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést. 

Bármely támogatott forrás adattárolóból Azure Cosmos DB vagy az Azure Cosmos Adatbázisból bármely támogatott fogadó adattárolóhoz adatainak másolhatja. Adatforrások vagy mosdók a másolási tevékenység által támogatott adattárolókhoz listájáért lásd: a [adattárolókhoz támogatott](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla. 

> [!IMPORTANT]
> Azure Cosmos DB connector csak az SQL API támogatja.

Az adatok másolása-van/JSON-fájlokat vagy egy másik Cosmos DB gyűjteményhez, lásd: [Import/Export JSON-dokumentumok](#importexport-json-documents).

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, mely az adatok Azure Cosmos DB és a különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**, és **REST API**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory.
2. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. 
3. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása az Cosmos DB használandó adat-előállító entitások JSON-definíciók minták, lásd: [JSON példák](#json-examples) című szakaszát. 

A következő szakaszok részletesen bemutatják, amely segítségével határozza meg a Data Factory tartozó entitások Cosmos DB JSON-tulajdonságok: 

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
A következő táblázat a JSON-elemek szerepelnek Azure Cosmos DB kapcsolódó szolgáltatásra vonatkozó leírást.

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **DocumentDb** |Igen |
| connectionString |Adja meg Azure Cosmos DB adatbázishoz való kapcsolódáshoz szükséges adatokat. |Igen |

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
A szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listájáért tekintse meg a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A typeProperties szakasz más adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A typeProperties szakasz az adatkészlet típusú **DocumentDbCollection** a következő tulajdonságokkal rendelkezik.

| **Tulajdonság** | **Leírás** | **Szükséges** |
| --- | --- | --- |
| CollectionName |A Cosmos DB dokumentum gyűjtemény nevét. |Igen |

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
### <a name="schema-by-data-factory"></a>Adat-előállító sémája
Például az Azure Cosmos DB tárolóinak sémamentes adatokra a Data Factory szolgáltatásnak kikövetkezteti a séma a következő módszerek valamelyikével:  

1. Ha az adatok szerkezete használatával adja meg a **struktúra** tulajdonsághoz a DataSet adatkészlet-definícióban a Data Factory szolgáltatásnak eleget tegyen a séma szerint ez a struktúra. Ebben az esetben ha egy sort tartalmaz egy olyan oszlop értékét, null értékű nyújtanak az.
2. Ha nincs megadva az adatok szerkezete használatával a **struktúra** tulajdonság az adatkészlet-definícióban, a Data Factory szolgáltatásnak kikövetkezteti a séma az adatok első sora használatával. Ebben az esetben ha az első sort tartalmazza a teljes séma, néhány oszlop nem érhető el a másolási művelet eredménye.

Ezért sémamentes adatforrások, az ajánlott eljárás, hogy adja meg az adatok szerkezete a **struktúra** tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listájáért tekintse meg a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység során csak egy bemenettel rendelkezik, és csak egy kimenetet.

Tulajdonságok érhetők el a tevékenység typeProperties szakaszában viszont eltérőek a tevékenységek minden típusának, és a források és mosdók típusától függően változik másolási tevékenység esetén.

Ha forrás típusa másolási tevékenység esetén **DocumentDbCollectionSource** a következő tulajdonságok érhetők el **typeProperties** szakasz:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| lekérdezés |Adja meg a lekérdezés adatainak olvasására. |Lekérdezés-karakterlánc hossza Azure Cosmos DB által támogatott. <br/><br/>Példa: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítást, amely végrehajtja a rendszer: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Jelzi, hogy a dokumentum van beágyazva speciális karakter |Bármely karakter. <br/><br/>Azure Cosmos-adatbázis egy NoSQL-tároló JSON-dokumentumok, amelyben beágyazott struktúrákat engedélyezett. Az Azure Data Factory lehetővé teszi, hogy a felhasználó nestingSeparator, amely használatával a hierarchia jelöléséhez "." a fenti példákban. A elválasztóval, a másolási tevékenység hoz létre a "Name" objektum három gyermekek elemekkel először középső és az utolsó, "Name.First", "Name.Middle" és "Name.Last" tábla definíciójában. |Nem |

**DocumentDbCollectionSink** támogatja a következő tulajdonságokkal:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Szükséges** |
| --- | --- | --- | --- |
| nestingSeparator |A forrás oszlop nevét jelzi, hogy a beágyazott dokumentum egy különleges karakterek van szükség. <br/><br/>Például fent: `Name.First` a kimeneti táblát hoz létre a következő JSON struktúrában a Cosmos DB dokumentumban:<br/><br/>"Name": {<br/>    "Első": "John"<br/>}, |A beágyazási szinteket elválasztó karakter.<br/><br/>Alapértelmezett érték `.` (pont). |A beágyazási szinteket elválasztó karakter. <br/><br/>Alapértelmezett érték `.` (pont). |
| WriteBatchSize |Dokumentumok létrehozásához Azure Cosmos DB szolgáltatás párhuzamos kérelmek száma.<br/><br/>A teljesítmény úgy finomhangolhatja, és a Cosmos DB adatok másolásakor e tulajdonság használatával. A jobb teljesítmény számíthat, mivel több párhuzamos kérelem Cosmos DB writeBatchSize növelésével. Azonban kell elkerülheti a sávszélesség-szabályozás, amely képes throw a hibaüzenet a következő: "Ez nagy lekérő".<br/><br/>Sávszélesség-szabályozás tényező, beleértve a dokumentumok, a dokumentumok számát méretét, indexelő házirend célgyűjteményt stb határoz meg. A másolási műveletek segítségével jobban gyűjtemény (pl. S3) rendelkezik a legtöbb átviteli sebesség érhető el (2500 kérés egység/másodperc). |Egész szám |Nem (alapértelmezett: 5) |
| writeBatchTimeout |Várakozási idő a művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

## <a name="importexport-json-documents"></a>Importálási/exportálási JSON-dokumentumok
A Cosmos DB összekötő használatával egyszerűen

* Importálja a JSON-dokumentumok különböző forrásokból Cosmos DB Azure Blob, beleértve az Azure Data Lake, a helyszíni fájlrendszer vagy egyéb fájlalapú tárolók Azure Data Factory által támogatott.
* A Cosmos DB collecton JSON-dokumentumok exportálása különböző fájlalapú tárolók.
* Adatok áttelepítése között két Cosmos DB gyűjteményeket-van.

A séma-független másolat eléréséhez 
* Másolása varázsló segítségével, hogy a **"exportálni-JSON-fájlokat vagy Cosmos DB gyűjtemény"** lehetőséget.
* Ha JSON szerkesztésére használatával nem adnak meg a "structure" szakasz Cosmos DB adatkészlet(ek), sem "nestingSeparator" tulajdonságának Cosmos DB forrás/fogadó a másolási tevékenység. Importálhat / JSON fájlokba exportálását, a fájl tároló adatkészlet adja meg formázási típusa "JsonFormat", "filePattern" konfigurációs és hagyja ki a többi formázási beállítások. További információ: [JSON formátumban](data-factory-supported-file-and-compression-formats.md#json-format) részletei szakaszban.

## <a name="json-examples"></a>JSON-példák
Az alábbi példák megadják minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure Cosmos DB és az Azure Blob Storage mutatnak. Azonban az adatok átmásolhatók **közvetlenül** bármelyik bármely, a megadott nyelő források [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Példa: Adatok másolása az Azure Cosmos DB az Azure-Blobba
Az alábbi példa látható:

1. A társított szolgáltatás típusa [DocumentDb](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [DocumentDbCollection](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [DocumentDbCollectionSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta Azure Cosmos DB adatainak Azure Blob másolása. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**A társított szolgáltatásnak Azure Cosmos DB:**

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
**Az Azure Blob storage társított szolgáltatásnak:**

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
**Az Azure Document DB rendszerbe bemeneti adatkészletet:**

A példa feltételezi, hogy rendelkezik-e egy nevű gyűjtemény **személy** Azure Cosmos DB adatbázisban.

"External" beállítása: "true", és externalData házirenddel kapcsolatos információk az Azure Data Factory szolgáltatásnak, hogy a tábla külső data factoryval való, és nem egy adat-előállító tevékenység által előállított megadásával.

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

**Az Azure Blob kimeneti adatkészlet:**

Adatokat egy új blob minden órában az elérési úttal rendelkező a BLOB a megadott dátum és idő órában lépésköz tükröző másolja.

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
A minta JSON-dokumentum a személy gyűjtemény egy Cosmos DB adatbázisban:

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
Cosmos DB támogatja a JSON-dokumentumok hierarchikus over szintaxis például egy SQL használatával dokumentumok lekérdezését.

Példa: 

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

A következő adatcsatorna másol adatokat az Azure Cosmos DB adatbázisban a személy gyűjtemény egy Azure-blobot. Része a másolási tevékenység során a bemeneti és kimeneti adatkészletek van megadva.  

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Példa: Adatok másolása az Azure Blob az Azure Cosmos DB 
Az alábbi példa látható:

1. A társított szolgáltatás típusa [DocumentDb](#azure-documentdb-linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [DocumentDbCollection](#azure-documentdb-dataset-type-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).

A minta másolja az adatokat az Azure blob az Azure Cosmos-Adatbázishoz. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**Az Azure Blob storage társított szolgáltatásnak:**

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
**A társított szolgáltatásnak Azure Cosmos DB:**

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
**Az Azure Blob bemeneti adatkészletet:**

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
**Az Azure Cosmos DB kimeneti adatkészlet:**

A minta egy "Személy" nevű gyűjtemény másolja az adatokat.

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
A következő adatcsatorna másol adatokat Azure Blob a Cosmos DB a személy gyűjteménynek. Része a másolási tevékenység során a bemeneti és kimeneti adatkészletek van megadva.

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
          }
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
Ha a minta blob bemeneti van, mint

```
1,John,,Doe
```
A kimeneti JSON-t Cosmos DB lesz majd megfelelően:

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
Azure Cosmos-adatbázis egy NoSQL-tároló JSON-dokumentumok, amelyben beágyazott struktúrákat engedélyezett. Az Azure Data Factory lehetővé teszi, hogy a felhasználó keresztül hierarchia jelöléséhez **nestingSeparator**, amely "." Ebben a példában. A elválasztóval, a másolási tevékenység hoz létre a "Name" objektum három gyermekek elemekkel először középső és az utolsó, "Name.First", "Name.Middle" és "Name.Last" tábla definíciójában.

## <a name="appendix"></a>Függelék
1. **Kérdés:** a másolási tevékenység támogatási frissítés a meglévő rekordok Does?

    **Válasz:** nem.
2. **Kérdés:** hogyan működik az Azure Cosmos DB kezelésére másolatának újrapróbálkozást már másolt rekordok?

    **Válasz:** ha rögzíti egy "ID" mezőt rendelkezik, és a másolási művelet megkísérli beszúrására ugyanezzel az Azonosítóval rendelkező, a másolási művelet hibát jelez.  
3. **Kérdés:** nem támogatja a Data Factory [tartományt vagy a kivonat-alapú adatparticionálás](../../cosmos-db/sql-api-partition-data.md)?

    **Válasz:** nem.
4. **Kérdés:** állítható be egy táblához több Azure Cosmos DB gyűjtemény?

    **Válasz:** nem. Jelenleg csak egy gyűjteményhez adható meg.

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
