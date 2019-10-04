---
title: Folyamat végrehajtása tevékenység a Azure Data Factoryban | Microsoft Docs
description: Ebből a témakörből megtudhatja, hogy a folyamat végrehajtása tevékenység használatával hogyan hívhat meg egy Data Factory folyamatot egy másik Data Factory folyamatból.
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
ms.openlocfilehash: 1611f740f6b55ecf9f15ecd234d63b5e95baeba1
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141707"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Folyamat végrehajtása tevékenység Azure Data Factory
A folyamat végrehajtása tevékenység lehetővé teszi egy Data Factory folyamat számára egy másik folyamat meghívását.

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
name | A folyamat végrehajtása tevékenység neve. | Sztring | Igen
type | A következőre kell beállítani: **ExecutePipeline**. | Sztring | Igen
adatcsatorna | A folyamat által meghívott függő folyamathoz tartozó folyamatra mutató hivatkozás. A folyamat hivatkozási objektumának két tulajdonsága van: **hivatkozásnév** és **Type**. A hivatkozásnév tulajdonság a hivatkozási folyamat nevét adja meg. A Type tulajdonságot PipelineReference értékre kell beállítani. | PipelineReference | Igen
parameters | A meghívott folyamatnak átadandó paraméterek | Egy JSON-objektum, amely a paraméterek nevét az argumentum értékére képezi. | Nem
waitOnCompletion | Meghatározza, hogy a tevékenység-végrehajtás megvárja-e a függő folyamat végrehajtásának befejeződését. Az alapértelmezett érték a false (hamis). | Logikai | Nem

## <a name="sample"></a>Minta
Ennek a forgatókönyvnek két folyamata van:

- **Főfolyamat** – ez a folyamat egyetlen végrehajtási folyamattal rendelkezik, amely meghívja a meghívott folyamatot. A fő folyamat két paramétert vesz `masterSourceBlobContainer`igénybe:, `masterSinkBlobContainer`.
- **Meghívott folyamat** – ez a folyamat egy másolási tevékenységgel rendelkezik, amely egy Azure Blob-forrásból származó adatok másolását végzi az Azure Blob fogadóba. A meghívott folyamat két paramétert vesz `sourceBlobContainer`igénybe:, `sinkBlobContainer`.

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

**Társított szolgáltatás**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**Forrás adatkészlete**
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

**Fogadó adatkészlet**
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

A fő folyamat ebben a példában való futtatásához a masterSourceBlobContainer és a masterSinkBlobContainer paraméterek a következő értékeket adja át: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

A fő folyamat továbbítja ezeket az értékeket a meghívott folyamatnak az alábbi példában látható módon: 

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
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
