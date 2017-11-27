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
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
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

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-roles"></a>Azure-szerepkörök
Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie. Az Azure Portalon kattintson a **felhasználónévre** a jobb felső sarokban, majd válassza az **Engedélyek** elemet az előfizetésben található engedélyek megtekintéséhez. Ha több előfizetéshez is rendelkezik hozzáféréssel, válassza ki a megfelelő előfizetést. Ha szeretne példautasításokat látni egy felhasználó szerepkörhöz adására, olvassa el a [Szerepkörök hozzáadása](../billing/billing-add-change-azure-subscription-administrator.md) című cikket.

### <a name="azure-storage-account"></a>Azure Storage-tárfiók neve
Ebben a rövid útmutatóban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk **forrás-** és **céladattárként**. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. 

#### <a name="get-storage-account-name-and-account-key"></a>Tárfióknév és fiókkulcs beszerzése
Ebben a rövid útmutatóban az Azure Storage-fiók nevét és kulcsát használjuk. Az alábbi eljárás bemutatja a tárfióknév és -kulcs beszerzéséhez szükséges lépéseket. 

1. Nyisson meg egy webböngészőt, és keresse fel az [Azure Portalt](https://portal.azure.com). Jelentkezzen be az Azure-beli felhasználónevével és jelszavával. 
2. Kattintson a **További szolgáltatások >** elemre a bal oldali menüben, állítson be egy szűrőt a **Tárfiók** kulcsszóval, majd válassza a **Tárfiókok** lehetőséget.

    ![Tárfiók keresése](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. A tárfiókok listájában állítson be szűrőt a tárfiók nevéhez (ha szükséges), majd válassza ki a **tárfiókját**. 
4. A **Tárfiók** oldalon a menüben válassza a **Hozzáférési kulcsok** elemet.

    ![Tárfióknév és -kulcs beszerzése](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. Másolja a **Tárfiók neve** és az **1. kulcs** mezők értékét a vágólapra. Illessze be őket a Jegyzettömbbe, vagy bármely más szerkesztőbe, majd mentse a fájlt.  

#### <a name="create-input-folder-and-files"></a>Bemeneti mappa és fájlok létrehozása
Ebben a szakaszban egy **adftutorial** nevű blobtárolót hoz létre az Azure Blob Storage-ban. Ezután létrehoz egy **input** nevű mappát a tárolóban, majd feltölt egy mintafájlt az input mappába. 

1. A **Storage-fiók** lapon váltson át az **Áttekintés** panelre, majd kattintson a **Blobok** elemre. 

    ![A Blobok elem választása](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. A **Blob service** lapon kattintson az eszköztár **+ Tároló** elemére. 

    ![Tároló hozzáadása gomb](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. Az **Új tároló** párbeszédablakban adja meg az **adftutorial** nevet, és kattintson az **OK** gombra. 

    ![Tárolónév megadása](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. A tárolók listájában kattintson az **adftutorial** elemre. 

    ![A tároló kiválasztása](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. A **Tároló** lapon kattintson az eszköztár **Feltöltés** elemére.  

    ![Feltöltés gomb](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. A **Blob feltöltése** lapon kattintson a **Speciális** elemre.

    ![Kattintás a Speciális hivatkozásra](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. Indítsa el a **Jegyzettömböt**, és hozzon létre egy **emp.txt** nevű fájlt a következő tartalommal. Mentse a fájlt a **c:\ADFv2QuickStartPSH** mappába. Ha még nem létezik, hozza létre az **ADFv2QuickStartPSH** mappát.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Az Azure Portal **Blob feltöltése** lapjának **Fájlok** mezőben keresse meg, és válassza ki az **emp.txt** fájlt. 
9. Adja meg az **input** értéket a **Feltöltés mappába** mezőben. 

    ![Blobbeállítások feltöltése](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. Ellenőrizze, hogy a mappa az **input** mappa-e, a fájl pedig az **emp.txt** fájl-e, majd kattintson a **Feltöltés** elemre.
11. A listában meg kell jelennie az **emp.txt** fájlnak és a feltöltés állapotának. 
12. A sarokban található **X** gombra kattintva zárja be a **Blob feltöltése** lapot. 

    ![A Blob feltöltése lap bezárása](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. Ne zárja be a **Tároló** lapot. A segítségével ellenőrizheti ennek a rövid útmutatónak az eredményét. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése
Ha még nincs a gépén, telepítse az Azure PowerShell legújabb verzióját. 

1. A webböngészőben lépjen az [Azure SDK letöltések és SDK-k](https://azure.microsoft.com/downloads/) lapra. 
2. A **Parancssori eszközök** -> **PowerShell** szakaszban kattintson a **Windows-telepítés** elemre. 
3. Futtassa az **MSI**-fájlt az Azure PowerShell telepítéséhez. 

Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe

1. Indítsa el a gépén a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva a rövid útmutató végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.

    ![A PowerShell indítása](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt Azure-beli felhasználónevét és jelszavát:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Ha több Azure-előfizetéssel is rendelkezik, futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le a **SubscriptionId** kifejezést az Azure-előfizetés azonosítójára:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása
1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`.
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Adjon meg egy változót az adat-előállító nevéhez. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Adjon meg egy változót az adat-előállító helyéhez: 

    ```powershell
    $location = "East US"
    ```
4. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot. 
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

## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a kimeneti mappát az adftutorial blobtárolóban. Ezután átmásolja az emp.txt fájlt a bemeneti mappából a kimeneti mappába. 

1. Az Azure Portal **adftutorial** tároló lapján kattintson a **Frissítés** elemre a kimeneti mappa megtekintéséhez. 
    
    ![Frissítés](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. Kattintson a mappalista **kimenet** elemére. 
2. Ellenőrizze, hogy az **emp.txt** fájl bekerült-e a kimeneti mappába. 

    ![Frissítés](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. Törölheti az [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md), amely tartalmazza az erőforráscsoportban lévő összes erőforrást. Ha a többi erőforrást érintetlenül szeretné hagyni, csak az ebben az oktatóanyagban létrehozott adat-előállítót törölje.

Egy erőforráscsoport törlése a csoportban található összes erőforrást törli, beleértve az adat-előállítókat is. Az alábbi parancsot futtassa a teljes erőforráscsoport törléséhez: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Ha csak az adat-előállítót szeretné törölni, nem pedig a teljes erőforráscsoportot, futtassa az alábbi parancsot: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
