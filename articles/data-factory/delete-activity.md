---
title: Törlése tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: Megtudhatja, hogyan különböző fájl tárolja a fájlokat törli a törlése tevékenységgel az Azure Data Factoryban.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 00658b650cdc0b1752bb9f2f205420018c1d6edd
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881783"
---
# <a name="delete-activity-in-azure-data-factory"></a>Az Azure Data Factory tevékenység törlése

Fájlok törlése az Azure Data Factoryban a törlése tevékenység is használhatja, vagy a helyszíni tárolók mappákról tárolja, vagy cloud storage tárol. Ez a tevékenység használatával tisztítása vagy archiválja a fájlokat, ha már nincs szüksége.

> [!WARNING]
> Nem lehet visszaállítani a törölt fájlok vagy mappák. Legyen óvatos a törlése tevékenység használatakor, hogy törli a fájlokat és mappákat.

## <a name="best-practices"></a>Ajánlott eljárások

Az alábbiakban a törlése tevékenység használatára vonatkozó ajánlásokat:

-   A fájlok biztonsági mentése előtt a törlése tevékenységgel törölni őket, abban az esetben vissza kell állítania a jövőben őket.

-   Győződjön meg arról, hogy a Data Factory rendelkezik írási engedéllyel a mappákat vagy fájlokat törli a storage-tárolóból.

-   Ellenőrizze, hogy nem törlünk egyszerre kiírt fájlokat. 

-   Ha azt szeretné, törli a fájlokat vagy mappát egy helyszíni rendszerből, győződjön meg arról, egy saját üzemeltetésű integrációs modult egy nagyobb, mint 3,14 verzióval használja.

## <a name="supported-data-stores"></a>Támogatott adattárak

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md)
-   [2. generációs Azure Data Lake Storage](connector-azure-data-lake-storage.md)

### <a name="file-system-data-stores"></a>A fájl rendszer adattárak

