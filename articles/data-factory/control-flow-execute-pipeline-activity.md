---
title: Folyamatfolyamat-tevékenység végrehajtása az Azure Data Factoryban
description: Megtudhatja, hogyan használhatja a folyamatfuttatási tevékenység végrehajtása egy Data Factory folyamat meghívására egy másik Data Factory-folyamatból.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 26dd7c4f33360030b13ddbfc1516396436724c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440442"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Folyamattevékenység végrehajtása az Azure Data Factoryban
A Folyamat végrehajtása tevékenység lehetővé teszi egy Data Factory-folyamat számára egy másik folyamat meghívását.

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
név | A végrehajtási folyamattevékenység neve. | Sztring | Igen
type | A következőt kell beállítani: **ExecutePipeline**. | Sztring | Igen
Csővezeték | A folyamat hivatkozása a függő folyamatra, amelyet ez a folyamat meghívja. A folyamat referenciaobjektumának két tulajdonsága van: **referenceName** és **type**. A referenceName tulajdonság a referencia-folyamat nevét adja meg. A típus tulajdonságot PipelineReference (PipelineReference) tulajdonságra kell állítani. | PipelineReference | Igen
paraméterek | A meghívott folyamatnak átadandó paraméterek | JSON-objektum, amely paraméterneveket rendel argumentumértékekhez | Nem
waitOnCompletion | Azt határozza meg, hogy a tevékenység végrehajtása megvárja-e a függő folyamat végrehajtásának befejezését. Az alapértelmezett érték a false (hamis). | Logikai | Nem

## <a name="sample"></a>Sample
Ebben a forgatókönyvben két folyamat található:

- **Fő feldolgozási folyamat** – Ez a folyamat egy végrehajtási folyamat tevékenység, amely meghívja a meghívott folyamat. A fő folyamat két `masterSourceBlobContainer`paramétert vesz igénybe: . `masterSinkBlobContainer`.
- **Meghívott folyamat** – Ez a folyamat rendelkezik egy másolási tevékenység, amely adatokat másol egy Azure Blob-forrásból az Azure Blob-fogadó. A meghívott folyamat két paramétert vesz igénybe: `sourceBlobContainer`. . `sinkBlobContainer`

### <a name="master-pipeline-definition"></a>Fő folyamat definíciója

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Meghívott folyamat definíciója

```json
{
  "name": "invokedPipeline",
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
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Társított szolgáltatások**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Forrásadatkészlet**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Fogadó-adatkészlet**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>A folyamat futtatása

Ebben a példában a fő folyamat futtatásához a következő értékek et adják át a masterSourceBlobContainer és a masterSinkBlobContainer paramétereknek: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

A fő folyamat továbbítja ezeket az értékeket a meghívott folyamatnak, ahogy az a következő példában látható: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamattevékenységeket: 

- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresstevékenységet](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
