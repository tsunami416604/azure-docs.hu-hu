---
title: Az Azure Data Factory - csoportos adatelemzési folyamat SQL Azure, SQL Server-adatok
description: Állítsa be az ADF-folyamatot, amely két együtt adatáthelyezést naponta helyszíni adatbázisok között, és a felhőbeli adatok migrálási tevékenységek composes.
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
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Adatok áthelyezése a helyszíni SQL Serverről az SQL Azure, az Azure Data Factoryvel

Ez a cikk bemutatja, hogyan helyezhetők át az adatok egy helyszíni SQL Server-adatbázisból egy SQL Azure-adatbázisba az Azure Blob Storage használatával a Azure Data Factory (ADF) használatával: Ez a módszer egy olyan támogatott örökölt megközelítés, amely a replikált átmeneti példányok előnyeivel rendelkezik, de [javasoljuk, hogy tekintse meg az adatáttelepítési oldalunkat a legújabb beállításokkal](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

Az adatAzure SQL Databaseba való áthelyezés különböző lehetőségeit összefoglaló táblázatért lásd: az [adatáthelyezés egy Azure SQL Databaseba Azure Machine learning](move-sql-azure.md).

## <a name="intro"></a>Bevezetés: Mi az az ADF, és mikor kell használni az adatáttelepítést?
Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatáthelyezési és -átalakítási adatok. A kulcs az ADF modell koncepciójuk folyamat. Egy folyamat olyan tevékenységek logikus csoportosításai, amelyek mindegyike meghatározza az adatkészletekben található adatokon végrehajtandó műveleteket. Társított szolgáltatások meghatározzák azokat az információkat a Data Factory az adatforrásokhoz való kapcsolódáshoz szolgálnak.

Az ADF használatával a meglévő adatfeldolgozási szolgáltatások összeállítható alakíthatók ki adatcsatornák, amelyek magas rendelkezésre állású, felügyelt felhőben. Ezek adatfolyamatok ütemezhető a betöltési, előkészítése, átalakíthatja, elemezheti és adatok közzététele, és az ADF felügyeli, és az összetett adatokat és a feldolgozási függőségek koordinálja. Megoldások is gyorsan létrehozott és a felhőben üzembe helyezett helyszíni egyre nagyobb számban csatlakoztatása és felhőbeli adatforrásokból.

Vegye figyelembe, hogy az ADF használatával:

* Ha adatokat lehet áttelepíteni, a folyamatosan egy hibrid forgatókönyvben, amely egyaránt hozzáfér a helyszíni és a felhőbeli erőforrások
* Ha az áttelepítés során az adatátalakításra vagy az üzleti logikára van szükség.

Az ADF lehetővé teszi, hogy az ütemezés és a figyelési feladatok használata kezelheti az adatmozgás rendszeres időközönként egyszerű JSON-szkript. Az ADF is tartalmaz egyéb szolgáltatásokat, például az összetett műveletek támogatása. Az ADF-vel kapcsolatos további információkért tekintse meg a dokumentációt a következő helyen: [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>A forgatókönyv
Beállítjuk az ADF-folyamatot, amely composes két az áttelepítési tevékenységeket. A helyszíni SQL Database és a felhőben lévő Azure SQL Database között napi szinten helyezik át az adatátvitelt. A két tevékenységek a következők:

* adatok másolása helyszíni SQL Server-adatbázisból Azure Blob Storage-fiók
* adatok másolása az Azure Blob Storage-fiókhoz az Azure SQL Database.

> [!NOTE]
> Az itt bemutatott lépéseket az ADF-csapat részletesebb oktatóanyaga alapján alakítottuk ki: [adatokat másolhat egy helyszíni SQL Server-adatbázisból az Azure Blob Storage-ra](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) az adott témakör megfelelő részeire, amennyiben szükséges.
>
>

## <a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure Storage-fiók**. Ez az oktatóanyag az adatok tárolása Azure storage-fiók használhat. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című cikket. Miután létrehozta a tárfiókot, szerezze be a tárterület elérésére használt fiók kulcsot kell. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).
* Hozzáférés egy **Azure SQL Databasehoz**. Ha be kell állítania egy Azure SQL Database, a témakör [első lépések a Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) a Azure SQL Database új példányának kiépítésével kapcsolatos információkat nyújt.
* **Azure PowerShell** helyileg telepítve és konfigurálva. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

> [!NOTE]
> Ez az eljárás a [Azure Portal](https://portal.azure.com/)használja.
>
>

## <a name="upload-data"></a>Töltse fel az adatait a helyszíni SQL Server
A [New York-i taxi-adatkészletet](https://chriswhong.com/open-data/foil_nyc_taxi/) használjuk az áttelepítési folyamat bemutatására. A New York-i taxi adatkészlete az Azure Blob Storage [NYC](https://www.andresmh.com/nyctaxitrips/)-beli, a Poston megjelenő módon érhető el. Az adatok rendelkezik két fájlt, a trip_data.csv fájlt, amely trip részleteit tartalmazza, és a trip_far.csv fájlt, amely tartalmazza az egyes út fizetett diszkont részleteit. Ezen fájlok mintáját és leírását a [New York-i taxis adatkészletének leírásában](sql-walkthrough.md#dataset)ismertetjük.

Az eljárás a saját adatok készletét használja az itt elérhető alkalmazkodik, vagy kövesse a lépéseket, a NYC Taxi adatkészlet használatával leírtak szerint. A New York-i taxi-adatkészlet helyszíni SQL Server adatbázisba való feltöltéséhez kövesse az [adatok tömeges importálása SQL Server-adatbázisba](sql-walkthrough.md#dbload)című szakaszban leírt eljárást. Ezek az utasítások a egy SQL Server Azure virtuális gépen, de tölt fel a helyszíni SQL Server eljárás megegyezik.

## <a name="create-adf"></a>Azure Data Factory létrehozása
Az új Azure Data Factory és egy erőforráscsoport létrehozásához szükséges útmutatást a [Azure Portal](https://portal.azure.com/) [hozzon létre Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Nevezze el az új ADF-példány *adfdsp* , és nevezze el a *adfdsprg*létrehozott erőforráscsoportot.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Telepítse és konfigurálja az Azure Data Factory integrációs modul
Az Integration Runtime egy ügyfél által felügyelt adatintegrációs infrastruktúra, amelyet a Azure Data Factory használ az adatintegrációs képességek különböző hálózati környezetekben történő biztosításához. Ez a modul nevén "Adatkezelési átjáró" volt.

A beállításához [kövesse a folyamat létrehozásának utasításait](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline) .

## <a name="adflinkedservices"></a>Társított szolgáltatások létrehozása az adaterőforrásokhoz való kapcsolódáshoz
A társított szolgáltatás határozza meg az információkat, amelyeket az Azure Data Factory egy adatforrás, melyhez csatlakozni. Három erőforrást van ebben a forgatókönyvben, amelyhez a társított szolgáltatások szükségesek:

1. On-premises SQL Server
2. Azure Blob Storage
3. Azure SQL Database

A társított szolgáltatások létrehozásához szükséges lépésenkénti eljárást a [társított szolgáltatások létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)című témakörben ismertetjük.


## <a name="adf-tables"></a>Táblázatok definiálása és létrehozása az adatkészletek elérési módjának megadásához
Hozzon létre táblákat, amelyek a struktúra, helyen és az adatkészletek rendelkezésre állását a parancsprogramfájlon alapuló menete. JSON-fájlok segítségével a-táblát határoz meg. További információ a fájlok struktúrájáról: [adatkészletek](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> A [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) parancsmag végrehajtása előtt végre kell hajtania a `Add-AzureAccount` parancsmagot annak ellenőrzéséhez, hogy a megfelelő Azure-előfizetés van-e kiválasztva a parancs végrehajtásához. A parancsmag dokumentációját lásd: [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

A táblák a JSON-alapú definíciókat használja a következő nevekkel:

* a helyszíni SQL Server-kiszolgáló **neve** *nyctaxi_data*
* a **tároló neve** az Azure Blob Storage-fiókban *ContainerName*

Három tábladefiníciókat az ADF folyamat van szükség:

1. [Helyszíni SQL-tábla](#adf-table-onprem-sql)
2. [BLOB-tábla](#adf-table-blob-store)
3. [SQL Azure táblázat](#adf-table-azure-sql)

> [!NOTE]
> Ezek az eljárások az Azure PowerShell használatával határozza meg, és hozzon létre az ADF tevékenységek. Azonban ezeket a feladatokat az Azure portal használatával is elvégezhető. Részletekért lásd: [adatkészletek létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>Helyszíni SQL-tábla
A helyszíni SQL Server-definíció van megadva a következő JSON-fájlban:

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

Az oszlopnevek nem kerültek bele itt. Az oszlopnevek kiválasztásához adja meg azokat itt is (a részletekért tekintse meg az [ADF dokumentációs](../../data-factory/copy-activity-overview.md) témakört.

Másolja a táblázat JSON-definícióját egy *onpremtabledef. JSON* nevű fájlba, és mentse egy ismert helyre (itt feltételezzük, hogy *C:\temp\onpremtabledef.JSON*). A tábla létrehozása az ADF-ben a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>BLOB-tábla
A tábla a kimeneti blob helyének meghatározása szerepel (Ez hozzárendeli a betöltött adatok a helyszínről az Azure-blobba) a következőket:

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

Másolja a táblázat JSON-definícióját egy *bloboutputtabledef. JSON* nevű fájlba, és mentse egy ismert helyre (itt feltételezzük, hogy *C:\temp\bloboutputtabledef.JSON*). A tábla létrehozása az ADF-ben a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure táblázat
Az SQL Azure-tábla definícióját, kimeneti (ebben a sémában leképezi a blob származó adatokon) a következőket:

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

Másolja a táblázat JSON-definícióját egy *tulajdonsága azuresqltable. JSON* nevű fájlba, és mentse egy ismert helyre (itt feltételezzük, hogy *C:\temp\AzureSqlTable.JSON*). A tábla létrehozása az ADF-ben a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>A folyamat definiálása és létrehozása
Adja meg a tevékenységek, amelyek az adatcsatornához tartozó, és létrehozhatja a folyamatot a parancsprogramfájlon alapuló az alábbi eljárások segítségével. Egy JSON-fájlt a folyamat tulajdonságainak definiálásához szolgál.

* A parancsfájl feltételezi, hogy a **folyamat neve** *AMLDSProcessPipeline*.
* Azt is vegye figyelembe, hogy a folyamat napi szinten kell végrehajtani, és az alapértelmezett végrehajtási idő (12 am UTC) a feladathoz a periodicitás állítottuk.

> [!NOTE]
> Az alábbi eljárások határozza meg, és hozhat létre az ADF-folyamatot az Azure PowerShell használatával. Azonban ez a feladat az Azure portal használatával is elvégezhető. Részletekért lásd: [folyamat létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

A korábban megadott tábladefiníciókat használja, az ADF folyamat definíciója van megadva a következő:

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

Másolja a folyamat JSON-definícióját egy *pipelinedef. JSON* nevű fájlba, és mentse egy ismert helyre (itt feltételezzük, hogy *C:\temp\pipelinedef.JSON*). A folyamat létrehozása az ADF-ben a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>A folyamat elindítása
Most már futtathatók a folyamat a következő paranccsal:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

A *StartDate* és a *EndDate* paraméter értékeit le kell cserélni azokra a tényleges dátumokra, amelyeknek a folyamatát futtatni kívánja.

A folyamat végrehajtása után megtekintheti az adatok megjelennek a blob, naponta egy fájlt a kiválasztott kell lennie.

Az ADF által biztosított funkcionalitást nem használjuk növekményes adatcsatornára. Ennek módjáról és az ADF által biztosított egyéb képességekről további információt az [ADF dokumentációjában](https://azure.microsoft.com/services/data-factory/)talál.
