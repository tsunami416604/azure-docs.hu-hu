---
title: Adatok másolása a Blob Storage-ban az Azure Data Factory használatával | Microsoft Docs
description: Létrehozhat egy Azure-beli adat-előállítót az adatok egy Azure Blob Storage-beli helyről egy másik helyre történő másolásához.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: fc2feff700220f57f82e8ac0a310843dd9b0cae6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436603"
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Azure-beli adat-előállító létrehozása a PowerShell használatával 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](quickstart-create-data-factory-powershell.md)

Ez a rövid útmutató bemutatja, hogyan használható a PowerShell egy Azure-beli adat-előállító létrehozásához. Az adat-előállítóban létrehozott folyamat adatokat **másol** egy Azure-blobtároló egyik mappájából egy másikba. Hogyan oktatóanyagot **átalakítása** Azure Data factoryval, lásd: [oktatóanyag: Adatok átalakítása a Spark használatával](transform-data-using-spark.md). 

> [!NOTE]
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez.

#### <a name="log-in-to-powershell"></a>Bejelentkezés a PowerShellbe

1. Indítsa el a gépén a **PowerShellt**. A PowerShellt hagyja megnyitva a rövid útmutató végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.
2. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt Azure-beli felhasználónevét és jelszavát:
       
    ```powershell
    Connect-AzureRmAccount
    ```        
3. Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

    ```powershell
    Get-AzureRmSubscription
    ```
4. Ha több előfizetés is tartozik a fiókjához, futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le a **SubscriptionId** kifejezést az Azure-előfizetés azonosítójára:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása
1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"ADFQuickStartRG"`. 
   
     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot. 
3. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. Például: ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
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
* Azure-régióban, amelyben a Data Factory jelenleg listája, válassza ki a régiók, amelyek a következő oldalon érdeklődésére számot tartó, és bontsa ki **Analytics** található **adat-előállító**: [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/). Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

Társított szolgáltatásokat hozhat létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóhoz történő társításához. Ebben a rövid útmutatóban létrehoz egy Azure Storage-beli társított szolgáltatást, amely forrás- és fogadóadattárként is használható. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor a hozzá való kapcsolódáshoz.

1. Hozzon létre egy JSON-fájlt **AzureStorageLinkedService.json** a **C:\ADFv2QuickStartPSH** mappában az alábbi tartalommal: (Hozza létre a mappa ADFv2QuickStartPSH Ha azt nem létezik.). 

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

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```
3. Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás létrehozásához: **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
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
                "folderPath": "@{dataset().path}"
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

2. Az adatkészlet létrehozásához: **BlobDataset**futtassa a **Set-AzureRmDataFactoryV2Dataset** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
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

2. A folyamat létrehozásához: **Adfv2QuickStartPipeline**futtassa a **Set-AzureRmDataFactoryV2Pipeline** parancsmagot.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
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
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja/illessze be az alábbi szkriptet a PowerShell-ablakba, majd nyomja le az Enter billentyűt. 

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
    2. Ellenőrizze, hogy a kapcsolati sztring formátuma helyes-e. A tulajdonságokat (például AccountName és AccountKey) pontosvessző (`;`) karakter választja el egymástól. 
    3. Ha a fióknév és a fiókkulcs szögletes zárójelben szerepel, távolítsa el a zárójeleket. 
    4. Itt láthat egy példát a kapcsolati sztringre: 

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
    Write-Output "Activity run details:"
    $Result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
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
    "usedDataIntegrationUnits": 2
    "billedDuration": 14
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
