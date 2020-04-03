---
title: Törlési tevékenység az Azure Data Factoryben
description: Megtudhatja, hogyan törölhetfájlokat a különböző fájltárolókban az Azure Data Factory tevékenység törlése szolgáltatással.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f265cdc955becd53ae7ba61ad827b2be69b92907
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618267"
---
# <a name="delete-activity-in-azure-data-factory"></a>Törlési tevékenység az Azure Data Factoryben

A Tevékenység törlése az Azure Data Factory segítségével törölheti a fájlokat vagy mappákat a helyszíni tárolókból vagy a felhőbeli tárolókból. Ezzel a tevékenységgel megtisztíthatja vagy archiválhatja a fájlokat, amikor már nincs rájuk szükség.

> [!WARNING]
> A törölt fájlok és mappák nem állíthatók vissza (kivéve, ha a tároló helyreállítható törlésengedélyezve van). Körültekintően járjon el, amikor a Törlés művelettel töröl fájlokat vagy mappákat.

## <a name="best-practices"></a>Ajánlott eljárások

Íme néhány javaslat a Törlés tevékenység használatára:

-   Biztonsági másolatot a fájlokról, mielőtt törölné őket a Törlés tevékenységgel arra az esetre, ha a jövőben vissza szeretné állítani őket.

-   Győződjön meg arról, hogy a Data Factory rendelkezik írási engedéllyel a mappák vagy fájlok törléséhez a tárolóból.

-   Győződjön meg arról, hogy nem egyszerre írott fájlokat. 

-   Ha fájlokat vagy mappákat szeretne törölni egy helyszíni rendszerből, győződjön meg arról, hogy 3.14-nél nagyobb verzióval rendelkező saját üzemeltetésű integrációs futásidőt használ.

## <a name="supported-data-stores"></a>Támogatott adattárak

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [1. generációs Azure Data Lake Storage](connector-azure-data-lake-store.md)
-   [2. generációs Azure Data Lake Storage](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)

### <a name="file-system-data-stores"></a>Fájlrendszer-adattárak

-   [Fájlrendszer](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)

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

