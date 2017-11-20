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
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 63e4c654409651f6655da1bed6ab2f544cf024dd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-azure-data-factory-and-pipeline-using-powershell"></a>Azure-beli adat-előállító és folyamat létrehozása a PowerShell használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](quickstart-create-data-factory-powershell.md)

Ez a rövid útmutató bemutatja, hogyan használható a PowerShell egy Azure-beli adat-előállító létrehozásához. Az adat-előállítóban létrehozott folyamat adatokat másol az Azure Blob Storage egyik helyéről egy másik helyére. Az adatok Azure Data Factoryval történő átalakításának útmutatásáért olvassa el az [az adatok Spark segítségével történő átalakítását ismertető oktatóanyagot](transform-data-using-spark.md). 

Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

### <a name="azure-storage-account"></a>Azure Storage-tárfiók neve
Ebben a rövid útmutatóban egy általános célú Azure Storage-fiókot (ebben az esetben blobtárolót) használunk **forrás-** és **cél-/fogadóadattárként**. Ha még nem rendelkezik általános célú Azure Storage-fiókkal, tekintse meg a [Tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. 

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
Ebben a szakaszban egy adftutorial nemű blobtárolót hoz létre az Azure Blob Storage-ban. Ezután létrehoz egy input nevű mappát a tárolóban, majd feltölt egy mintafájlt az input mappába. 

1. Ha még nincs a gépén, telepítse az [Azure Storage Explorert](https://azure.microsoft.com/features/storage-explorer/). 
2. Indítsa el a gépén a **Microsoft Azure Storage Explorert**.   
3. A **Csatlakozás az Azure Storage-hoz** ablakban válassza a **Tárfiók nevének és kulcsának használata** lehetőséget, és kattintson a **Tovább** gombra. Ha nem látja a **Csatlakozás az Azure Storage-hoz** ablakot, a fanézetben kattintson a jobb gombbal a **Tárfiókok** elemre, majd kattintson a **Csatlakozás az Azure Storage-hoz** elemre. 

    ![Csatlakozás az Azure Storage-hoz](media/quickstart-create-data-factory-powershell/storage-explorer-connect-azure-storage.png)
4. A **Csatolás a név és a kulcs használatával** ablakban illessze be az előző lépésben mentett **Fióknév** és **Fiókkulcs** értékeket. Ezután kattintson a **Tovább** gombra. 
5. A **Kapcsolatok összegzése** ablakban kattintson a **Csatlakozás** elemre.
6. Győződjön meg arról, hogy a tárfiók megjelenik a **(Helyi és csatolt)** -> **Tárfiókok** faszerkezetes nézetében. 
7. Bontsa ki a **Blobtárolók** elemet, és győződjön meg arról, hogy az **adftutorial** nevű blobtároló nem létezik. Ha már létezik, hagyja ki a következő, a tároló létrehozására vonatkozó lépéseket. 
8. Kattintson a jobb gombbal a **Blobtárolók** elemre, majd válassza a **Blobtároló létrehozása** lehetőséget.

    ![Blobtároló létrehozása](media/quickstart-create-data-factory-powershell/stroage-explorer-create-blob-container-menu.png)
9. Adja a tárolónak az **adftutorial** nevet, és nyomja le az **ENTER** billentyűt. 
10. Győződjön meg arról, hogy az **adftutorial** tároló van kijelölve a fanézetben. 
11. Kattintson az eszköztár **Új mappa** elemére. 

    ![Mappa létrehozása gomb](media/quickstart-create-data-factory-powershell/stroage-explorer-new-folder-button.png)
12. Az **Új virtuális könyvtár létrehozása** ablakban írja be az **input** értéket a **Név** mezőbe, majd kattintson az **OK** gombra. 

    ![Könyvtár létrehozása párbeszédpanel](media/quickstart-create-data-factory-powershell/storage-explorer-create-new-directory-dialog.png)
13. Indítsa el a **Jegyzettömböt**, és hozzon létre egy **emp.txt** nevű fájlt az alábbi tartalommal: 
    
    ```
    John, Doe
    Jane, Doe
    ```    
    Mentse a fájlt a **c:\ADFv2QuickStartPSH** mappába: Ha még nem létezik, hozza létre az **ADFv2QuickStartPSH** mappát. 
14. Kattintson az eszköztár **Feltöltés** gombjára, majd válassza a **Fájlok feltöltése** elemet. 

    ![Feltöltés gomb](media/quickstart-create-data-factory-powershell/storage-explorer-upload-button.png)
15. A **Fájlok feltöltése** ablakban a **Fájlok** elemnél válassza a `...` lehetőséget. 
16. A **Feltölteni kívánt mappa kiválasztása** ablakban lépjen az **emp.txt** fájlt tartalmazó mappába, majd válassza ki a fájlt. 

    ![Fájlok feltöltése párbeszédpanel](media/quickstart-create-data-factory-powershell/storage-explorer-upload-files-dialog.png)
17. A **Fájlok feltöltése** ablakban kattintson a **Feltöltés** elemre. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Az Azure PowerShell telepítése
Ha még nincs a gépén, telepítse az Azure PowerShell legújabb verzióját. 

1. A webböngészőben lépjen az [Azure SDK letöltések és SDK-k](https://azure.microsoft.com/downloads/) lapra. 
2. A **Parancssori eszközök** -> **PowerShell** szakaszban kattintson a **Windows-telepítés** elemre. 
3. Futtassa az **MSI**-fájlt az Azure PowerShell telepítéséhez. 

Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe
Indítsa el a gépén a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva a rövid útmutató végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell ezeket a parancsokat.

1. Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt Azure-beli felhasználói nevét és jelszavát:
       
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
1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az adat-előállító neveként használhat. 

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
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd próbálkozzon újra. Ha meg szeretné osztani az erőforráscsoportot másokkal, folytassa a következő lépéssel. 
5. Az adat-előállító létrehozásához futtassa az alábbi **Set-AzureRmDataFactoryV2** parancsmagot: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory-példányok létrehozásához az Azure-előfizetés **közreműködőjének** vagy **rendszergazdájának** kell lennie.
* A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

Társított szolgáltatásokat hozhat létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóhoz történő társításához. Ebben a rövid útmutatóban csak egy Azure Storage-beli társított szolgáltatást kell létrehoznia, amelyet forrás- és fogadóadattárként is használhat. Ebben a példában ennek a neve: AzureStorageLinkedService.

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

Megadhat egy adatkészletet, amely a forrásból a fogadóba másolt adatokat jelöli. Ebben a példában a blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik. Az adatkészlethez egy olyan paraméter szükséges, amelynek az értéke az adatkészletet feldolgozó tevékenységben van beállítva. A paraméter az adatok tárolására szolgáló helyre mutató **folderPath** létrehozására használható.

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
  
Ebben a példában a folyamat egy tevékenységet tartalmaz, és két paraméter szükséges hozzá: a bemeneti blob elérési útja és a kimeneti blob elérési útja. A paraméterek értékei a folyamat indításakor/futtatásakor lesznek beállítva. A másolási tevékenység az előző lépésben kimenetként és bemenetként létrehozott blob-adatkészletet használja. Ha az adatkészlet bemeneti adatkészletként van használatban, a bemeneti elérési út van megadva. Ha az adatkészlet kimeneti adatkészletként van használatban, a kimeneti elérési út van megadva. 

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

    Ha más tárolókat és mappákat használ, cserélje le az **inputPath** és **outputPath** paraméter értékét a forrás- és fogadóblob elérési útjára.

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
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
A folyamat automatikusan létrehozza a kimeneti mappát az adftutorial blobtárolóban. Ezután átmásolja az emp.txt fájlt a bemeneti mappából a kimeneti mappába. Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával ellenőrizze, hogy az inputBlobPath alatt található blobok át lettek-e másolva az outputBlobPath helyre. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. Törölheti az [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md), amely tartalmazza az erőforráscsoportban lévő összes erőforrást. Ha a többi erőforrást érintetlenül szeretné hagyni, csak az ebben az oktatóanyagban létrehozott adat-előállítót törölje.

Az alábbi parancsot futtassa a teljes erőforráscsoport törléséhez: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Az alábbi parancsot futtassa, ha csak az adat-előállítót szeretné törölni: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 
