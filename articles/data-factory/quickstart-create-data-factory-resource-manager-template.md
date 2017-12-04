---
title: "Azure-beli adat-előállító létrehozása Resource Manager-sablon használatával | Microsoft Docs"
description: "Ebben az oktatóprogramban egy egyszerű minta Azure Data Factory-folyamatot fog létrehozni egy Azure Resource Manager-sablon segítségével."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/28/2017
ms.author: spelluru
ms.openlocfilehash: 6a6d0af6ed4e2c4ece7d69f6d7606e3ca149f8a7
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Oktatóanyag: Azure-beli adat-előállító létrehozása Azure Resource Manager-sablon használatával
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [2. verzió – Előzetes verzió](quickstart-create-data-factory-resource-manager-template.md) 

A rövid útmutató bemutatja, hogyan hozhat létre Azure-beli adat-előállítókat Azure Resource Manager-sablonokkal. Az adat-előállítóban létrehozott folyamat adatokat **másol** egy Azure-blobtároló egyik mappájából egy másikba. Az adatok Azure Data Factoryval történő **átalakításának** útmutatásáért olvassa el az [az adatok Spark segítségével történő átalakítását ismertető oktatóanyagot](transform-data-using-spark.md). 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, tekintse meg [az első adat-előállító a Data Factory 1. verziójának használatával történő összeállítását ismertető cikket](v1/data-factory-build-your-first-pipeline-using-arm.md).
>
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

[!INCLUDE [data-factory-quickstart-prerequisites-2](../../includes/data-factory-quickstart-prerequisites-2.md)]

## <a name="resource-manager-templates"></a>Resource Manager-sablonok
A Resource Manager-sablonokkal kapcsolatos általános információkért tekintse meg [az Azure Resource Manager-sablonok készítését ismertető cikket](../azure-resource-manager/resource-group-authoring-templates.md). 

A következő szakasz a Data Factory-entitások meghatározására szolgáló teljes Resource Manager-sablont ismerteti, így gyorsan végighaladhat az oktatóanyagon és tesztelheti a sablont. Az egyes Data Factory-entitások meghatározásának megértéséhez tekintse meg a [Data Factory-entitások a sablonban](#data-factory-entities-in-the-template) szakaszt.

## <a name="data-factory-json"></a>Data Factory JSON 
Hozzon létre egy **ADFTutorialARM.json** nevű JSON-fájlt a **C:\ADFTutorial** mappában a következő tartalommal:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "dataFactoryName": {
            "type": "string",
            "metadata": {
                "description": "Name of the data factory. Must be globally unique."
            }
        },
        "dataFactoryLocation": {
            "type": "string",
            "allowedValues": [
                "East US",
                "East US 2"
            ],
            "defaultValue": "East US",
            "metadata": {
                "description": "Location of the data factory. Currently, only East US and East US 2 are supported. "
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey": {
            "type": "securestring",
            "metadata": {
                "description": "Key for the Azure storage account."
            }
        },
        "blobContainer": {
            "type": "string",
            "metadata": {
                "description": "Name of the blob container in the Azure Storage account."
            }
        },
        "inputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that has the input file."
            }
        },
        "inputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the input file/blob."
            }
        },
        "outputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that will hold the transformed data."
            }
        },
        "outputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the output file/blob."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },
    "variables": {
        "azureStorageLinkedServiceName": "ArmtemplateStorageLinkedService",
        "inputDatasetName": "ArmtemplateTestDatasetIn",
        "outputDatasetName": "ArmtemplateTestDatasetOut",
        "pipelineName": "ArmtemplateSampleCopyPipeline",
        "triggerName": "ArmTemplateTestTrigger"
    },
    "resources": [{
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "2017-09-01-preview",
        "type": "Microsoft.DataFactory/factories",
        "location": "[parameters('dataFactoryLocation')]",
        "properties": {
            "loggingStorageAccountName": "[parameters('storageAccountName')]",
            "loggingStorageAccountKey": "[parameters('storageAccountKey')]"
        },
        "resources": [{
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": {
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                            "type": "SecureString"
                        }
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('inputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
                        "fileName": "[parameters('inputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('outputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
                        "fileName": "[parameters('outputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "pipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "activities": [{
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "BlobSource"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "name": "MyCopyActivity",
                        "inputs": [{
                            "referenceName": "[variables('inputDatasetName')]",
                            "type": "DatasetReference"
                        }],
                        "outputs": [{
                            "referenceName": "[variables('outputDatasetName')]",
                            "type": "DatasetReference"
                        }]
                    }]
                }
            },
            {
                "type": "triggers",
                "name": "[variables('triggerName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]",
                    "[variables('pipelineName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "ScheduleTrigger",
                    "typeProperties": {
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1,
                            "startTime": "[parameters('triggerStartTime')]",
                            "endTime": "[parameters('triggerEndTime')]",
                            "timeZone": "UTC"
                        }
                    },
                    "pipelines": [{
                        "pipelineReference": {
                            "type": "PipelineReference",
                            "referenceName": "ArmtemplateSampleCopyPipeline"
                        },
                        "parameters": {}
                    }]
                }
            }
        ]
    }]
}
```

## <a name="parameters-json"></a>Paramétereket tartalmazó JSON-file
Hozzon létre egy **ADFTutorialARM-Parameters.json** elnevezésű JSON-fájlt, amely paramétereket tartalmaz az Azure Resource Manager-sablon számára.  

> [!IMPORTANT]
> - Adja meg az Azure Storage-fiók nevét és kulcsát a **storageAccountName** és **storageAccountKey** paraméterek értékeinek ebben a paraméterfájlban. Létrehozta az adftutorial tárolót, és feltöltötte a mintafájlt (emp.txt) az Azure Blob Storage bemeneti mappájába. 
> - Adjon meg egy globálisan egyedi nevet az adat-előállító számára a **dataFactoryName** paraméterhez. Például: ARMTutorialFactoryJohnDoe11282017. 
> - A **triggerStartTime** paraméterhez adja meg az aktuális napot a következő formátumban: `2017-11-28T00:00:00`.
> - A **triggerEndTime** paraméterhez adja meg a következő napot a következő formátumban: `2017-11-29T00:00:00`. Az aktuális UTC-időt is megtekintheti, és befejezési időpontként megadhatja a következő egy-két órát. Például ha az aktuális UTC-idő 1:32, adja meg a következőt befejezési időpontként: `2017-11-29:03:00:00`. Ebben az esetben az eseményindító kétszer futtatja a folyamatot (2:00-kor és 3:00-kor).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataFactoryName": {
      "value": "<datafactoryname>"
    },    
    "dataFactoryLocation": {
      "value": "East US"
    },
    "storageAccountName": {
      "value": "<yourstroageaccountname>"
    },
    "storageAccountKey": {
      "value": "<yourstorageaccountkey>"
    },
    "blobContainer": {
      "value": "adftutorial"
    },
    "inputBlobFolder": {
      "value": "input"
    },
    "inputBlobName": {
      "value": "emp.txt"
    },
    "outputBlobFolder": {
      "value": "output"
    },
    "outputBlobName": {
      "value": "emp.txt"
    },
    "triggerStartTime": {
        "value": "2017-11-28T00:00:00. Set to today"
    },
    "triggerEndTime": {
        "value": "2017-11-29T00:00:00. Set to tomorrow"
    }
  }
}
```

