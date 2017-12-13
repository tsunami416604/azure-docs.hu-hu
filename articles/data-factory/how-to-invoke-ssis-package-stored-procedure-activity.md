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
ms.openlocfilehash: 664c900bae580f4eb7421e3dffdfef8c9a29b720
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Egy SSIS-csomagot, a tárolt eljárási tevékenység az Azure Data Factory meghívása
Ez a cikk ismerteti, hogyan lehet meghívni egy SSIS-csomagot az Azure Data Factory-folyamat a tárolt eljárási tevékenység használatával. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [meghívása SSIS-csomagok használata tárolt eljárási tevékenység az 1-es](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
Ez a cikk a forgatókönyv, amelyen az SSIS-katalógus Azure SQL-adatbázis. Egy Azure SQL felügyelt példányát (magán előnézetben) is használhatja.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Ha még nem rendelkezik a részletes utasításokat a következő, hozzon létre egy Azure-SSIS-integrációs futásidejű a [oktatóanyag: telepítése SSIS-csomagok](tutorial-deploy-ssis-packages-azure.md).

### <a name="azure-powershell"></a>Azure PowerShell
Telepítse a legújabb Azure PowerShell-modul a következő témakör utasításait: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-data-factory"></a>Data factory létrehozása
Használja az ugyanazon adat-előállítóban, amely rendelkezik az Azure-SSIS infravörös, vagy hozzon létre egy külön adat-előállítóban. Az alábbi eljárás lépéseit egy adat-előállító létrehozása. A tárolt eljárási tevékenység a data factory hoz létre egy folyamatot. A tárolt eljárási tevékenység végrehajtja a tárolt eljárás az SSISDB adatbázis a SSIS-csomag futtatásához. 

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
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

5. Az adat-előállító létrehozásához futtassa az alábbi **Set-AzureRmDataFactoryV2** parancsmagot a $ResGrp változó Location és ResourceGroupName tulajdonsága használatával: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
A társított szolgáltatás, amely futtatja az Azure SQL database összekapcsolására. az SSIS-katalógusban a data factory létrehozása. Data Factory szolgáltatásnak információk használatával kapcsolódik az SSISDB adatbázist, és végrehajtja a tárolt eljárás egy SSIS-csomag futtatásához. 

1. Hozzon létre egy JSON fájlt **AzureSqlDatabaseLinkedService.json** a **C:\ADF\RunSSISPackage** mappa a következő tartalmát: (hozza létre a mappát ADFv2TutorialBulkCopy Ha még nem létezik.)

    > [!IMPORTANT]
    > Cserélje le &lt;kiszolgálónév&gt;, &lt;databasename&gt;, &lt;felhasználónév&gt;,&lt;kiszolgálónév&gt;, és &lt;jelszó&gt; rendelkező a fájl mentése előtt az Azure SQL Database értékeit.

    ```json
    {
        "name": "AzureSqlDbLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<AZURE SQL SERVER NAME>.database.windows.net,1433;Database=SSISDB;User ID=<USER ID>;Password=<PASSWORD>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. A **Azure PowerShell**, váltson a **C:\ADF\RunSSISPackage** mappa.

3. Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureSqlDatabaseLinkedService** társított szolgáltatás létrehozásához. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>Hozzon létre egy folyamatot tárolt eljárási tevékenység 
Ebben a lépésben hoz létre egy folyamatot egy tárolt eljárás tevékenység. A tevékenység a SSIS-csomag futtatásához a sp_executesql tárolt eljárást hív meg. 

1. Hozzon létre egy JSON fájlt **RunSSISPackagePipeline.json** a a **C:\ADF\RunSSISPackage** mappa a következő tartalmát:

    > [!IMPORTANT]
    > Cserélje le &lt;MAPPANÉV&gt;, &lt;projekt neve&gt;, &lt;CSOMAGNÉV&gt; nevű mappa, a projekt és a csomag az SSIS-katalógusban a fájl mentése előtt. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDbLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. A folyamat létrehozása: **RunSSISPackagePipeline**- ben futtassa a **Set-AzureRmDataFactoryV2Pipeline** parancsmag.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Itt látható a minta kimenete:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása
Használja a **Invoke-AzureRmDataFactoryV2Pipeline** parancsmag futtatjuk a folyamatot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name -ParameterFile .\PipelineParameters.json
```

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja/illessze be az alábbi szkriptet a PowerShell-ablakba, majd nyomja le az Enter billentyűt. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

## <a name="create-a-trigger"></a>Egy eseményindító létrehozása
Az előző lépésben a csővezeték-igény szerinti meghívni. Egy ütemezés eseményindító fussanak ütemezés szerint (óránként, naponta, stb.) a feldolgozási sor is létrehozhat.

1. Hozzon létre egy JSON fájlt **MyTrigger.json** a **C:\ADF\RunSSISPackage** mappa a következő tartalmát: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. A **Azure PowerShell**, váltson a **C:\ADF\RunSSISPackage** mappa.
3. Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmaggal hozhat létre az eseményindító. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Alapértelmezés szerint az eseményindító leállított állapotban van. Indítsa el az eseményindító a Start-AzureRmDataFactoryV2Trigger parancsmag futtatásával. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Győződjön meg arról, hogy az eseményindító elindult-e a Get-AzureRmDataFactoryV2TriggerRun parancsmag futtatásával. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. A következő parancsot a következő óra múlva. Például ha az aktuális idő du. 3:25 UTC, futtassa a parancsot du. 4 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    A következő lekérdezés alapján futtathatók az SSISDB adatbázis annak ellenőrzésére, hogy az Azure SQL Server a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Következő lépések
Ugyanígy figyelheti a folyamatot, az Azure portál használatával. Részletes útmutatásért lásd: [a folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
