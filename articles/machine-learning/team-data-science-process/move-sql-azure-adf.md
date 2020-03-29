---
title: SQL Server-adatok az SQL Azure-ba az Azure Data Factory segítségével – Csapatadatelemzési folyamat
description: Hozzon létre egy ADF-folyamatot, amely két adatáttelepítési tevékenységet állít össze, amelyek naponta áthelyezik az adatokat a helyszíni és a felhőbeli adatbázisok között.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8f696f1c6c414cd9db082e79e0f34c56156e1ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722492"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Adatok áthelyezése egy helyszíni SQL-kiszolgálóról az SQL Azure-ba az Azure Data Factory segítségével

Ez a cikk bemutatja, hogyan helyezhetát át az adatokat egy helyszíni SQL Server-adatbázisból egy SQL Azure-adatbázisba az Azure Data Storage (ADF) segítségével: ez a módszer egy támogatott örökölt megközelítés, amely a replikált átmeneti példány előnyeit kínálja, bár [javasoljuk, hogy tekintse meg az adatáttelepítési oldalunkat a legújabb lehetőségekért.](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1)

Az Adatok Áthelyezése Azure SQL-adatbázisba való áthelyezésének különböző lehetőségeit összegző táblázatról az [Adatok áthelyezése Azure Machine Learninghez](move-sql-azure.md)című témakörben található.

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Bevezetés: Mi az ADF, és mikor kell használni az adatok áttelepítéséhez?
Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás, amely vezényli és automatizálja az adatok mozgását és átalakítását. Az ADF-modell kulcsfogalma a folyamat. A folyamat a tevékenységek logikai csoportosítása, amely meghatározza az adatkészletekben lévő adatokon végrehajtandó műveleteket. A csatolt szolgáltatások segítségével határozható meg a Data Factory az adaterőforrásokhoz való csatlakozáshoz szükséges információk.

Az ADF segítségével a meglévő adatfeldolgozási szolgáltatások olyan adatfolyamatokba is összeállhatnak, amelyek magas rendelkezésre állásúak és a felhőben kezelhetők. Ezek az adatfolyamatok ütemezhetők adatok betöltésére, előkészítésére, átalakítására, elemzésére és közzétételére, és az ADF kezeli és vezényli az összetett adat- és feldolgozási függőségeket. A megoldások gyorsan kiépíthetők és üzembe helyezhetők a felhőben, és egyre több helyszíni és felhőbeli adatforrást kapcsolnak össze.

Fontolja meg az ADF használatát:

* amikor az adatokat folyamatosan át kell telepíteni egy olyan hibrid forgatókönyvben, amely mind a helyszíni, mind a felhőbeli erőforrásokhoz hozzáfér
* amikor az adatok átalakításra szorulnak, vagy az áttelepítés során üzleti logikát adnak hozzá.

Az ADF lehetővé teszi a feladatok ütemezését és figyelését egyszerű JSON-parancsfájlok használatával, amelyek rendszeres időközönként kezelik az adatok mozgását. Az ADF más képességekkel is rendelkezik, például összetett műveletek támogatásával. Az ADF-ről az Azure [Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/)dokumentációjában talál további információt.

## <a name="the-scenario"></a><a name="scenario"></a>A forgatókönyv
Létrehoztunk egy ADF-folyamatot, amely két adatáttelepítési tevékenységet állít össze. Együtt naponta áthelyezik az adatokat egy helyszíni SQL-adatbázis és egy Azure SQL-adatbázis között a felhőben. A két tevékenység a következő:

* adatok másolása egy helyszíni SQL Server-adatbázisból egy Azure Blob Storage-fiókba
* adatok másolása az Azure Blob Storage-fiókból egy Azure SQL-adatbázisba.

> [!NOTE]
> Az itt látható lépéseket az ADF-csapat által biztosított részletesebb oktatóanyagból igazítottuk: [Adatok másolása egy helyszíni SQL Server-adatbázisból](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) az Azure Blob storage-hivatkozásokba az adott témakör megfelelő szakaszaira, adott esetben.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy:

