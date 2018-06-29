---
title: Adatcsatorna tevékenység végrehajtása az Azure Data Factory |} Microsoft Docs
description: Ismerje meg, hogyan használhatja a feldolgozási sor végrehajtása tevékenység meghívni egy másik Data Factory-folyamat a Data Factory-folyamathoz.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 2aa25004fb9c2e914cd8c669095953e174686197
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051763"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Feldolgozási sor tevékenység végrehajtása az Azure Data Factory
A feldolgozási sor végrehajtása tevékenység lehetővé teszi, hogy a Data Factory-folyamathoz meghívni egy másik folyamat.

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

## <a name="type-properties"></a>A típus tulajdonságai
Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | A végrehajtási folyamat tevékenység nevét. | Sztring | Igen
type | Értékre kell állítani: **ExecutePipeline**. | Sztring | Igen
adatcsatorna | Hivatkozás a függő folyamat, amely ebben az adatcsatornában hívja meg a következő feldolgozási sorban. A folyamat referenciaobjektum két tulajdonságokkal rendelkezik: **hivatkozásnév** és **típus**. A hivatkozásnév tulajdonság határozza meg a referencia-feldolgozási folyamat nevét. A type tulajdonságot meg kell PipelineReference. | PipelineReference | Igen
paraméterek | A meghívott csővezeték átadandó paraméterek | Egy JSON-objektum, amely leképezhető argumentumérték paraméterének neve | Nem
waitOnCompletion | Meghatározza, hogy tevékenység végrehajtási megvárja-e a függő feldolgozási sor végrehajtása befejeződik. | Az alapértelmezett érték a false (hamis). | Logikai | Nem

## <a name="sample"></a>Sample
Ebben a forgatókönyvben két folyamatok rendelkezik:

- **Fő folyamat** -Ez az adatcsatorna egy folyamat végrehajtása tevékenység, amely behívja a meghívott csővezeték rendelkezik. A fő folyamat két paramétereket fogadja: `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Meghívott adatcsatorna** -Ez az adatcsatorna egy másolási tevékenység során, amely adatokat másol egy Azure Blob-forrás Azure Blob fogadó rendelkezik. A meghívott folyamat két paramétereket fogadja: `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Fő folyamat meghatározása

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
            "sinkBlobCountainer": {
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

### <a name="invoked-pipeline-definition"></a>Meghívott folyamat meghatározása

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

**Forrás adatkészlet**
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

**A dataset gyűjtése**
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

### <a name="running-the-pipeline"></a>A folyamat fut

Ebben a példában a fő folyamat elindításához a következő érték lett átadva a masterSourceBlobContainer és masterSinkBlobContainer paraméterek: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

A fő folyamat továbbítja ezeket az értékeket a meghívott folyamat, a következő példában látható módon: 

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
        "sinkBlobCountainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>További lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