> [!IMPORTANT]
> Készíthet különálló JSON-paraméterfájlokat a fejlesztési, tesztelési és az éles környezetek számára, amelyeket ugyanazzal a Data Factory JSON-sablonnal használhat. Ezekben a környezetekben automatizálhatja a Data Factory-entitások üzembe helyezését egy PowerShell-szkript használatával. 

## <a name="deploy-data-factory-entities"></a>Data Factory-entitások üzembe helyezése 
A PowerShellben futtassa a következő parancsot Data Factory-entitások a rövid útmutató során korábban létrehozott Resource Manager-sablonnal történő üzembe helyezéséhez. 

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

A következő mintához hasonló kimenet jelenik meg: 

```
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 11/29/2017 3:11:13 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                 Type            Value
                          ===============      ============    ==========
                          dataFactoryName      String          <data factory name>
                          dataFactoryLocation  String          East US
                          storageAccountName   String          <storage account name>
                          storageAccountKey    SecureString
                          blobContainer        String          adftutorial
                          inputBlobFolder      String          input
                          inputBlobName        String          emp.txt
                          outputBlobFolder     String          output
                          outputBlobName       String          emp.txt
                          triggerStartTime     String          11/29/2017 12:00:00 AM
                          triggerEndTime       String          11/29/2017 4:00:00 AM

Outputs                 :
DeploymentDebugLogLevel :
```

## <a name="start-the-trigger"></a>Az eseményindító elindítása

A sablon a következő Data Factory-entitásokat helyezi üzembe: 

- Azure Storage társított szolgáltatás
- Azure Blob-adatkészletek (bemeneti és kimeneti)
- Másolási tevékenységgel rendelkező folyamat
- A folyamatot elindító eseményindító

Az üzembe helyezett eseményindító leállított állapotban van. Az eseményindító elindításának egyik módja a **Start-AzureRmDataFactoryV2Trigger** PowerShell-parancsmag használata. A következő eljárás részletesen bemutatja a lépéseket: 

