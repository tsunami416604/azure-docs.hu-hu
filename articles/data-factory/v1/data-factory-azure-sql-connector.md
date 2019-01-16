---
title: Adatok másolása és- tárolókról az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat és-tárolókról az Azure SQL Database, Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 13e00acaf287a9e153aaa8e5ce7d630f8d198f02
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330415"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Adatok másolása, és az Azure Data Factory használatával az Azure SQL Database-ből
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-azure-sql-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-sql-database.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben az Azure SQL Database-összekötő](../connector-azure-sql-database.md).

Ez a cikk ismerteti az Azure Data Factory a másolási tevékenység használatával adatok importálására és az Azure SQL Database-ből. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolja **Azure SQL Database-ből** tárolja, a következő adatokat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatokat másolja **az Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típus
Az Azure SQL Database-összekötő az egyszerű hitelesítést támogatja.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az Azure SQL Database és az adatok áthelyezéséhez az más eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több folyamattal is tartalmazhat.
2. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához. Például ha a másolt adatokat egy Azure blob storage-ból egy Azure SQL Database-adatbázishoz, létrehozhat két társított szolgáltatást az Azure storage-fiók és Azure SQL database összekapcsolása a data factory. Konkrétan az Azure SQL Database társított szolgáltatás tulajdonságait, lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.
3. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. A példában az előző lépésben említett megadja a blobtárolót és a bemeneti adatokat tartalmazó mappát egy adatkészletet hoz létre. És adja meg az SQL-táblát az Azure SQL Database tárolja az adatokat a blob storage-tárhelyéből, egy másik adatkészletet hoz létre. Az Azure Data Lake Store adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakaszban.
4. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. A példában azt korábban említettük használhat BlobSource a forrás-és SqlSink fogadóként a másolási tevékenység. Hasonlóképpen, az Azure SQL Database-ből az Azure Blob Storage másolása, SqlSource és a használata BlobSink a másolási tevékenység. Másolási tevékenység tulajdonságai konkrétan az Azure SQL Database, lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakaszban. A forrás vagy a fogadó adattár használatát részletekért kattintson a hivatkozásra az adattár az előző szakaszban.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban. Az adatok másolása és- tárolókról az Azure SQL Database használt Data Factory-entitások JSON-definíciói minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-sql-database) című szakaszát.

Az alábbi szakaszok nyújtanak az Azure SQL Database adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Egy Azure SQL társított szolgáltatás egy Azure SQL database, az adat-előállítóhoz. Az alábbi táblázatban az adott Azure SQL társított szolgáltatás JSON-elemek leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **AzureSqlDatabase** |Igen |
| kapcsolati Sztringje |Adja meg a connectionString tulajdonság az Azure SQL Database-példányhoz való kapcsolódáshoz szükséges adatokat. Csak az alapszintű hitelesítést is támogatja. |Igen |

