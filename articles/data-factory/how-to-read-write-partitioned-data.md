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
ms.topic: article
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: e3b6ccd1e7066ed86b3d6d2d85228688b06931c4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory-version-2"></a>Hogyan olvasására vagy írására particionálva adatokat az Azure Data Factory 2-es verzió
1-es verziójával Azure Data Factory Olvasás vagy írás a particionált SliceStart/SliceEnd/WindowStart/WindowEnd rendszerváltozók használatával támogatott. A 2-es verzióját ez a viselkedés a paraméter értékének csővezeték paraméter és a trigger elem ütemezett időpont/kezdete segítségével érhet el. 

## <a name="use-a-pipeline-parameter"></a>Egy folyamat paraméterrel 
1. verziójában használhatja a partitionedBy tulajdonság és SliceStart rendszer változó a következő példában látható módon: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/yearno={Year}/monthno={Month}/dayno={Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

A partitonedBy tulajdonságra vonatkozó további információkért lásd: [1-es verziójú Azure Blob összekötő](v1/data-factory-azure-blob-connector.md#dataset-properties) cikk. 

2. verziója Ez a viselkedés érdekében, hogy a következő műveleteket hajthatja végre: 

1. Adja meg a **paraméter a következő feldolgozási sorban** karakterlánc típusú. A következő példában az adatcsatorna paraméter neve nem **scheduledRunTime**. 
2. Állítsa be **folderPath** az adatcsatorna paraméter értéke az adatkészlet-definícióban. 
3. A paraméter szoftveresen kötött értéket továbbítani a feldolgozási sor futtatása előtt. Vagy adja át a trigger elem kezdési ideje vagy ütemezett időpont dinamikusan futásidőben. 

```json
"folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%d'), '/')",
      "type": "Expression"
},
```

## <a name="pass-in-value-from-a-trigger"></a>Adjon át egy értéket
A következő eseményindító definícióját, az ütemezett az eseményindító már elmúlt értékeként a **scheduledRunTime** paraméter a következő feldolgozási sorban: 

```json
{
    "name": "MyTrigger",
    "properties": {
       ...
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "scheduledRunTime": "@trigger().scheduledTime"
            }
        }
    }
}
```

## <a name="example"></a>Példa

Íme egy minta adatkészlet-definícióban (nevű paramétert használó: `date`):

```json
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "@concat(pipeline().parameters.blobContainer, '/logs/marketingcampaigneffectiveness/yearno=', formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy'), '/monthno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%M'), '/dayno=', formatDateTime(pipeline().parameters.scheduledRunTime, '%d'), '/')",
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
                    "PARTITIONEDOUTPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/partitionedgameevents/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, '%M')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.scheduledRunTime, '%d')",
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
            "scheduledRunTime": {
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
            },
            "partitionHiveScriptFile": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések
Egy adat-előállító létrehozása és a részletes útmutatást lásd: [gyors üzembe helyezés: hozzon létre egy adat-előállító](quickstart-create-data-factory-powershell.md). 