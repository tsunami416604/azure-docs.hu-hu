---
title: Hogyan lehet olvasási vagy írási particionálva az Azure Data Factory |} A Microsoft Docs
description: Megtudhatja, hogyan olvasására vagy írására particionált adatok az Azure Data Factoryban.
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
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: 24464d110b00508cfb3fde4ab1a050773511e255
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091049"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Hogyan lehet olvasási vagy írási particionálva az Azure Data Factory

Az Azure Data Factory 1. verziójának, hogy sikerült olvasási vagy írási particionált adatok használatával a **SliceStart**, **SliceEnd**, **WindowStart**, és **WindowEnd** rendszerváltozók. A jelenlegi verzióban a Data Factory a folyamat paraméter és a egy eseményindító kezdési és a megadott időpont a paraméter értékével érheti el ezt a viselkedést. 

## <a name="use-a-pipeline-parameter"></a>Egy folyamat paraméterrel 

A Data Factory 1. verziójának, használhatja a **partitionedBy** tulajdonság és **SliceStart** rendszerváltozó az alábbi példában látható módon: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

További információ a **partitonedBy** tulajdonságot használja, lásd: [másolhat az Azure Blob storage-ból az Azure Data Factory használatával](v1/data-factory-azure-blob-connector.md#dataset-properties). 

Ez a viselkedés a jelenlegi verzióban adat-előállító eléréséhez: 

1. Adja meg egy *paraméter folyamat* típusú **karakterlánc**. A következő példában a folyamat paraméter neve nem **windowStartTime**. 
2. Állítsa be **folderPath** való hivatkozáshoz a folyamat paraméter értékét az adatkészlet-definícióban. 
3. Írjon be a tényleges paraméter értéke, a folyamat igény szerinti meghívása során. Is átadhat egy eseményindító kezdési és a megadott időpont dinamikusan, futásidőben. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-a-value-from-a-trigger"></a>Egy eseményindítóból ad meg értéket

Az átfedésmentes ablak eseményindító alábbi definíciójában a az ablak indítási időpontja az eseményindító átadott, a folyamat paraméter értékeként **windowStartTime**: 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>Példa

Itt látható egy minta adatkészlet definíciója:

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Folyamat definíciója: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

Bemutatja, hogyan hozhat létre adat-előállítók álló folyamatot tartalmaz részletes útmutatást lásd: [a rövid útmutató: adat-előállító létrehozása](quickstart-create-data-factory-powershell.md). 

