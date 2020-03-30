---
title: Adatok áthelyezése az SQL Server kiszolgálóra és az SQL Serverkiszolgálóról
description: Megtudhatja, hogy miként helyezhet át adatokat a helyszíni SQL Server-adatbázisba vagy egy Azure-beli virtuális gépbe az Azure Data Factory használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5e4bbe1e6bd944787d47c5e3ed98de582c088a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265765"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Adatok áthelyezése a helyszíni SQL Serverbe vagy az IaaS-re (Azure VM) az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-sqlserver-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-sql-server.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [SQL Server-összekötő a V2](../connector-sql-server.md)alkalmazásban című témakört.

Ez a cikk bemutatja, hogyan használhatja a másolási tevékenység az Azure Data Factory adatok áthelyezése/ egy helyszíni SQL Server-adatbázis. Az [adatmozgatási tevékenységek](data-factory-data-movement-activities.md) cikkre épül, amely általános áttekintést nyújt az adatmozgásról a másolási tevékenységgel.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Támogatott esetek
Az SQL **Server adatbázisból** adatokat másolhat a következő adattárakba:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

A következő adattárakból adatokat másolhat **SQL Server adatbázisba:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Az SQL Server támogatott verziói
Ez az SQL Server-összekötő támogatja az adatok másolását/a helyszíni vagy Azure IaaS rendszerben üzemeltetett példány következő verzióiba SQL-hitelesítéssel és Windows hitelesítéssel: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005

## <a name="enabling-connectivity"></a>Csatlakozás engedélyezése
A helyszíni vagy az Azure IaaS (Infrastruktúra-szolgáltatásként szolgáltatás) virtuális gépeken üzemeltetett SQL Server-kiszolgálóval való csatlakozáshoz szükséges fogalmak és lépések megegyeznek. Mindkét esetben adatkezelési átjárót kell használnia a kapcsolathoz.

Tekintse meg [az adatok áthelyezését a helyszíni helyek és a felhőalapú](data-factory-move-data-between-onprem-and-cloud.md) cikk között, és ismerje meg az Adatkezelési átjárót és az átjáró beállításának lépésenkénti útmutatóját. Az átjárópéldány beállítása előfeltétele az SQL Server hez való csatlakozásnak.

