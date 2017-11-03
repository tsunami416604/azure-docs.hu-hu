---
title: "Az Azure Data Factory keresési tevékenység |} Microsoft Docs"
description: "Megtudhatja, hogyan használja a keresési tevékenység külső forrásból kereshet meg egy értéket. Erre a kimenetre a későbbi tevékenységek is hivatkozhatnak."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: 30173f8eea2ccbbcd44018596cf34b3769a64b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Az Azure Data Factory keresési tevékenység
A Keresési tevékenység segítségével bármely külső forrásból kiolvashat vagy megkereshet egy rekordot, táblanevet vagy értéket. Erre a kimenetre a későbbi tevékenységek is hivatkozhatnak. 

A következő adatforrások jelenleg támogatott keresési:
- Az Azure Blob JSON-fájl
- A helyszíni JSON-fájl
- Az Azure SQL Database (JSON-adatok lekérdezés konvertálásához)
- Az Azure Table Storage (JSON-adatok lekérdezés konvertálásához)

Keresési tevékenység akkor hasznos, ha a keresett dinamikusan azon fájlok listáját vagy rekordok, illetve táblákat a konfigurációs fájlban vagy egy adatforrásról. A tevékenység kimenetét további segítségével más tevékenységek hajtsa végre az adott feldolgozási csak azokat az elemeket.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Data Factory V1 dokumentáció](v1/data-factory-introduction.md).


## <a name="example"></a>Példa
Ebben a példában a másolási tevékenység során másol adatokat az Azure SQL-adatbázis SQL-szereplő Azure Blob Storage tárolóban. Az SQL-tábla neve tárolódik a JSON-fájl, a Blob Storage tárolóban. A keresési tevékenység keresi a futási időben a tábla neve. Ez a megközelítés lehetővé teszi, hogy a JSON folyamatok/adatkészletek üzembe helyezésével dinamikusan módosítani. 

### <a name="pipeline"></a>Folyamat
Ez az adatcsatorna két tevékenységet tartalmaz: **kereshető** és **másolási**. 

- A keresési tevékenység a LookupDataset, amely egy Azure Blob Storage a hely használatára van konfigurálva. A keresési tevékenység ezen a helyen JSON fájlból olvassa be az SQL-tábla neve. 
- A másolási tevékenység során használja a keresési tevékenység (az SQL-tábla neve). A forrás adatkészletben (SourceDataset) tableName a keresési tevékenység kimenete használatára van konfigurálva. A másolási tevékenység során másol adatokat az SQL-tábla az Azure Blob Storage a SinkDataset által meghatározott helyre. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Keresési adatkészlet
A keresési dataset az Azure Storage AzureStorageLinkedService által meghatározott, a keresési mappában sourcetable.json fájlra vonatkozik. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>A másolási tevékenység során a forrás adatkészlet
A forrás adatkészlet használja a keresési tevékenység, amely az SQL-tábla neve. A másolási tevékenység során másol adatokat az SQL-tábla az az Azure Blob Storage a fogadó dataset által meghatározott helyre. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>A másolási tevékenységhez dataset gyűjtése
A másolási tevékenység során másol adatokat az SQL-tábla filebylookup.csv fájl az Azure Storage a AzureStorageLinkedService által meghatározott, a fürt megosztott kötetei szolgáltatás mappában. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage társított szolgáltatás
Ez a tárfiók a JSON-fájl SQL-táblák nevét tartalmazza. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database társított szolgáltatás
Az Azure SQL-adatbázis tartalmazza az adatokat a blob Storage tárolóban kell másolni. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>SourceTable.JSON

#### <a name="set-of-objects"></a>Objektumok csoportja

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```
#### <a name="array-of-objects"></a>Objektumokból álló tömb

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```



## <a name="type-properties"></a>A típus tulajdonságai
Név | Leírás | Típus | Szükséges
---- | ----------- | ---- | --------
Adatkészlet | A dataset attribútum a adathalmazra mutató hivatkozást biztosít a kereséshez. A támogatott adatkészlet típusok jelenleg a következők:<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | kulcs/érték pár | Igen
Forrás | Adatkészlet-specifikus tulajdonságait, ugyanaz, mint a másolási tevékenység forrása | Kulcs/érték pár | Nem
firstRowOnly | Első sor vagy az összes sort adja vissza. | Logikai érték | Nem

## <a name="next-steps"></a>Következő lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
