---
title: Adatok áthelyezése, és az SQL-kiszolgáló |} Microsoft Docs
description: További tudnivalók áthelyezni az adatokat, vagy a helyszíni SQL Server-adatbázist vagy egy Azure virtuális gép Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c4038ea5a450f32a46f24a306d1ee30bd61308a5
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054585"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Azure Data Factory használatával történő és a helyszíni SQL Server vagy a IaaS (Azure virtuális gép) adatok áthelyezése
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verziójával](data-factory-sqlserver-connector.md)
> * [(Az aktuális verzió) 2-es verzió](../connector-sql-server.md)

> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [SQL Server-összekötőt, a V2](../connector-sql-server.md).

Ez a cikk ismerteti, hogyan használható a másolási tevékenység során az Azure Data Factory adatok belőle egy helyi SQL Server-adatbázis áthelyezése. Buildekről nyújtanak a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, amelynek során adatátvitel a másolási tevékenység az általános áttekintést. 

## <a name="supported-scenarios"></a>Támogatott esetek
Adatokat másolhat **egy SQL Server-adatbázisból** tárolja a következő adatokat:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Adatok másolása a következő adatokat tárolja **SQL Server-adatbázishoz**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Támogatott SQL Server-verziók
Az SQL Server connector támogatása az adatok másolásának, vagy a következő verziók helyszínen üzemeltetett példányt, vagy az SQL-hitelesítést és a Windows-hitelesítést használó Azure IaaS: SQL Server 2016, az SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A fogalmakat és a helyszíni SQL Server által futtatott vagy Azure IaaS (infrastruktúra-,--szolgáltatás) virtuális gépeket a csatlakozáshoz szükséges lépések megegyeznek. Mindkét esetben kell használnia az adatkezelési átjáró a hálózati kapcsolatot.

