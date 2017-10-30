---
title: "Azure-beli adat-előállító létrehozása a PowerShell használatával | Microsoft Docs"
description: "Létrehozhat egy Azure-beli adat-előállítót az adatok egy Azure Blob Storage egyik helyéről egy másik helyére történő másolásához."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: efcdcac0edcdc3e3bd87dae89609e04985a3579e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="create-a-data-factory-and-pipeline-using-powershell"></a>Adat-előállító és folyamat létrehozása a PowerShell használatával
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket. 

Ez a rövid útmutató bemutatja, hogyan használható a PowerShell egy Azure-beli adat-előállító létrehozásához. Az adat-előállító folyamata adatokat másol az Azure Blob Storage egyik helyéről egy másik helyére.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Storage-fiók** A blobtároló **forrás-** és **fogadó**adattárként lesz használatban. Ha még nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. 
* Hozzon létre egy **blobtárolót** a Blob Storage alatt, majd hozzon létre egy bemeneti **mappát** a tárolóban, és töltsön fel néhány fájlt a mappába. Az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) és hozzá hasonló eszközökkel csatlakozhat az Azure Blob Storage-hoz, blobtárolókat hozhat létre, bemeneti fájlokat tölthet fel, és ellenőrizheti a kimeneti fájlokat.
* **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

1. Indítsa el a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva a rövid útmutató végéig. Ha bezárja és újra megnyitja a programot, akkor újra le kell futtatnia a parancsokat.

    Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

    ```powershell
    Get-AzureRmSubscription
    ```
    Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le a **SubscriptionId** kifejezést az Azure-előfizetés azonosítójára:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. Futtassa a **Set-AzureRmDataFactoryV2** parancsmagot egy adat-előállító létrehozásához. A parancs végrehajtása előtt cserélje le a helyőrzőket a saját értékeire. A saját értékeire lecserélt **helyőrzők**. 

    Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként használhat. 
    ```powershell
    $resourceGroupName = "<your resource group to create the factory>";
    ```

    Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az adat-előállító neveként használhat. 

    ```powershell
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    ```

    Futtassa a következő parancsot egy adat-előállító létrehozásához. 
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

    Vegye figyelembe a következő szempontokat:

    * Az Azure adat-előállító nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének vagy rendszergazdájának kell lennie.
    * A Data Factory 2-es verziója jelenleg csak az USA **keleti** vagy **2. keleti** régiójában teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

Társított szolgáltatásokat hozhat létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóhoz történő társításához. Ebben a rövid útmutatóban csak egy Azure Storage-beli társított szolgáltatást kell létrehoznia, amelyet forrás- és fogadóadattárként is használhat. Ebben a példában ennek a neve: AzureStorageLinkedService.

1. Hozzon létre egy **AzureStorageLinkedService.json** nevű JSON-fájlt a **C:\ADFv2QuickStartPSH** mappában az alábbi tartalommal (ha még nem létezne, hozza létre az ADFv2QuickStartPSH nevű mappát). A fájl mentése előtt az &lt;accountName&gt; és az &lt;accountKey&gt; kifejezést cserélje le az Azure Storage-fiók nevére és kulcsára.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. Az **Azure PowerShellben** váltson az **ADFv2QuickStartPSH** mappára.

3. Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureStorageLinkedService** társított szolgáltatás létrehozásához. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Adatkészlet létrehozása

Megadhat egy adatkészletet, amely a forrásból a fogadóba másolt adatokat jelöli. Ebben a példában a blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik. Az adatkészlethez egy olyan paraméter szükséges, amelynek az értéke az adatkészletet feldolgozó tevékenységben van beállítva. A paraméter az adatok tárolására szolgáló helyre mutató „folderPath” létrehozására használható.

1. Hozzon létre egy **BlobDataset.json** nevű JSON-fájlt a **C:\ADFv2QuickStartPSH** mappában az alábbi tartalommal.

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. A **BlobDataset** adatkészlet létrehozásához futtassa a **Set-AzureRmDataFactoryV2** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Itt látható a minta kimenete:

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Folyamat létrehozása
  
Ebben a példában a folyamat egy tevékenységet tartalmaz, és két paraméter szükséges hozzá: a bemeneti blob elérési útja és a kimeneti blob elérési útja. A paraméterek értékei a folyamat indításakor/futtatásakor lesznek beállítva. A másolási tevékenység az előző lépésben kimenetként és bemenetként létrehozott blob-adatkészletre hivatkozik. Ha az adatkészlet bemeneti adatkészletként van használatban, a bemeneti elérési út van megadva. Ha az adatkészlet kimeneti adatkészletként van használatban, a kimeneti elérési út van megadva. 

1. Hozzon létre egy **Adfv2QuickStartPipeline.json** nevű JSON-fájlt a **C:\ADFv2QuickStartPSH** mappában az alábbi tartalommal.

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Az **Adfv2QuickStartPipeline** folyamat létrehozásához futtassa a **Set-AzureRmDataFactoryV2Pipeline** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
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

Ebben a lépésben beállítja a folyamatparaméterek értékeit: az **inputPath** és **outputPath** esetén a forrás- és fogadó-blob elérési útjának tényleges értékeit adja meg. Ezután hozzon létre egy folyamat futását az alábbi argumentumok használatával. 

1. Hozzon létre egy **PipelineParameters.json** nevű JSON-fájlt a **C:\ADFv2QuickStartPSH** mappában az alábbi tartalommal.

    A fájl mentése előtt cserélje le az inputPath és outputPath paraméter értékét a forrás- és fogadó-blob elérési útjára az adatok másolásához.

    ```json
    {
        "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/foldername>",
        "outputPath": "<the blob path to copy data to, e.g. containername/foldername>"
    }
    ```

2. Futtassa az **Invoke-AzureRmDataFactoryV2Pipeline** parancsmagot egy folyamat futásának létrehozásához és a paraméterértékek megadásához. Így megőrizheti a folyamat futásának azonosítóját későbbi monitorozás céljából.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>Folyamat futásának monitorozása

1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi szkriptet.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```

    Itt látható a folyamat futásának mintakimenete:

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

2. A másolási tevékenység futtatási részleteinek (például az írt vagy olvasott adatok méretének) lekéréséhez futtassa az alábbi szkriptet.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Győződjön meg arról, hogy a kimenet hasonlít az alábbi tevékenységfuttatás eredményének mintakimenetéhez:

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>Kimenet ellenőrzése
Például az [Azure Storage Explorerhez](https://azure.microsoft.com/features/storage-explorer/) hasonló eszközök használatával ellenőrizheti, hogy az inputBlobPath alatt található blobok át lettek-e másolva az outputBlobPath helyre.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. Törölheti az [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md), amely tartalmazza az erőforráscsoportban lévő összes erőforrást. Ha a többi erőforrást érintetlenül szeretné hagyni, csak az ebben az oktatóanyagban létrehozott adat-előállítót törölje.

Az alábbi parancsot futtassa a teljes erőforráscsoport törléséhez: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Az alábbi parancsot futtassa, ha csak az adat-előállítót szeretné törölni: 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
