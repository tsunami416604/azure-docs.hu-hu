---
title: Az Azure Data Factory - csoportos adatelemzési folyamat SQL Azure, SQL Server-adatok
description: Állítsa be az ADF-folyamatot, amely két együtt adatáthelyezést naponta helyszíni adatbázisok között, és a felhőbeli adatok migrálási tevékenységek composes.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a1bb841c1218be0a418583af8ca95b2dff2f67d9
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309501"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Adatok áthelyezése a helyszíni SQL Serverről az SQL Azure, az Azure Data Factoryvel

Ez a cikk bemutatja az adatok áthelyezése egy helyszíni SQL Server-adatbázisból származó SQL Azure-adatbázishoz az Azure Blob Storage-n keresztül az Azure Data Factory (ADF) használatával.

Egy táblázat foglalja össze különböző lehetőségei az adatok áthelyezése az Azure SQL Database, lásd: [adatok áthelyezése az Azure SQL Database az Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Bemutatása: Mi az ADF, és ha ez használható az adatok áttelepítéséhez?
Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatáthelyezési és -átalakítási adatok. A kulcs az ADF modell koncepciójuk folyamat. Egy folyamat olyan tevékenységek logikus csoportosításai, amelyek mindegyike meghatározza az adatkészletekben található adatokon végrehajtandó műveleteket. Társított szolgáltatások meghatározzák azokat az információkat a Data Factory az adatforrásokhoz való kapcsolódáshoz szolgálnak.

Az ADF használatával a meglévő adatfeldolgozási szolgáltatások összeállítható alakíthatók ki adatcsatornák, amelyek magas rendelkezésre állású, felügyelt felhőben. Ezek adatfolyamatok ütemezhető a betöltési, előkészítése, átalakíthatja, elemezheti és adatok közzététele, és az ADF felügyeli, és az összetett adatokat és a feldolgozási függőségek koordinálja. Megoldások is gyorsan létrehozott és a felhőben üzembe helyezett helyszíni egyre nagyobb számban csatlakoztatása és felhőbeli adatforrásokból.

Vegye figyelembe, hogy az ADF használatával:

* Ha adatokat lehet áttelepíteni, a folyamatosan egy hibrid forgatókönyvben, amely egyaránt hozzáfér a helyszíni és a felhőbeli erőforrások
* Ha az adatok tranzakció van, vagy üzleti logikát hozzáadni azt, ha az áttelepítés alatt álló, illetve módosítani kell.

Az ADF lehetővé teszi, hogy az ütemezés és a figyelési feladatok használata kezelheti az adatmozgás rendszeres időközönként egyszerű JSON-szkript. Az ADF is tartalmaz egyéb szolgáltatásokat, például az összetett műveletek támogatása. Az ADF további információkért lásd a dokumentációban a [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>A forgatókönyv
Beállítjuk az ADF-folyamatot, amely composes két az áttelepítési tevékenységeket. Együtt mozognak adatok naponta egy helyszíni SQL-adatbázis és a felhőbeli Azure SQL Database közötti. A két tevékenységek a következők:

* adatok másolása helyszíni SQL Server-adatbázisból Azure Blob Storage-fiók
* adatok másolása az Azure Blob Storage-fiókhoz az Azure SQL Database.

> [!NOTE]
> Az itt látható lépéseket igazítani a részletes oktatóanyag az ADF csapatunk voltak: [Adatok másolása helyszíni SQL Server-adatbázisból Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) témakör vonatkozó szakaszaihoz vezetnek mutató hivatkozások találhatók, szükség esetén.
>
>

## <a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure storage-fiók**. Ez az oktatóanyag az adatok tárolása Azure storage-fiók használhat. Ha nem rendelkezik Azure storage-fiókkal, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md) cikk. Miután létrehozta a tárfiókot, szerezze be a tárterület elérésére használt fiók kulcsot kell. Lásd: [a tárelérési kulcsok kezelése](../../storage/common/storage-account-manage.md#access-keys).
* A hozzáférést egy **az Azure SQL Database**. Ha be kell állítania az Azure SQL Database, a témakör [Ismerkedés a Microsoft Azure SQL Database ](../../sql-database/sql-database-get-started.md) információt nyújt az kiépítése az Azure SQL Database egy új példányát.
* Telepített és konfigurált **Azure PowerShell-lel** helyileg. Útmutatásért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

> [!NOTE]
> Ez az eljárás használja a [az Azure portal](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Töltse fel az adatokat a helyszíni SQL Server
Használjuk a [NYC Taxi adatkészlet](http://chriswhong.com/open-data/foil_nyc_taxi/) az áttelepítési folyamat bemutatásához. A NYC Taxi adatkészlet érhető el, a bejegyzés véleményezésével, akkor az Azure blob Storage-feljegyzett [NYC-i taxik adatait](http://www.andresmh.com/nyctaxitrips/). Az adatok rendelkezik két fájlt, a trip_data.csv fájlt, amely trip részleteit tartalmazza, és a trip_far.csv fájlt, amely tartalmazza az egyes út fizetett diszkont részleteit. Egy mintát és egy leírást a fájlok találhatók [NYC Taxi lelassítja adatkészlet leírása](sql-walkthrough.md#dataset).

Az eljárás a saját adatok készletét használja az itt elérhető alkalmazkodik, vagy kövesse a lépéseket, a NYC Taxi adatkészlet használatával leírtak szerint. Töltse fel a NYC Taxi adatkészlet a helyszíni SQL Server-adatbázisba, kövesse a leírt eljárást [tömeges adatimportálás SQL Server-adatbázisba](sql-walkthrough.md#dbload). Ezek az utasítások a egy SQL Server Azure virtuális gépen, de tölt fel a helyszíni SQL Server eljárás megegyezik.

## <a name="create-adf"></a> Az Azure Data Factory létrehozása
Egy új Azure Data Factory és a egy erőforráscsoportban létrehozására vonatkozó utasításokat a [az Azure portal](https://portal.azure.com/) biztosított [hozzon létre egy Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Nevezze el az új ADF-példány *adfdsp* és nevezze el az erőforráscsoport létrehozásánál *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Telepítse és konfigurálja az Azure Data Factory integrációs modul 
Az integrációs modul az egy ügyfél által kezelt adatintegrációs infrastruktúra különböző hálózati környezetekben adatintegrációs képességeket biztosít az Azure Data Factory által használt. Ez a modul nevén "Adatkezelési átjáró" volt. 

Állítsa be, hogy [kövesse a folyamat létrehozására szolgáló instrutions](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>Az adatforrásokhoz való kapcsolódáshoz társított szolgáltatások létrehozása
A társított szolgáltatás határozza meg az információkat, amelyeket az Azure Data Factory egy adatforrás, melyhez csatlakozni. Három erőforrást van ebben a forgatókönyvben, amelyhez a társított szolgáltatások szükségesek:

1. On-premises SQL Server
2. Azure Blob Storage
3. Azure SQL-adatbázis

Lépésről lépésre társított szolgáltatásokat hoz létre a megadott [társított szolgáltatásokat hoz létre](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="adf-tables"></a>Határozza meg, és adja meg az adatkészletek elérése a táblák létrehozása
Hozzon létre táblákat, amelyek a struktúra, helyen és az adatkészletek rendelkezésre állását a parancsprogramfájlon alapuló menete. JSON-fájlok segítségével a-táblát határoz meg. Ezek a fájlok szerkezete további információkért lásd: [adatkészletek](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Végre kell hajtani a `Add-AzureAccount` parancsmag végrehajtása előtt a [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) parancsmaggal győződjön meg arról, hogy a megfelelő Azure-előfizetés van-e kiválasztva a parancs végrehajtásához. Ez a parancsmag dokumentációjáért lásd: [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

A táblák a JSON-alapú definíciókat használja a következő nevekkel:

* a **táblanév** a helyszíni SQL server rendszer *nyctaxi_data*
* a **Tárolónév** az Azure Blob Storage-fiók van *containername*  

Három tábladefiníciókat az ADF folyamat van szükség:

1. [A helyszíni SQL-tábla](#adf-table-onprem-sql)
2. [Blob Table ](#adf-table-blob-store)
3. [SQL Azure-tábla](#adf-table-azure-sql)

> [!NOTE]
> Ezek az eljárások az Azure PowerShell használatával határozza meg, és hozzon létre az ADF tevékenységek. Azonban ezeket a feladatokat az Azure portal használatával is elvégezhető. További információkért lásd: [adatkészleteket hoz létre](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="adf-table-onprem-sql"></a>A helyszíni SQL-tábla
A helyszíni SQL Server-definíció van megadva a következő JSON-fájlban:

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

Az oszlopnevek nem kerültek bele itt. Ez az optimálisnál kiválaszthatja az oszlopneveket fel őket itt (részletekért ellenőrizze a [ADF dokumentáció](../../data-factory/copy-activity-overview.md) témakör.

A JSON-definícióját egy fájlba a tábla nevű másolási *onpremtabledef.json* fájlt, és mentse egy ismert helyre (Itt azt feltételezi, hogy *C:\temp\onpremtabledef.json*). A tábla létrehozása az ADF-ben a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Blobtábla
A tábla a kimeneti blob helyének meghatározása szerepel (Ez hozzárendeli a betöltött adatok a helyszínről az Azure-blobba) a következőket:

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

A JSON-definícióját egy fájlba a tábla nevű másolási *bloboutputtabledef.json* fájlt, és mentse egy ismert helyre (Itt azt feltételezi, hogy *C:\temp\bloboutputtabledef.json*). A tábla létrehozása az ADF-ben a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sql"></a>SQL Azure-tábla
Az SQL Azure-tábla definícióját, kimeneti (ebben a sémában leképezi a blob származó adatokon) a következőket:

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
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

A JSON-definícióját egy fájlba a tábla nevű másolási *AzureSqlTable.json* fájlt, és mentse egy ismert helyre (Itt azt feltételezi, hogy *C:\temp\AzureSqlTable.json*). A tábla létrehozása az ADF-ben a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>Adja meg, és a folyamat létrehozása
Adja meg a tevékenységek, amelyek az adatcsatornához tartozó, és létrehozhatja a folyamatot a parancsprogramfájlon alapuló az alábbi eljárások segítségével. Egy JSON-fájlt a folyamat tulajdonságainak definiálásához szolgál.

* A parancsfájl feltételezi, hogy a **folyamat neve** van *AMLDSProcessPipeline*.
* Azt is vegye figyelembe, hogy a folyamat napi szinten kell végrehajtani, és az alapértelmezett végrehajtási idő (12 am UTC) a feladathoz a periodicitás állítottuk.

> [!NOTE]
> Az alábbi eljárások határozza meg, és hozhat létre az ADF-folyamatot az Azure PowerShell használatával. Azonban ez a feladat az Azure portal használatával is elvégezhető. További információkért lásd: [folyamat létrehozása](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

A korábban megadott tábladefiníciókat használja, az ADF folyamat definíciója van megadva a következő:

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

A JSON-definícióját egy fájlba a folyamat nevű másolási *pipelinedef.json* fájlt, és mentse egy ismert helyre (Itt azt feltételezi, hogy *C:\temp\pipelinedef.json*). A folyamat létrehozása az ADF-ben a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>A folyamat elindítása
Most már futtathatók a folyamat a következő paranccsal:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

A *startdate* és *enddate* kell cserélni a tényleges dátum, amelyek között azt szeretné, a folyamat futtatásához szükséges paraméterértékeket.

A folyamat végrehajtása után megtekintheti az adatok megjelennek a blob, naponta egy fájlt a kiválasztott kell lennie.

Vegye figyelembe, hogy használtuk nem rendelkezik ki által biztosított funkcióknak ADF cső adatokat növekményes módon. Ezzel és más ADF által biztosított képességek módjáról további információkért lásd: a [ADF dokumentáció](https://azure.microsoft.com/services/data-factory/).