Bár a jobb teljesítmény érdekében az átjárót ugyanazon a helyszíni gépen vagy felhőbeli virtuálisgép-példányon telepítheti, a jobb teljesítmény érdekében azt javasoljuk, hogy telepítse őket külön gépekre. Az átjáró és az SQL Server külön gépeken való megjelenítése csökkenti az erőforrás-versengést.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy folyamatot egy másolási tevékenységgel, amely az adatokat egy helyszíni SQL Server-adatbázisba helyezi át/ onnan különböző eszközök/API-k használatával.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló**használata. Olvassa el [az oktatóanyagot: Folyamat létrehozása a Másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakörben egy gyors útmutatót a folyamat másolása az adatok másolása varázslóval történő létrehozásához.

A következő eszközökkel is létrehozhat egy folyamatot: **Visual Studio,** **Azure PowerShell**, **Azure Resource Manager sablon**, **.NET API**és REST **API.** Lásd: [Tevékenység-oktatóanyag másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) című témakörben részletes útmutatást talál egy másolási tevékenységgel rendelkező folyamat létrehozásához.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépéseket hajthatja végre egy olyan folyamat létrehozásához, amely adatokat helyezi át a forrásadattárból a fogadó adattárába:

1. Hozzon létre egy **adat-előállító**. Az adat-előállító egy vagy több folyamatot tartalmazhat.
2. **Összekapcsolt szolgáltatások** létrehozása a bemeneti és kimeneti adattárak és az adat-előállító összekapcsolására. Ha például adatokat másol egy SQL Server-adatbázisból egy Azure blobstorage-ba, két összekapcsolt szolgáltatást hoz létre az SQL Server-adatbázis és az Azure-tárfiók és az adat-előállító összekapcsolására. Az SQL Server adatbázisra jellemző csatolt szolgáltatástulajdonságokról a [Csatolt szolgáltatás tulajdonságai](#linked-service-properties) című szakaszban találhatók.
3. **Adatkészletek** létrehozása a másolási művelet bemeneti és kimeneti adatainak ábrázolására. Az utolsó lépésben említett példában létrehoz egy adatkészletet, amely megadja az SQL-táblát az SQL Server adatbázisában, amely a bemeneti adatokat tartalmazza. És hozzon létre egy másik adatkészletet a blob tároló és az SQL Server adatbázisból másolt adatokat tároló mappa megadásához. Az SQL Server adatbázisra jellemző adatkészlet-tulajdonságokat lásd: [adatkészlettulajdonságai](#dataset-properties) szakasz.
4. Hozzon létre egy **folyamatot** egy másolási tevékenységgel, amely egy adatkészletet bemenetként, egy adatkészletet pedig kimenetként vesz fel. A korábban említett példában az SqlSource-t használja forrásként, a BlobSink-et pedig a másolási tevékenység fogadójaként. Hasonlóképpen, ha az Azure Blob Storage-ból az SQL Server Database-be másolja a másolási tevékenységet, használja a BlobSource és az SqlSink szolgáltatást. Az SQL Server Database-re jellemző másolási tevékenységtulajdonságokról a [Tevékenység tulajdonságainak másolása](#copy-activity-properties) című szakaszban található. Az adattár forrásként vagy fogadóként való használatáról az adattár előző szakaszában található hivatkozásra kattintva.

A varázsló használatakor a Data Factory entitásokhoz (csatolt szolgáltatások, adatkészletek és a folyamat) json-definíciók automatikusan létrejönnek. Eszközök/API-k használatakor (a .NET API kivételével) ezeket a Data Factory entitásokat a JSON formátum használatával definiálhatja. A helyszíni SQL Server-adatbázisba adatok másolására használt Data Factory-entitások JSON-definícióival rendelkező mintákat lásd: A cikk [JSON-példái](#json-examples-for-copying-data-from-and-to-sql-server) című részében.

A következő szakaszok az SQL Server re vonatkozó Data Factory-entitások definiálására használt JSON-tulajdonságok részleteit ismertetik:

## <a name="linked-service-properties"></a>Csatolt szolgáltatás tulajdonságai
Hozzon létre egy **onPremisesSqlServer** típusú kapcsolt szolgáltatást, amely egy helyszíni SQL Server-adatbázist egy adat-előállítóhoz kapcsol. Az alábbi táblázat a helyszíni SQL Server-hivatkozott szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

Az alábbi táblázat az SQL Server csatolt szolgáltatásra jellemző JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| type |A típustulajdonságot a következőre kell állítani: **OnPremisesSqlServer**. |Igen |
| connectionString (kapcsolati karakterlánc) |Adja meg a helyszíni SQL Server adatbázishoz SQL-hitelesítés vagy Windows-hitelesítés használatával történő csatlakozáshoz szükséges connectionString-adatokat. |Igen |
| átjárónév |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak a helyszíni SQL Server-adatbázishoz való csatlakozáshoz használnia kell. |Igen |
| felhasználónév |Ha Windows-hitelesítést használ, adja meg a felhasználónevet. Példa: **\\tartománynév felhasználóneve**. |Nem |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |Nem |

A hitelesítő adatokat a **New-AzDataFactoryEncryptValue** parancsmag használatával titkosíthatja, és a kapcsolati karakterláncban használhatja, ahogy az a következő példában (**EncryptedCredential** tulajdonság) látható:

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Példák
**JSON az SQL-hitelesítés hez**

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
**JSON a Windows-hitelesítés hez**

Az Adatkezelési átjáró megszemélyesíti a megadott felhasználói fiókot, hogy csatlakozzon a helyszíni SQL Server adatbázishoz.

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
A mintákban **egy SqlServerTable** típusú adatkészletet használt egy SQL Server adatbázis táblájának ábrázolására.

Az adatkészletek definiálására szolgáló & tulajdonságok teljes listáját az [Adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben olvashat. A JSON adatkészletek például a struktúra, a rendelkezésre állás és a szabályzat hasonlóak az összes adatkészlettípushoz (SQL Server, Azure blob, Azure table stb.).

A typeProperties szakasz az adatkészlet egyes típusainál eltérő, és tájékoztatást nyújt az adatok helyéről az adattárban. Az **SqlServerTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla vagy nézet neve a csatolt szolgáltatás által hivatkozott SQL Server Database-példányban. |Igen |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
Ha sql server adatbázisból helyez át adatokat, a másolási tevékenység forrástípusát sqlsource -ra **állítja.** Hasonlóképpen, ha adatokat helyez át egy SQL Server-adatbázisba, a másolási tevékenység fogadótípusát **sqlsink-re állítja.** Ez a szakasz az SqlSource és az SqlSink által támogatott tulajdonságok listáját tartalmazza.

A tevékenységek definiálására rendelkezésre álló szakaszok & tulajdonságok teljes listáját a [Folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben olvashat. Az olyan tulajdonságok, mint a név, a leírás, a bemeneti és kimeneti táblák és a házirendek minden típusú tevékenységhez elérhetők.

> [!NOTE]
> A Másolási tevékenység csak egy bemenetet vesz igénybe, és csak egy kimenetet hoz létre.

Mivel a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusoktól függően változnak. Másolási tevékenység esetén a források és a fogadók típusától függően változnak.

### <a name="sqlsource"></a>SqlSource
Ha egy másolási tevékenység forrása **SqlSource**típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az adatok olvasásához használja az egyéni lekérdezést. |SQL lekérdezési karakterlánc. Például: válassza a * lehetőséget a MyTable táblából. A bemeneti adatkészlet által hivatkozott adatbázisból több táblára is hivatkozhat. Ha nincs megadva, a végrehajtásra kerülő SQL utasítás: válassza ki a MyTable elemet. |Nem |
| sqlReaderStoredProcedureName |Annak a tárolt eljárásnak a neve, amely adatokat olvas be a forrástáblából. |A tárolt eljárás neve. Az utolsó SQL utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |

Ha az **sqlReaderQuery** meg van adva az SqlSource-hoz, a Másolási tevékenység futtatja ezt a lekérdezést az SQL Server Database forrásával az adatok lekérni.

Másik lehetőségként megadhatja a tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paramétereket vesz igénybe).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName értéket, a struktúraszakaszban definiált oszlopok segítségével az SQL Server adatbázison futtatandó választó lekérdezést hoz létre. Ha az adatkészlet-definíció nem rendelkezik a szerkezettel, akkor a program az összes oszlopot kijelöli a táblából.

> [!NOTE]
> Az **sqlReaderStoredProcedureName**használatakor továbbra is meg kell adnia egy értéket a **TableName** tulajdonsághoz a JSON adatkészletben. Nincsenek érvényesítések végzett ezen a táblán mégis.

### <a name="sqlsink"></a>SqlSink
**Az SqlSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| writeBatchTimeout |Várjon időt a kötegbehelyezési művelet befejezésére, mielőtt az időtúljárna. |időtartomány<br/><br/> Példa: "00:30:00" (30 perc). |Nem |
| writeBatchSize |Adatokat szúr be az SQL-táblába, amikor a puffer mérete eléri a WriteBatchSize-ot. |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| sqlWriterCleanupScript |Adja meg a lekérdezést a Másolási tevékenységhez, hogy egy adott szelet adatait töröljék. További információt az [ismételhető másolási](#repeatable-copy) szakaszban talál. |Lekérdezési utasítás. |Nem |
| sliceIdentifierColumnName |Adja meg az oszlop nevét az automatikusan létrehozott szeletazonosítóval kitöltendő Másolási tevékenységhez, amely egy adott szelet adatainak tisztítására szolgál az újrafuttatáskor. További információt az [ismételhető másolási](#repeatable-copy) szakaszban talál. |A bináris adattípusú oszlop oszlopneve [32]. |Nem |
| sqlWriterDProcedureName |A tárolt eljárás neve, amely meghatározza a forrásadatok céltáblára való alkalmazását, például upserts vagy átalakítás a saját üzleti logikával. <br/><br/>Vegye figyelembe, hogy ez a tárolt eljárás kötegenként lesz **meghívva.** Ha olyan műveletet szeretne végezni, amely csak egyszer fut, és semmi köze a `sqlWriterCleanupScript` forrásadatokhoz, például a törléshez/csonkoláshoz, használja a tulajdonságot. |A tárolt eljárás neve. |Nem |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárásparamétereinek nevével és burkolatával. |Nem |
| sqlWriterTableType típus |Adja meg a tárolt eljárásban használandó táblatípus nevét. A másolási tevékenység lehetővé teszi az áthelyezett adatokat egy ideiglenes táblában ezzel a táblatípussal. A tárolt eljáráskód ezután egyesítheti a másolt adatokat a meglévő adatokkal. |Táblatípus neve. |Nem |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-példák adatok másolására az SQL Server kiszolgálóról és az SQL Server programba
Az alábbi példák minta JSON-definíciókat tartalmaznak, amelyek segítségével folyamatot hozhat létre a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy az [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. A következő minták bemutatják, hogyan másolhat adatokat az SQL Server és az Azure Blob Storage. Azonban az adatok **közvetlenül** másolhatók bármelyik forrásból bármelyik fogadók [az itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott az Azure Data Factory másolási tevékenység használatával.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Példa: Adatok másolása az SQL Serverről az Azure Blobba
A következő minta a következőket mutatja:

1. [OnPremisesSqlServer](#linked-service-properties)típusú csatolt szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú kapcsolt szolgáltatás.
3. [SqlServerTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet.](data-factory-create-datasets.md)
5. Az [SqlSource](#copy-activity-properties) és [a BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)programot használó másolási tevékenységgel rendelkező [folyamat.](data-factory-create-pipelines.md)

A minta óránként egy SQL Server-táblából másolja az idősorozat-adatokat egy Azure blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni helyek és a felhőalapú cikk [közötti átmozgatási](data-factory-move-data-between-onprem-and-cloud.md) adatokban találhatók.

**SQL Server csatolt szolgáltatás**
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
**Azure Blob storage-hoz csatolt szolgáltatás**

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

A minta feltételezi, hogy létrehozott egy "MyTable" táblát az SQL Server ben, és tartalmaz egy "timestampcolumn" nevű oszlopot az idősorozat-adatokhoz. Ugyanazon az adatbázison belül több táblát is lekérdezhet egyetlen adatkészlet használatával, de az adatkészlet táblaname typeProperty eleméhez egyetlen táblát kell használni.

A "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül található, és nem az adat-előállító tevékenység által előállított.

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

Az adatok óránként egy új blobba (gyakoriság: óra, időköz: 1) kerül beírásra. A blob mappaelérési útja dinamikusan kiértékelve a feldolgozás alatt álló szelet kezdési időpontja alapján történik. A mappa elérési útja a kezdési időpont év-, hónap-, nap- és órarészeit használja.

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
**Folyamat másolási tevékenységgel**

A folyamat tartalmaz egy másolási tevékenységet, amely úgy van beállítva, hogy használja ezeket a bemeneti és kimeneti adatkészletek, és az ütemezés szerint óránként fut. A folyamat JSON-definíciójában a **forrástípus** **sqlsource-ra** van állítva, **a fogadó** típusa pedig **BlobSink**. Az **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés kiválasztja a másolni kívánt adatokat az elmúlt órában.

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
Ebben a példában az **sqlReaderQuery** az SqlSource-hoz van megadva. A Másolási tevékenység futtatja ezt a lekérdezést az SQL Server adatbázis forrásában az adatok lekérdezéséhez. Másik lehetőségként megadhatja a tárolt eljárást az **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paramétereket vesz igénybe). Az sqlReaderQuery a bemeneti adatkészlet által hivatkozott adatbázison belül több táblára is hivatkozhat. Ez nem korlátozódik csak a táblakészlet, mint az adatkészlet tableName typeProperty.

Ha nem adja meg az sqlReaderQuery vagy az sqlReaderStoredProcedureName értéket, a struktúraszakaszban definiált oszlopok segítségével az SQL Server-adatbázison futtatandó választó lekérdezést hoz létre. Ha az adatkészlet-definíció nem rendelkezik a szerkezettel, akkor a program az összes oszlopot kijelöli a táblából.

Tekintse meg az [Sql Source](#sqlsource) szakasz és [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) az SqlSource és a BlobSink által támogatott tulajdonságok listáját.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Példa: Adatok másolása az Azure Blobból az SQL Serverkiszolgálóra
A következő minta a következőket mutatja:

1. [Az OnPremisesSqlServer](#linked-service-properties)típusú csatolt szolgáltatás.
2. [Az AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú összekapcsolt szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet.](data-factory-create-datasets.md)
4. [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. A [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és az SqlSink-et használó másolási tevékenységet tartalmazó [folyamat.](data-factory-create-pipelines.md)

A minta óránként egy Azure-blobból egy SQL Server-táblába másolja az idősorozat-adatokat. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszok ismertetik.

**SQL Server csatolt szolgáltatás**

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
**Azure Blob storage-hoz csatolt szolgáltatás**

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
**Azure Blob bemeneti adatkészlet**

Az adatokat óránként veszi fel egy új blob (gyakoriság: óra, időköz: 1). A blob mappaelérési útja és fájlneve dinamikusan kiértékelésre kerül a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap és nap részét, a fájlnév pedig a kezdési időpont órarészét használja. "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet az adat-előállítón kívül van, és nem az adat-előállító tevékenység által előállított.

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

A minta az adatokat egy "MyTable" nevű táblába másolja az SQL Server rendszerben. Hozza létre a táblát az SQL Serverben ugyanannyi oszloppal, mint amennyit a Blob CSV-fájl tartalmaz. A rendszer óránként új sorokat ad a táblához.

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
**Folyamat másolási tevékenységgel**

A folyamat tartalmaz egy másolási tevékenységet, amely úgy van beállítva, hogy használja ezeket a bemeneti és kimeneti adatkészletek, és az ütemezés szerint óránként fut. A folyamat JSON-definíciójában a **forrástípus** **BlobSource** lesz állítva, **a fogadó** típusa pedig **SqlSink**.

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
1. Állítsa be az SQL Server t távoli kapcsolatok fogadására. Indítsa el **az SQL Server Management Studio**alkalmazást, kattintson a jobb gombbal a **kiszolgálóra**, és kattintson **a Tulajdonságok parancsra.** Válassza a **Kapcsolatok** elemet a listából, és jelölje be **a Távoli kapcsolatok engedélyezése a kiszolgálóhoz jelölőnégyzetet.**

    ![Távoli kapcsolatok engedélyezése](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    A részletes lépésekért olvassa el [a Távelérés-kiszolgáló konfigurációs beállításának konfigurálása](https://msdn.microsoft.com/library/ms191464.aspx) című témakört.
2. Indítsa el **az SQL Server Configuration Manager programot.** Bontsa ki a kívánt **példánysql Server hálózati konfigurációját,** és válassza **az MSSQLSERVER protokolljai**lehetőséget. A jobb oldali ablaktáblában meg kell jelennie a protokolloknak. A TCP/IP engedélyezése a jobb gombbal a **TCP/IP elemre** kattintva, majd az **Engedélyezés**parancsra kattintva.

    ![TCP/IP engedélyezése](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    A TCP/IP protokoll engedélyezéséről [és letiltásáról a Kiszolgálóhálózati protokoll engedélyezése vagy letiltása](https://msdn.microsoft.com/library/ms191294.aspx) című témakörben talál.
3. Ugyanebben az ablakban kattintson duplán a **TCP/IP** elemre a **TCP/IP tulajdonságai** ablak elindításához.
4. Váltson az **IP-címek** lapra. Görgessen le az **IPAll** szakasz megtekintéséhez. Megjegyzés a **TCP-porton**(az alapértelmezett **érték 1433**).
5. Hozzon létre egy **szabályt a számítógépen lévő Windows tűzfalhoz,** amely engedélyezi a bejövő forgalmat ezen a porton keresztül.
6. **Kapcsolat ellenőrzése**: Ha teljesen minősített névvel szeretne csatlakozni az SQL Server kiszolgálóhoz, használja az SQL Server Management Studio alkalmazást egy másik gépről. Például:\<"\>gép . \<domain\>\<.corp.\>company .com,1433."

   > [!IMPORTANT]
   > 
   > Részletes információkért [lásd: Adatok áthelyezése a helyszíni források és a felhő között az adatkezelési átjáróval](data-factory-move-data-between-onprem-and-cloud.md) című témakörben.
   > 
   > A kapcsolatokkal/átjáróval kapcsolatos problémák elhárításával kapcsolatos tippek az [átjáróval kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) című témakörben olvashat.


## <a name="identity-columns-in-the-target-database"></a>Identitásoszlopok a céladatbázisban
Ez a szakasz egy olyan példát tartalmaz, amely egy identitásoszlop nélküli forrástáblából másolja az adatokat egy identitásoszlopot tartalmazó céltáblába.

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

Figyelje meg, hogy a céltábla identitásoszloppal rendelkezik.

**JSON forrásadatkészlet-definíció**

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
**Céladatkészlet JSON-definíciója**

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

Figyelje meg, hogy a forrás és a céltábla különböző sémával rendelkezik (a cél nak van egy további identitású oszlopa). Ebben a forgatókönyvben meg kell **adnia a struktúra** tulajdonságot a céladatkészlet-definícióban, amely nem tartalmazza az identitásoszlopot.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Tárolt eljárás meghívása az SQL-fogadóból
Lásd: [Sql sink tárolt eljárás meghívása másolási tevékenység cikkben](data-factory-invoke-stored-procedure-from-copy-activity.md) egy példa a tárolt eljárás az SQL-fogadó egy folyamat másolási tevékenysége.

## <a name="type-mapping-for-sql-server"></a>Az SQL-kiszolgáló típusleképezése
Az [adatmozgatási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben említettek szerint a Másolás tevékenység automatikus típuskonverziót hajt végre a forrástípusokról a fogadótípusokra a következő kétlépéses megközelítéssel:

1. Konvertálás natív forrástípusokból .NET-típussá
2. Konvertálás .NET típusból natív fogadótípussá

Amikor adatokat helyez át & SQL-kiszolgálóról, a rendszer a következő leképezéseket használja az SQL-típusról a .NET típusra, és fordítva.

A leképezés megegyezik a ADO.NET SQL Server adattípus-leképezésével.

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

## <a name="mapping-source-to-sink-columns"></a>Forrás leképezése oszlopokhoz
Ha oszlopokat szeretne leképezni a forrásadatkészletről a fogadó adatkészletoszlopaira, olvassa el [az Adatkészletoszlopok leképezése az Azure Data Factoryban című témakört.](data-factory-map-columns.md)

## <a name="repeatable-copy"></a>Ismételhető másolat
Amikor adatokat másol az SQL Server Database szolgáltatásba, a másolási tevékenység alapértelmezés szerint adatokat fűz a fogadótáblához. UpSERT-művelet végrehajtásához tekintse meg [az SqlSink-cikk ismétlése.](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink)

Ha relációs adattárakból másolja az adatokat, tartsa szem előtt az ismételhetőséget a nem kívánt eredmények elkerülése érdekében. Az Azure Data Factoryban manuálisan futtathatja a szeletet. Az adatkészlet újrapróbálkozási házirendje is konfigurálható, így a szelet újrafut, ha hiba történik. Ha egy szeletet mindkét irányban újrafuttat, meg kell győződnie arról, hogy ugyanazokat az adatokat olvassa el, függetlenül attól, hogy hányszor fut egy szelet. Lásd: [Ismételhető olvasás relációs forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [Tevékenység teljesítményének másolása & hangolási útmutatóban](data-factory-copy-activity-performance.md) megismerést talál az adatok (másolási tevékenység) azure Data Factory ban az adatmozgatás (másolási tevékenység) teljesítményét befolyásoló legfontosabb tényezőkről, valamint az optimalizálás különböző módjairól.