> [!IMPORTANT]
> Konfigurálása [Azure SQL Database-tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) a kiszolgáló [a kiszolgálóhoz való hozzáféréshez Azure-szolgáltatások engedélyezése](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Emellett az adatokat az Azure SQL Database kívül az Azure például a data factory-átjáró a helyszíni adatforrásokból származó másolása, beállítható, megfelelő IP-címtartományt, a gép, amely adatokat küld az Azure SQL Database.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Adjon meg egy adatkészletet, amely egy Azure SQL database-ben a bemeneti vagy kimeneti adatokat jelöli, állítsa be a type tulajdonság, az adatkészlet: **AzureSqlTable**. Állítsa be a **linkedServiceName** tulajdonságot az adatkészlet neve lesz az Azure SQL társított szolgáltatás.

Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (az Azure SQL, az Azure blob-, az Azure table-, stb.).

A typeProperties szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyére vonatkozó információkat. A **typeProperties** szakasz az adatkészlet típusa **AzureSqlTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet az Azure SQL Database-példányban, amelyek a társított szolgáltatás neve hivatkozik. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és a házirend az összes típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység csak egy bemenettel rendelkezik, és csak egy kimenetet.

Mivel a tulajdonságok érhetők el a **typeProperties** a tevékenység szakaszban tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

Adatok áthelyezéséhez az Azure SQL database-ből, a forrás típusaként állítsa be a másolási tevékenység **SqlSource**. Ehhez hasonlóan adatok áthelyezéséhez az Azure SQL Database-adatbázishoz, állítsa be a fogadó típusa a másolási tevékenység **SqlSink**. Ez a szakasz SqlSource és az SqlSink által támogatott tulajdonságok listáját tartalmazza.

### <a name="sqlsource"></a>SqlSource
A másolási tevékenység, ha a forrás típusa **SqlSource**, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. |A tárolt eljárás neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység a lekérdezés fut az Azure SQL Database forrás, az adatok beolvasásához. Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az adatkészlet JSON struktúra szakaszában meghatározott oszlopokat segítségével lekérdezés összeállításához kell használni (`select column1, column2 from mytable`) az Azure SQL-adatbázis futtatásához. Ha az adatkészlet definíciója nem rendelkezik a struktúrát, az összes oszlop ki van jelölve, a táblából.

> [!NOTE]
> Ha használ **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy értéket a **tableName** tulajdonságot az adatkészlet JSON. Nincsenek nem lettek elvégezve, mint ez a táblázat azonban ellenőrzések.
>
>

### <a name="sqlsource-example"></a>SqlSource example

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**A tárolt eljárás definíciója:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |
| WriteBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a másolási tevékenység végrehajtásához úgy, hogy az adott szeletre vonatkozó adatok törlődnek. További információkért lásd: [reprodukálható másolatot](#repeatable-copy). |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg az automatikusan generált szelet azonosítóval, amelyet egy adott szeletre mikor futtassa újra a adatainak töltse ki a másolási tevékenység oszlop nevét. További információkért lásd: [reprodukálható másolatot](#repeatable-copy). |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárást, amely meghatározza, hogyan alkalmazhatja a forrásadatok céloldali táblához, pl. do upserts vagy a saját üzleti logika átalakító neve. <br/><br/>Megjegyzés: Ez a tárolt eljárás lesz **kötegenként meghívása**. Ha azt szeretné, hogy csak egyszer fut, és nem a forrásadatokat, például törlés/truncate, használja a művelet elvégzéséhez `sqlWriterCleanupScript` tulajdonság. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típusú név a tárolt eljárás használható. Másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. |Egy tábla típusú név. |Nem |

#### <a name="sqlsink-example"></a>SqlSink példa

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Az adatok másolása az SQL Database szolgáltatásba és onnan JSON-példák
Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure SQL Database és az Azure Blob Storage mutatnak. Azonban az adatok átmásolhatók **közvetlenül** bármelyik források a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Példa: Adatok másolása az Azure SQL Database-ből az Azure Blobba
Azonos határozza meg a következő Data Factory-entitások:

1. A társított szolgáltatás típusa [AzureSqlDatabase](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureSqlTable](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) egy másolási tevékenységgel, amely használja [SqlSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta másol idősorozat-adatok (óránként, naponta stb.) az Azure SQL database egyik táblájába blob óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure SQL Database-beli társított szolgáltatást:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Tekintse meg a [Azure SQL társított szolgáltatás](#linked-service) listája a társított szolgáltatás által támogatott tulajdonságok szakaszában.

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
Tekintse meg a [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) a társított szolgáltatás által támogatott tulajdonságok listája a cikk.


**Az Azure SQL bemeneti adatkészlet:**

A minta azt feltételezi, létrehozott egy táblát "MyTable" az Azure SQL-ben és a egy idősorozat-adatok a "timestampcolumn" nevű oszlopot tartalmaz.

Beállítás az "external": "true" tájékoztatja az Azure Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

Tekintse meg a [Azure SQL-adatkészlet típus tulajdonságainak](#dataset) ehhez az adathalmaztípushoz által támogatott tulajdonságok szakaszában listája.

**Azure blobkimeneti adatkészlet:**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Tekintse meg a [Azure Blob-adatkészlet típus tulajdonságait](data-factory-azure-blob-connector.md#dataset-properties) ehhez az adathalmaztípushoz által támogatott tulajdonságok szakaszában listája.

**Az SQL-forrás és fogadó Blob egy folyamatot egy másolási tevékenységgel:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **SqlSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
A példában **sqlReaderQuery** a SqlSource van megadva. A másolási tevékenység a lekérdezés fut az Azure SQL Database forrás, az adatok beolvasásához. Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az adatkészlet JSON struktúra szakaszában meghatározott oszlopokat futtatásához az Azure SQL Database-lekérdezés összeállításához használja. Például: `select column1, column2 from mytable`. Ha az adatkészlet definíciója nem rendelkezik a struktúrát, az összes oszlop ki van jelölve, a táblából.

Tekintse meg a [Sql-forrás](#sqlsource) szakasz és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) SqlSource és BlobSink által támogatott tulajdonságok listáját.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Példa: Adatok másolása az Azure-Blobból Azure SQL Database-adatbázishoz
A minta az alábbi Data Factory-entitások határozza meg:

1. A társított szolgáltatás típusa [AzureSqlDatabase](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureSqlTable](#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlSink](#copy-activity-properties).

A minta másolatokat idősorozat-adatok (óránként, naponta stb.) az Azure-blobból egy Azure SQL-táblára adatbázis óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az Azure SQL társított szolgáltatást:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Tekintse meg a [Azure SQL társított szolgáltatás](#linked-service) listája a társított szolgáltatás által támogatott tulajdonságok szakaszában.

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
Tekintse meg a [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) a társított szolgáltatás által támogatott tulajdonságok listája a cikk.


**Azure blobbemeneti adatkészlet:**

Adatok felülettől új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útjának év, hónap és nap részét a kezdési időpont és fájlnevet a kezdő időpontja óra részét használja. "external": "true" beállítással, hogy ez a táblázat a data factory a külső, és nem egy adat-előállító tevékenység által előállított arról tájékoztatja a Data Factory szolgáltatásban.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
Tekintse meg a [Azure Blob-adatkészlet típus tulajdonságait](data-factory-azure-blob-connector.md#dataset-properties) ehhez az adathalmaztípushoz által támogatott tulajdonságok szakaszában listája.

**Az Azure SQL Database kimeneti adatkészlet:**

A minta adatokat másol egy Azure SQL-ben "MyTable" nevű táblát. A tábla létrehozása az Azure SQL-azonos számú oszlopot a Blob CSV-fájl tartalmazza a várt módon. Új sorok hozzáadódnak a tábla minden órában.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Tekintse meg a [Azure SQL-adatkészlet típus tulajdonságainak](#dataset) ehhez az adathalmaztípushoz által támogatott tulajdonságok szakaszában listája.

**Fogadó-Blob-forrás- és SQL rendelkező folyamatot egy másolási tevékenységgel:**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **BlobSource** és **fogadó** típusa **SqlSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Tekintse meg a [Sql-fogadó](#sqlsink) szakasz és [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) SqlSink és a BlobSource által támogatott tulajdonságok listáját.

## <a name="identity-columns-in-the-target-database"></a>A céladatbázis azonosító oszlop
Ez a szakasz egy példát biztosít az adatok másolása a forrástábla egy identitásoszlop nélkül, az identity oszlopot tartalmazó táblát.

**Forrástábla:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Céltábla:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Figyelje meg, hogy a céloldali tábla rendelkezik-e egy identitásoszlop.

**Forrás adatkészlet JSON-definíció**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Cél adatkészlet JSON-definíció**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Figyelje meg, hogy a forrás és cél táblaként eltérő sémával rendelkezik (cél van egy további oszlop identitás). Ebben az esetben meg kell adnia **struktúra** tulajdonság a célként megadott adatkészlet definíciójában, amely nem tartalmazza az identitásoszlop.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Az SQL-fogadó tárolt eljárás meghívása
A folyamat egy másolási tevékenység fogadó SQL tárolt eljárás meghívása egy példa: [a fogadó SQL tárolt eljárás meghívása a másolási tevékenység](data-factory-invoke-stored-procedure-from-copy-activity.md) cikk.

## <a name="type-mapping-for-azure-sql-database"></a>Írja be az Azure SQL Database-leképezés
Említetteknek megfelelően az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) cikk másolási tevékenység hajt végre automatikus típuskonverziók a fogadó-típusokat az alábbi 2. lépés – a módszert használja a forrás típusa:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Helyez át adatokat, és az Azure SQL Database-ből, ha a következő hozzárendeléseket használják az SQL-típus .NET típusát, és ez fordítva is igaz. A leképezés megegyezik az SQL Server adattípus-hozzárendelés az ADO.NET esetén.

| SQL Server adatbázismotor típusa | .NET-keretrendszer típusa |
| --- | --- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai |
| CHAR |Karakterlánc, Char] |
| dátum |DateTime |
| Dátum és idő |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| tizedes tört |tizedes tört |
| A FILESTREAM attribútum (varbinary(max)) |Byte] |
| Lebegőpontos |Dupla |
| image |Byte] |
| int |Int32 |
| költséget takaríthat meg |tizedes tört |
| nchar |Karakterlánc, Char] |
| ntext |Karakterlánc, Char] |
| numerikus |tizedes tört |
| nvarchar |Karakterlánc, Char] |
| valódi |Önálló |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| pénz |tizedes tört |
| sql_variant |Objektum * |
| szöveg |Karakterlánc, Char] |
| time |Időtartam |
| időbélyeg |Byte] |
| tinyint |Bájt |
| UniqueIdentifier |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>A fogadó-oszlopok térkép forrása
Fogadó-adatkészlet oszlopaihoz forrásadatkészlet leképezés oszlopai kapcsolatos további információkért lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Reprodukálható másolatot
Adatok másolása az SQL Server-adatbázist, amikor a másolási tevékenység adatokat fűz hozzá a fogadó tábla alapértelmezés szerint. Ehelyett hajtsa végre az UPSERT, lásd: [Repeatable írni az SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) cikk.

Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
