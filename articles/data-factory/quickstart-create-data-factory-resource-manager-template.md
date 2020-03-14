---
title: Azure-beli adatelőállító létrehozása Resource Manager-sablonnal
description: Ebben az oktatóprogramban egy egyszerű minta Azure Data Factory-folyamatot fog létrehozni egy Azure Resource Manager-sablon segítségével.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 02/20/2019
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 7ad0367a89730c3aba37c5f75158cb42ae4ae668
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240743"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Oktatóanyag: Azure-beli adat-előállító létrehozása Azure Resource Manager-sablon használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Aktuális verzió](quickstart-create-data-factory-resource-manager-template.md)

A rövid útmutató bemutatja, hogyan hozhat létre Azure-beli adat-előállítókat Azure Resource Manager-sablonokkal. Az adat-előállítóban létrehozott folyamat adatokat **másol** egy Azure-blobtároló egyik mappájából egy másikba. Az adatok Azure Data Factoryval történő **átalakításának** útmutatásáért olvassa el az [az adatok Spark segítségével történő átalakítását ismertető oktatóanyagot](transform-data-using-spark.md).

> [!NOTE]
> Ez a cikk nem mutatja be részletesen a Data Factory szolgáltatást. Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-Az-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez.

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

A Resource Manager-sablonokkal kapcsolatos általános információkért tekintse meg [az Azure Resource Manager-sablonok készítését ismertető cikket](../azure-resource-manager/templates/template-syntax.md).

