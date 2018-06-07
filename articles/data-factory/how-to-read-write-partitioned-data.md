---
title: Hogyan olvasására vagy írására particionálva adatokat az Azure Data Factory |} Microsoft Docs
description: Megtudhatja, hogyan olvasására vagy írására particionált adatokat az Azure Data Factory 2-es verzióját.
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
ms.openlocfilehash: cdf305e3607d7483186185a014883cff5458b89f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619082"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory-version-2"></a>Hogyan olvasására vagy írására particionálva adatokat az Azure Data Factory 2-es verzió
1-es verziójával Azure Data Factory Olvasás vagy írás a particionált SliceStart/SliceEnd/WindowStart/WindowEnd rendszerváltozók használatával támogatott. A 2-es verzióját ez a viselkedés a paraméter értékének csővezeték paraméter és a trigger elem ütemezett időpont/kezdete segítségével érhet el. 

## <a name="use-a-pipeline-parameter"></a>Egy folyamat paraméterrel 
1. verziójában használhatja a partitionedBy tulajdonság és SliceStart rendszer változó a következő példában látható módon: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

A partitonedBy tulajdonságra vonatkozó további információkért lásd: [1-es verziójú Azure Blob összekötő](v1/data-factory-azure-blob-connector.md#dataset-properties) cikk. 

2. verziója Ez a viselkedés érdekében, hogy a következő műveleteket hajthatja végre: 

1. Adja meg a **paraméter a következő feldolgozási sorban** karakterlánc típusú. A következő példában az adatcsatorna paraméter neve nem **windowStartTime**. 
2. Állítsa be **folderPath** hivatkozhasson rá az adatcsatorna paraméter értékének adatkészlet-definícióban. 
3. A csővezeték-igény szerinti hívásakor adja át a a tényleges érték a paraméterhez, vagy egy eseményindító ütemezett időpont/kezdete dinamikusan futásidőben adja át. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-value-from-a-trigger"></a>Adjon át egy értéket
A következő átfedésmentes ablak eseményindító definícióját, az ablak indítási időpontja az eseményindító a feldolgozási sor paraméter értékeként átadott **windowStartTime**: 

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

Íme egy minta adatkészlet-definícióban:

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

Feldolgozási sor definíciója: 

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
Egy adat-előállító létrehozása és a részletes útmutatást lásd: [gyors üzembe helyezés: hozzon létre egy adat-előállító](quickstart-create-data-factory-powershell.md). 
