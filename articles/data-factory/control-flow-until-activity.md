---
title: "Amíg az Azure Data Factory tevékenység |} Microsoft Docs"
description: "A Until tevékenység tevékenységek egy készletét ismétlődő végrehajtja a addig, amíg a tevékenységhez társított feltétel értéke true, vagy azt az időkorlátot."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: da15ddd47580a37a7fdb35adfd80482b698074fd
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="until-activity-in-azure-data-factory"></a>Amíg az Azure Data Factory tevékenység
A Until tevékenység ugyanezt a funkcionalitást biztosítja, hogy egy tegye-mindaddig, amíg az ismétlési struktúra programozási nyelvek nyújt. Egy tevékenységkészletet futtat le ciklusosan addig, amíg a tevékenységhez rendelt feltétel igaz értéket nem ad vissza. Megadhat egy időtúllépési értéket az Until tevékenységhez a Data Factoryban. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Data Factory V1 dokumentáció](v1/data-factory-introduction.md).

## <a name="syntax"></a>Szintaxis

```json
{
    "type": "Until",
    "typeProperties": {
        "expression":  {
            "value":  "<expression that evaluates to true or false>", 
            "type": "Expression"
        },
        "timeout": "<time out for the loop. for example: 00:01:00 (1 minute)>",
        "activities": [
            {
                "<Activity 1 definition>"
            },
            {
                "<Activity 2 definition>"
            },
            {
                "<Activity N definition>"
            }
        ]
    },
    "name": "MyUntilActivity"
}

```

## <a name="type-properties"></a>A típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | Neve a `Until` tevékenység. | Karakterlánc | Igen
type | Meg kell **amíg**. | Karakterlánc | Igen
kifejezés | IGAZ vagy hamis kifejezés | Kifejezés.  | Igen
timeout | A ne-csak a megadott idő elteltével időtúllépést hurok. | Karakterlánc. `d.hh:mm:ss`(vagy) `hh:mm:ss`. Az alapértelmezett érték 7 nap. Maximális érték: 90 nap. | Nem
Tevékenységek | Amíg a kifejezés eredménye végrehajtott tevékenység `true`. | Tevékenységek tömbje. |  Igen

## <a name="example-1"></a>1. példa

> [!NOTE]
> Ez a szakasz a JSON-definíciók és futtatjuk a folyamatot a PowerShell Példaparancsok nyújt. Az Azure PowerShell és a JSON-definíciók használatával a Data Factory-folyamathoz létrehozásának részletes útmutatóját bemutatóért lásd: [oktatóanyag: Azure PowerShell használatával létrehozni egy adat-előállító](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-until-activity"></a>A tevékenység amíg feldolgozási sorban
Ebben a példában az adatcsatorna két tevékenység rendelkezik: **amíg** és **Várjon, amíg**. A Várakozás tevékenység vár a megadott időszak előtt a webalkalmazás tevékenység fut-e a ciklusban. Kifejezések és a Data Factory funkciók kapcsolatos további tudnivalókért lásd: [kifejezés nyelvet és a funkciók](control-flow-expression-language-functions.md). 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="example-2"></a>2. példa 
Ez a példa a csővezeték másol adatokat egy bemeneti mappa ismétlődő kimeneti mappa. A hurok ér véget, amikor az ismétlődő paraméter false értékre van beállítva, vagy egy perc után időtúllépéssel leáll.   

### <a name="pipeline-with-until-activity-adfv2quickstartpipelinejson"></a>A tevékenység (Adfv2QuickStartPipeline.json) amíg feldolgozási sorban

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression":  {
                        "value":  "@equals('false', pipeline().parameters.repeat)", 
                        "type": "Expression"
                    },
                    "timeout": "00:01:00",
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
                            },
                            "policy": {
                                "retry": 1,
                                "timeout": "00:10:00",
                                "retryIntervalInSeconds": 60
                            }
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            },
            "repeat": {
                "type": "String"
            }                        
        }        
    }
}

```


### <a name="azure-storage-linked-service-azurestoragelinkedservicejson"></a>Az Azure tárolás társított szolgáltatásának (AzureStorageLinkedService.json)

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<Azure Storage account name>;AccountKey=<Azure Storage account key>",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="parameterized-azure-blob-dataset-blobdatasetjson"></a>A paraméteres Azure Blob-adathalmazra (BlobDataset.json)
A folyamat beállítása a **folderPath** vagy értékével **outputPath1** vagy **outputPath2** paraméter, a folyamatának. 

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

### <a name="pipeline-parameter-json-pipelineparametersjson"></a>A paraméter (PipelineParameters.json) JSON-feldolgozási folyamat

```json
{
    "inputPath": "adftutorial/input",
    "outputPath": "adftutorial/outputUntil",
    "repeat": "true"
}
```

### <a name="powershell-commands"></a>PowerShell-parancsok
A parancsok használata, hogy a JSON-fájlokat mentette a mappába: C:\ADF. 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription "<Your subscription name>"

$resourceGroupName = "<Resource Group Name>"
$dataFactoryName = "<Data Factory Name. Must be globally unique>";
Remove-AzureRmDataFactoryV2 $dataFactoryName -ResourceGroupName $resourceGroupName -force


Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile "C:\ADF\AzureStorageLinkedService.json"
Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile "C:\ADF\BlobDataset.json"
Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile "C:\ADF\Adfv2QuickStartPipeline.json"
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile C:\ADF\PipelineParameters.json

while ($True) {
    $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

    if ($run) {
        if ($run.Status -ne 'InProgress') {
            Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
            $run
            break
        }
        Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        Write-Host "Activity run details:" -foregroundcolor "Yellow"
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
        $result

        Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
        $result.Output -join "`r`n"
    }

    Start-Sleep -Seconds 15
}
```

## <a name="next-steps"></a>További lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)