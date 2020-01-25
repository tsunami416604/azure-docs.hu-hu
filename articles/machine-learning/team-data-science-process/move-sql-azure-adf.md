---
title: Az Azure Data Factory-Team adatelemzési folyamattal SQL Azure SQL Server
description: Állítson be egy olyan ADF-folyamatot, amely két olyan adatáttelepítési tevékenységet hoz létre, amelyek napi rendszerességgel helyezik át az adatátvitelt a helyszíni és a Felhőbeli adatbázisok között.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722492"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Adatok áthelyezése helyszíni SQL Server-kiszolgálóról SQL Azurera Azure Data Factory

Ez a cikk bemutatja, hogyan helyezhetők át az adatok egy helyszíni SQL Server-adatbázisból egy SQL Azure-adatbázisba az Azure Blob Storage használatával a Azure Data Factory (ADF) használatával: Ez a módszer egy olyan támogatott örökölt megközelítés, amely a replikált átmeneti példányok előnyeivel rendelkezik, de [javasoljuk, hogy tekintse meg az adatáttelepítési oldalunkat a legújabb beállításokkal](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

Az adatAzure SQL Databaseba való áthelyezés különböző lehetőségeit összefoglaló táblázatért lásd: az [adatáthelyezés egy Azure SQL Databaseba Azure Machine learning](move-sql-azure.md).

## <a name="intro"></a>Bevezetés: Mi az az ADF, és mikor kell használni az adatáttelepítést?
A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás, amely összehangolja és automatizálja az adatátvitelt és-átalakítást. Az ADF-modell legfontosabb koncepciója a folyamat. A folyamat a tevékenységek logikai csoportosítása, amelyek mindegyike meghatározza az adatkészletekben található adatokon végrehajtandó műveleteket. A társított szolgáltatások az adaterőforrásokhoz való kapcsolódás Data Factoryához szükséges információk meghatározására szolgálnak.

Az ADF-sel a meglévő adatfeldolgozási szolgáltatások a felhőben elérhető és felügyelt adatfolyamatokból is állhatnak. Ezeket az adatfolyamatokat ütemezheti az adatfeldolgozásra,-előkészítésre,-átalakításra,-elemzésre és-közzétételre, és az ADF kezeli és koordinálja az összetett adatmennyiségeket és a feldolgozási függőségeket. A megoldások gyorsan létrehozhatók és üzembe helyezhetők a felhőben, így egyre több helyszíni és Felhőbeli adatforráshoz kapcsolódhat.

Használjon ADF-et:

* Ha a helyszíni és a Felhőbeli erőforrásokhoz egyaránt hozzáférő hibrid forgatókönyvben folyamatosan át kell telepíteni az adatátvitelt
* Ha az áttelepítés során az adatátalakításra vagy az üzleti logikára van szükség.

Az ADF lehetővé teszi a feladatok ütemezését és figyelését olyan egyszerű JSON-parancsfájlokkal, amelyek rendszeres időközönként kezelik az adatok áthelyezését. Az ADF más képességekkel is rendelkezik, mint például az összetett műveletek támogatása. Az ADF-vel kapcsolatos további információkért tekintse meg a dokumentációt a következő helyen: [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>A forgatókönyv
Beállíthat egy ADF-folyamatot, amely két adatáttelepítési tevékenységet állít össze. A helyszíni SQL Database és a felhőben lévő Azure SQL Database között napi szinten helyezik át az adatátvitelt. A két tevékenység a következők:

* adatok másolása helyszíni SQL Server-adatbázisból egy Azure Blob Storage-fiókba
* adatok másolása az Azure Blob Storage-fiókból egy Azure SQL Databaseba.

> [!NOTE]
> Az itt bemutatott lépéseket az ADF-csapat részletesebb oktatóanyaga alapján alakítottuk ki: [adatokat másolhat egy helyszíni SQL Server-adatbázisból az Azure Blob Storage-ra](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) az adott témakör megfelelő részeire, amennyiben szükséges.
>
>

## <a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy rendelkezik a következővel:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure Storage-fiók**. Az ebben az oktatóanyagban tárolt adattároláshoz Azure Storage-fiókot használunk. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című cikket. A Storage-fiók létrehozása után be kell szereznie a tárolóhoz való hozzáféréshez használt fiók kulcsát. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).
* Hozzáférés egy **Azure SQL Databasehoz**. Ha be kell állítania egy Azure SQL Database, a témakör [első lépések a Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) a Azure SQL Database új példányának kiépítésével kapcsolatos információkat nyújt.
* **Azure PowerShell** helyileg telepítve és konfigurálva. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

> [!NOTE]
> Ez az eljárás a [Azure Portal](https://portal.azure.com/)használja.
>
>

## <a name="upload-data"></a>Töltse fel az adatait a helyszíni SQL Server
A [New York-i taxi-adatkészletet](https://chriswhong.com/open-data/foil_nyc_taxi/) használjuk az áttelepítési folyamat bemutatására. A New York-i taxi adatkészlete az Azure Blob Storage [NYC](https://www.andresmh.com/nyctaxitrips/)-beli, a Poston megjelenő módon érhető el. Az adatoknak két fájlja van, a trip_data. csv fájl, amely tartalmazza az utazás részleteit, valamint a trip_far. csv fájlt, amely tartalmazza az egyes utazásokhoz fizetett viteldíj részleteit. Ezen fájlok mintáját és leírását a [New York-i taxis adatkészletének leírásában](sql-walkthrough.md#dataset)ismertetjük.

Az itt megadott eljárást a saját adataihoz igazíthatja, vagy a New York-i taxi-adatkészletben leírt lépéseket követve hajthatja végre. A New York-i taxi-adatkészlet helyszíni SQL Server adatbázisba való feltöltéséhez kövesse az [adatok tömeges importálása SQL Server-adatbázisba](sql-walkthrough.md#dbload)című szakaszban leírt eljárást. Ezek az utasítások az Azure-beli virtuális gépek SQL Serverére vonatkoznak, de a helyszíni SQL Server való feltöltésének eljárása azonos.

## <a name="create-adf"></a>Azure Data Factory létrehozása
Az új Azure Data Factory és egy erőforráscsoport létrehozásához szükséges útmutatást a [Azure Portal](https://portal.azure.com/) [hozzon létre Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Nevezze el az új ADF-példány *adfdsp* , és nevezze el a *adfdsprg*létrehozott erőforráscsoportot.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Azure Data Factory telepítése és konfigurálása Integration Runtime
Az Integration Runtime egy ügyfél által felügyelt adatintegrációs infrastruktúra, amelyet a Azure Data Factory használ az adatintegrációs képességek különböző hálózati környezetekben történő biztosításához. Ezt a futtatókörnyezetet korábban "adatkezelés átjárónak" hívták.

A beállításához [kövesse a folyamat létrehozásának utasításait](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline) .

## <a name="adflinkedservices"></a>Társított szolgáltatások létrehozása az adaterőforrásokhoz való kapcsolódáshoz
A társított szolgáltatások meghatározzák azokat az információkat, amelyek szükségesek ahhoz, hogy a Azure Data Factory egy adaterőforráshoz kapcsolódjanak. Ebben a forgatókönyvben három olyan erőforrás van, amelyhez társított szolgáltatások szükségesek:

1. Helyszíni SQL Server
2. Azure Blob Storage
3. Azure SQL Database

A társított szolgáltatások létrehozásához szükséges lépésenkénti eljárást a [társított szolgáltatások létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)című témakörben ismertetjük.


## <a name="adf-tables"></a>Táblázatok definiálása és létrehozása az adatkészletek elérési módjának megadásához
Olyan táblákat hozhat létre, amelyek a következő parancsfájl-alapú eljárásokkal határozzák meg az adatkészletek szerkezetét, helyét és rendelkezésre állását. A táblák definiálásához a JSON-fájlok használhatók. További információ a fájlok struktúrájáról: [adatkészletek](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> A [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) parancsmag végrehajtása előtt végre kell hajtania a `Add-AzureAccount` parancsmagot annak ellenőrzéséhez, hogy a megfelelő Azure-előfizetés van-e kiválasztva a parancs végrehajtásához. A parancsmag dokumentációját lásd: [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

A táblák JSON-alapú definíciói a következő neveket használják:

* a helyszíni SQL Server-kiszolgáló **neve** *nyctaxi_data*
* a **tároló neve** az Azure Blob Storage-fiókban *ContainerName*

Ehhez az ADF-folyamathoz három táblázatos definíció szükséges:

1. [Helyszíni SQL-tábla](#adf-table-onprem-sql)
2. [BLOB-tábla](#adf-table-blob-store)
3. [SQL Azure táblázat](#adf-table-azure-sql)

> [!NOTE]
> Ezek az eljárások a Azure PowerShell segítségével határozzák meg és hozhatják létre az ADF-tevékenységeket. Ezek a feladatok azonban a Azure Portal használatával is elvégezhetők. Részletekért lásd: [adatkészletek létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>Helyszíni SQL-tábla
A helyszíni SQL Server táblázatos definíciója a következő JSON-fájlban van megadva:

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

Az oszlopnevek nem szerepelnek itt. Az oszlopnevek kiválasztásához adja meg azokat itt is (a részletekért tekintse meg az [ADF dokumentációs](../../data-factory/copy-activity-overview.md) témakört.

Másolja a táblázat JSON-definícióját egy *onpremtabledef. JSON* nevű fájlba, és mentse egy ismert helyre (itt feltételezzük, hogy *C:\temp\onpremtabledef.JSON*). Hozza létre a táblát az ADF-ben a következő Azure PowerShell parancsmaggal:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>BLOB-tábla
A kimeneti blob helyéhez tartozó táblázat definíciója a következő (ez képezi le a helyszíni adatok Azure blobba történő betöltését):

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

Másolja a táblázat JSON-definícióját egy *bloboutputtabledef. JSON* nevű fájlba, és mentse egy ismert helyre (itt feltételezzük, hogy *C:\temp\bloboutputtabledef.JSON*). Hozza létre a táblát az ADF-ben a következő Azure PowerShell parancsmaggal:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure táblázat
A SQL Azure kimenetéhez tartozó táblázat definíciója a következő (ez a séma képezi le a blobból érkező adatokat):

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

Másolja a táblázat JSON-definícióját egy *tulajdonsága azuresqltable. JSON* nevű fájlba, és mentse egy ismert helyre (itt feltételezzük, hogy *C:\temp\AzureSqlTable.JSON*). Hozza létre a táblát az ADF-ben a következő Azure PowerShell parancsmaggal:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>A folyamat definiálása és létrehozása
Adja meg a folyamathoz tartozó tevékenységeket, és hozza létre a folyamatot a következő parancsfájl-alapú eljárásokkal. A folyamat tulajdonságainak definiálásához JSON-fájl használható.

* A parancsfájl feltételezi, hogy a **folyamat neve** *AMLDSProcessPipeline*.
* Azt is vegye figyelembe, hogy a folyamat gyakoriságát napi rendszerességgel kell végrehajtani, és a feladatokhoz tartozó alapértelmezett végrehajtási időt (12 UTC) kell használni.

> [!NOTE]
> Az alábbi eljárások az ADF-folyamat definiálásához és létrehozásához Azure PowerShell használnak. Ez a feladat azonban a Azure Portal használatával is végrehajtható. Részletekért lásd: [folyamat létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

A korábban megadott táblázat-definíciók használatával az ADF-hez tartozó folyamat definícióját a következőképpen adja meg:

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

Másolja a folyamat JSON-definícióját egy *pipelinedef. JSON* nevű fájlba, és mentse egy ismert helyre (itt feltételezzük, hogy *C:\temp\pipelinedef.JSON*). Hozza létre a folyamatot az ADF-ben a következő Azure PowerShell parancsmaggal:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>A folyamat elindítása
A folyamat mostantól a következő paranccsal futtatható:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

A *StartDate* és a *EndDate* paraméter értékeit le kell cserélni azokra a tényleges dátumokra, amelyeknek a folyamatát futtatni kívánja.

Miután a folyamat végrehajtja a folyamatot, látnia kell a blobhoz kiválasztott tárolóban megjelenő adatmennyiséget, naponta egy fájlt.

Az ADF által biztosított funkcionalitást nem használjuk növekményes adatcsatornára. Ennek módjáról és az ADF által biztosított egyéb képességekről további információt az [ADF dokumentációjában](https://azure.microsoft.com/services/data-factory/)talál.
