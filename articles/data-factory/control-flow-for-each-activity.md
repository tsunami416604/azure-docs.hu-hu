---
title: ForEach tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: A tartozó minden tevékenység ismétlődő átvitelvezérlést határoz meg a folyamatban. Ez egy gyűjtemény elemein léptetés használja, és hajtsa végre a megadott.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: shlo
ms.openlocfilehash: 90c36e728a8ec91606f93c080258eeca9c3825e6
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020778"
---
# <a name="foreach-activity-in-azure-data-factory"></a>ForEach tevékenység az Azure Data Factoryban
A ForEach tevékenység ismétlődő átvitelvezérlést határoz meg a folyamatban. Ez a tevékenység egy gyűjtemény megismétlésére, valamint egy megadott ciklustevékenység végrehajtására szolgál. E tevékenység ciklusos megvalósítása hasonló a Foreach ciklusos szerkezetéhez a programozási nyelvek esetében.

## <a name="syntax"></a>Szintaxis
A cikk későbbi részében tulajdonságait ismerteti. Az elemek tulajdonság a gyűjteményt, és a gyűjtemény minden elemén használatával hivatkozik a `@item()` , ahogyan a következő szintaxist:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Tulajdonságok

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
név | A for-each tevékenység neve. | Karakterlánc | Igen
type | Meg kell **ForEach** | Karakterlánc | Igen
isSequential | Itt adhatja meg, akár a hurok egymás után vagy a párhuzamos végrehajtja.  Legfeljebb 20 ciklus ismétléseinek is végrehajtható egyszerre párhuzamosan). Például, ha rendelkezik egy ForEach tevékenység léptetés keresztül egy másolási tevékenységgel ellátott 10 különböző forrás és fogadó adatkészleteit a **isSequential** értéke HAMIS, minden példány tevékenységében egyszerre. Alapértelmezett érték a False. <br/><br/> Ha "isSequential" hamis értékre van állítva, győződjön meg arról, hogy nincs-e a megfelelő konfigurációs több végrehajtható fájlok futtatását. Ellenkező esetben ez a tulajdonság használandó körültekintően írási ütközések elkerüléséhez. További információkért lásd: [párhuzamos végrehajtása](#parallel-execution) szakaszban. | Logikai | Nem. Alapértelmezett érték a False.
batchCount | Kötegek száma való párhuzamos végrehajtás száma (ha isSequential hamis értékre van állítva) használható. | Egész szám (legfeljebb 50) | Nem. Alapértelmezett érték 20.
Elemek | Egy kifejezés, amely megismételhető, egy JSON-tömböt ad vissza. | Kifejezés (amely egy JSON-tömböt ad vissza) | Igen
Tevékenységek | A tevékenységek végrehajtását. | Tevékenységek listája | Igen

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha **isSequential** hamis értékre van állítva, a tevékenység, amely legfeljebb 20 egyidejű ismétlések párhuzamosan ismétlődik. Ezzel a beállítással kell legyen óvatos. Ha az egyidejű ismétlésének ugyanabban a mappában, de különböző fájlok írása, ez a megközelítés is megfelel. Egyidejű ismétlésének egyidejűleg írás pontosan ugyanazt a fájlt, ha ez a megközelítés valószínűleg hibát okoz. 

## <a name="iteration-expression-language"></a>Iteráció kifejezés nyelve
A ForEach tevékenység adja meg a tulajdonság végrehajtódik a tömb **elemek**. " Használat `@item()` egyetlen enumerálás ciklustevékenység a ForEach tevékenység. Például ha **elemek** tömb: [1, 2, 3], `@item()` 1 az első példányát, a második ismétléskor 2 és 3, a harmadik ismétléskor adja vissza.

## <a name="iterating-over-a-single-activity"></a>Egyetlen tevékenységgel keresztül léptetés
**Forgatókönyv:** Ugyanazt a fájlt az Azure Blob másolása az Azure-Blobban található több cél fájlt.

### <a name="pipeline-definition"></a>Folyamat meghatározása

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>BLOB-adatkészletek definíciójában

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Futtassa a paraméterértékek

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Több tevékenységtől ciklustevékenység
Több tevékenység ciklustevékenység lehetséges (például: másolás és a webes tevékenység) egy ForEach tevékenységbe. Ebben a forgatókönyvben azt javasoljuk, hogy ki több tevékenység egy külön folyamatba absztrakt. Ezután használhatja a [ExecutePipeline tevékenység](control-flow-execute-pipeline-activity.md) a ForEach tevékenység meghívni a különálló folyamat több tevékenységet a folyamatban. 


### <a name="syntax"></a>Szintaxis

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```
### <a name="example"></a>Példa
**Forgatókönyv:** Egy ForEach tevékenység a folyamat végrehajtása tevékenység belül egy InnerPipeline ciklustevékenység. A paraméteres sémadefiníciók belső másol.

#### <a name="master-pipeline-definition"></a>Minta folyamatdefiníció

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Belső folyamatdefiníció

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Forrás adatkészlet definíciója

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Fogadó adatkészlet definíciója

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Fő folyamat paraméterei
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}

```
## <a name="aggregating-metric-output"></a>Metrika kimeneti összesítése
Egy ForEach összes ismétlésének kimenetének összegyűjtése kifejezése van `@activity('NameofInnerActivity')`. Például ha egy ForEach-tevékenység egy "MyCopyActivity" többször is végrehajtódik a szintaxis lenne: `@activity('MyCopyActivity')`. A kimenet egy tömb, az egyes elemek részleteit egy adott iteráció jogosultságot ad a.

> [!NOTE]
> Ha azt szeretné, hogy egy adott iteráció részleteit, a szintaxis lenne: `@activity('NameofInnerActivity')[0]` számára a legújabb verzió továbbfejlesztésében. Az adott iteráció a tömbben, eléréséhez használja a zárójelek közé a számot. Egy adott tulajdonságra egy adott ismétlés eléréséhez használja: `@activity('NameofInnerActivity')[0].output` vagy `@activity('NameofInnerActivity')[0].pipelineName`.

**Kimenet részletei tömb minden ismétlését:**
```json
[    
    {      
        "pipelineName": "db1f7d2b-dbbd-4ea8-964e-0d9b2d3fe676",      
        "jobId": "a43766cb-ba13-4c68-923a-8349af9a76a3",      
        "activityRunId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",      
        "linkedServiceName": "ADFService",      
        "status": "Succeeded",      
        "statusCode": null,      
        "output": 
            {        
                "progress": 100,        
                "loguri": null,        
                "dataRead": "6.00 Bytes",        
                "dataWritten": "6.00 Bytes",        
                "regionOrGateway": "West US",        
                "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",        
                "copyDuration": "00:00:05",        
                "dataVolume": "6.00 Bytes",        
                "throughput": "1.16 Bytes/s",       
                 "totalDuration": "00:00:10"      
            },      
        "resumptionToken": 
            {       
                "ExecutionId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",        
                "ResumptionToken": 
                    {          
                        "in progress": "217526fa-0218-42f1-b85c-e0b4f7b170ce/wu/cloud/"       
                    },        
                "ExtendedProperties": 
                    {          
                        "dataRead": "6.00 Bytes",          
                        "dataWritten": "6.00 Bytes",          
                        "regionOrGateway": "West US",          
                        "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",          
                        "copyDuration": "00:00:05",          
                        "dataVolume": "6.00 Bytes",          
                        "throughput": "1.16 Bytes/s",          
                        "totalDuration": "00:00:10"        
                    }      
            },      
        "error": null,      
        "executionStartTime": "2017-08-01T04:17:27.5747275Z",      
        "executionEndTime": "2017-08-01T04:17:46.4224091Z",     
        "duration": "00:00:18.8476816"    
    },
    {      
        "pipelineName": "db1f7d2b-dbbd-4ea8-964e-0d9b2d3fe676",      
        "jobId": "54232-ba13-4c68-923a-8349af9a76a3",      
        "activityRunId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",      
        "linkedServiceName": "ADFService",      
        "status": "Succeeded",      
        "statusCode": null,      
        "output": 
            {        
                "progress": 100,        
                "loguri": null,        
                "dataRead": "6.00 Bytes",        
                "dataWritten": "6.00 Bytes",        
                "regionOrGateway": "West US",        
                "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",        
                "copyDuration": "00:00:05",        
                "dataVolume": "6.00 Bytes",        
                "throughput": "1.16 Bytes/s",       
                 "totalDuration": "00:00:10"      
            },      
        "resumptionToken": 
            {       
                "ExecutionId": "217526fa-0218-42f1-b85c-e0b4f7b170ce",        
                "ResumptionToken": 
                    {          
                        "in progress": "217526fa-0218-42f1-b85c-e0b4f7b170ce/wu/cloud/"       
                    },        
                "ExtendedProperties": 
                    {          
                        "dataRead": "6.00 Bytes",          
                        "dataWritten": "6.00 Bytes",          
                        "regionOrGateway": "West US",          
                        "details": "Data Read: 6.00 Bytes, Written: 6.00 Bytes",          
                        "copyDuration": "00:00:05",          
                        "dataVolume": "6.00 Bytes",          
                        "throughput": "1.16 Bytes/s",          
                        "totalDuration": "00:00:10"        
                    }      
            },      
        "error": null,      
        "executionStartTime": "2017-08-01T04:18:27.5747275Z",      
        "executionEndTime": "2017-08-01T04:18:46.4224091Z",     
        "duration": "00:00:18.8476816"    
    }
]

```

## <a name="limitations-and-workarounds"></a>Korlátozások és a lehetséges megoldások

Az alábbiakban néhány korlátozást a ForEach tevékenység és a javasolt megoldások.

| Korlátozás | Áthidaló megoldás |
|---|---|
| A ForEach ciklus belül egy másik ForEach ciklus (vagy egy Until ciklus) nem ágyazhatók egymásba. | Tervezhet, ahol a külső folyamat és a külső ForEach ciklus ismétel-e a beágyazott hurok-egy belső folyamat két szinten folyamat. |
| A ForEach tevékenység rendelkezik maximális `batchCount` 50 párhuzamos feldolgozásra, és a egy legfeljebb 100 000 elemet. | Ha a külső folyamat ForEach tevékenysége a ismétel-e egy belső folyamat két szinten folyamat tervezése. |
| | |

## <a name="next-steps"></a>További lépések
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
