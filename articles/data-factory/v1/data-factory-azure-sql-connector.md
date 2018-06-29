---
title: Adatok másolása az Azure SQL Database |} Microsoft Docs
description: 'Útmutató: Azure SQL Database szolgáltatáshoz Azure Data Factory és a-adatok másolása.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d010fd90d96409b262be59f1db4fac9e4cec835c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051559"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Másolja az adatokat, és az Azure SQL adatbázis Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-azure-sql-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-azure-sql-database.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [Azure SQL Database-összekötőt, a V2](../connector-azure-sql-database.md).

Ez a cikk ismerteti, hogyan a másolási tevékenység során az Azure Data Factory és az Azure SQL Database áthelyezni az adatokat. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést.  

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolhat **az Azure SQL Database** tárolja a következő adatokat:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Adatok másolása a következő adatokat tárolja **az Azure SQL Database**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típushoz
Az Azure SQL Database-összekötő az egyszerű hitelesítést támogatja.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely helyezi át az adatokat belőle egy Azure SQL Database különböző eszközök/API-k használatával létrehozhat egy folyamatot.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre egy **adat-előállító**. Egy adat-előállító tartalmazhat egy vagy több folyamatok. 
2. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory. Például ha a másolt adatok az az Azure blob storage Azure SQL-adatbázishoz, hoz létre az Azure storage-fiók és az Azure SQL adatbázis összekapcsolása a data factory két összekapcsolt szolgáltatások. Az Azure SQL Database jellemző csatolt szolgáltatás tulajdonságait, lásd: [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz. 
3. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. A példában az előző lépésben említett hozzon létre egy adatkészlet adja meg a blob-tároló és a bemeneti adatokat tartalmazó mappát. És hoz létre, ha meg szeretné adni az SQL-tábla az Azure SQL-adatbázis, amely tárolja az adatokat a blob-tároló átmásolja egy másik DataSet adatkészletben. Azure Data Lake Store adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. A korábban említett példában BlobSource forrás-és SqlSink akár használhatja a fogadó a másolási tevékenységhez. Ehhez hasonlóan az Azure SQL Database az Azure Blob Storage másolása, használható SqlSource és BlobSink a másolási tevékenység. Az Azure SQL Database adott tevékenység Tulajdonságok másolása, lásd: [tevékenység Tulajdonságok másolása](#copy-activity-properties) szakasz. További részletek a tárolóban használatáról a forrás vagy a fogadó a hivatkozásra a adattároló az előző szakaszban.

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  Adatok másolása az Azure SQL adatbázis használandó adat-előállító entitások JSON-definíciók minták, lásd: [JSON példák](#json-examples-for-copying-data-to-and-from-sql-database) című szakaszát. 

A következő szakaszok részletesen bemutatják az Azure SQL Database Data Factory tartozó entitások meghatározásához használt JSON tulajdonságokat: 

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
Egy Azure SQL Azure SQL-adatbázis kapcsolódó a szolgáltatás hivatkozások a data factory. Az alábbi táblázatban az adott Azure SQL társított szolgáltatásnak JSON-elemek leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **AzureSqlDatabase** |Igen |
| connectionString |Adja meg az Azure SQL Database-példányt a connectionString tulajdonság való kapcsolódáshoz szükséges adatokat. Csak az alapszintű hitelesítést is támogatja. |Igen |

> [!IMPORTANT]
> Konfigurálása [Azure SQL Database-tűzfal](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) az adatbázis-kiszolgálót [a kiszolgálóhoz való hozzáféréshez Azure-szolgáltatások engedélyezése](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Ezenkívül az adatokat az Azure SQL Database a külső Azure többek között a helyszíni adatforrásokból a data factory átjáróval másolása, az IP-címtartományt a gép, amely adatokat küld az Azure SQL Database konfigurálni.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Adja meg a bemeneti vagy kimeneti adatok Azure SQL adatbázis dataset, állítsa be a adatkészlet type tulajdonsága: **AzureSqlTable**. Állítsa be a **linkedServiceName** tulajdonsághoz a DataSet adatkészlet neve az Azure SQL társított szolgáltatásnak.  

Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (Azure SQL, az Azure blob, Azure-tábla, stb.).

A typeProperties szakasz más adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A **typeProperties** szakasz az adatkészlet típusú **AzureSqlTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet társított szolgáltatásnak Azure SQL Database-példány neve hivatkozik. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Például a nevét, leírását, valamint bemeneti és kimeneti táblák és házirend tulajdonságai minden típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység során csak egy bemenettel rendelkezik, és csak egy kimenetet.

Mivel a tulajdonságok érhetők el a **typeProperties** szakasz a tevékenység tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

Ha adatokat az Azure SQL-adatbázis, a másolási tevékenység beállítása a forrástípus **SqlSource**. Hasonlóképpen, ha adatait Azure SQL-adatbázishoz, beállítása a fogadó típusa a másolási tevékenység **SqlSink**. Ez a témakör SqlSource és SqlSink által támogatott tulajdonságokról.

### <a name="sqlsource"></a>SqlSource
A másolási tevékenység, ha az adatforrás típusú **SqlSource**, a következő tulajdonságok érhetők el **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. |A tárolt eljárás neve. Az utolsó SQL-utasítás a következő tárolt eljárást a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység fut ez a lekérdezés az adatok lekérdezése az Azure SQL Database forrása. Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az adatkészlet JSON struktúrában szakaszában meghatározott oszlopokat-lekérdezés összeállításához használt (`select column1, column2 from mytable`) az Azure SQL-adatbázis futtatásához. Az adatkészlet-definícióban nem rendelkezik a struktúra, ha minden kiválasztott oszlop. a táblából.

> [!NOTE]
> Amikor **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy értéket a **tableName** az adatkészlet JSON tulajdonság. Nincs érvényesítést hajt végre ezt a táblázatot, ha van.
>
>

### <a name="sqlsource-example"></a>SqlSource – példa

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
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |
| writeBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adja meg egy lekérdezést a másolási tevékenység végrehajtása úgy, hogy egy adott szelet adatait. További információkért lásd: [ismételhető másolási](#repeatable-copy). |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg a másolási tevékenység során automatikusan létrejön szelet azonosítóval, amely távolítja el az adatokat egy adott szelet, amikor futtassa újra a kitöltésének oszlop nevét. További információkért lásd: [ismételhető másolási](#repeatable-copy). |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárás, amely meghatározza, hogyan alkalmazhat forrásadatok a céloldali tábla, pl. do upserts vagy a saját üzleti logikát használó átalakító neve. <br/><br/>Megjegyzés: Ez a tárolt eljárás fog **kötegenként meghívott**. Ha művelet, amelynek csak egyszer futnak, és érinti a elvégezni a segítségével például törlés/truncate forrásadatok használja szeretné `sqlWriterCleanupScript` tulajdonság. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |
| sqlWriterTableType |Adjon meg egy tábla típus a következő tárolt eljárás használható. Másolási tevékenység elérhetővé teszi az adatok áthelyezése egy ideiglenes táblát, amely a táblatípus. Tárolt eljárás kódot is majd egyesítheti az adatokat, a meglévő adatok másolásának. |Egy tábla környezettípus nevét. |Nem |

#### <a name="sqlsink-example"></a>SqlSink – példa

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>Adatok másolása és az SQL-adatbázis a JSON példák
Az alábbi példák megadják minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Adatok másolása az Azure SQL Database és az Azure Blob Storage mutatnak. Azonban az adatok átmásolhatók **közvetlenül** a forrásokban, sem a megadott nyelő [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Példa: Adatok másolása az Azure SQL Database az Azure-Blobba
A következő adat-előállító entitások azonos meghatározása:

1. A társított szolgáltatás típusa [AzureSqlDatabase](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureSqlTable](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) , a másolási tevékenység által használt [SqlSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta idősorozat adatainak másolása (óránként, naponta, stb.) az Azure SQL-adatbázis egy táblából egy blobba óránként. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.  

**A társított szolgáltatásnak Azure SQL Database:**

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
Tekintse meg a [Azure SQL társított szolgáltatást](#linked-service) szolgáltatásnak által támogatott tulajdonságokról szakasza listája.

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
Tekintse meg a [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) cikkében listája a társított szolgáltatás által támogatott tulajdonságokról.


**Az Azure SQL bemeneti adatkészlet:**

A minta azt feltételezi, hogy létrehozott egy tábla "MyTable" Azure SQL, egy "timestampcolumn" nevű adatsorozat időadatok oszlopot tartalmaz.

"External" beállítása: "true" arról értesíti az Azure Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

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

Tekintse meg a [Azure SQL dataset típustulajdonságokat](#dataset) ehhez az adathalmaztípushoz által támogatott tulajdonságokról szakasza listája.  

**Az Azure Blob kimeneti adatkészlet:**

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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
Tekintse meg a [Azure-Blob adatkészletet típustulajdonságokat](data-factory-azure-blob-connector.md#dataset-properties) ehhez az adathalmaztípushoz által támogatott tulajdonságokról szakasza listája.  

**A másolási tevékenység során az SQL-forrás és fogadó Blob egy folyamaton belül:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **SqlSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

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
A példában **sqlReaderQuery** a SqlSource van megadva. A másolási tevékenység fut ez a lekérdezés az adatok lekérdezése az Azure SQL Database forrása. Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az adatkészlet JSON struktúrában szakaszában meghatározott oszlopokat futtatni az Azure SQL adatbázis-lekérdezés összeállításához használt. Például: `select column1, column2 from mytable`. Az adatkészlet-definícióban nem rendelkezik a struktúra, ha minden kiválasztott oszlop. a táblából.

Tekintse meg a [Sql-forrás](#sqlsource) szakasz és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) SqlSource és BlobSink által támogatott tulajdonságok listája.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Példa: Adatok másolása az Azure Blob az Azure SQL adatbázis
A minta a következő adat-előállító entitások határozza meg:  

1. A társított szolgáltatás típusa [AzureSqlDatabase](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureSqlTable](#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlSink](#copy-activity-properties).

A minta másolatok idősorozat adatok (óránként, naponta, stb.) az Azure blob-egy táblához, az Azure SQL adatbázis-óránként. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**Az Azure SQL társított szolgáltatásnak:**

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
Tekintse meg a [Azure SQL társított szolgáltatást](#linked-service) szolgáltatásnak által támogatott tulajdonságokról szakasza listája.

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
Tekintse meg a [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) cikkében listája a társított szolgáltatás által támogatott tulajdonságokról.


**Az Azure Blob bemeneti adatkészletet:**

Adatok van felvett egy új blobból minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja év, hónap és nap részét kezdési idejét, valamint fájl nevét a kezdő időpontja óra részét. "external": "true" beállítás arról értesíti az, hogy ezt a táblázatot az adat-előállítóban külső, és egy tevékenység adat-előállító nem hozzák a Data Factory szolgáltatásnak.

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
Tekintse meg a [Azure-Blob adatkészletet típustulajdonságokat](data-factory-azure-blob-connector.md#dataset-properties) ehhez az adathalmaztípushoz által támogatott tulajdonságokról szakasza listája.

**Az Azure SQL Database kimeneti adatkészlet:**

A minta másolja az adatokat az Azure SQL "MyTable" nevű tábla. A tábla létrehozása az azonos számú oszlopot az Azure SQL-ben a Blob CSV-fájl tartalmazza a várt módon. Új sorok hozzáadásakor a tábla minden órában.

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
Tekintse meg a [Azure SQL dataset típustulajdonságokat](#dataset) ehhez az adathalmaztípushoz által támogatott tulajdonságokról szakasza listája.

**A másolási tevékenység során a Blob-forrás és fogadó SQL-feldolgozási folyamat:**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **BlobSource** és **fogadó** típusúra **SqlSink**.

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
Tekintse meg a [Sql fogadó](#sqlsink) szakasz és [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) SqlSink és BlobSource által támogatott tulajdonságok listája.

## <a name="identity-columns-in-the-target-database"></a>A céladatbázis azonosító oszlop
Ez a szakasz egy példát biztosít arra az adatok másolása a forrástábla nélkül azonosító oszlop azonosító oszlopot tartalmazó táblát.

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
Figyelje meg, hogy a céltábla rendelkezik-e az azonosító oszlop.

**Forrás adatkészlet JSON-definícióból**

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
**Cél adatkészlet JSON-definícióból**

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

Figyelje meg, hogy a forrás és cél táblázatként különböző sémája (cél rendelkezik egy olyan további oszlop identitású). Ilyen esetben meg kell adnia **struktúra** tulajdonság az a tároló adatkészlet-definícióban, amely nem tartalmazza az identitásoszlop.

## <a name="invoke-stored-procedure-from-sql-sink"></a>A fogadó SQL tárolt eljárás meghívása
A folyamat a másolási tevékenység SQL fogadó egy tárolt eljárás végrehajtásával hívásának példáért lásd: [fogadó SQL tárolt eljárás meghívása a másolási tevékenység](data-factory-invoke-stored-procedure-from-copy-activity.md) cikk. 

## <a name="type-mapping-for-azure-sql-database"></a>Írja be az Azure SQL Database leképezése
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk másolási tevékenység az eseményforrás-típusnak gyűjtése módszert használja a következő 2. lépés típusok automatikus típuskonverziók hajtja végre:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Ha az adatok mozgatása az Azure SQL Database, a következő megfeleltetéseket használ az SQL-típus a .NET-típus, és ez fordítva is igaz. Leképezése nem ugyanaz, mint az SQL Server adattípus-hozzárendelése az ADO.NET.

| SQL Server adatbázismotor típusa | .NET-keretrendszer típusa |
| --- | --- |
| bigint |Int64 |
| Bináris |Byte] |
| bit |Logikai |
| karakter |Karakterlánc, Char] |
| dátum |DateTime |
| Dátum és idő |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimális |Decimális |
| A FILESTREAM attribútum (varbinary(max)) |Byte] |
| Lebegőpontos |Dupla |
| image |Byte] |
| int |Int32 |
| pénz |Decimális |
| nchar |Karakterlánc, Char] |
| ntext |Karakterlánc, Char] |
| numerikus |Decimális |
| nvarchar |Karakterlánc, Char] |
| valós |Önálló |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| kis pénz típusú értéknél |Decimális |
| sql_variant |Objektum * |
| szöveg |Karakterlánc, Char] |
| time |A TimeSpan |
| időbélyeg |Byte] |
| tinyint |Bájt |
| egyedi azonosító |GUID |
| varbinary |Byte] |
| varchar |Karakterlánc, Char] |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Térkép forrás oszlopok gyűjtése
A forrás oszlop szerepel a fogadó dataset adatkészlet leképezési oszlopok, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Ismételhető másolása
Amikor adat másolása az SQL Server-adatbázis, a másolási tevékenység hozzáfűzi adatokat a fogadó tábla alapértelmezés szerint. Ehelyett egy UPSERT végrehajtásához tekintse meg [Repeatable írni SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) cikk. 

Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [relációs források olvasni Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