* **Egy Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure-tárfiók.** Az oktatóanyagban található adatok tárolására azure-tárfiókot használ. Ha nem rendelkezik Azure-tárfiókkal, tekintse meg a [tárfiók létrehozása](../../storage/common/storage-account-create.md) cikket. Miután létrehozta a tárfiókot, be kell szereznie a tároló eléréséhez használt fiókkulcsot. Lásd: [Tárfiók hozzáférési kulcsainak kezelése.](../../storage/common/storage-account-keys-manage.md)
* Hozzáférés egy **Azure SQL-adatbázishoz.** Ha be kell állítania egy Azure SQL-adatbázist, az Első lépések a [Microsoft Azure SQL Database-rel](../../sql-database/sql-database-get-started.md) című témakörben található, amely az Azure SQL-adatbázis új példányának kiépítéséről nyújt tájékoztatást.
* Helyileg telepített és konfigurált **Azure PowerShell.** További információt az [Azure PowerShell telepítése és konfigurálása című témakörben talál.](/powershell/azure/overview)

> [!NOTE]
> Ez az eljárás az [Azure Portalt](https://portal.azure.com/)használja.
>
>

## <a name="upload-the-data-to-your-on-premises-sql-server"></a><a name="upload-data"></a>Az adatok feltöltése a helyszíni SQL Server kiszolgálóra
Az áttelepítési folyamat bemutatására a [NYC Taxi adatkészletet használjuk.](https://chriswhong.com/open-data/foil_nyc_taxi/) A NYC Taxi adatkészlet érhető el, amint azt a post, az Azure blob storage [NYC Taxi data](https://www.andresmh.com/nyctaxitrips/). Az adatok két fájlt tartalmaznak, a trip_data.csv fájlt, amely tartalmazza az utazás részleteit, és a trip_far.csv fájlt, amely az egyes utazásokért fizetett viteldíj részleteit tartalmazza. A minta és leírása ezeket a fájlokat a [nyc taxi utak adatkészlet leírása](sql-walkthrough.md#dataset).

Módosíthatja az itt megadott eljárást a saját adataihoz, vagy követheti a NYC Taxi adatkészlet használatával leírt lépéseket. A NYC Taxi adatkészlet nek a helyszíni SQL Server adatbázisba való feltöltéséhez kövesse a [Tömeges adatok importálása az SQL Server adatbázisba](sql-walkthrough.md#dbload)című dokumentumban ismertetett eljárást. Ezek az utasítások egy Azure virtuális gépen lévő SQL Server, de a helyszíni SQL Server-re való feltöltési eljárás ugyanaz.

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a>Azure-adatgyár létrehozása
Az [azure-portálon](https://portal.azure.com/) egy új Azure Data Factory és egy erőforráscsoport létrehozásához adott utasítások az [Azure Data Factory létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory)szolgáltatást tartalmaznak. Nevezze el az új ADF-példány *adfdsp-t,* és nevezze el az *adfdsprg-et*létrehozó erőforráscsoportot.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Az Azure Data Factory Integration Runtime telepítése és konfigurálása
Az integrációs futásidő az Azure Data Factory által használt ügyfél által felügyelt adatintegrációs infrastruktúra, amely különböző hálózati környezetekben biztosítja az adatintegrációs képességeket. Ezt a futási időt korábban "Adatkezelési átjárónak" hívták.

A beállításhoz [kövesse a csővezeték létrehozásához vezető utasításokat](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Csatolt szolgáltatások létrehozása az adaterőforrásokhoz való csatlakozáshoz
A csatolt szolgáltatás határozza meg az Azure Data Factory egy adaterőforráshoz való csatlakozáshoz szükséges információkat. Ebben a forgatókönyvben három erőforrással rendelkezünk, amelyekhez kapcsolódó szolgáltatásokra van szükség:

1. Helyszíni SQL Server
2. Azure Blob Storage
3. Azure SQL Database

A csatolt szolgáltatások létrehozásának lépésről lépésre történő eljárása a [Csatolt szolgáltatások létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)című részben található.


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Táblák definiálása és létrehozása az adatkészletek elérésének megadásához
Hozzon létre táblákat, amelyek az adatkészletek szerkezetét, helyét és elérhetőségét a következő parancsfájlalapú eljárásokkal határozzák meg. JSON fájlokat használnak, hogy meghatározza a táblákat. A fájlok szerkezetéről az [Adatkészletek](../../data-factory/concepts-datasets-linked-services.md)című témakörben talál további információt.

> [!NOTE]
> A `Add-AzureAccount` [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) parancsmag végrehajtása előtt végre kell hajtania a parancsmast, és meg kell győződnie arról, hogy a parancs végrehajtásához a megfelelő Azure-előfizetés van-e kiválasztva. A parancsmag dokumentációját az [Add-AzureAccount .For](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0)documentation of this cmdlet, see Add-AzureAccount .
>
>

A táblázatokBan szereplő JSON-alapú definíciók a következő neveket használják:

* a helyszíni SQL-kiszolgálótábla **neve** *nyctaxi_data*
* az Azure Blob Storage-fiókban lévő **tárolónév** *tárolónév*

Ehhez az ADF-folyamathoz három tábladefiníció szükséges:

1. [SQL helyszíni tábla](#adf-table-onprem-sql)
2. [Blob-tábla](#adf-table-blob-store)
3. [SQL Azure-tábla](#adf-table-azure-sql)

> [!NOTE]
> Ezek az eljárások az Azure PowerShell használatával határozzák meg és hozzák létre az ADF-tevékenységeket. De ezek a feladatok is elvégezhetők az Azure Portal használatával. További információt az [Adatkészletek létrehozása című](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)témakörben talál.
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>SQL helyszíni tábla
A helyszíni SQL Server tábladefiníciója a következő JSON-fájlban van megadva:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Az oszlopnevek itt nem szerepeltek. Az oszlopneveken az oszlopok neveit itt mellékelheti (a részletekért tekintse meg az [ADF dokumentációs](../../data-factory/copy-activity-overview.md) témakörét.

Másolja a tábla JSON-definícióját egy *onpremtabledef.json* fájlba, és mentse egy ismert helyre (itt a feltételezett érték *C:\temp\onpremtabledef.json*). Hozza létre a táblát az ADF-ben a következő Azure PowerShell-parancsmaggal:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Blob-tábla
A kimeneti blob helyének a tábla definíciója a következő (ez leképezi a bevitt adatokat a helyszíni Azure blobhoz):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Másolja a tábla JSON-definícióját egy *bloboutputtabledef.json* fájlba, és mentse egy ismert helyre (itt a tanértéke *C:\temp\bloboutputtabledef.json).* Hozza létre a táblát az ADF-ben a következő Azure PowerShell-parancsmaggal:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure-tábla
Az SQL Azure-kimenet tábla definíciója a következő (ez a séma leképezi a blobból érkező adatokat):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Másolja a tábla JSON-definícióját egy *AzureSqlTable.json* fájlba, és mentse egy ismert helyre (itt a következő a *C:\temp\AzureSqlTable.json).* Hozza létre a táblát az ADF-ben a következő Azure PowerShell-parancsmaggal:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>A folyamat definiálása és létrehozása
Adja meg a folyamathoz tartozó tevékenységeket, és hozza létre a folyamatot a következő parancsfájlalapú eljárásokkal. A JSON-fájl a folyamat tulajdonságainak definiálására szolgál.

* A parancsfájl feltételezi, hogy a **folyamat neve** *AMLDSProcessPipeline*.
* Azt is vegye figyelembe, hogy a folyamat napi rendszerességgel végrehajtandó gyakoriságát, és a feladat alapértelmezett végrehajtási idejét használjuk (12:00 UTC).

> [!NOTE]
> A következő eljárások az Azure PowerShell használatával határozzák meg és hozzák létre az ADF-folyamatot. De ez a feladat is elvégezhető az Azure Portal használatával. További információt a [Folyamat létrehozása című témakörben](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)talál.
>
>

A korábban megadott tábladefiníciók használatával az ADF csővezeték-definíciója a következőképpen van megadva:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Másolja a folyamat JSON-definícióját egy *pipelinedef.json* fájlba, és mentse egy ismert helyre (itt *c:\temp\pipelinedef.json).* Hozza létre a folyamatot az ADF-ben a következő Azure PowerShell-parancsmaggal:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>A folyamat indítása
A folyamat most már futtatható a következő paranccsal:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

A *kezdő és* a végpont *paraméterértékeit* le kell cserélni azokkal a tényleges dátumokkal, amelyek között a folyamatot futtatni szeretné.

A folyamat végrehajtása után látnia kell, hogy az adatok megjelennek a blobhoz kiválasztott tárolóban, naponta egy fájlban.

Az ADF által nyújtott funkciókat nem használjuk ki az adatok növekményes csőzésére. Ennek módjáról és az ADF által biztosított egyéb funkciókról az [ADF dokumentációjában](https://azure.microsoft.com/services/data-factory/)olvashat bővebben.
