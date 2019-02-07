---
title: Adatok importálására és az SQL Serverből |} A Microsoft Docs
description: Ismerje meg és-tárolókról, amely a helyszíni SQL Server-adatbázist vagy egy Azure virtuális Gépen az Azure Data Factory használatával az adatok áthelyezése.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 38070c3073febbdbea896c177ae68d4b9519314d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813381"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Helyezze át az adatokat, és a helyszíni SQL Server vagy az IaaS (Azure VM) az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-sqlserver-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sql-server.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a v2-ben az SQL Server-összekötő](../connector-sql-server.md).

Ez a cikk bemutatja, hogyan kell használni a másolási tevékenység az Azure Data Factoryban az adatok egy helyszíni SQL Server-adatbázis áthelyezése. Épül a [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket, amely megadja az adatok áthelyezését a másolási tevékenységgel rendelkező általános áttekintése.

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolja **SQL Server-adatbázisból** tárolja, a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból származó adatokat másolja **az SQL Server-adatbázis**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Támogatott SQL Server-verziók
Az SQL Server connector támogatása az adatokat a/üzemeltetett példány helyszíni, vagy Azure IaaS SQL-hitelesítés és a Windows-hitelesítés együttes használatával a következő verziók másolása: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A fogalmakat és csatlakoztatása a helyszíni SQL Server által futtatott vagy Azure IaaS (infrastruktúra--szolgáltatásként)-beli virtuális gépeken szükséges lépések megegyeznek. Mindkét esetben szeretné használni az adatkezelési átjárót a hálózati kapcsolatot.

Lásd: [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk további információt talál az adatkezelési átjáró-lépésenként az átjáró beállítása. Csatlakozás az SQL Server előfeltételeként gateway-példány beállítása.

Amíg az átjáró a ugyanarra a gépre a helyszíni vagy felhőbeli VM-példány és a jobb teljesítmény érdekében az SQL Server telepítése, javasoljuk, hogy telepítse őket a külön gépeken. Az erőforrás-versengés kellene az átjáró és az SQL Server külön gépeken csökkenti.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel az adatok egy helyszíni SQL Server-adatbázis áthelyezéséhez a különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja az, hogy használja a **másolása varázsló**. Lásd: [oktatóanyag: Hozzon létre egy folyamatot a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md) gyors bemutató létrehozása egy folyamatot az adatok másolása varázsló használatával.

A következő eszközök használatával hozzon létre egy folyamatot: **Az Azure portal**, **Visual Studio**, **Azure PowerShell-lel**, **Azure Resource Manager-sablon**, **.NET API**, és  **REST API-val**. Lásd: [másolási tevékenység oktatóanyagát](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy másolási tevékenységgel ellátott adatcsatorna létrehozása a részletes útmutatóját.

Az eszközök vagy az API-kat használja, hogy létrehoz egy folyamatot, amely a helyez át adatokat egy forrásadattárból egy fogadó adattárba a következő lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több folyamattal is tartalmazhat.
2. Hozzon létre **társított szolgáltatásokat** mutató hivatkozást a bemeneti és kimeneti adatokat tárolja a data factoryjához. Például ha az adatokat az SQL Server-adatbázisból egy Azure blob Storage-tárolóba másol, hoz létre az SQL Server-adatbázis és az Azure storage-fiók összekapcsolása a data factory két társított szolgáltatást. Konkrétan az SQL Server-adatbázis a társított szolgáltatás tulajdonságait, lásd: [társított szolgáltatások tulajdonságai](#linked-service-properties) szakaszban.
3. Hozzon létre **adatkészletek** , amely a másolási művelet bemeneti és kimeneti adatokat jelöli. A példában az előző lépésben említett adja meg az SQL-táblát az SQL Server-adatbázis, amely tartalmazza a bemeneti adatokat egy adatkészletet hoz létre. És megadja a blobtárolót és a mappát, amely tárolja az adatokat másolja az SQL Server-adatbázisból egy másik adatkészletet hoz létre. SQL Server-adatbázis adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakaszban.
4. Hozzon létre egy **folyamat** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, és a egy adatkészletet pedig kimenetként. A példában azt korábban említettük SqlSource forrás-és BlobSink fogadóként esetében használja a másolási tevékenység. Hasonlóképpen, ha az SQL Server-adatbázis másolása az Azure Blob Storage-ból, BlobSource és a használata SqlSink a másolási tevékenység. Másolási tevékenység tulajdonságai adott SQL Server-adatbázis, lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakaszban. A forrás vagy a fogadó adattár használatát részletekért kattintson a hivatkozásra az adattár az előző szakaszban.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory-entitásokat (társított szolgáltatások, adatkészletek és folyamat) JSON-definíciói az Ön számára. Eszközök/API-k (kivéve a .NET API) használatakor adja meg a Data Factory-entitások a JSON formátumban. Az adatok másolása helyszíni SQL Server-adatbázisból/használt Data Factory-entitások JSON-definíciói minták, lásd: [JSON példák](#json-examples-for-copying-data-from-and-to-sql-server) című szakaszát.

Az alábbi szakaszok nyújtanak az SQL Server adott Data Factory-entitások definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Létrehoz egy társított szolgáltatást típusú **OnPremisesSqlServer** egy helyszíni SQL Server-adatbázis összekapcsolása a data factoryt. A következő táblázat a JSON-elemeket a helyszíni SQL Server-alapú társított szolgáltatás leírását.

Az alábbi táblázatban az adott SQL Server-alapú társított szolgáltatás JSON-elemek leírását.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell beállítani: **OnPremisesSqlServer**. |Igen |
| kapcsolati Sztringje |Adja meg a connectionString adatokat a helyszíni SQL Server-adatbázis SQL-hitelesítés vagy a Windows-hitelesítés használatával való kapcsolódáshoz szükséges. |Igen |
| átjáró neve |Az átjáró által a Data Factory szolgáltatás a helyszíni SQL Server adatbázishoz való csatlakozáshoz használandó neve. |Igen |
| felhasználónév |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **domainname\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

A hitelesítő adatok titkosíthatók a **New-AzureRmDataFactoryEncryptValue** parancsmag és a kapcsolati karakterláncot használja őket az alábbi példában látható módon (**EncryptedCredential** tulajdonság):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Példák
**JSON-t az SQL-hitelesítés használata**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON-t a Windows-hitelesítés használatával**

Az adatkezelési átjáró megszemélyesíti a megadott felhasználói fióknak a helyi SQL Server adatbázishoz való csatlakozáshoz.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
A minták használja egy adatkészlet típusú **SqlServerTable** , amelyek egy SQL Server-adatbázisban lévő táblából.

Szakaszok & adatkészletek definiálását tulajdonságainak teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például a szerkezetet, rendelkezésre állást és szabályzatát adatkészlet JSON szakaszok hasonlóak az összes adatkészlet esetében (SQL Server, az Azure blob-, az Azure table-, stb.).

A typeProperties szakasz eltérő az egyes adatkészlet, és az adattárban lévő adatok helyére vonatkozó információkat. A **typeProperties** szakasz az adatkészlet típusa **SqlServerTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet a SQL Server-adatbázis példányában, amelyre a társított szolgáltatás neve hivatkozik. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Adatok áthelyezéséhez az SQL Server-adatbázisból, a forrás típusaként állítsa be a másolási tevékenység **SqlSource**. Ehhez hasonlóan az SQL Server-adatbázis áthelyezéséhez adatokat, állítsa be a fogadó típusa a másolási tevékenység **SqlSink**. Ez a szakasz SqlSource és az SqlSink által támogatott tulajdonságok listáját tartalmazza.

Szakaszok & definiálását tevékenységek tulajdonságainak teljes listáját lásd: a [folyamatok létrehozása](data-factory-create-pipelines.md) cikk. Tulajdonságok, mint például a nevét, leírását, bemeneti és kimeneti táblák és szabályzatok minden típusú tevékenységek érhetők el.

> [!NOTE]
> A másolási tevékenység csak egy bemenettel rendelkezik, és csak egy kimenetet.

Mivel a tevékenység a typeProperties szakasz tulajdonságai tevékenységek minden típusának számától függ. A másolási tevékenységhez azok változhat a forrásként és fogadóként típusú is.

### <a name="sqlsource"></a>SqlSource
Ha a másolási tevékenység a forrása típusa **SqlSource**, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével olvassa el az adatokat. |SQL-lekérdezési karakterláncot. Például: válassza ki * from tábla. Több táblát is hivatkozhatnak az adatbázisból, a bemeneti adatkészlet hivatkozik. Ha nincs megadva, az SQL-utasítás végrehajtott: válasszon a táblanév. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárást, amely adatokat olvas be a forrás-tábla neve. |A tárolt eljárás neve. Az utolsó SQL-utasítást a tárolt eljárás a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |

Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység a lekérdezés fut az SQL Server-adatbázis forrás, az adatok beolvasásához.

Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges).

Ha sqlReaderQuery vagy sqlReaderStoredProcedureName nincs megadva, a struktúra szakaszban meghatározott oszlopokat hozhat létre egy választó lekérdezést az SQL Server-adatbázis futtatásához használja. Ha az adatkészlet definíciója nem rendelkezik a struktúrát, az összes oszlop ki van jelölve, a táblából.

> [!NOTE]
> Ha használ **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy értéket a **tableName** tulajdonságot az adatkészlet JSON. Nincsenek nem lettek elvégezve, mint ez a táblázat azonban ellenőrzések.

### <a name="sqlsink"></a>SqlSink
**SqlSink** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon, amíg a kötegelt insert művelet befejezését, mielőtt azt az időkorlátot. |Időtartam<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |
| WriteBatchSize |Amikor a puffer mérete eléri a writeBatchSize adatok beszúrása SQL-táblát. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adja meg a lekérdezés végrehajtása úgy, hogy az adott szeletre vonatkozó adatok törlődnek a másolási tevékenységhez. További információkért lásd: [reprodukálható másolatot](#repeatable-copy) szakaszban. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg az oszlop nevét adja meg az automatikusan generált szelet azonosítóval, amelyet egy adott szeletre mikor futtassa újra a adatainak a másolási tevékenység. További információkért lásd: [reprodukálható másolatot](#repeatable-copy) szakaszban. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárást, amely meghatározza, hogyan alkalmazhatja a forrásadatok céloldali táblához, pl. do upserts vagy a saját üzleti logika átalakító neve. <br/><br/>Megjegyzés: Ez a tárolt eljárás lesz **kötegenként meghívása**. Ha azt szeretné, hogy csak egyszer fut, és nem a forrásadatokat, például törlés/truncate, használja a művelet elvégzéséhez `sqlWriterCleanupScript` tulajdonság. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név-érték párokat. Nevek és a kis-és a paraméterek meg kell egyeznie a neveket és a kis-és nagybetűhasználatot, a tárolt eljárás paraméterértékeinek. |Nem |
| sqlWriterTableType |Adja meg a tábla neve a tárolt eljárás használható. Másolási tevékenység egy ideiglenes táblát a tábla típusú elérhetővé teszi az adatok áthelyezését. Tárolt eljárás kód majd egyesítheti az adatok másolását a adatokkal. |Egy tábla típusú név. |Nem |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-példák az adatok másolása a kezdő és a SQL Server
Az alábbi példák megadják példa JSON-definíciók, amelyek segítségével létrehoz egy folyamatot használatával [az Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell-lel](data-factory-copy-activity-tutorial-using-powershell.md). A következő példák bemutatják, hogyan másolhat adatokat az SQL Server és az Azure Blob Storage. Azonban az adatok átmásolhatók **közvetlenül** bármelyik források a conditions stated above fogadóként valamelyik [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenységgel az Azure Data Factoryban.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Példa: Adatok másolása az SQL Server az Azure Blob
Az alábbi mintában látható:

1. A társított szolgáltatás típusa [OnPremisesSqlServer](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [SqlServerTable](#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [SqlSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta idősorozat-adatokat másol egy SQL Server-táblát az Azure-blobba óránként. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

Első lépésként a telepítő az adatkezelési átjárót. A rendszer az utasításokat a [adatok áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**Az SQL Server-alapú társított szolgáltatás**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Az Azure Blob storage-beli társított szolgáltatás**

```json
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
**Az SQL Server bemeneti adatkészlet**

A minta azt feltételezi, létrehozott egy táblát "MyTable" az SQL Server és a egy idősorozat-adatok a "timestampcolumn" nevű oszlopot tartalmaz. Egyetlen adatkészlet használata ugyanabban az adatbázisban több tábla alatt lekérdezheti, de az adatkészlet tableName typeProperty egyetlen táblában kell használni.

Beállítás az "external": "true" tájékoztatja a Data Factory szolgáltatásban, hogy az adatkészletet a data factory a külső, és nem hozzák az adat-előállító adott tevékenységéhez.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Azure blobkimeneti adatkészlet**

Adatokat írt egy új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját a BLOB a feldolgozás alatt álló szelet kezdő időpontja alapján dinamikusan kiértékeli. A mappa elérési útját használja, év, hónap, nap és óra részei a kezdési időpontot.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
**Másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **SqlSource** és **fogadó** típusa **BlobSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztja az adatokat másolni az elmúlt órában.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
Ebben a példában **sqlReaderQuery** a SqlSource van megadva. A másolási tevékenység a lekérdezés fut az SQL Server-adatbázis forrás, az adatok beolvasásához. Másik lehetőségként megadhat egy tárolt eljárást megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméter szükséges). A sqlReaderQuery hivatkozhat több tábla a bemeneti adatkészlet által hivatkozott adatbázishoz. Nem korlátozódik az csak az a tábla, az adatkészlet tableName typeProperty állítja be.

Ha sqlReaderQuery vagy sqlReaderStoredProcedureName nincs megadva, a struktúra szakaszban meghatározott oszlopokat hozhat létre egy választó lekérdezést az SQL Server-adatbázis futtatásához használja. Ha az adatkészlet definíciója nem rendelkezik a struktúrát, az összes oszlop ki van jelölve, a táblából.

Tekintse meg a [Sql-forrás](#sqlsource) szakasz és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) SqlSource és BlobSink által támogatott tulajdonságok listáját.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Példa: Adatok másolása Azure blobból SQL Server
Az alábbi mintában látható:

1. A társított szolgáltatás típusa [OnPremisesSqlServer](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Egy bemeneti [adatkészlet](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Kimenet [adatkészlet](data-factory-create-datasets.md) típusú [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. A [folyamat](data-factory-create-pipelines.md) másolási tevékenységgel, amely használja [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és SqlSink.

A minta másolatokat idősorozat-adatokat egy Azure blobból SQL-kiszolgáló minden órában tábla. Ezek a minták a használt JSON-tulajdonságokat a minták a következő szakaszok ismertetik.

**Az SQL Server-alapú társított szolgáltatás**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Az Azure Blob storage-beli társított szolgáltatás**

```json
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
**Azure blobbemeneti adatkészlet**

Adatok felülettől új blob minden órában (frequency: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan a feldolgozás alatt álló szelet kezdő időpontja alapján értékeli ki. A mappa elérési útjának év, hónap és nap részét a kezdési időpont és fájlnevet a kezdő időpontja óra részét használja. "external": "true" beállítással, hogy az adatkészletet a data factory a külső, és nem egy adat-előállító tevékenység által előállított arról tájékoztatja a Data Factory szolgáltatásban.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
**Az SQL Server kimeneti adatkészlete**

A minta adatokat másol az SQL Server "MyTable" nevű tábla. Az SQL Server azonos számú oszlopot az a tábla létrehozásához, a Blob CSV-fájl tartalmazza a várt módon. Új sorok hozzáadódnak a tábla minden órában.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Másolási tevékenységgel rendelkező folyamat**

A folyamat egy másolási tevékenység, amely a bemeneti és kimeneti adatkészleteket használatára van konfigurálva, és óránként ütemezett tartalmazza. A folyamat JSON-definíciót a **forrás** típusa **BlobSource** és **fogadó** típusa **SqlSink**.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Kapcsolati problémák hibaelhárítása
1. Konfigurálja az SQL Server távoli kapcsolatokat fogadjon. Indítsa el a **SQL Server Management Studio**, kattintson a jobb gombbal **kiszolgáló**, és kattintson a **tulajdonságok**. Válassza ki **kapcsolatok** csoportot a listából, és ellenőrzés **a kiszolgáló távoli kapcsolatok engedélyezése**.

    ![Távoli kapcsolatok engedélyezése](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Lásd: [konfigurálja a távelérési kiszolgálói konfigurációs beállítás](https://msdn.microsoft.com/library/ms191464.aspx) a részletes lépéseket.
2. Indítsa el a **SQL Server Configuration Manager**. Bontsa ki a **SQL Server hálózati konfigurációja** , és válassza ki a példány **MSSQLSERVER protokolljai**. A jobb oldali ablaktáblában protokollok kell megjelennie. Kattintson a jobb gombbal a TCP/IP engedélyezése **TCP/IP** kattintva **engedélyezése**.

    ![Engedélyezze a TCP/IP használatát](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Lásd: [engedélyezheti vagy tilthatja le a kiszolgálói hálózati protokoll](https://msdn.microsoft.com/library/ms191294.aspx) kapcsolatos részletekért és a TCP/IP-protokoll engedélyezése alternatív módszert is.
3. Ugyanebben a ablakban kattintson duplán **TCP/IP** elindításához **TCP/IP-tulajdonságok** ablak.
4. Váltson a **IP-címek** fülre. Legörgetve találja meg **IPAll** szakaszban. Jegyezze fel a ** TCP-Port ** (alapértelmezett érték a **1433-as**).
5. Hozzon létre egy **szabály a Windows tűzfal** ezen a porton keresztül bejövő adatforgalmát engedélyező a gépen.
6. **Kapcsolat ellenőrzése**: Szeretne csatlakozni az SQL Server teljesen minősített nevet, használja az SQL Server Management Studio egy másik gépről. Például: "\<gép\>.\< tartomány\>. mindkettőnek\<vállalati\>.com, 1433. "

   > [!IMPORTANT]

   > Lásd: [adatok áthelyezése a felhőbe, az adatkezelési átjáróval és a egy helyszíni forrásra](data-factory-move-data-between-onprem-and-cloud.md) részletes információkat.
   >
   > Lásd: [gateway hibáinak elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolódási/átjáró hibaelhárítási tippek a kapcsolatos problémákat.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>A céladatbázis azonosító oszlop
Ez a szakasz azt szemlélteti, amely adatokat másol a forrástábla nem identitásoszlop az identity oszlopot tartalmazó táblát.

**Forrástábla:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Céltábla:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Figyelje meg, hogy a céloldali tábla rendelkezik-e egy identitásoszlop.

**Forrás adatkészlet JSON-definíció**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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
Lásd: [a fogadó SQL tárolt eljárás meghívása a másolási tevékenység](data-factory-invoke-stored-procedure-from-copy-activity.md) a cikk a folyamat egy másolási tevékenység fogadó SQL tárolt eljárás meghívása egy példát.

## <a name="type-mapping-for-sql-server"></a>Az SQL server-leképezés típusa
Említetteknek megfelelően az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) a cikkben a másolási tevékenység végzi az automatikus típuskonverziók a fogadó-típusokat az alábbi 2. lépés – a módszert használja a forrás típusa:

1. A natív forrástípusok átalakítása typ .NET
2. A .NET-típusból átalakítása natív fogadó típusa

Adatok áthelyezése az & SQL Serverről, amikor a következő hozzárendeléseket használják az SQL-típus .NET típusát, és ez fordítva is igaz.

A leképezés megegyezik az SQL Server adattípus-hozzárendelés az ADO.NET esetén.

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

## <a name="mapping-source-to-sink-columns"></a>A fogadó-oszlopok forrása
Fogadó-adatkészlet az oszlopok a forrásadatkészlet oszlopok leképezésére, lásd: [az Azure Data Factoryban adatkészletoszlopok leképezése](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Reprodukálható másolatot
Adatok másolása az SQL Server-adatbázist, amikor a másolási tevékenység adatokat fűz hozzá a fogadó tábla alapértelmezés szerint. Ehelyett hajtsa végre az UPSERT, lásd: [Repeatable írni az SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) cikk.

Amikor adatmásolásra, relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja az újrapróbálkozási szabályzat egy adatkészlethez, úgy, hogy a szelet akkor fut újra, ha hiba történik. Ha a szelet akkor fut újra, vagy módon, győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy hány alkalommal fut egy szeletet, kell. Lásd: [olvasni a relációs források Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
Lásd: [másolási tevékenységek teljesítményéhez és teljesítményhangolási útmutatóból](data-factory-copy-activity-performance.md) megismerheti a kulcsfontosságú szerepet játszik az adatáthelyezés (másolási tevékenység) az Azure Data Factory és a különféle módokon optimalizálhatja azt, hogy hatással lehet a teljesítményre.