## <a name="type-properties"></a>Típus tulajdonságai

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| Adatkészlet | Az adatkészlethivatkozás sal határozza meg, hogy mely fájlokat vagy mappákat kell törölni | Igen |
| Rekurzív | Azt jelzi, hogy a fájlok rekurzív módon törlődnek-e az almappákból, vagy csak a megadott mappából.  | Nem. A mező alapértelmezett értéke: `false`. |
| maxConcurrentConnections | A tárolóhoz a mappa vagy fájlok törléséhez egyidejűleg csatlakozó kapcsolatok száma.   |  Nem. A mező alapértelmezett értéke: `1`. |
| engedélyezés | Azt jelzi, hogy rögzítenie kell-e a törölt mappa- vagy fájlneveket. Ha igaz, a naplófájl mentéséhez további tárfiókot kell biztosítania, hogy a naplófájl olvasásával nyomon követhesse a Törlés tevékenység viselkedését. | Nem |
| logStorageSettings | Csak akkor alkalmazható, ha enablelogging = igaz.<br/><br/>Tárolótulajdonságok csoportja, amely megadható, hogy hová szeretné menteni a törlési tevékenység által törölt mappát vagy fájlneveket tartalmazó naplófájlt. | Nem |
| linkedServiceName | Csak akkor alkalmazható, ha enablelogging = igaz.<br/><br/>Az Azure [Storage,](connector-azure-blob-storage.md#linked-service-properties) [az Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties)vagy az [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) kapcsolt szolgáltatása a törlési tevékenység által törölt mappát vagy fájlneveket tartalmazó naplófájl tárolására. Ne feledje, hogy a fájlok törléséhez a törlési tevékenység által használt hoz a törlési futtatási idő vel azonos típusú konfigurálású. | Nem |
| path | Csak akkor alkalmazható, ha enablelogging = igaz.<br/><br/>A naplófájl tárfiókba mentésének elérési útja. Ha nem ad meg elérési utat, a szolgáltatás létrehoz egy tárolót. | Nem |

## <a name="monitoring"></a>Figyelés

Két helyen láthatja és figyelheti a Törlés tevékenység eredményeit: 
-   A Törlés tevékenység kimenetéből.
-   A naplófájlból.

### <a name="sample-output-of-the-delete-activity"></a>A Törlés tevékenység mintakimenete

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

### <a name="sample-log-file-of-the-delete-activity"></a>A Törlés tevékenység naplófájlja

| Név | Kategória | status | Hiba |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Fájl | Törölve |  |
| test2/hello789.txt | Fájl | Törölve |  |
| test2/test3/hello000.txt | Fájl | Törölve |  |
| test2/test3/zzz.json | Fájl | Törölve |  |

## <a name="examples-of-using-the-delete-activity"></a>Példák a Törlés tevékenység használatára

### <a name="delete-specific-folders-or-files"></a>Adott mappák vagy fájlok törlése

Az üzlet mappastruktúrája a következő:

Gyökér/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Most a Törlés tevékenységgel törölheti a mappát vagy a fájlokat az adatkészlet és a Törlés tevékenység különböző tulajdonságértékének kombinációja alapján:

| folderPath (adatkészletből) | fájlnév (adatkészletből) | rekurzív (a Törlés tevékenységből) | Kimenet |
|:--- |:--- |:--- |:--- |
| Gyökér/ Folder_A_2 | NULL | False (Hamis) | Gyökér/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Gyökér/ Folder_A_2 | NULL | True (Igaz) | Gyökér/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Gyökér/ Folder_A_2 | *.txt | False (Hamis) | Gyökér/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Gyökér/ Folder_A_2 | *.txt | True (Igaz) | Gyökér/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Az időpartidott mappa vagy fájlok rendszeres karbantartása

Létrehozhat egy folyamatot a particionált mappa vagy fájlok rendszeres törléséhez.  A mappastruktúra például hasonló `/mycontainer/2018/12/14/*.csv`a következőkhöz: .  Az Ütemezési eseményindító ADF-rendszerváltozójával azonosíthatja, hogy melyik mappát vagy fájlokat kell törölni az egyes folyamatok futtatásakor. 

#### <a name="sample-pipeline"></a>Mintafolyamat

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

#### <a name="sample-dataset"></a>Mintaadatkészlet

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

#### <a name="sample-trigger"></a>Minta eseményindító

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

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>A 2018.1 előtt utoljára módosított lejárt fájlok karbantartása

Létrehozhat egy folyamatot a régi vagy lejárt fájlok karbantartásához a fájlattribútum-szűrő: "LastModified" beállítással az adatkészletben.  

#### <a name="sample-pipeline"></a>Mintafolyamat

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

#### <a name="sample-dataset"></a>Mintaadatkészlet

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

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Fájlok áthelyezése a Másolás tevékenység és a Törlés tevékenység láncolásával

A fájlokat úgy helyezheti át, hogy másolási tevékenységgel másol egy fájlt, majd egy törlési tevékenységgel törli a folyamatban lévő fájlt.  Ha több fájlt szeretne áthelyezni, használhatja a GetMetadata tevékenység + Szűrés tevékenység + Foreach tevékenység + Tevékenység másolása + Tevékenység törlése a következő példában látható módon:

> [!NOTE]
> Ha a teljes mappát úgy szeretné áthelyezni, hogy csak egy mappaelérési utat tartalmazó adatkészletet határoz meg, majd másolási tevékenységet és egy Törlés tevékenységet használ egy mappát képviselő adatkészletre való hivatkozáshoz, nagyon óvatosnak kell lennie. Ez azért van, mert meg kell győződnie arról, hogy nem lesz új fájlok érkeznek a mappába a másolási művelet és a törlési művelet között.  Ha vannak új fájlok érkeznek a mappába abban a pillanatban, amikor a másolási tevékenység csak befejezte a másolási feladatot, de a Törlés tevékenység nem nézett, lehetséges, hogy a Törlés tevékenység törli ezt az új érkező fájlt, amely még nem másolt a célba a teljes mappa törlésével. 

#### <a name="sample-pipeline"></a>Mintafolyamat

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

A GetMetadata tevékenység által a fájllista számbavételére használt adatkészlet.

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

A másolási tevékenység és a Törlés tevékenység által használt adatforrás adatkészlete.

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

A másolási tevékenység által használt adatcél adatkészlete.

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

Azt is kap a sablont, hogy helyezze át a fájlokat [innen](solution-template-move-files.md).

## <a name="known-limitation"></a>Ismert korlátozás

-   A törlési tevékenység nem támogatja a helyettesítő karakter által leírt mappák listájának törlését.

-   Fájlattribútum-szűrő használatakor: modifiedDatetimeStart és modifiedDatetimeEnd a törölni kívánt fájlok kiválasztásához győződjön meg arról, hogy a "fileName": "*" szót állítja be az adatkészletben.

## <a name="next-steps"></a>További lépések

További információ a fájlok azure Data Factoryban való áthelyezéséről.

-   [Adatok másolása eszköz az Azure Data Factoryben](copy-data-tool.md)
