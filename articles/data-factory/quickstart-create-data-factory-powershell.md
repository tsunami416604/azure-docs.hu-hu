---
title: "Adatok másolása a Blob Storage-ban az Azure Data Factory használatával | Microsoft Docs"
description: "Létrehozhat egy Azure-beli adat-előállítót, ha adatokat szeretne másolni az Azure Blob Storage-ban egy mappából egy olyan mappába, amely ugyanabban a blobtárolóban található."
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
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: cb58fe167fe8b369f51e234badd8e419ebd284e4
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Azure-beli adat-előállító létrehozása a PowerShell használatával 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](quickstart-create-data-factory-powershell.md)

Ez a rövid útmutató bemutatja, hogyan használható a PowerShell egy Azure-beli adat-előállító létrehozásához. Az adat-előállítóban létrehozott folyamat adatokat **másol** egy Azure-blobtároló egyik mappájából egy másikba. Az adatok Azure Data Factoryval történő **átalakításának** útmutatásáért olvassa el az [az adatok Spark segítségével történő átalakítását ismertető oktatóanyagot](transform-data-using-spark.md). 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

[!INCLUDE [data-factory-quickstart-prerequisites-2](../../includes/data-factory-quickstart-prerequisites-2.md)]

## <a name="create-a-data-factory"></a>Data factory létrehozása
1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot. 
3. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. Például: ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```
1. Adjon meg egy változót az adat-előállító helyéhez: 

    ```powershell
    $location = "East US"
    ```
5. Az adat-előállító létrehozásához futtassa az alábbi **Set-AzureRmDataFactoryV2** parancsmagot: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

Társított szolgáltatásokat hozhat létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóhoz történő társításához. Ebben a rövid útmutatóban létrehoz egy Azure Storage-beli társított szolgáltatást, amely forrás- és fogadóadattárként is használható. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor a hozzá való kapcsolódáshoz.

1. Hozzon létre egy **AzureStorageLinkedService.json** nevű JSON-fájlt a **C:\ADFv2QuickStartPSH** mappában az alábbi tartalommal (ha még nem létezne, hozza létre az ADFv2QuickStartPSH nevű mappát). 

    > [!IMPORTANT]
    > A fájl mentése előtt az &lt;accountName&gt; és az &lt;accountKey&gt; kifejezést cserélje le az Azure Storage-fiók nevére és kulcsára.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
    Ha a Jegyzettömböt használja, a **Mentés másként** párbeszédpanel **Fájltípus** mezőjénél válassza a **Minden fájl** lehetőséget. Ellenkező esetben a rendszer `.txt` kiterjesztést adhat a fájlhoz. Például: `AzureStorageLinkedService.json.txt`. Ha létrehozza a fájlt a Fájlkezelőben, mielőtt megnyitná a Jegyzettömbben, előfordulhat, hogy nem fogja látni a hozzáadott `.txt` kiterjesztést, mivel alapértelmezés szerint engedélyezve van az **Ismert fájltípusok kiterjesztéseinek elrejtése** beállítás. A továbblépés előtt távolítsa el a hozzáadott `.txt` kiterjesztést.
2. A **PowerShellben** lépjen az **ADFv2QuickStartPSH** mappára.

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
Ebben a lépésben megad egy adatkészletet, amely a forrásból a fogadóba másolt adatokat jelöli. Az adatkészlet típusa **AzureBlob**. Ez az előző lépésben létrehozott **Azure Storage társított szolgáltatásra** vonatkozik. Felvesz egy paramétert a **folderPath** tulajdonság létrehozásához. Bemeneti adatkészlet esetén a folyamat másolási tevékenysége a bemeneti útvonalat adja meg a paraméter értékeként. Hasonlóképp, kimeneti adatkészlet esetén a másolási tevékenység a kimeneti útvonalat adja meg a paraméter értékeként. 

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
  
Ebben a rövid útmutatóban létrehoz egy folyamatot egy tevékenységgel, amelyhez két paraméter szükséges: a bemeneti blob elérési útja és a kimeneti blob elérési útja. A paraméterek értékei a folyamat indításakor/futtatásakor lesznek beállítva. A másolási tevékenység az előző lépésben kimenetként és bemenetként létrehozott blob-adatkészletet használja. Ha az adatkészlet bemeneti adatkészletként van használatban, a bemeneti elérési út van megadva. Ha az adatkészlet kimeneti adatkészletként van használatban, a kimeneti elérési út van megadva. 

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

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Futtassa az **Invoke-AzureRmDataFactoryV2Pipeline** parancsmagot egy folyamat futásának létrehozásához és a paraméterértékek megadásához. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja/illessze be az alábbi szkriptet a PowerShell-ablakba, majd nyomja le az Enter billentyűt. 

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

        Start-Sleep -Seconds 10
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

    Ha a következő hibát látja:
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    Tegye az alábbiakat: 
    1. Az AzureStorageLinkedService.json fájlban ellenőrizze, hogy az Azure Storage-fiók neve és kulcsa helyes-e. 
    2. Ellenőrizze, hogy a kapcsolati karakterlánc formátuma helyes-e. A tulajdonságokat (például AccountName és AccountKey) pontosvessző (`;`) karakter választja el egymástól. 
    3. Ha a fióknév és a fiókkulcs szögletes zárójelben szerepel, távolítsa el a zárójeleket. 
    4. Itt láthat egy példát a kapcsolati karakterláncra: 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. Hozza létre újra a társított szolgáltatást a [Társított szolgáltatás létrehozása](#create-a-linked-service) című szakasz lépéseit követve. 
    6. Futtassa újra a folyamatot a [Folyamat futásának létrehozása](#create-a-pipeline-run) című szakasz lépéseit követve. 
    7. Futtassa újra az aktuális monitorozási parancsot az új folyamatfutás monitorozásához. 
1. A másolási tevékenység futtatási részleteinek (például az írt vagy olvasott adatok méretének) lekéréséhez futtassa az alábbi szkriptet.

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

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
