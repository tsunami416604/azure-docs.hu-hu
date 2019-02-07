---
title: Adatok áthelyezése és- tárolókról az Azure Cosmos DB |} A Microsoft Docs
description: Ismerje meg, hogyan át az adatokat, és- tárolókról az Azure Data Factory használatával az Azure Cosmos DB-gyűjtemények
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 54e78496b98fffefd2932a2ce7625f8b325fb32a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812650"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Adatok importálására és az Azure Cosmos DB az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-azure-documentdb-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben az Azure Cosmos DB-összekötő](../connector-azure-cosmos-db.md).

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factoryban adatáthelyezést és-tárolókról az Azure Cosmos DB (az SQL API-t). Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

Másolhatja az adatokat bármely támogatott forrás adattárból az Azure Cosmos DB-hez vagy az Azure Cosmos DB bármely támogatott fogadó adattárba. A másolási tevékenység által források vagy fogadóként támogatott adattárak listáját lásd: a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla.

> [!IMPORTANT]
> Az Azure Cosmos DB-összekötő csak az SQL API támogatja.

Az adatok másolása – van- tárolókról a JSON-fájlokat, vagy egy másik Cosmos DB-gyűjtemények, lásd: [Import/Export JSON-dokumentumok](#importexport-json-documents).

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok és-tárolókról az Azure Cosmos DB áthelyezéséhez a különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához.
2. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli.
3. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban. A Data Factory-entitások, amelyek/Cosmos DB az adatok másolása JSON-definíciói minták, lásd: [JSON példák](#json-examples) című szakaszát.

A következő szakaszok a Cosmos DB-hez adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletek:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázatban az adott Azure Cosmos DB-hez társított szolgáltatás JSON-elemek leírását.

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **DocumentDb** |Igen |
| kapcsolati Sztringje |Azure Cosmos DB-adatbázishoz való kapcsolódáshoz szükséges információkat adják meg. |Igen |

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
Szakaszok & adatkészletek definiálását tulajdonságainak teljes listájáért tekintse meg a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A typeProperties szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyére vonatkozó információkat. A typeProperties szakasz az adatkészlet típusa **DocumentDbCollection** a következő tulajdonságokkal rendelkezik.

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| collectionName |A Cosmos DB-dokumentumgyűjteményt neve. |Igen |

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
### <a name="schema-by-data-factory"></a>Adat-előállítók által séma
A séma nélküli adattárakban, mint az Azure Cosmos DB a Data Factory szolgáltatás kikövetkezteti a használandó sémát az alábbi módszerek egyikével:

1. Ha az adatok struktúráját használatával adja meg a **struktúra** tulajdonságot az adatkészlet-definícióban, a Data Factory szolgáltatás figyelembe veszi a sémát, ez a struktúra. Ebben az esetben ha egy sort tartalmaz egy oszlop értékét, null értékű biztosítjuk.
2. Ha nincs megadva az adatok struktúráját használatával a **struktúra** tulajdonságot az adatkészlet-definícióban, a Data Factory szolgáltatás kikövetkezteti a használandó sémát az első sor az adatok használatával. Ebben az esetben ha az első sor nem tartalmazza a teljes séma, egyes oszlopok nem érhető el a másolási művelet eredményét.

Ezért sémamentes adatforrások esetében az ajánlott eljárás, hogy adja meg az adatok a struktúra a **struktúra** tulajdonság.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listájáért tekintse meg a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység csak egy bemenettel rendelkezik, és csak egy kimenetet.

A tevékenység a typeProperties szakasz tulajdonságai másrészről a tevékenységek minden típusának eltérőek lehetnek, és a másolási tevékenység esetén azok eltérőek lehetnek attól függően, a forrásként és fogadóként típusú.

Ha forrás típusa másolási tevékenység esetén **DocumentDbCollectionSource** érhetők el a következő tulajdonságokat a **typeProperties** szakaszban:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| lekérdezés |Adja meg a lekérdezés adatokat olvasni. |Lekérdezés-karakterlánc, az Azure Cosmos DB által támogatott. <br/><br/>Például: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nem <br/><br/>Ha nincs megadva, az SQL-utasítást, amely hajtja végre: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Jelzi, hogy a dokumentum van beágyazva speciális karakter |Bármely karakter. <br/><br/>Azure Cosmos DB a NoSQL-alapú tárolót a JSON-dokumentumok, amelyben beágyazott struktúrák engedélyezettek. Az Azure Data Factory lehetővé teszi, hogy a felhasználó nestingSeparator, amely használatával a hierarchia jelölésére "." a fenti példákban. Az elválasztó, a másolási tevékenység hoz létre három gyermeket elem "Name" objektum először, középső és az utolsó, "Name.First", "Name.Middle" és "Name.Last" tábla definíciójában. |Nem |

**DocumentDbCollectionSink** támogatja a következő tulajdonságokkal:

| **Tulajdonság** | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| nestingSeparator |A forrás oszlop nevét jelzi, hogy a beágyazott dokumentum egy speciális karaktert van szükség. <br/><br/>Például a fent: `Name.First` a kimeneti tábla hoz létre a következő JSON-struktúrát a Cosmos DB-dokumentumban:<br/><br/>"Name": {}<br/>    "First": "János"<br/>}, |A beágyazási szinteket elválasztó karakter.<br/><br/>Alapértelmezett érték `.` (pont). |A beágyazási szinteket elválasztó karakter. <br/><br/>Alapértelmezett érték `.` (pont). |
| WriteBatchSize |Dokumentumok létrehozása az Azure Cosmos DB szolgáltatás párhuzamos kérelmek száma.<br/><br/>A teljesítmény finomhangolására, példatípust az adatok és a Cosmos DB használatával ezt a tulajdonságot. A jobb teljesítmény várható writeBatchSize növelése, mert a Cosmos DB-hez további párhuzamos kérés lesz elküldve. Azonban, amely szabályozás elkerülése érdekében kell nagyvállalat a hibaüzenet: "Meg túl sok kérelmet adott".<br/><br/>Szabályozás számos tényezőtől, beleértve a dokumentumokat, a feltételek a dokumentumok száma méretét, indexelési szabályzat célgyűjtemény stb határoz meg. A másolási műveletek segítségével jobb gyűjtemény (például S3 szintű) rendelkezik a legtöbb számára elérhető adatátviteli mennyiség (2500 kérés kérelemegység/s). |Egész szám |Nem (alapértelmezett: 5) |
| writeBatchTimeout |Várjon, amíg a művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |

## <a name="importexport-json-documents"></a>Importálási/exportálási JSON-dokumentumok
A Cosmos DB-összekötő használatával egyszerűen

* JSON-dokumentumok importálása különböző forrásokból származó az Azure Blob, beleértve az Azure Data Lake, a helyi fájlrendszer vagy egyéb fájlalapú tárolók az Azure Data Factory által támogatott, Cosmos DB-be.
* JSON-dokumentumok exportálása a Cosmos DB collecton különböző fájlalapú tárolók.
* Adatok áttelepítése a között, két Cosmos DB-gyűjtemények-van.

Az ilyen sémafüggetlen másolási eléréséhez
* A másolás varázsló használata esetén ellenőrizze a **"exportálása – JSON-fájlokat vagy a Cosmos DB-gyűjtemény"** lehetőséget.
* Ha használja a JSON-adatok szerkesztése, ne adja meg a "struktúra" szakaszban Cosmos DB adatkészlet(ek) sem "nestingSeparator" tulajdonság a Cosmos DB forrás/fogadó a másolási tevékenység. JSON-fájlok exportálása / importálása, fájl store adatkészlet adja meg formátum típusa "JsonFormat", "filePattern" konfigurációs és hagyja ki a rest-fájlformátum beállításai, lásd: [JSON formátumban](data-factory-supported-file-and-compression-formats.md#json-format) szakaszban talál.

## <a name="json-examples"></a>JSON-példák
Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure Cosmos DB és az Azure Blob Storage mutatnak. Azonban az adatok átmásolhatók **közvetlenül** egyetlen forrás sem a conditions stated above fogadóként [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Példa: Adatok másolása az Azure Cosmos DB az Azure Blob
Az alábbi példában látható:

1. A társított szolgáltatás típusa [DocumentDb](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [DocumentDbCollection](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [DocumentDbCollectionSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta adatokat másol az Azure Cosmos DB az Azure-Blobba. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure Cosmos DB-beli társított szolgáltatást:**

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
**Az Azure Blob storage-beli társított szolgáltatást:**

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
**Az Azure Document DB bemeneti adatkészlet:**

A példa feltételezi, hogy a gyűjteményt **személy** egy Azure Cosmos DB-adatbázisban.

Beállítás az "external": "true", és externalData házirend adatait az Azure Data Factory szolgáltatásban, hogy a tábla a data factory a külső, és nem egy adat-előállító tevékenység által előállított megadásával.

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

**Azure blobkimeneti adatkészlet:**

Adatokat másolja egy új blob minden órában a BLOB szótárral kezdheti az adott dátum/idő óra granularitással az elérési útját.

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
Példa JSON-dokumentumok egy Cosmos DB-adatbázisban a személy gyűjtemény:

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
A cosmos DB támogatja a dokumentumok lekérdezését egy SQL-szintaxis típusú hierarchikus JSON-dokumentumokon végzett.

Példa:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

A következő folyamat adatokat másol egy Azure-blobból az Azure Cosmos DB-adatbázisban a személy gyűjteményből. Része a másolási tevékenység a bemeneti és kimeneti adatkészleteket lett megadva.

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
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Példa: Adatok másolása Azure blobból az Azure Cosmos DB-hez
Az alábbi példában látható:

1. A DocumentDb típusú társított szolgáltatás.
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) DocumentDbCollection típusú.
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és DocumentDbCollectionSink.

A minta adatokat másol az Azure blob az Azure Cosmos DB-hez. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure Blob storage-beli társított szolgáltatást:**

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
**Az Azure Cosmos DB-beli társított szolgáltatást:**

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
**Azure blobbemeneti adatkészlet:**

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
A következő folyamat adatokat másol az Azure-Blobból a személy gyűjteményhez, a Cosmos DB-ben. Része a másolási tevékenység a bemeneti és kimeneti adatkészleteket lett megadva.

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
Ha a minta blob bemeneti van, mint

```
1,John,,Doe
```
A kimenet a Cosmos dB-ben JSON lesz majd megfelelően:

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
Azure Cosmos DB a NoSQL-alapú tárolót a JSON-dokumentumok, amelyben beágyazott struktúrák engedélyezettek. Az Azure Data Factory lehetővé teszi, hogy a felhasználó keresztül hierarchia jelölésére **nestingSeparator**, amely "." Ebben a példában. Az elválasztó, a másolási tevékenység hoz létre három gyermeket elem "Name" objektum először, középső és az utolsó, "Name.First", "Name.Middle" és "Name.Last" tábla definíciójában.

## <a name="appendix"></a>Függelék
1. **Kérdés:** A másolási tevékenység támogatási a meglévő rekordok módosítása nem?

    **Válasz:** Nem.
2. **Kérdés:** Hogyan foglalkozik az Azure Cosmos DB-példány ismételt már másolt rekordok?

    **Válasz:** Ha a rekordok rendelkezik egy "ID" mezőt, és a másolási művelet próbál beszúrni egy rekord ugyanazzal az azonosítóval, a másolási művelet hibát jelez.
3. **Kérdés:** A Data Factory támogatja [tartomány vagy az ujjlenyomat-alapú adatparticionálás](../../cosmos-db/sql-api-partition-data.md)?

    **Válasz:** Nem.
4. **Kérdés:** Meghatározható, hogy a táblázat egynél több Azure Cosmos DB-gyűjtemények?

    **Válasz:** Nem. Jelenleg csak egy gyűjteményhez adható meg.

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
