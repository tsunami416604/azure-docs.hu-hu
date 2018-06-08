---
title: Adatok áthelyezése egy helyi SQL Server az SQL Azure-bA az Azure Data Factoryvel |} Microsoft Docs
description: Állítsa be az ADF-feldolgozási folyamat composes adatok áttelepítési két tevékenység váltó együtt adatok naponta helyszíni adatbázisok között, és a felhőben.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 36837c83-2015-48be-b850-c4346aa5ae8a
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: e9f6de3d4f4f731c2e727889bef1aef129cb00bf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838101"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Adatok áthelyezése egy helyi SQL server SQL Azure és az Azure Data Factory
Ez a témakör bemutatja, hogyan tárolt adatok mozgatása egy helyi SQL Server-adatbázis SQL Azure adatbázishoz keresztül Azure Blob Storage használata az Azure Data Factory (ADF).

Az egy táblázatot, amely összefoglalja az adatok áthelyezése az Azure SQL Database különböző beállítások [adatok áthelyezése az Azure SQL Database az Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Bemutatása: Mi az az ADF, és amikor azt használatával telepítse át az adatokat?
Az Azure Data Factory koordinálja és automatizálja az adatátviteli és az adatok átalakítása felhőalapú teljes körűen felügyelt adatok integrációs szolgáltatás. A kulcs az ADF modell fogalma folyamat. Egy folyamat tevékenységek, logikai csoportját, amelyek mindegyike meghatározza, hogy a műveletek végrehajtását az adatkészletek szereplő adatokat. Társított szolgáltatások segítségével határozza meg a Data Factory az adatok erőforrások eléréséhez szükséges információkat.

Az ADF meglévő adatfeldolgozási szolgáltatások összeállítható az adatok folyamatok, amelyek magas rendelkezésre állású és kezelt a felhőben. Ezen adatok folyamatok ütemezhető a betöltési, előkészítése, átalakítására, elemzése és adatok közzétételére, és az ADF kezeli, és az összetett adatokat és a feldolgozási függőségek koordinálja. Megoldások gyorsan kell a beépített és csatlakozás helyszíni egyre több telepített a felhőben és a felhő tárolt adatforrások.

Érdemes lehet ADF:

* Ha folyamatosan áttelepítendő adatokat egy hibrid forgatókönyvben, amely mindkettőt fér hozzá a helyszíni és felhőalapú erőforrások
* Ha az adatok tranzakcióalapú van, vagy módosítani vagy üzleti logikát, ha az áttelepítés alatt álló hozzáadott rendelkezik.

ADF lehetővé teszi, hogy az ütemezés és rendszeres időközönként adatok mozgása kezelő egyszerű JSON-parancsfájlokat használó feladatok figyelését. ADF is rendelkeznek egyéb képességeit, például a összetett műveletek támogatása. Az ADF további információkért lásd a dokumentációban a [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>A forgatókönyv
Az ADF-feldolgozási folyamat két adatok áttelepítési tevékenységek composes beállítjuk. Együtt mozognak adatok naponta egy helyi SQL-adatbázis és a felhőben Azure SQL adatbázis között. A két tevékenység a következők:

* a helyszíni SQL Server adatbázisból származó adatok másolása az Azure Blob Storage-fiók
* adatok másolása az Azure Blob Storage-fiók egy Azure SQL-adatbázis.

> [!NOTE]
> Itt volt módosítani a részletesebb oktatóanyagot, az ADF-csapat által biztosított bemutatott lépések: [helyezze át az adatokat a helyszíni adatforrások és az adatkezelési átjáró felhő között](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) témakör vonatkozó szakaszaihoz-hivatkozások megadott, amikor szükséges.
>
>

## <a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy rendelkezik:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure storage-fiók**. Egy Azure storage-fiókot használ az adatok tárolása az oktatóanyag. Ha egy Azure storage-fiók nem rendelkezik, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account) cikk. Miután létrehozta a tárfiókot, be kell szereznie a tároló elérésére használt fiókot kulcs. Lásd: [a tárelérési kulcsok kezelése](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* A hozzáférést egy **Azure SQL adatbázis**. Ha be kell állítania egy Azure SQL Database, a témakör [Ismerkedés a Microsoft Azure SQL Database ](../../sql-database/sql-database-get-started.md) bemutatja, hogy miként lehet kiépíteni egy Azure SQL adatbázis új példányát.
* Telepített és konfigurált **Azure PowerShell** helyileg. Útmutatásért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Ez az eljárás használja a [Azure-portálon](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Az adatok feltöltése a helyszíni SQL Server
Használjuk a [NYC Taxi dataset](http://chriswhong.com/open-data/foil_nyc_taxi/) az áttelepítési folyamat bemutatásához. A következőt: Taxi adatkészlet érhető el, a feladás egy vagy több, az Azure blob storage leírtaknak megfelelően [NYC Taxi adatok](http://www.andresmh.com/nyctaxitrips/). Az adatok van két fájlt, a trip_data.csv fájlt, amely tartalmazza-e vissza adatokat, és a trip_far.csv fájlt, amely a jegy minden út kifizette ára részleteit tartalmazza. A minta és az ezek a fájlok leírása szerepelnek [NYC Taxi Utazgatással adatkészlet leírása](sql-walkthrough.md#dataset).

Az itt megadott saját adatok eljárás igazítja, vagy hajtsa végre a következőt: Taxi adatkészlet szerint. Töltse fel a következőt: Taxi dataset a helyszíni SQL Server-adatbázisba, kövesse az ismertetett eljárás [tömeges adatok importálása az SQL Server-adatbázisba](sql-walkthrough.md#dbload). Ezek az utasítások az Azure virtuális gép az SQL Server, de a helyszíni SQL Server feltöltését eljárás megegyezik.

## <a name="create-adf"></a> Egy Azure Data Factory létrehozása
Egy új Azure Data Factory és az erőforráscsoport létrehozására vonatkozó utasításokat a [Azure-portálon](https://portal.azure.com/) biztosított [hozzon létre egy Azure Data Factory](../../data-factory/v1/data-factory-build-your-first-pipeline-using-editor.md#create-a-data-factory). Az új ADF-példány neve *adfdsp* és az erőforráscsoport létrehozásánál *adfdsprg*.

## <a name="install-and-configure-up-the-data-management-gateway"></a>Telepítse és konfigurálja az adatkezelési átjáró mentése
Ahhoz, hogy az egy az Azure data factory szeretne dolgozni egy helyi SQL Server adatcsatornák, szüksége csatolt szolgáltatásként hozzáadása az adat-előállítóban. A társított szolgáltatás létrehozása egy helyi SQL Server, a következőket kell tennie:

* Töltse le és telepítse a Microsoft adatkezelési átjáró a helyi számítógépre.
* a társított szolgáltatás a helyszíni adatforráshoz az átjáró használatára konfigurálja.

Az adatkezelési átjáró rendezi sorba, és a forrás és a fogadó adatokat azon a számítógépen, amelyen található deserializes.

Telepítési utasításokat és az adatkezelési átjáró részleteinek: [helyezze át az adatokat a helyszíni adatforrások és az adatkezelési átjáró felhő között](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)

## <a name="adflinkedservices"></a>Az adatok erőforrásokhoz való társított szolgáltatások létrehozásához
A társított szolgáltatás határozza meg az Azure Data Factory egy adatforrás, melyhez való kapcsolódáshoz szükséges adatokat. Három erőforrások van ebben a forgatókönyvben, amelynek társított szolgáltatások szükségesek:

1. On-premises SQL Server
2. Azure Blob Storage
3. Azure SQL Database

Lépésről lépésre társított szolgáltatások létrehozásához megadott [társított szolgáltatások létrehozásához](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-linked-services).


## <a name="adf-tables"></a>Adja meg, és adja meg, hogyan férhet hozzá az adatkészletek táblák létrehozása
Adja meg a struktúra, helyét és az adatkészletek rendelkezésre állását az alábbi parancsfájl-alapú eljárások táblák létrehozása. JSON-fájlokat a táblák meghatározásához használják. Ezek a fájlok szerkezetének további információkért lásd: [adatkészletek](../../data-factory/v1/data-factory-create-datasets.md).

> [!NOTE]
> Végre kell hajtani a `Add-AzureAccount` parancsmag végrehajtása előtt a [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) parancsmag segítségével győződjön meg arról, hogy a jobb oldali Azure-előfizetés parancs végrehajtásának van kiválasztva. Ez a parancsmag dokumentációjáért lásd: [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

A JSON-alapú definíciók táblázatokban az alábbi neveket használja:

* a **táblanév** a helyszíni SQL server rendszer *nyctaxi_data*
* a **Tárolónév** az Azure Blob Storage fiók van *containername*  

Három definíciói az ADF adatcsatorna van szükség:

1. [A helyszíni SQL-tábla](#adf-table-onprem-sql)
2. [Blob Table ](#adf-table-blob-store)
3. [Az SQL Azure-tábla](#adf-table-azure-sql)

> [!NOTE]
> Ezek az eljárások Azure PowerShell használatával határozza meg, és az ADF tevékenységek létrehozása. De ezek a feladatok az Azure portál használatával is elvégezhető. További információkért lásd: [adatkészletek létrehozása](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-datasets).
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

Az oszlopnevek nem szerepeltek itt. Részterv jelölheti meg az oszlopok neveit itt többek között (a részleteket tekintse meg a [ADF dokumentáció](../../data-factory/v1/data-factory-data-movement-activities.md) témakör.

Másolás fájlba a tábla JSON-definícióból nevű *onpremtabledef.json* fájlt, és mentse egy ismert helyre (Itt feltételezhető *C:\temp\onpremtabledef.json*). A tábla létrehozása az ADF a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>A BLOB tábla
A következő táblázatban a kimeneti blob helyére vonatkozó definíciójának szerepel (Ez leképezi a feldolgozott adatokat a helyszíni Azure blob) a következő:

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

Másolás fájlba a tábla JSON-definícióból nevű *bloboutputtabledef.json* fájlt, és mentse egy ismert helyre (Itt feltételezhető *C:\temp\bloboutputtabledef.json*). A tábla létrehozása az ADF a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sql"></a>Az SQL Azure-tábla
Definíciója a következő táblázatban az SQL Azure kimeneti (ebben a sémában az adatokat a blob érkező képezi le) a következőket:

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

Másolás fájlba a tábla JSON-definícióból nevű *AzureSqlTable.json* fájlt, és mentse egy ismert helyre (Itt feltételezhető *C:\temp\AzureSqlTable.json*). A tábla létrehozása az ADF a következő Azure PowerShell-parancsmagot:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>Adja meg, és a folyamat létrehozása
Adja meg az adatcsatornához tartozó, és hozzon létre a folyamat a következő parancsprogram-alapú eljárásokkal a tevékenységek. A JSON-fájl folyamat tulajdonságainak definiálásához szolgál.

* A parancsfájl feltételezi, hogy a **adatcsatorna neve** van *AMLDSProcessPipeline*.
* Ne feledje, hogy be van állítva az ismétlődési gyakoriságára vonatkozóan a feldolgozási sor napi szinten hajtható végre, és az alapértelmezett végrehajtási idő használata a feladathoz (12 óra UTC).

> [!NOTE]
> Az alábbi eljárások Azure PowerShell használatával határozza meg, és hozzon létre a ADF-feldolgozási folyamat. Azonban ez a feladat az Azure portál használatával is elvégezhető. További információkért lásd: [létrehozási folyamat](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-pipeline).
>
>

A korábban megadott definíciói használ, az ADF csővezeték definíciója van megadva az alábbiak szerint:

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

A JSON-definícióból a folyamat egy fájlba nevű példány *pipelinedef.json* fájlt, és mentse egy ismert helyre (Itt feltételezhető *C:\temp\pipelinedef.json*). Hozzon létre a folyamat a következő Azure PowerShell-parancsmaggal ADF:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>A folyamat elindítása
A folyamat futtatható a következő parancsot:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

A *startdate* és *enddate* paraméterértékeket kell cserélni a tényleges dátum között, amelyek a folyamatot futtatni szeretné.

Ha a feldolgozási sor végrehajtása során, kell megjeleníti őket a tárolóban, a BLOB, naponta egy fájl kiválasztott adatok megtekintéséhez.

Vegye figyelembe, hogy a jelenleg nem alkalmazhatók az által biztosított funkcióknak ADF cső adatok Növekményesen rendelkezik. Ez, és más ADF által biztosított képességek módjáról további információkért lásd: a [ADF dokumentáció](https://azure.microsoft.com/services/data-factory/).