Lásd: [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikkben tájékozódhat az adatkezelési átjáró és az átjáró beállításával kapcsolatos részletes útmutatás. Kapcsolódás az SQL Server olyan előfeltételt egy átjárópéldányt beállítása.

Amíg átjárót telepítheti az ugyanabban a helyi számítógépen vagy a felhő Virtuálisgép-példány és az SQL Server, a jobb teljesítmény, ajánlott külön gépeken telepíteni. Az átjáró és az SQL Server különböző gépeken csökkenti a Erőforrásverseny.

## <a name="getting-started"></a>Első lépések
A másolási tevékenység, amely a különböző eszközök/API-k használatával helyezi át az adatokat belőle egy helyi SQL Server-adatbázis egy folyamat hozhatja létre.

Hozzon létre egy folyamatot a legegyszerűbb módja használatára a **másolása varázsló**. Lásd: [oktatóanyag: hozzon létre egy folyamatot, másolása varázslóval](data-factory-copy-data-wizard-tutorial.md) létrehozásával egy folyamatot, az adatok másolása varázsló segítségével gyorsan útmutatást.

Az alábbi eszközöket használhatja a folyamatokat létrehozni: **Azure-portálon**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-sablon** , **.NET API**, és **REST API-t**. Lásd: [másolási tevékenység oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) hozzon létre egy folyamatot a másolási tevékenység részletes útmutatóját. 

Akár az eszközök vagy API-k, hajtsa végre a következő lépésekkel hozza létre egy folyamatot, amely mozgatja az adatokat a forrás-tárolóban a fogadó tárolóban: 

1. Hozzon létre egy **adat-előállító**. Egy adat-előállító tartalmazhat egy vagy több folyamatok. 
2. Hozzon létre **összekapcsolt szolgáltatások** bemeneti és kimeneti adatok csatolásához tárolja a a data factory. Például adatokat az SQL Server-adatbázis egy Azure blob Storage másolása, akkor két társított szolgáltatások létrehozásához az SQL Server-adatbázis és az Azure storage-fiók összekapcsolása a data factory. SQL Server-adatbázis jellemző csatolt szolgáltatás tulajdonságait, lásd: [szolgáltatástulajdonságok kapcsolódó](#linked-service-properties) szakasz. 
3. Hozzon létre **adatkészletek** a másolási művelet bemeneti és kimeneti adatok. A példa az előző lépésben említett Ha meg szeretné adni az SQL-tábla az SQL Server-adatbázis a bemeneti adatokat tartalmazó adatkészlet hoz létre. Továbbá adja meg a blob-tároló és a mappa, amely tárolja az adatokat másolni az SQL Server-adatbázisból egy másik dataset létrehozhat. SQL Server-adatbázis adott adatkészlet tulajdonságai, lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon létre egy **csővezeték** , amely fogad egy bemeneti adatkészlet és egy kimeneti adatkészletet másolási tevékenységgel. A korábban említett példában SqlSource forrás-és BlobSink akár használhatja a fogadó a másolási tevékenységhez. Hasonlóképpen ha SQL Server-adatbázis másolása az Azure Blob-tárolóból, használható BlobSource és SqlSink a másolási tevékenység. SQL Server-adatbázis adott tevékenység Tulajdonságok másolása, lásd: [tevékenység Tulajdonságok másolása](#copy-activity-properties) szakasz. További részletek a tárolóban használatáról a forrás vagy a fogadó a hivatkozásra a adattároló az előző szakaszban. 

A varázsló használatakor a Data Factory entitások (összekapcsolt szolgáltatások adatkészletek és a feldolgozási sor) JSON-definíciók automatikusan létrejönnek. Eszközök/API-k (kivéve a .NET API-t) használata esetén adja meg a Data Factory entitások a JSON formátum használatával.  JSON-definíciók, amely segítségével másolja az adatokat a helyszíni SQL Server adatbázis az adat-előállító entitások minták, lásd: [JSON példák](#json-examples-for-copying-data-from-and-to-sql-server) című szakaszát. 

A következő szakaszok részletesen bemutatják az SQL Server Data Factory tartozó entitások meghatározásához használt JSON tulajdonságokat: 

## <a name="linked-service-properties"></a>A kapcsolódószolgáltatás-tulajdonságok
Típusú társított szolgáltatás létrehozása **OnPremisesSqlServer** a helyszíni SQL Server adatbázis összekapcsolása egy adat-előállítóban. A következő táblázat a JSON-elemek szerepelnek a helyszíni SQL Server kapcsolódó szolgáltatásra vonatkozó leírást.

A következő táblázat a JSON-elemek szerepelnek kapcsolódó SQL Server-szolgáltatásra vonatkozó leírást.

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| type |A type tulajdonságot kell megadni: **OnPremisesSqlServer**. |Igen |
| connectionString |Az SQL-hitelesítéssel vagy a Windows-hitelesítés a helyszíni SQL Server adatbázishoz való kapcsolódáshoz szükséges connectionString információkat adják meg. |Igen |
| gatewayName |Neve az átjáró, amely a Data Factory szolgáltatásnak csatlakoznia kell a helyszíni SQL Server adatbázishoz. |Igen |
| felhasználónév |Adja meg a felhasználónevet, ha a Windows-hitelesítést használ. Példa: **tartománynév\\felhasználónév**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

Hitelesítő adatok használatával titkosíthatja az **New-AzureRmDataFactoryEncryptValue** parancsmag és a következő példában látható módon használhatja őket a kapcsolódási karakterláncban (**EncryptedCredential** tulajdonság):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Példák
**Az SQL-hitelesítéssel JSON**

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
**A Windows-hitelesítést használó JSON**

Az adatkezelési átjáró fog megszemélyesíteni a megadott felhasználói fióknak a helyi SQL Server-adatbázishoz való kapcsolódáshoz. 

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
A minták használja egy adatkészlet típusú **SqlServerTable** képviselő egy SQL Server adatbázis egyik táblája.  

Szakaszok & meghatározása adatkészletek esetében elérhető tulajdonságok teljes listáját lásd: a [adatkészletek létrehozása](data-factory-create-datasets.md) cikk. Például struktúra, a rendelkezésre állás és a házirend a DataSet adatkészlet JSON hasonlítanak minden adatkészlet esetében (SQL Server, az Azure blob, Azure-tábla, stb.).

A typeProperties szakasz más adatkészlet egyes típusai és információkat nyújt azokról az adattárban adatok helyét. A **typeProperties** szakasz az adatkészlet típusú **SqlServerTable** tulajdonságai a következők:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| tableName |A tábla vagy nézet, amelyre a társított szolgáltatás SQL Server adatbázis-példány neve hivatkozik. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Ha adatokat egy SQL Server-adatbázisból, a másolási tevékenység beállítása a forrástípus **SqlSource**. Hasonlóképpen, ha adatok SQL Server-adatbázishoz, beállítása a fogadó típusa a másolási tevékenység **SqlSink**. Ez a témakör SqlSource és SqlSink által támogatott tulajdonságokról.

Szakaszok & rendelkezésre álló tevékenységek meghatározó tulajdonságok teljes listáját lásd: a [létrehozása folyamatok](data-factory-create-pipelines.md) cikk. Az összes tevékenység tulajdonságai, például nevét, leírását, valamint bemeneti és kimeneti táblák és házirendek érhetők el.

> [!NOTE]
> A másolási tevékenység során csak egy bemenettel rendelkezik, és csak egy kimenetet.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok tevékenységek minden típusának függenek. A másolási tevékenység során két érték források és mosdók típusától függően.

### <a name="sqlsource"></a>SqlSource
Ha a másolási tevékenység során a forrás típusa nem **SqlSource**, a következő tulajdonságok érhetők el **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés segítségével adatokat olvasni. |SQL-lekérdezési karakterlánc. Például: Válasszon * from tábla. Előfordulhat, hogy a bemeneti adatkészlet által hivatkozott adatbázishoz több táblát is hivatkozik. Ha nincs megadva, az SQL-utasítás végrehajtott: táblanév kiválaszthatja. |Nem |
| sqlReaderStoredProcedureName |A tárolt eljárás, amely adatokat olvas a forrástábla neve. |A tárolt eljárás neve. Az utolsó SQL-utasítás a következő tárolt eljárást a SELECT utasítással kell lennie. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |

Ha a **sqlReaderQuery** van megadva a SqlSource, a másolási tevékenység során ez a lekérdezés fut az SQL Server-adatbázis forrás az adatok eléréséhez.

Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, struktúra szakaszában meghatározott oszlopokat válassza futtatni az SQL Server adatbázis-lekérdezés összeállításához használt. Az adatkészlet-definícióban nem rendelkezik a struktúra, ha minden kiválasztott oszlop. a táblából.

> [!NOTE]
> Amikor **sqlReaderStoredProcedureName**, továbbra is meg kell adnia egy értéket a **tableName** az adatkészlet JSON tulajdonság. Nincs érvényesítést hajt végre ezt a táblázatot, ha van.

### <a name="sqlsink"></a>SqlSink
**SqlSink** támogatja a következő tulajdonságokkal:

| Tulajdonság | Leírás | Megengedett értékek | Szükséges |
| --- | --- | --- | --- |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezését, mielőtt azt az időkorlátot. |A TimeSpan<br/><br/> Példa: "00: 30:00" (30 perc). |Nem |
| writeBatchSize |Szúr be az SQL-tábla adatokat, amikor a puffer mérete eléri writeBatchSize. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adja meg a lekérdezést úgy, hogy egy adott szelet adatait végrehajtásához másolási tevékenységhez. További információkért lásd: [ismételhető másolási](#repeatable-copy) szakasz. |A lekérdezési utasítást. |Nem |
| sliceIdentifierColumnName |Adja meg a másolási tevékenység során automatikusan létrejön szelet azonosító, amely segítségével távolítja el az adatokat egy adott szelet, amikor futtassa újra a töltse ki az oszlopnevet. További információkért lásd: [ismételhető másolási](#repeatable-copy) szakasz. |Egy oszlop binary(32) adattípusú oszlop neve. |Nem |
| sqlWriterStoredProcedureName |A tárolt eljárás, amely meghatározza, hogyan alkalmazhat forrásadatok a céloldali tábla, pl. do upserts vagy a saját üzleti logikát használó átalakító neve. <br/><br/>Megjegyzés: Ez a tárolt eljárás fog **kötegenként meghívott**. Ha művelet, amelynek csak egyszer futnak, és érinti a elvégezni a segítségével például törlés/truncate forrásadatok használja szeretné `sqlWriterCleanupScript` tulajdonság. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paramétereit. |A név/érték párok. Nevét és a kis-és a paraméterek meg kell egyeznie a nevek és a kis-és nagybetűhasználat a tárolt eljárás paramétereit. |Nem |
| sqlWriterTableType |Adja meg a tárolt eljárásban használandó tábla neve. Másolási tevékenység elérhetővé teszi az adatok áthelyezése egy ideiglenes táblát, amely a táblatípus. Tárolt eljárás kódot is majd egyesítheti az adatokat, a meglévő adatok másolásának. |Egy tábla környezettípus nevét. |Nem |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>Adatok másolása a kezdő és a SQL Server JSON példák
Az alábbi példák megadják minta JSON-definíciókat tartalmazzon, segítségével hozzon létre egy folyamatot [Azure-portálon](data-factory-copy-activity-tutorial-using-azure-portal.md) vagy [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). A következő minták adatok másolása az SQL Server és az Azure Blob Storage megjelenítése. Azonban az adatok átmásolhatók **közvetlenül** a forrásokban, sem a megadott nyelő [Itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) a másolási tevékenység során az Azure Data Factory használatával.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Példa: Adatok másolása az SQL Server az Azure-Blobba
A következő példában:

1. A társított szolgáltatás típusa [OnPremisesSqlServer](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [SqlServerTable](#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [SqlSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A minta-idősoros adatok egy SQL Server tábla másolja az Azure blob minden órában. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

Első lépésként a telepítő az adatkezelési átjáró. Az utasítások szerepelnek a [adatokat a helyszíni helyek és a felhő közötti áthelyezése](data-factory-move-data-between-onprem-and-cloud.md) cikk.

**Kapcsolódó SQL Server szolgáltatás**
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
**Az Azure Blob storage társított szolgáltatás**

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
**SQL Server bemeneti adatkészlet**

A minta azt feltételezi, hogy létrehozott egy tábla "MyTable" SQL Server és a "timestampcolumn" nevű adatsorozat időadatok oszlopot tartalmaz. Lekérheti az egyetlen adatkészlet ugyanazon adatbázis több tábla keresztül, de egyetlen tábla a dataset tableName typeProperty kell használni.

"External" beállítása: "true" tájékoztatja Data Factory szolgáltatásnak, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák.

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

Adatot ír egy új blob minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját a BLOB a szelet által feldolgozott kezdési ideje alapján dinamikusan történik. A mappa elérési útját használja, év, hónap, nap és a kezdési idő órában részeit.

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
**A másolási tevékenység-feldolgozási folyamat**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **SqlSource** és **fogadó** típusúra **BlobSink**. A megadott SQL-lekérdezést a **SqlReaderQuery** tulajdonság kiválasztása az adatok másolása az elmúlt órában.

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
Ebben a példában **sqlReaderQuery** a SqlSource van megadva. A másolási tevékenység során ez a lekérdezés fut az SQL Server-adatbázis forrás az adatok eléréséhez. Másik lehetőségként megadhat tárolt eljárás megadásával a **sqlReaderStoredProcedureName** és **storedProcedureParameters** (Ha a tárolt eljárás paraméterek fogadja el). A sqlReaderQuery hivatkozhat több táblák az adatbázisban a következő bemeneti adatkészlet hivatkozik. Nincs korlátozva csak a tábla a dataset tableName typeProperty állítja be.

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, struktúra szakaszában meghatározott oszlopokat válassza futtatni az SQL Server adatbázis-lekérdezés összeállításához használt. Az adatkészlet-definícióban nem rendelkezik a struktúra, ha minden kiválasztott oszlop. a táblából.

Tekintse meg a [Sql-forrás](#sqlsource) szakasz és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) SqlSource és BlobSink által támogatott tulajdonságok listája.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Példa: Adatok másolása az Azure Blob az SQL Server
A következő példában:

1. A társított szolgáltatás típusa [OnPremisesSqlServer](#linked-service-properties).
2. A társított szolgáltatás típusa [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Bemeneti [dataset](data-factory-create-datasets.md) típusú [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Egy kimeneti [dataset](data-factory-create-datasets.md) típusú [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. A [csővezeték](data-factory-create-pipelines.md) a másolási tevékenység által használt [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlSink](#sql-server-copy-activity-type-properties).

A minta másolatok idősorozat adatokat az Azure blob-egy SQL Server minden órában tábla. A mintákat a következő szakaszok ismertetik ezeket a mintákat használt JSON-tulajdonságok.

**Kapcsolódó SQL Server szolgáltatás**

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
**Az Azure Blob storage társított szolgáltatás**

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
**Az Azure Blob bemeneti adatkészlet**

Adatok van felvett egy új blobból minden órában (gyakoriság: óra, időköz: 1). A mappa elérési útját és nevét a BLOB dinamikusan értékeli ki a kezdési időt a szelet által feldolgozott alapján. A mappa elérési útját használja év, hónap és nap részét kezdési idejét, valamint fájl nevét a kezdő időpontja óra részét. "external": "true" beállítás arról értesíti az, hogy az adatkészlet data factoryval való külső, és egy tevékenység adat-előállító nem hozzák a Data Factory szolgáltatásnak.

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
**SQL Server kimeneti adatkészlet**

A minta másolja az adatokat az SQL Server "MyTable" nevű tábla. A tábla létrehozása az SQL Server azonos számú oszlopot a Blob CSV-fájl tartalmazza a várt módon. Új sorok hozzáadásakor a tábla minden órában.

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
**A másolási tevékenység-feldolgozási folyamat**

A feldolgozási sor tartalmazza a másolási tevékenység, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra nem ütemezték. Az adatcsatorna JSON-definícióból a **forrás** típusúra **BlobSource** és **fogadó** típusúra **SqlSink**.

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

## <a name="troubleshooting-connection-issues"></a>Kapcsolati problémák elhárítása
1. Konfigurálja az SQL Server távoli kapcsolatokat fogadjon. Indítsa el **SQL Server Management Studio**, kattintson a jobb gombbal **server**, és kattintson a **tulajdonságok**. Válassza ki **kapcsolatok** csoportot a listából, és ellenőrzés **a kiszolgáló távoli kapcsolatok engedélyezése a**.

    ![Távoli kapcsolatok engedélyezése](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Lásd: [konfigurálja a távelérési kiszolgálói konfigurációs beállítás megadása](https://msdn.microsoft.com/library/ms191464.aspx) a részletes lépéseket.
2. Indítsa el **SQL Server Konfigurációkezelő**. Bontsa ki a **SQL Server hálózati konfigurációja** , és válassza ki a példány **MSSQLSERVER protokolljai**. A jobb oldali ablaktáblában protokollok kell megjelennie. Engedélyezze a TCP/IP protokollt kattintson a jobb gombbal **TCP/IP** elemre kattintva **engedélyezése**.

    ![Engedélyezze a TCP/IP protokollt](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Lásd: [engedélyezheti vagy tilthatja le a hálózati protokoll](https://msdn.microsoft.com/library/ms191294.aspx) részleteit és más módon, hogy a TCP/IP-protokoll.
3. Az azonos ablakban kattintson duplán **TCP/IP** indítása **TCP/IP-tulajdonságok** ablak.
4. Váltás a **IP-címek** fülre. Görgessen le lásd: **IPAll** szakasz. Jegyezze fel a ** TCP-Port ** (alapértelmezett érték a **1433**).
5. Hozzon létre egy **szabály a Windows tűzfal** ezen a porton keresztül bejövő adatforgalmat engedélyezi a számítógépen.  
6. **Ellenőrizze a kapcsolat**: teljesen minősített nevet az SQL Serverhez való kapcsolódáshoz használja az SQL Server Management Studio egy másik gépről. Például: "<machine>.<domain>. Corp.<company>.com, 1433. "

   > [!IMPORTANT]

   > Lásd: [helyezze át az adatokat a helyszíni adatforrások és az adatkezelési átjáró a felhő közötti](data-factory-move-data-between-onprem-and-cloud.md) részletes információkat.
   >
   > Lásd: [átjáró elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) kapcsolati/átjáró hibaelhárítási tippek a kapcsolódó problémákat.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>A céladatbázis azonosító oszlop
Ez a szakasz azt mutatja, hogy adatokat másol a forrástábla nem azonosító oszlop az azonosító oszlopot tartalmazó táblát.

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

Figyelje meg, hogy a céltábla rendelkezik-e az azonosító oszlop.

**Forrás adatkészlet JSON-definícióból**

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
**Cél adatkészlet JSON-definícióból**

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

Figyelje meg, hogy a forrás és cél táblázatként különböző sémája (cél rendelkezik egy olyan további oszlop identitású). Ilyen esetben meg kell adnia **struktúra** tulajdonság az a tároló adatkészlet-definícióban, amely nem tartalmazza az identitásoszlop.

## <a name="invoke-stored-procedure-from-sql-sink"></a>A fogadó SQL tárolt eljárás meghívása
Lásd: [fogadó SQL tárolt eljárás meghívása a másolási tevékenység](data-factory-invoke-stored-procedure-from-copy-activity.md) cikk SQL fogadó a folyamat a másolási tevékenység a tárolt eljárás meghívása példát.

## <a name="type-mapping-for-sql-server"></a>Az SQL server leképezésének
Ahogyan az a [adatok mozgása tevékenységek](data-factory-data-movement-activities.md) cikk, a másolási tevékenység során hajtja végre a módszert használja a következő 2. lépés típusok gyűjtése eseményforrás-típusnak automatikus típuskonverziók:

1. A natív eseményforrás-típusnak átalakítása .NET-típusa
2. .NET-típus konvertálása natív a fogadó típusa

Ha adatok áthelyezése az & SQL Server, a következő megfeleltetéseket használ az SQL-típus a .NET-típus, és ez fordítva is igaz.

Leképezése nem ugyanaz, mint az SQL Server adattípus-hozzárendelése az ADO.NET.

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

## <a name="mapping-source-to-sink-columns"></a>Leképezési forráshoz oszlopok gyűjtése
Képezze le a fogadó adatkészletből oszlopok forrás adatkészletből oszlopokat, lásd: [Azure Data Factory dataset oszlopai leképezési](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Ismételhető másolása
Amikor adat másolása az SQL Server-adatbázis, a másolási tevékenység hozzáfűzi adatokat a fogadó tábla alapértelmezés szerint. Ehelyett egy UPSERT végrehajtásához tekintse meg [Repeatable írni SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) cikk. 

Ha az adatok másolását a relációs adatokat tárol, ismételhetőség tartsa szem előtt, nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryben futtathatja a szelet manuálisan. Beállíthatja úgy is egy adatkészlet újrapróbálkozási házirendje, hogy a szelet akkor fut újra, ha hiba történik. A szelet akkor fut újra, vagy módon, ha győződjön meg arról, hogy ugyanazokat az adatokat olvasható függetlenül attól, hogy a szelet futtatása hány alkalommal kell. Lásd: [relációs források olvasni Repeatable](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény- és hangolása
Lásd: [másolási tevékenység teljesítmény- és hangolása útmutató](data-factory-copy-activity-performance.md) tájékozódhat az kulcsfontosságú szerepet játszik adatátvitelt jelölik a (másolási tevékenység során) az Azure Data Factory és különböző módokon optimalizálhatja azt, hogy hatás teljesítményét.
