---
title: ForEach tevékenység az Azure Data Factoryban
description: Az egyes tevékenységekhez egy ismétlődő vezérlési folyamatot határoz meg a folyamatban. Gyűjtemény en keresztüli iterációra és meghatározott tevékenységek végrehajtására szolgál.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 35d61e896a395c3044a51780fef72d54c211a31f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417184"
---
# <a name="foreach-activity-in-azure-data-factory"></a>ForEach tevékenység az Azure Data Factoryban
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A ForEach tevékenység ismétlődő vezérlési folyamatot határoz meg a folyamatban. Ez a tevékenység egy gyűjtemény megismétlésére, valamint egy megadott ciklustevékenység végrehajtására szolgál. E tevékenység ciklusos megvalósítása hasonló a Foreach ciklusos szerkezetéhez a programozási nyelvek esetében.

## <a name="syntax"></a>Szintaxis
A tulajdonságokleírása a cikk későbbi részében található. A items tulajdonság a gyűjtemény, és a gyűjtemény minden `@item()` egyes elemére a következő szintaxisban látható módon hivatkozunk:  

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

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
név | Az egyes tevékenységek neve. | Sztring | Igen
type | ForEach **(ForEach)** beállításra kell állítva | Sztring | Igen
szekvenciális | Itt adható meg, hogy a hurkot egymás után vagy párhuzamosan kell-e végrehajtani.  Legfeljebb 20 ciklus ismétlések lehet végrehajtani egyszerre párhuzamosan). Ha például egy ForEach tevékenység iterálása egy másolási tevékenység 10 különböző forrás- és fogadó adatkészletek **isSequential** értéke Hamis, az összes példány végrehajtása egyszerre. Az alapértelmezett érték hamis. <br/><br/> Ha az "isSequential" értéke Hamis, győződjön meg arról, hogy megfelelő konfiguráció van több végrehajtható fájl futtatásához. Ellenkező esetben ezt a tulajdonságot óvatosan kell használni az írási ütközések elkerülése érdekében. További információ: [Párhuzamos végrehajtás](#parallel-execution) szakasz. | Logikai | Nem. Az alapértelmezett érték hamis.
kötegszám | A párhuzamos végrehajtások számának szabályozásához használt kötegszám (ha az isSequential értéke false). Ez a felső egyidejűségi korlát, de az egyes tevékenységek nem mindig hajtják végre ezt a számot | Egész szám (legfeljebb 50) | Nem. Az alapértelmezett érték 20.
Elemek | Egy olyan kifejezés, amely egy JSON-tömböt ad vissza, amely nek kell iterálni. | Kifejezés (amely JSON tömböt ad vissza) | Igen
Tevékenységek | A végrehajtandó tevékenységek. | Tevékenységek listája | Igen

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha **az isSequential** értéke hamis, a tevékenység iterálja párhuzamosan legfeljebb 20 egyidejű iterációk. Ezt a beállítást óvatosan kell használni. Ha az egyidejű ismétlések ugyanabba a mappába, de különböző fájlokba írnak, ez a megközelítés rendben van. Ha az egyidejű ismétlések egyidejűleg ugyanahhoz a fájlhoz írásban vannak, ez a megközelítés valószínűleg hibát okoz. 

## <a name="iteration-expression-language"></a>Iterációs kifejezés nyelve
A ForEach tevékenységben adjon meg egy tömböt, amelyet a **tulajdonságelemekhez**el kell adni." A `@item()` ForEach tevékenység egyetlen felsorolása esetén használható. Ha például az **elemek** egy tömb: [1, `@item()` 2, 3], akkor az első iterációban 1, a második iterációban 2, a harmadik ban pedig 3 értéket ad eredményül.

## <a name="iterating-over-a-single-activity"></a>Egyetlen tevékenység relevandálása
**Forgatókönyv:** Másolja ugyanabból a forrásfájlból az Azure Blobban több célfájlba az Azure Blobban.

### <a name="pipeline-definition"></a>Csővezeték definíciója

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

### <a name="blob-dataset-definition"></a>Blob-adatkészlet definíciója

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

### <a name="run-parameter-values"></a>Paraméterértékek futtatása

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Több tevékenységre is átitatva
Lehetőség van több tevékenység (például másolás és webes tevékenységek) itetésére egy ForEach tevékenységben. Ebben a forgatókönyvben azt javasoljuk, hogy több tevékenységet absztrakt egy külön folyamatba. Ezután használhatja az [ExecutePipeline tevékenységet](control-flow-execute-pipeline-activity.md) a folyamat foreach tevékenység meghívásához a különálló folyamat több tevékenységet. 


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
**Forgatókönyv:** A ForEach tevékenységen belül egy InnerPipeline-on keresztül iterate. A belső folyamat a sémadefiníciók paraméterezett másolatával másolja.

#### <a name="master-pipeline-definition"></a>Fő folyamat definíciója

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

#### <a name="inner-pipeline-definition"></a>Belső csővezeték definíciója

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

#### <a name="source-dataset-definition"></a>Forrásadatkészlet-definíció

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

#### <a name="sink-dataset-definition"></a>Adatforrás-definíció jának lesújtása

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

## <a name="aggregating-outputs"></a>Kimenetek összesítése

A __foreach__ tevékenység kimeneteinek összesítéséhez használja a _Változók_ és _a Változók hozzáfűzése_ tevékenységet.

Először deklarálegy `array` _változót_ a folyamatban. Ezután hívja meg _a hozzáfűzési változó_ tevékenységet minden __egyes foreach__ cikluson belül. Ezt követően lekérheti az összesítést a tömbből.

## <a name="limitations-and-workarounds"></a>Korlátozások és kerülő megoldások

Az alábbiakban a ForEach tevékenység néhány korlátozását és a javasolt kerülő megoldásokat olvashatja.

| Korlátozás | Áthidaló megoldás |
|---|---|
| A ForEach ciklus nem ágyazható be egy másik ForEach hurokba (vagy egy Until ciklusba). | Tervezzen egy kétszintű folyamatot, ahol a külső ForEach hurkot leadó külső csővezeték egy belső csővezetéken keresztül a beágyazott ciklussal. |
| A ForEach tevékenység `batchCount` legfeljebb 50 párhuzamos feldolgozással rendelkezik, és legfeljebb 100 000 cikk. | Tervezzen egy kétszintű folyamatot, ahol a külső folyamat a ForEach tevékenységitulátegy belső csővezetéken keresztül. |
| | |

## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamattevékenységeket: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