A következő szakasz a Data Factory-entitások meghatározására szolgáló teljes Resource Manager-sablont ismerteti, így gyorsan végighaladhat az oktatóanyagon és tesztelheti a sablont. Az egyes Data Factory-entitások meghatározásának megértéséhez tekintse meg a [Data Factory-entitások a sablonban](#data-factory-entities-in-the-template) szakaszt.

A sablon Data Factory erőforrásainak JSON-szintaxisával és tulajdonságaival kapcsolatos információkért lásd: [Microsoft. DataFactory-erőforrástípusok](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json"></a>Data Factory JSON

Hozzon létre egy **ADFTutorialARM. JSON** nevű JSON-fájlt a **C:\ADFTutorial** mappában (hozza létre a ADFTutorial mappát, ha még nem létezik) a következő tartalommal:

```json
{  
    "$schema":"http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "type":"string",
            "metadata":"Data Factory Name"
        },
        "dataFactoryLocation":{  
            "type":"string",
            "defaultValue":"East US",
            "metadata":{  
                "description":"Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName":{  
            "type":"string",
            "metadata":{  
                "description":"Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey":{  
            "type":"securestring",
            "metadata":{  
                "description":"Key for the Azure storage account."
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
    "variables":{  
        "factoryId":"[concat('Microsoft.DataFactory/factories/', parameters('dataFactoryName'))]"
    },
    "resources":[  
        {  
            "name":"[parameters('dataFactoryName')]",
            "apiVersion":"2018-06-01",
            "type":"Microsoft.DataFactory/factories",
            "location":"[parameters('dataFactoryLocation')]",
            "identity":{  
                "type":"SystemAssigned"
            },
            "resources":[  
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
                    "type":"Microsoft.DataFactory/factories/linkedServices",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "type":"AzureBlobStorage",
                        "typeProperties":{  
                            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "fileName":"emp.txt",
                                "folderPath":"input",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "folderPath":"output",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
                    "type":"Microsoft.DataFactory/factories/pipelines",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "activities":[  
                            {  
                                "name":"MyCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  

                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  

                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"BinarySource",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageReadSettings",
                                            "recursive":true
                                        }
                                    },
                                    "sink":{  
                                        "type":"BinarySink",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageWriteSettings"
                                        }
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetIn",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetOut",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ]
                            }
                        ],
                        "annotations":[  

                        ]
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
                    "type":"Microsoft.DataFactory/factories/triggers",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "runtimeState":"Started",
                        "pipelines":[  
                            {  
                                "pipelineReference":{  
                                    "referenceName":"ArmtemplateSampleCopyPipeline",
                                    "type":"PipelineReference"
                                },
                                "parameters":{  

                                }
                            }
                        ],
                        "type":"ScheduleTrigger",
                        "typeProperties":{  
                            "recurrence":{  
                                "frequency":"Hour",
                                "interval":1,
                                "startTime":"[parameters('triggerStartTime')]",
                                "endTime":"[parameters('triggerEndTime')]",
                                "timeZone":"UTC"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
                    ]
                }
            ]
        }
    ]
}
```
## <a name="parameters-json"></a>Paramétereket tartalmazó JSON-file

Hozzon létre egy **ADFTutorialARM-Parameters.json** elnevezésű JSON-fájlt, amely paramétereket tartalmaz az Azure Resource Manager-sablon számára.

> [!IMPORTANT]
> - Adja meg az Azure Storage-fiók nevét és kulcsát a **storageAccountName** és **storageAccountKey** paraméterek értékeinek ebben a paraméterfájlban. Létrehozta az adftutorial tárolót, és feltöltötte a mintafájlt (emp.txt) az Azure Blob Storage bemeneti mappájába.
> - Adjon meg egy globálisan egyedi nevet az adat-előállító számára a **dataFactoryName** paraméterhez. Például: ARMTutorialFactoryJohnDoe11282017.
> - A **triggerStartTime** paraméterhez adja meg az aktuális napot a következő formátumban: `2019-09-08T00:00:00`.
> - A **triggerEndTime** paraméterhez adja meg a következő napot a következő formátumban: `2019-09-09T00:00:00`. Az aktuális UTC-időt is megtekintheti, és befejezési időpontként megadhatja a következő egy-két órát. Például ha az aktuális UTC-idő 1:32, adja meg a következőt befejezési időpontként: `2019-09-09:03:00:00`. Ebben az esetben az eseményindító kétszer futtatja a folyamatot (2:00-kor és 3:00-kor).

```json
{  
    "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "value":"<datafactoryname>"
        },
        "dataFactoryLocation":{  
            "value":"East US"
        },
        "storageAccountName":{  
            "value":"<yourstorageaccountname>"
        },
        "storageAccountKey":{  
            "value":"<yourstorageaccountkey>"
        },
        "triggerStartTime":{  
            "value":"2019-09-08T11:00:00"
        },
        "triggerEndTime":{  
            "value":"2019-09-08T14:00:00"
        }
    }
}
```

> [!IMPORTANT]
> Készíthet különálló JSON-paraméterfájlokat a fejlesztési, tesztelési és az éles környezetek számára, amelyeket ugyanazzal a Data Factory JSON-sablonnal használhat. Ezekben a környezetekben automatizálhatja a Data Factory-entitások üzembe helyezését egy PowerShell-szkript használatával.

## <a name="deploy-data-factory-entities"></a>Data Factory-entitások üzembe helyezése

A PowerShellben futtassa a következő parancsot, hogy Data Factory entitásokat telepítsen az erőforráscsoporthoz (ebben az esetben a ADFTutorialResourceGroup példaként használja) az ebben a rövid útmutatóban korábban létrehozott Resource Manager-sablonnal.

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

A következő mintához hasonló kimenet jelenik meg:

```console
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 9/8/2019 10:52:29 AM
Mode                    : Incremental
TemplateLink            : 
Parameters              : 
                          Name                   Type                       Value     
                          =====================  =========================  ==========
                          dataFactoryName        String                     <data factory name>
                          dataFactoryLocation    String                     East US   
                          storageAccountName     String                     <storage account name>
                          storageAccountKey      SecureString                         
                          triggerStartTime       String                     9/8/2019 11:00:00 AM
                          triggerEndTime         String                     9/8/2019 2:00:00 PM
                          
Outputs                 : 
DeploymentDebugLogLevel : 
```

## <a name="start-the-trigger"></a>Az eseményindító elindítása

A sablon a következő Data Factory-entitásokat helyezi üzembe:

- Azure Storage társított szolgáltatás
- Bináris adatkészletek (bemeneti és kimeneti)
- Másolási tevékenységgel rendelkező folyamat
- A folyamatot elindító eseményindító

Az üzembe helyezett eseményindító leállított állapotban van. Az trigger indításának egyik módja a **Start-AzDataFactoryV2Trigger PowerShell-** parancsmag használata. A következő eljárás részletesen bemutatja a lépéseket:

1. A PowerShell ablakában hozzon létre egy változót, amely az erőforráscsoport nevét tárolja. Másolja be a következő parancsot a PowerShell ablakába, majd nyomja le az ENTER billentyűt. Ha más erőforráscsoport-nevet adott meg a New-AzResourceGroupDeployment parancshoz, itt frissítheti az értéket.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Hozzon létre egy változót, amely az adat-előállító nevét tárolja. Adja meg ugyanazt a nevet, amelyet az ADFTutorialARM-Parameters.json fájlban adott meg.

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Állítson be egy változót az eseményindító nevéhez. Az eseményindító neve szoftveresen kötött a Resource Manager-sablonfájlban (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Az adat-előállító és az eseményindító nevének megadása után kérje le **az eseményindító állapotát** a következő PowerShell-parancs futtatásával:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Itt látható a kimenet mintája:

    ```json

    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Ekkor az eseményindító futtatási állapota **Leállítva**.
5. **Indítsa el az eseményindítót**. Az eseményindító egész órakor futtatja a sablonban meghatározott folyamatot. Ez azt jelenti, hogy ha 14:25-kor hajtotta végre a parancsot, az eseményindító 15:00-kor futtatja először a folyamatot. Ezt követően óránként futtatja a folyamatot az triggerhez megadott befejezési időpontig.

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Itt látható a kimenet mintája:
    
    ```console
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. A Get-AzDataFactoryV2Trigger parancs újbóli futtatásával ellenőrizze, hogy elindult-e az trigger.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Itt látható a kimenet mintája:
    
    ```console
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Miután bejelentkezett az [Azure Portalra](https://portal.azure.com/), kattintson a **Minden szolgáltatás** elemre, végezzen keresést a kulcsszóval (például: **adat-el**), és válassza az **Adat-előállítók** lehetőséget.

2. Az **Adat-előállítók** lapon kattintson a létrehozott adat-előállítóra. Szükség esetén szűrje a listát az adat-előállító nevével.

3. Az adatfeldolgozó lapon kattintson a **szerző & figyelés** csempére.

4. Az **első lépések** lapon válassza a **figyelés fület**.  ![figyelő folyamatának futtatása](media/doc-common-process/get-started-page-monitor-button.png)

    > [!IMPORTANT]
    > Látható, hogy a folyamat csak egész órakor fut (például: 4:00, 5:00, 6:00 stb.). Kattintson az eszköztár **Frissítés** gombjára a lista frissítéséhez, amikor megkezdődik a következő óra.

5. Kattintson a **műveletek** oszlop **Megtekintés tevékenység futtatása** hivatkozására.

    ![Folyamat művelethivatkozása](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)

6. Itt a folyamat futtatásához kapcsolódó tevékenységfuttatások láthatóak. Ebben a rövid útmutatóban a folyamat csak egyetlen tevékenységtípussal rendelkezik: Másolás. Így ennek a tevékenységnek a futtatása látható.

    ![Tevékenységfuttatások](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
7. Kattintson a **kimenet** hivatkozásra a műveletek oszlop alatt. Megjelenik a másolási művelet kimenete egy **Kimenet** ablakban. A teljes kimenet megtekintéséhez kattintson a teljes méret gombra. Visszaállíthatja a kimeneti ablak eredeti méretét, vagy be is zárhatja azt.

8. Ha sikeres/sikertelen futtatást lát, állítsa le az eseményindítót. Az eseményindító óránként egyszer futtatja a folyamatot. A folyamat minden futtatáskor átmásolja ugyanazt a fájlt a bemeneti mappából a kimeneti mappába. Az eseményindító leállításához futtassa a következő parancsot a PowerShell ablakában.
    
    ```powershell
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="data-factory-entities-in-the-template"></a> Entitások JSON-definíciói

Az alábbi Data Factory-entitások a JSON-sablonban vannak definiálva:

- [Azure Storage társított szolgáltatás](#azure-storage-linked-service)
- [Bináris bemeneti adatkészlet](#binary-input-dataset)
- [Bináris kimeneti adatkészlet](#binary-output-dataset)
- [Másolási tevékenységgel rendelkező adatfolyamat](#data-pipeline)
- [Eseményindító](#trigger)

#### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás

Az AzureStorageLinkedService az Azure Storage-fiókot társítja az adat-előállítóval. Létrehozott egy tárolót, és adatokat töltött fel ebbe a tárfiókba az előfeltételek részeként. Ebben a szakaszban megadhatja az Azure-tárfiók nevét és kulcsát. Az Azure Storage társított szolgáltatás definiálásához használt JSON-tulajdonságokkal kapcsolatos információkért tekintse meg az [Azure Storage társított szolgáltatás](connector-azure-blob-storage.md#linked-service-properties) című szakaszt.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
    "type":"Microsoft.DataFactory/factories/linkedServices",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]"
    ]
}
```

A connectionString a storageAccountName és storageAccountKey paramétereket használja. A paraméterek értékei a konfigurációs fájlok használatával adhatók át. A definíció a sablonban definiált változókat is használja: azureStorageLinkedService és dataFactoryName.

#### <a name="binary-input-dataset"></a>Bináris bemeneti adatkészlet

Az Azure Storage társított szolgáltatása határozza meg azt a kapcsolati sztringet, amelyet futtatáskor a Data Factory szolgáltatás az Azure Storage-fiók csatlakoztatásához használ. A bináris adatkészlet definíciójában adja meg a bemeneti adatokat tartalmazó blob-tároló, mappa és fájl nevét. A bináris adatkészlet definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért lásd a [bináris adatkészlet tulajdonságai](format-binary.md#dataset-properties) című témakört.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="binary-output-dataset"></a>Bináris kimeneti adatkészlet

Megadhatja az Azure Blob Storage-ban található mappa nevét, amely a bemeneti mappából másolt adatokat tartalmazza. A bináris adatkészlet definiálásához használt JSON-tulajdonságokkal kapcsolatos részletekért lásd a [bináris adatkészlet tulajdonságai](format-binary.md#dataset-properties) című témakört.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="data-pipeline"></a>Adatfolyamat

Definiálhat egy folyamatot, amely adatokat másol egy bináris adatkészletből egy másik bináris adatkészletbe. A példában található folyamat definiálásához használt JSON-elemek leírásához tekintse meg [A folyamat JSON-fájlja](concepts-pipelines-activities.md#pipeline-json) című szakaszt.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "apiVersion":"2018-06-01",
    "properties":{  
        "activities":[  
            {  
                "name":"MyCopyActivity",
                "type":"Copy",
                "dependsOn":[  

                ],
                "policy":{  
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[  

                ],
                "typeProperties":{  
                    "source":{  
                        "type":"BinarySource",
                        "storeSettings":{  
                            "type":"AzureBlobStorageReadSettings",
                            "recursive":true
                        }
                    },
                    "sink":{  
                        "type":"BinarySink",
                        "storeSettings":{  
                            "type":"AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging":false
                },
                "inputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetIn",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ],
                "outputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetOut",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ]
            }
        ],
        "annotations":[  

        ]
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
    ]
}
```

#### <a name="trigger"></a>Eseményindító

Meghatározhat egy eseményindítót, amely óránként egyszer futtatja a folyamatot. Az üzembe helyezett eseményindító leállított állapotban van. Indítsa el a triggert a **Start-AzDataFactoryV2Trigger** parancsmag használatával. Az eseményindítókról további információkat a [Folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#triggers) című cikkben talál.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
    "type":"Microsoft.DataFactory/factories/triggers",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "runtimeState":"Started",
        "pipelines":[  
            {  
                "pipelineReference":{  
                    "referenceName":"ArmtemplateSampleCopyPipeline",
                    "type":"PipelineReference"
                },
                "parameters":{  

                }
            }
        ],
        "type":"ScheduleTrigger",
        "typeProperties":{  
            "recurrence":{  
                "frequency":"Hour",
                "interval":1,
                "startTime":"[parameters('triggerStartTime')]",
                "endTime":"[parameters('triggerEndTime')]",
                "timeZone":"UTC"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
    ]
}
```

## <a name="reuse-the-template"></a>A sablon ismételt felhasználása

Az oktatóanyagban létrehozott egy sablont a Data Factory-entitások definiálásához, illetve egy másikat a paraméterek értékeinek átadásához. Ha ugyanazt a sablont szeretné használni a Data Factory-entitások különböző környezetekben történő üzembe helyezéséhez, hozzon létre egy paraméterfájlt az egyes környezetekhez, és használja azt az adott környezetben történő üzembe helyezéskor.

Példa:

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

Megfigyelheti, hogy az első parancs a fejlesztőkörnyezet, a második a tesztkörnyezet, a harmadik pedig az éles környezet paraméterfájlját használja.

Emellett ismétlődő feladatok elvégzéséhez is újból felhasználhatja a sablont. Például létrehozhat több olyan, egy vagy több folyamattal rendelkező adat-előállítót, amelyek ugyanazt a logikát alkalmazzák, de az egyes adat-előállítók különböző Azure Storage-fiókokat használnak. Ebben a forgatókönyvben ugyanazt a sablont használja ugyanabban a környezetben (fejlesztői, teszt vagy éles) különböző paraméterfájlokkal a data factoryk létrehozásához.

## <a name="next-steps"></a>Következő lépések

A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md).