1. A PowerShell ablakában hozzon létre egy változót, amely az erőforráscsoport nevét tárolja. Másolja be a következő parancsot a PowerShell ablakába, majd nyomja le az ENTER billentyűt. Ha más erőforráscsoport-nevet adott meg a New-AzureRmResourceGroupDeployment parancs számára, itt frissítheti az értéket. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ``` 
1. Hozzon létre egy változót, amely az adat-előállító nevét tárolja. Adja meg ugyanazt a nevet, amelyet az ADFTutorialARM-Parameters.json fájlban adott meg.   

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Állítson be egy változót az eseményindító nevéhez. Az eseményindító neve szoftveresen kötött a Resource Manager-sablonfájlban (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Az adat-előállító és az eseményindító nevének megadása után kérje le **az eseményindító állapotát** a következő PowerShell-parancs futtatásával:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Itt látható a minta kimenete: 

    ```json
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Ekkor az eseményindító futtatási állapota **Leállítva**. 
5. **Indítsa el az eseményindítót**. Az eseményindító egész órakor futtatja a sablonban meghatározott folyamatot. Ez azt jelenti, hogy ha 14:25-kor hajtotta végre a parancsot, az eseményindító 15:00-kor futtatja először a folyamatot. Ezután óránként futtatja a folyamatot az eseményindító számára megadott befejezési időpontig. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Itt látható a minta kimenete: 
    
    ```
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. A Get-AzureRmDataFactoryV2Trigger parancs újbóli futtatásával ellenőrizze, hogy elindult-e az eseményindító.  

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Itt látható a minta kimenete:
    
    ```
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. Miután bejelentkezett az [Azure Portalra](https://portal.azure.com/), kattintson a **További szolgáltatások** elemre, végezzen keresést a kulcsszóval (például `data fa`), és válassza az **Adat-előállítók** lehetőséget.

    ![Böngészés az adat-előállítók menüjében](media/quickstart-create-data-factory-resource-manager-template/browse-data-factories-menu.png)
2. Az **Adat-előállítók** lapon kattintson a létrehozott adat-előállítóra. Szükség esetén szűrje a listát az adat-előállító nevével.  

    ![Adat-előállító kiválasztása](media/quickstart-create-data-factory-resource-manager-template/select-data-factory.png)
3. Az Adat-előállító lapon kattintson a **Figyelés és felügyelet** csempére. 

    ![Figyelés és felügyelet csempe](media/quickstart-create-data-factory-resource-manager-template/monitor-manage-tile.png)
4. Ekkor megnyílik a webböngészőben az **adatintegrációs alkalmazás** egy új lapon. Ha a figyelés lap nem aktív, váltson át a **figyelés lapra**. A folyamat futtatását egy **ütemező eseményindító** indította el. 

    ![Folyamat futtatásának figyelése](media/quickstart-create-data-factory-resource-manager-template/monitor-pipeline-run.png)    

    > [!IMPORTANT]
    > Látható, hogy a folyamat csak egész órakor fut (például: 4:00, 5:00, 6:00 stb.). Kattintson az eszköztár **Frissítés** gombjára a lista frissítéséhez, amikor megkezdődik a következő óra. 
5. Kattintson a **Műveletek** oszlopokban található hivatkozásra. 

    ![Folyamat művelethivatkozása](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)
6. Itt a folyamat futtatásához kapcsolódó tevékenységfuttatások láthatóak. Ebben a rövid útmutatóban a folyamat csak egyetlen tevékenységtípussal rendelkezik: Másolás. Így ennek a tevékenységnek a futtatása látható. 

    ![Tevékenységfuttatások](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
1. Kattintson a **Kimenet** oszlop alatti hivatkozásra. Megjelenik a másolási művelet kimenete egy **Kimenet** ablakban. A teljes kimenet megtekintéséhez kattintson a teljes méret gombra. Visszaállíthatja a kimeneti ablak eredeti méretét, vagy be is zárhatja azt. 

    ![Kimeneti ablak](media/quickstart-create-data-factory-resource-manager-template/output-window.png)
7. Ha sikeres/sikertelen futtatást lát, állítsa le az eseményindítót. Az eseményindító óránként egyszer futtatja a folyamatot. A folyamat minden futtatáskor átmásolja ugyanazt a fájlt a bemeneti mappából a kimeneti mappába. Az eseményindító leállításához futtassa a következő parancsot a PowerShell ablakában. 
    
    ```powershell
    Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="json-definitions-for-entities"></a>Entitások JSON-definíciói
Az alábbi Data Factory-entitások a JSON-sablonban vannak definiálva: 

- [Azure Storage társított szolgáltatás](#azure-storage-linked-service)
- [Azure blobbemeneti adatkészlet](#azure-blob-input-dataset)
- [Azure blobkimeneti adatkészlet](#azure-blob-output-dataset)
- [Másolási tevékenységgel rendelkező adatfolyamat](#data-pipeline)
- [Eseményindító](#trigger)

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Létrehozott egy tárolót, és adatokat töltött fel ebbe a tárfiókba az előfeltételek részeként. Ebben a szakaszban megadhatja az Azure-tárfiók nevét és kulcsát. Az Azure Storage társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Storage társított szolgáltatás](connector-azure-blob-storage.md#linked-service-properties) című szakaszt. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": {
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                "type": "SecureString"
            }
        }
    }
}
```

A connectionString a storageAccountName és storageAccountKey paramétereket használja. A paraméterek értékei a konfigurációs fájlok használatával adhatók át. A definíció változókat is használ: azureStroageLinkedService és dataFactoryName, amelyek a sablonban vannak definiálva. 

#### <a name="azure-blob-input-dataset"></a>Azure blobbemeneti adatkészlet
Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. Az Azure Blob-adatkészletek definíciójában adhatja meg a bemeneti adatokat tartalmazó blobtároló, mappa és fájl nevét. Az Azure Blob-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Blob-adatkészlet tulajdonságai](connector-azure-blob-storage.md#dataset-properties) című szakaszt. 

```json
{
    "type": "datasets",
    "name": "[variables('inputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
        "fileName": "[parameters('inputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
},

```

#### <a name="azure-blob-output-dataset"></a>Azure blobkimeneti adatkészlet
Megadhatja az Azure Blob Storage-ban található mappa nevét, amely a bemeneti mappából másolt adatokat tartalmazza. Az Azure Blob-adatkészletek definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Blob-adatkészlet tulajdonságai](connector-azure-blob-storage.md#dataset-properties) című szakaszt. 

```json
{
    "type": "datasets",
    "name": "[variables('outputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
        "fileName": "[parameters('outputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
}
```

#### <a name="data-pipeline"></a>Adatfolyamat
Meghatározhat egy folyamatot, amely adatokat másol egy Azure Blob-adatkészletből egy másik Azure Blob-adatkészletbe. A példában található folyamat definiálásához használt JSON-elemek leírásához tekintse meg [A folyamat JSON-fájlja](concepts-pipelines-activities.md#pipeline-json) című szakaszt. 

```json
{
    "type": "pipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('inputDatasetName')]",
    "[variables('outputDatasetName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "activities": [
        {
        "type": "Copy",
        "typeProperties": {
            "source": {
            "type": "BlobSource"
            },
            "sink": {
            "type": "BlobSink"
            }
        },
        "name": "MyCopyActivity",
        "inputs": [
            {
            "referenceName": "[variables('inputDatasetName')]",
            "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
            "referenceName": "[variables('outputDatasetName')]",
            "type": "DatasetReference"
            }
        ]
        }
    ]
    }
}
```

#### <a name="trigger"></a>Eseményindító
Meghatározhat egy eseményindítót, amely óránként egyszer futtatja a folyamatot. Az üzembe helyezett eseményindító leállított állapotban van. Indítsa el az eseményindítót a **Start-AzureRmDataFactoryV2Trigger** parancsmaggal. Az eseményindítókról további információkat a [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#triggers) című cikkben talál. 

```json
{
    "type": "triggers",
    "name": "[variables('triggerName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('inputDatasetName')]",
        "[variables('outputDatasetName')]",
        "[variables('pipelineName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-28T00:00:00",
                "endTime": "2017-11-29T00:00:00",
                "timeZone": "UTC"               
            }
        },
        "pipelines": [{
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "ArmtemplateSampleCopyPipeline"
            },
            "parameters": {}
        }]
    }
}
```

## <a name="reuse-the-template"></a>A sablon ismételt felhasználása
Az oktatóanyagban létrehozott egy sablont a Data Factory-entitások definiálásához, illetve egy másikat a paraméterek értékeinek átadásához. Ha ugyanazt a sablont szeretné használni a Data Factory-entitások különböző környezetekben történő üzembe helyezéséhez, hozzon létre egy paraméterfájlt az egyes környezetekhez, és használja azt az adott környezetben történő üzembe helyezéskor.     

Példa:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Megfigyelheti, hogy az első parancs a fejlesztőkörnyezet, a második a tesztkörnyezet, a harmadik pedig az éles környezet paraméterfájlját használja.  

Emellett ismétlődő feladatok elvégzéséhez is újból felhasználhatja a sablont. Például létrehozhat több olyan, egy vagy több folyamattal rendelkező adat-előállítót, amelyek ugyanazt a logikát alkalmazzák, de az egyes adat-előállítók különböző Azure Storage-fiókokat használnak. Ebben a forgatókönyvben ugyanazt a sablont használja ugyanabban a környezetben (fejlesztői, teszt vagy éles) különböző paraméterfájlokkal a data factoryk létrehozásához. 


## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md). 