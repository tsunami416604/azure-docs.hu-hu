---
title: "SSIS-csomag használata az Azure Data Factory - tárolt eljárási tevékenység meghívása |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan lehet meghívni egy Azure Data Factory-folyamat a tárolt eljárási tevékenység használja az SQL Server Integration Services (SSIS) csomag."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 12/07/2017
ms.author: jingwang
ms.openlocfilehash: ff0d1e5d644926864641ceb3e3c3a102167c1fd2
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Egy SSIS-csomagot, a tárolt eljárási tevékenység az Azure Data Factory meghívása
Ez a cikk ismerteti, hogyan lehet meghívni egy SSIS-csomagot az Azure Data Factory-folyamat a tárolt eljárási tevékenység használatával. 

> [!NOTE]
> Ez a cikk vonatkozik 1 a Data Factory általánosan elérhető. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely a nyilvános előzetes verziójához, használatakor [meghívása SSIS-csomagok használata a tárolt eljárási tevékenység a 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
Ez a cikk a forgatókönyv, amelyen az SSIS-katalógus Azure SQL-adatbázis. Egy Azure SQL felügyelt példányát (magán előnézetben) is használhatja.

### <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Ha még nem rendelkezik a részletes utasításokat a következő, hozzon létre egy Azure-SSIS-integrációs futásidejű a [oktatóanyag: telepítése SSIS-csomagok](../tutorial-deploy-ssis-packages-azure.md). Egy adat-előállító létrehozása az Azure-SSIS-integrációs futásidejű 2-es verzióját, létre kell hoznia. 

### <a name="azure-powershell"></a>Azure PowerShell
Telepítse a legújabb Azure PowerShell-modul a következő témakör utasításait: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Data factory létrehozása
Az alábbi eljárás lépéseit egy adat-előállító létrehozása. A tárolt eljárási tevékenység a data factory hoz létre egy folyamatot. A tárolt eljárási tevékenység végrehajtja a tárolt eljárás az SSISDB adatbázis a SSIS-csomag futtatásához.

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot. 
3. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Az adat-előállítóban létrehozásához futtassa a következő **New-AzureRmDataFactory** parancsmag, a $ResGrp változóból helyét és a ResourceGroupName tulajdonság használatával: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    $df 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
A társított szolgáltatás, amely futtatja az Azure SQL database összekapcsolására. az SSIS-katalógusban a data factory létrehozása. Data Factory szolgáltatásnak információk használatával kapcsolódik az SSISDB adatbázist, és végrehajtja a tárolt eljárás egy SSIS-csomag futtatásához. 

1. Hozzon létre egy JSON fájlt **AzureSqlDatabaseLinkedService.json** a **C:\ADF\RunSSISPackage** mappa a következő tartalmát: (hozza létre a mappát ADFv2TutorialBulkCopy Ha még nem létezik.)

    > [!IMPORTANT]
    > A fájl mentése előtt a &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; és &lt;password&gt; értékeket cserélje le az Azure SQL Database értékeire.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<AZURE SQL SERVER NAME>.database.windows.net,1433;Database=SSISDB;User ID=<USERNAME>;Password=<PASSWORD>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. A **Azure PowerShell**, váltson a **C:\ADF\RunSSISPackage** mappa.
3. Futtassa a **New-AzureRmDataFactoryLinkedService** parancsmaggal hozhat létre a társított szolgáltatás: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
A kimeneti adatkészlet dummy adatkészletre mutató ütemezését a folyamat a meghajtók. Figyelje meg, hogy a gyakoriságának beállítása óra és időköz értéke 1. Ezért a folyamat fut. után egy órán belül a folyamat kezdési és befejezési időpontja 

1. Hozzon létre egy OuputDataset.json fájlt a következő tartalommal: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Futtassa a **New-AzureRmDataFactoryDataset** parancsmaggal hozzon létre egy DataSet objektum. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>Hozzon létre egy folyamatot tárolt eljárási tevékenység 
Ebben a lépésben hoz létre egy folyamatot egy tárolt eljárás tevékenység. A tevékenység a SSIS-csomag futtatásához a sp_executesql tárolt eljárást hív meg. 

1. Hozzon létre egy JSON fájlt **MyPipeline.json** a a **C:\ADF\RunSSISPackage** mappa a következő tartalmát:

    > [!IMPORTANT]
    > Cserélje le &lt;MAPPANÉV&gt;, &lt;projekt neve&gt;, &lt;CSOMAGNÉV&gt; nevű mappa, a projekt és a csomag az SSIS-katalógusban a fájl mentése előtt. 

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'SsisAdfTest', @project_name=N'MyProject', @package_name=N'Package.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. A folyamat létrehozása: **RunSSISPackagePipeline**- ben futtassa a **Set-AzureRmDataFactoryPipeline** parancsmag.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása
Használja a **Invoke-AzureRmDataFactoryPipeline** parancsmag futtatjuk a folyamatot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = New-AzureRmDataFactoryPipeline $df -File ".\MyPipeline.json"
```

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

2. Futtatás **Get-AzureRmDataFactorySlice** részletes információkat, a kimeneti adatkészlet **, amely a folyamat a bemeneti tábla összes szeletek.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Megfigyelheti, hogy a StartDateTime itt megadott értéke megegyezik a folyamat JSON-fájljában megadott kezdési idővel. 
3. A **Get-AzureRmDataFactoryRun** parancs futtatásával lekérheti az adott szeletre vonatkozó tevékenységfuttatások részleteit.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Futtassa a parancsmagot, amíg a szelet **Ready** (Kész) vagy **Failed** (Sikertelen) állapotú nem lesz. Ha a szelet Ready (Kész) állapotú, a kimeneti adatok blobtárolójának **adfgetstarted** tárolójában ellenőrizze a **partitioneddata** mappát.  Az igény szerinti HDInsight-fürt létrehozása általában eltart egy ideig.

    A következő lekérdezés alapján futtathatók az SSISDB adatbázis annak ellenőrzésére, hogy az Azure SQL Server a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Következő lépések
A tárolt eljárási tevékenység kapcsolatos részletekért lásd: a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) cikk.