-   [Fájlrendszer](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Tulajdonságok

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| Adatkészlet | Itt az adatkészlet-hivatkozásban, határozza meg, hogy mely fájlok vagy törlődnek mappa | Igen |
| a rekurzív | Azt jelzi, hogy a fájlokat a törölt rekurzív módon az almappák vagy csak a megadott mappába.  | Nem. A mező alapértelmezett értéke: `false`. |
| maxConcurrentConnections | A storage áruház egyidejűleg mappára vagy fájlokra törlése csatlakozni a kapcsolatok száma.   |  Nem. A mező alapértelmezett értéke: `1`. |
| EnableLogging | Azt jelzi, hogy kell-e rögzíteni a fájl vagy mappa nevét, amely törölve lett. Igaz értéke esetén szüksége további adjon meg egy tárfiókot, a naplófájl mentéséhez, hogy a naplófájl olvassa el a viselkedéseket a törlése tevékenység követheti nyomon. | Nem |
| logStorageSettings | Csak érvényes akkor, ha enablelogging = true.<br/><br/>A megadott tárolótulajdonságok, amely egy csoportját, mentse a fájl vagy mappa törlése tevékenység által törölt nevét tartalmazó naplófájlt szeretné. | Nem |
| linkedServiceName | Csak érvényes akkor, ha enablelogging = true.<br/><br/>A társított szolgáltatás [Azure Storage](connector-azure-blob-storage.md#linked-service-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties), vagy [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) a napló tárolásához, amely tartalmazza a mappa vagy fájl neve, amely a törlési tevékenységet törölték. | Nem |
| elérési út | Csak érvényes akkor, ha enablelogging = true.<br/><br/>Mentse a naplófájlt a tárfiókja elérési útja. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót az Ön számára. | Nem |

## <a name="monitoring"></a>Figyelés

Nincsenek két helyen, ahol megtekintheti és az eredmények a törlése tevékenység figyelése: 
-   A törlési tevékenységet a kimenetből.
-   Az a naplófájlban.

### <a name="sample-output-of-the-delete-activity"></a>Minta kimenet törlése tevékenység

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Minta log fájl törlése tevékenység

| Name (Név) | Kategória | status | Hiba |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Fájl | Törölve |  |
| test2/hello789.txt | Fájl | Törölve |  |
| test2/test3/hello000.txt | Fájl | Törölve |  |
| test2/test3/zzz.json | Fájl | Törölve |  |

## <a name="examples-of-using-the-delete-activity"></a>Példák a törlése tevékenységgel

### <a name="delete-specific-folders-or-files"></a>Meghatározott mappák vagy fájlok törlése

A tároló a következő mappastruktúra rendelkezik:

Legfelső szintű /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Most már használja a Delete mappára vagy fájlokra törli az adatkészlet különböző tulajdonság értékét kombinációja szerint és a törlése tevékenység:

| folderPath (adatkészletből) | Fájlnév (adatkészletből) | a rekurzív (az a törlése tevékenység) | Kimenet |
|:--- |:--- |:--- |:--- |
| Root/ Folder_A_2 | NULL | False (Hamis) | Legfelső szintű /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root/ Folder_A_2 | NULL | True (Igaz) | Legfelső szintű /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Root/ Folder_A_2 | *.txt | False (Hamis) | Legfelső szintű /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root/ Folder_A_2 | *.txt | True (Igaz) | Legfelső szintű /<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Rendszeres időközönként a idő particionált mappára vagy fájlokra tisztítása

A folyamat rendszeres időközönként karbantartása az idő a particionált mappára vagy fájlokra is létrehozhat.  Például hasonló,-e a gyökérmappa-szerkezetében: `/mycontainer/2018/12/14/*.csv`.  Az ütemezési eseményindító azonosítására, mely mappára vagy fájlokra törölni kell minden egyes folyamatfuttatás az ADF rendszerváltozó használhatja. 

#### <a name="sample-pipeline"></a>Minta feldolgozási sor

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Minta adatkészlet

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Minta-eseményindító

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Utoljára módosítva 2018.1.1 előtt lejárt fájlok törlése

Létrehozhat egy folyamatot, fájlszűrő attribútum kihasználva a régi vagy lejárt fájlok karbantartása: "LastModified" adatkészletben.  

#### <a name="sample-pipeline"></a>Minta feldolgozási sor

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Minta adatkészlet

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "*",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Helyezze át a fájlokat a láncolás a másolási tevékenység és a törlése tevékenység

Fájl másolása másolási tevékenységgel és egy törlési tevékenységet a folyamat fájlok törléséhez áthelyezheti egy fájlt.  Ha több fájlt helyezni szeretné, használja a GetMetadata tevékenység + a tevékenység: Filter + a Foreach tevékenység + a másolási tevékenység, + törlése tevékenység a következő példa:

> [!NOTE]
> Ha áthelyezi a mappát egy adatkészletet, amely tartalmazza a mappa elérési útja csak, és ezután egy másolási tevékenységgel és egy hivatkozást az ugyanahhoz az adatkészlethez egy mappába, amely a törlése tevékenység óvatosan kell. Győződjön meg arról, hogy nem lesz a mappába művelet másolása és törlése művelet között érkező új fájlokat, mert van.  Ha a mappa abban a pillanatban, amikor a másolási tevékenység csak a másolási feladat befejeződött, de a törlési tevékenységet nem lett stared érkező új fájlokat, is lehet, hogy a törlési tevékenységet törli az új beérkező fájlt, amely a destinati sem lett átmásolva még törlésével a teljes mappát. 

#### <a name="sample-pipeline"></a>Minta feldolgozási sor

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Mintaadatkészletek

A fájlok listájának számbavétele GetMetadata tevékenység által használt adatkészlet.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

A másolási tevékenység és a törlése tevékenység által használt adatforrás adatkészlet.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Másolási tevékenység által használt adatok cél adatkészletet.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```
## <a name="known-limitation"></a>Ismert korlátozás

-   Törlése tevékenység nem támogatja a helyettesítő karakteres által leírt mappák törlése listáját.

-   Fájlszűrő attribútum használata esetén: modifiedDatetimeStart és modifiedDatetimeEnd válassza ki a fájlokat törli azt, hogy feltétlenül állítson be a "fájlnevet": "*" adatkészletben.

## <a name="next-steps"></a>További lépések

További információ a fájlok áthelyezése az Azure Data Factoryban.

-   [Adatok másolása eszköz az Azure Data Factoryban](copy-data-tool.md)