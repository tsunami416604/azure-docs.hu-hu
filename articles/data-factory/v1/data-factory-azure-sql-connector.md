---
title: Adatok másolása az Azure SQL-adatbázisba/onnan
description: Ismerje meg, hogyan másolhat adatokat az Azure SQL Database-ből az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7fc0b2822195d952c2a4f9c02bf3758c0e2b809a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260500"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Adatok másolása az Azure SQL Database-be és onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-sql-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-sql-database.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Azure SQL Database-összekötő a V2 alkalmazásban című témakört.](../connector-azure-sql-database.md)

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése és az Azure SQL Database.This article explain how to use the Copy Activity in Azure Data Factory to move data to and from Azure SQL Database. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

## <a name="supported-scenarios"></a>Támogatott esetek
Az Azure **SQL Database adataia** következő adattárakba másolhatók:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból másolhat adatokat **az Azure SQL Database-be:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típus
Az Azure SQL Database-összekötő támogatja az alapszintű hitelesítést.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely az adatokat áthelyezi/onnan egy Azure SQL-adatbázisból különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. Hozzon létre egy **adat-előállító**. Az adat-előállító egy vagy több folyamatot tartalmazhat.
2. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására. Ha például adatokat másol egy Azure blob storage-ból egy Azure SQL-adatbázisba, két összekapcsolt szolgáltatást hoz létre az Azure storage-fiók és az Azure SQL-adatbázis és az adat-előállító összekapcsolására. Az Azure SQL Database-re jellemző csatolt szolgáltatástulajdonságokról a [csatolt szolgáltatástulajdonságok](#linked-service-properties) című szakaszban található.
3. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. Az utolsó lépésben említett példában hozzon létre egy adatkészletet a blob tároló és a bemeneti adatokat tartalmazó mappa megadásához. És hozzon létre egy másik adatkészletet az SQL-tábla megadásához az Azure SQL-adatbázisban, amely a blob storage-ból másolt adatokat tartalmazza. Az Azure Data Lake Store-ra jellemző adatkészlet-tulajdonságokat lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. A korábban említett példában a BlobSource-ot forrásként, az SqlSink-et pedig a másolási tevékenység fogadójaként használja. Hasonlóképpen, ha az Azure SQL Database-ből az Azure Blob Storage-ba másolja, az SqlSource és a BlobSink a másolási tevékenység. Az Azure SQL Database-re jellemző másolási tevékenységtulajdonságokról a [Másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban található. Az adattár forrásként vagy fogadóként való használatáról az adattár előző szakaszában található hivatkozásra kattintva.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. Az Azure SQL-adatbázisba adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező mintákat lásd: A cikk [JSON-példái](#json-examples-for-copying-data-to-and-from-sql-database) című részében.

Az alábbi szakaszok az Azure SQL Database-re jellemző Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Egy Azure SQL-kapcsolt szolgáltatás egy Azure SQL-adatbázist kapcsol össze az adat-előállítóval. Az alábbi táblázat az Azure SQL-alapú szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot a következő re kell állítani: **AzureSqlDatabase** |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg az Azure SQL Database-példányhoz való csatlakozáshoz szükséges információkat a connectionString tulajdonsághoz. Csak az alapfokú hitelesítés támogatott. |Igen |

> [!IMPORTANT]
> Konfigurálja az [Azure SQL Database Firewall adatbázis-kiszolgálót,](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) hogy az Azure Services [hozzáférhessen a kiszolgálóhoz.](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) Továbbá ha adatokat másol az Azure SQL Database-be az Azure-on kívülről, beleértve a helyszíni adatforrások adat-elő-factory gateway, konfigurálja a megfelelő IP-címtartománya a gép, amely adatokat küld az Azure SQL Database.Additionally if you are copying data to Azure SQL Database from outside Azure including from on-premises data sources with data factory gateway, configure appropriate IP address range for the machine that is sending data to Azure SQL Database.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Ha egy Azure SQL-adatbázisban bemeneti vagy kimeneti adatokat ábrázoló adatkészletet szeretne megadni, állítsa be az adatkészlet típustulajdonságát a következőre: **AzureSqlTable**. Állítsa be az adatkészlet **linkedServiceName** tulajdonságát az Azure SQL csatolt szolgáltatás nevére.

Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A json-i adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (Azure SQL, Azure blob, Azure table stb.).

A typeProperties szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. Az **AzureSqlTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla vagy nézet neve az Azure SQL Database-példányban, amelyre a csatolt szolgáltatás hivatkozik. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Tulajdonságok, például név, leírás, bemeneti és kimeneti táblák és házirend állnak rendelkezésre minden típusú tevékenységek.

> [!NOTE]
> A Másolási tevékenység csak egy bemenetet vesz igénybe, és csak egy kimenetet hoz létre.

Mivel a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

Ha adatokat helyez át egy Azure SQL-adatbázisból, a másolási tevékenység forrástípusát **az SqlSource -ra állítja**be. Hasonlóképpen, ha adatokat helyez át egy Azure SQL-adatbázisba, a copy tevékenység fogadótípusát **az SqlSink-re állítja**be. Ez a szakasz az SqlSource és az SqlSink által támogatott tulajdonságok listáját tartalmazza.

### <a name="sqlsource"></a>SqlSource
A másolási tevékenységben, ha a forrás **sqlsource**típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Példa: `select * from MyTable`. |Nem |
| sqlReaderStoredProcedureName |Annak a tárolt eljárásnak a neve, amely adatokat olvas be a forrástáblából. |A tárolt eljárás neve. Az utolsó SQL utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |

Ha az **sqlReaderQuery** meg van adva az SqlSource-hoz, a másolási tevékenység futtatja ezt a lekérdezést az Azure SQL Database forrásában az adatok lekérni. Másik lehetőségként megadhatja a tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paramétereket vesz igénybe).

Ha nem adja meg sem az sqlReaderQuery, sem az sqlReaderStoredProcedureName értéket, a JSON adatkészlet`select column1, column2 from mytable`struktúraszakaszában definiált oszlopok at az Azure SQL-adatbázison futtatandó lekérdezés ( ) létrehozásához használja a rendszer. Ha az adatkészlet-definíció nem rendelkezik a szerkezettel, akkor a program az összes oszlopot kijelöli a táblából.

> [!NOTE]
> Az **sqlReaderStoredProcedureName**használatakor továbbra is meg kell adnia egy értéket a **TableName** tulajdonsághoz a JSON adatkészletben. Nincsenek érvényesítések végzett ezen a táblán mégis.
>
>

### <a name="sqlsource-example"></a>Példa sqlSource-ra

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

**A tárolt eljárás meghatározása:**

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
**Az SqlSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: "00:30:00" (30 perc). |Nem |
| writeBatchSize |Adatokat szúr be az SQL-táblába, amikor a puffer mérete eléri a WriteBatchSize-ot. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adjon meg egy lekérdezést a Másolási tevékenységhez, hogy egy adott szelet adatait töröljék. További információt az [ismételhető másolat című témakörben talál.](#repeatable-copy) |Lekérdezési utasítás. |Nem |
| sliceIdentifierColumnName |Adja meg az automatikusan létrehozott szeletazonosítóval kitöltendő Másolási tevékenység oszlopnevét, amely egy adott szelet adatainak tisztítására szolgál az újrafuttatáskor. További információt az [ismételhető másolat című témakörben talál.](#repeatable-copy) |A bináris adattípusú oszlop oszlopneve [32]. |Nem |
| sqlWriterDProcedureName |A tárolt eljárás neve, amely meghatározza a forrásadatok céltáblára való alkalmazását, például upserts vagy átalakítás a saját üzleti logikával. <br/><br/>Vegye figyelembe, hogy ez a tárolt eljárás kötegenként lesz **meghívva.** Ha olyan műveletet szeretne végezni, amely csak egyszer fut, és semmi köze a `sqlWriterCleanupScript` forrásadatokhoz, például a törléshez/csonkoláshoz, használja a tulajdonságot. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |
| sqlWriterTableType típus |Adja meg a tárolt eljárásban használandó táblatípus nevét. A másolási tevékenység lehetővé teszi az áthelyezett adatokat egy ideiglenes táblában ezzel a táblatípussal. A tárolt eljáráskód ezután egyesítheti a másolt adatokat a meglévő adatokkal. |Táblatípus neve. |Nem |

#### <a name="sqlsink-example"></a>Példa sqlsink

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

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON-példák adatok másolására az SQL Database-be és az SQL Adatbázisból
Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Ezek azt mutatják, hogyan másolhatja az adatokat, és az Azure SQL Database és az Azure Blob Storage. Azonban az adatok **közvetlenül** másolhatók bármelyik forrásból bármelyik fogadók [az itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott az Azure Data Factory másolási tevékenység használatával.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Példa: Adatok másolása az Azure SQL Database-ből az Azure Blobba
Ugyanez határozza meg a következő Data Factory entitásokat:

1. [AzureSqlDatabase](#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [AzureSqlTable](#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [SqlSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta az Azure SQL-adatbázis táblájából óránként idomásolja az idősorozat-adatokat (óránként, naponta stb.). Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure SQL Database csatolt szolgáltatás:**

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
Tekintse meg az Azure SQL csatolt szolgáltatás szakasza a hivatkozott szolgáltatás által támogatott tulajdonságok listáját.

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
Tekintse meg az [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) cikket a hivatkozott szolgáltatás által támogatott tulajdonságok listáját.


**Azure SQL bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "MyTable" táblát az Azure SQL-ben, és tartalmaz egy "timestampcolumn" nevű oszlopot az idősorozat-adatokhoz.

"külső": "true" beállítás tájékoztatja az Azure Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

Tekintse meg az Azure SQL-adatkészlet típusának tulajdonságai szakaszban az adatkészlet-típus által támogatott tulajdonságok listáját.

**Azure Blob kimeneti adatkészlet:**

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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
Tekintse meg az [Azure Blob-adatkészlet típusának tulajdonságai](data-factory-azure-blob-connector.md#dataset-properties) szakaszban az adatkészlet-típus által támogatott tulajdonságok listáját.

**SQL-forrással és Blob-fogadóval rendelkező folyamat másolási tevékenysége:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **sqlsource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**. Az **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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
A példában az **sqlReaderQuery** az SqlSource-hoz van megadva. A másolási tevékenység futtatja ezt a lekérdezést az Azure SQL Database-forrás az adatok lekérdezéséhez. Másik lehetőségként megadhatja a tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paramétereket vesz igénybe).

Ha nem adja meg sem az sqlReaderQuery, sem az sqlReaderStoredProcedureName értéket, a JSON adatkészlet struktúraszakaszában definiált oszlopok at az Azure SQL-adatbázison futtatandó lekérdezés létrehozásához használja a rendszer. Például: `select column1, column2 from mytable`. Ha az adatkészlet-definíció nem rendelkezik a szerkezettel, akkor a program az összes oszlopot kijelöli a táblából.

Tekintse meg az [Sql Source](#sqlsource) szakasz és [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) az SqlSource és a BlobSink által támogatott tulajdonságok listáját.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Példa: Adatok másolása az Azure Blobból az Azure SQL Database-be
A minta a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDatabase](#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [AzureSqlTable](#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlSink](#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként idokivonatot ad az Azure blobból az Azure SQL-adatbázis egyik táblájára. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**Azure SQL-hez csatolt szolgáltatás:**

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
Tekintse meg az Azure SQL csatolt szolgáltatás szakasza a hivatkozott szolgáltatás által támogatott tulajdonságok listáját.

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
Tekintse meg az [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) cikket a hivatkozott szolgáltatás által támogatott tulajdonságok listáját.


**Azure Blob bemeneti adatkészlet:**

Az adatokat óránként veszi fel egy új blob (gyakoriság: óra, időköz: 1). A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap és nap részét, a fájlnév pedig a kezdési időpont órarészét használja. "külső": az "igaz" beállítás tájékoztatja a Data Factory szolgáltatást, hogy ez a tábla az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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
Tekintse meg az [Azure Blob-adatkészlet típusának tulajdonságai](data-factory-azure-blob-connector.md#dataset-properties) szakaszban az adatkészlet-típus által támogatott tulajdonságok listáját.

**Azure SQL Database kimeneti adatkészlet:**

A minta az adatokat egy "MyTable" nevű táblába másolja az Azure SQL-ben. Hozza létre a táblát az Azure SQL-ben ugyanannyi oszlopot, mint ablob CSV-fájl tartalmaz. A rendszer óránként új sorokat ad a táblához.

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
Tekintse meg az Azure SQL-adatkészlet típusának tulajdonságai szakaszban az adatkészlet-típus által támogatott tulajdonságok listáját.

**Másolási tevékenység blobforrással és SQL-fogadóval rendelkező folyamatban:**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránként i. A folyamat JSON-definíciójában a **forrástípus** **BlobSource** lesz állítva, **a fogadó** típusa pedig **SqlSink**.

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
Tekintse meg az [Sql Sink](#sqlsink) szakaszés [blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) az SqlSink és a BlobSource által támogatott tulajdonságok listáját.

## <a name="identity-columns-in-the-target-database"></a>Identitásoszlopok a céladatbázisban
Ez a szakasz egy identitásoszlop nélküli forrástáblából származó adatok identitásoszlop nélküli másolására szolgál egy identitásoszlopot tartalmazó céltáblába.

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
Figyelje meg, hogy a céltábla identitásoszloppal rendelkezik.

**JSON forrásadatkészlet-definíció**

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
**Céladatkészlet JSON-definíciója**

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

Figyelje meg, hogy a forrás és a céltábla különböző sémával rendelkezik (a cél nak van egy további identitású oszlopa). Ebben a forgatókönyvben meg kell **adnia a struktúra** tulajdonságot a céladatkészlet-definícióban, amely nem tartalmazza az identitásoszlopot.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Tárolt eljárás meghívása az SQL-fogadóból
Egy példa a tárolt eljárás meghívása az SQL-fogadó egy folyamat másolási tevékenységében, [olvassa el a Tárolt eljárás meghívása SQL-fogadó másolási tevékenység cikkben.](data-factory-invoke-stored-procedure-from-copy-activity.md)

## <a name="type-mapping-for-azure-sql-database"></a>Típusleképezés az Azure SQL Database-hez
Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkben említettek szerint a Másolástevékenység automatikus típuskonverziókat hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Az Azure SQL Database-be és az Azure SQL-adatbázisból történő áthelyezéskor a következő leképezések sql-típusról .NET típusúra kerülnek, és fordítva. A leképezés megegyezik a ADO.NET SQL Server adattípus-leképezésével.

| SQL Server adatbázis-kezelő motor típusa | .NET keretrendszer típusa |
| --- | --- |
| bigint |Int64 |
| binary |Bájt[] |
| bit |Logikai |
| Char |Karakterlánc, Karakter |
| dátum |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Dátumidő-eltolás |DateTimeOffset (Dátumidő-eltolás) |
| Decimal |Decimal |
| FILESTREAM attribútum (varbinary(max)) |Bájt[] |
| Float |Double |
| image |Bájt[] |
| int |Int32 |
| Pénzt |Decimal |
| nchar |Karakterlánc, Karakter |
| nszöveg |Karakterlánc, Karakter |
| numerikus |Decimal |
| nvarchar |Karakterlánc, Karakter |
| real |Egyirányú |
| rowversion (sorverzió) |Bájt[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| kis pénz |Decimal |
| sql_variant |Objektum * |
| szöveg |Karakterlánc, Karakter |
| time |időtartam |
| időbélyeg |Bájt[] |
| tinyint |Bájt |
| uniqueidentifier |Guid |
| varbinary között |Bájt[] |
| varchar |Karakterlánc, Karakter |
| xml |Xml |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha többet szeretne tudni arról, hogy a forrásadatkészlet oszlopait a fogadó adatkészlet oszlopaihoz szeretné-e leképezni, olvassa [el az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-copy"></a>Ismételhető másolat
Amikor adatokat másol az SQL Server Database szolgáltatásba, a másolási tevékenység alapértelmezés szerint adatokat fűz a fogadótáblához. UpSERT-művelet végrehajtásához tekintse meg [az SqlSink-cikk ismétlése.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
