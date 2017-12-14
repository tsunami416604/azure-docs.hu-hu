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
ms.date: 12/12/2017
ms.author: spelluru
ms.openlocfilehash: e0a1613f2f820f0c108e97c2c15585a581041181
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Az Azure Data Factory keresési tevékenység
A Keresési tevékenység segítségével bármely külső forrásból kiolvashat vagy megkereshet egy rekordot, táblanevet vagy értéket. Erre a kimenetre a későbbi tevékenységek is hivatkozhatnak. 

Keresési tevékenység akkor hasznos, ha a keresett dinamikusan azon fájlok listáját vagy rekordok, illetve táblákat a konfigurációs fájlban vagy egy adatforrásról. A tevékenység kimenetét további segítségével más tevékenységek hajtsa végre az adott feldolgozási csak azokat az elemeket.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [Data Factory V1 dokumentáció](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességei

A következő adatforrások jelenleg támogatott keresési:
- Az Azure Blob JSON-fájl
- JSON-fájl a fájlrendszerben
- Az Azure SQL Database (JSON-adatok lekérdezés konvertálásához)
- Az SQL Data Warehouse (JSON-adatok lekérdezés konvertálásához)
- SQL Server (JSON-adatok lekérdezés konvertálásához)
- Az Azure Table Storage (JSON-adatok lekérdezés konvertálásához)

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>A típus tulajdonságai
Név | Leírás | Típus | Szükséges
---- | ----------- | ---- | --------
Adatkészlet | A dataset attribútum a adathalmazra mutató hivatkozást biztosít a kereséshez. A támogatott adatkészlet típusok jelenleg a következők:<ul><li>`AzureBlobDataset`a [Azure Blob Storage](connector-azure-blob-storage.md#dataset-properties) forrásaként</li><li>`FileShareDataset`a [fájlrendszer](connector-file-system.md#dataset-properties) forrásaként</li><li>`AzureSqlTableDataset`a [Azure SQL Database](connector-azure-sql-database.md#dataset-properties) vagy [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#dataset-properties) forrásaként</li><li>`SqlServerTable`a [SQL Server](connector-sql-server.md#dataset-properties) forrásaként</li><li>`AzureTableDataset`a [Azure Table Storage](connector-azure-table-storage.md#dataset-properties) forrásaként</li> | Kulcs/érték pár | Igen
forrás | Adatkészlet-specifikus tulajdonságait, ugyanaz, mint a másolási tevékenység forrását. Ismerje meg, hogy minden egyes megfelelő összekötő témakör "Másolása a tevékenység tulajdonságai" című adataiból. | Kulcs/érték pár | Igen
firstRowOnly | Azt jelzi, hogy csak az első sor vagy az összes sor visszaadása. | Logikai érték | Nem. Alapértelmezett érték a `ture`.

## <a name="use-lookup-activity-result-in-subsequent-activity"></a>Használja a keresési tevékenység eredménye a soron következő tevékenységek

A keresési eredmény eredmény abban az esetben a `output` című szakasza a tevékenységfuttatási eredménye.

**Ha `firstRowOnly` értéke `true` (alapértelmezett)**, a kimeneti formátum a következőképpen történik. A keresési eredménye alapján egy rögzített `firstRow` kulcs. Az eredmény a soron következő tevékenységek használatához a mintát `@{activity('MyLookupActivity').output.firstRow.TableName}`.

```json
{
    "firstRow":
    {
        "Id": "1",
        "TableName" : "Table1"
    }
}
```

**Ha `firstRowOnly` értéke `false`** , a kimeneti foramt a következőképpen történik. A `count` mező azt jelzi, hogy hány rekordot ad vissza, és részletes értékei alapján egy rögzített `value` tömb. Ilyen esetben a keresési tevékenység általában követi egy [Foreach tevékenység](control-flow-for-each-activity.md), továbbíthatja a `value` ForEach tevékenység tömbben `items` mezőben szerkezet használatával `@activity('MyLookupActivity').output.value`.

```json
{
    "count": "2",
    "value": [
        {
            "Id": "1",
            "TableName" : "Table1"
        },
        {
            "Id": "2",
            "TableName" : "Table2"
        }
    ]
} 
```

## <a name="example"></a>Példa
Ebben a példában a másolási tevékenység során másol adatokat az Azure SQL-adatbázis SQL-szereplő Azure Blob Storage tárolóban. Az SQL-tábla neve tárolódik a JSON-fájl, a Blob Storage tárolóban. A keresési tevékenység keresi a futási időben a tábla neve. Ez a megközelítés lehetővé teszi, hogy a JSON folyamatok/adatkészletek üzembe helyezésével dinamikusan módosítani. 

Ez a példa demostrates csak az első sor kereshet. Keresse meg a sorok és a lánc ForEach tevékenységet, tekintse meg a [oktatóanyag - adatok másolása tömeges](tutorial-bulk-copy.md) minta.

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
                    "source": {
                        "type": "BlobSource"
                    },
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
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
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
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
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

## <a name="next-steps"></a>Következő lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
