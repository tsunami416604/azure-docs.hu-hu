---
title: "Az Azure Data Factory keresési tevékenység |} Microsoft Docs"
description: "Megtudhatja, hogyan kereshet meg egy értéket külső forrásból keresési tevékenység használata. Erre a kimenetre a későbbi tevékenységek is hivatkozhatnak."
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
ms.openlocfilehash: 3c4f401682e5d1789c6e15597ced145a230bbcd6
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Az Azure Data Factory keresési tevékenység
Keresési tevékenység olvasására vagy külső forrásból kereshet egy rekord, a tábla neve vagy az érték használható. Erre a kimenetre a későbbi tevékenységek is hivatkozhatnak. 

Keresési tevékenység akkor hasznos, ha azt szeretné, dinamikusan egy konfigurációs fájlban vagy egy adatforrást a fájlok, a rekordok és a táblázatok listájának lekérése. A tevékenység kimenetét további segítségével más tevékenységek hajtsa végre az adott feldolgozási csak azokat az elemeket.

> [!NOTE]
> Ez a cikk az Azure Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Támogatott képességei

A következő adatforrások jelenleg támogatott keresési:
- Az Azure Blob storage JSON-fájl
- JSON-fájl a fájlrendszerben
- Az Azure SQL Database (JSON-adatok lekérdezés konvertálásához)
- Az SQL Data Warehouse (JSON-adatok lekérdezés konvertálásához)
- SQL Server (JSON-adatok lekérdezés konvertálásához)
- Az Azure Table storage (JSON-adatok lekérdezés konvertálásához)

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
Name (Név) | Leírás | Típus | Kötelező?
---- | ----------- | ---- | --------
Adatkészlet | Az adatkészlet-hivatkozásban biztosít a kereséshez. A támogatott adatkészlet típusok jelenleg a következők:<ul><li>`AzureBlobDataset`a [Azure Blob Storage tárolóban](connector-azure-blob-storage.md#dataset-properties) forrásaként</li><li>`FileShareDataset`a [fájlrendszer](connector-file-system.md#dataset-properties) forrásaként</li><li>`AzureSqlTableDataset`a [Azure SQL Database](connector-azure-sql-database.md#dataset-properties) vagy [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#dataset-properties) forrásaként</li><li>`SqlServerTable`a [SQL Server](connector-sql-server.md#dataset-properties) forrásaként</li><li>`AzureTableDataset`a [Azure Table storage](connector-azure-table-storage.md#dataset-properties) forrásaként</li> | Kulcs/érték pár | Igen
forrás | Adatkészlet-specifikus forrás tulajdonságait, ugyanaz, mint a másolási tevékenység forrását tartalmazza. Beolvasása – részletek minden egyes megfelelő összekötő cikkben "Másolása a tevékenység tulajdonságai" szakaszában. | Kulcs/érték pár | Igen
firstRowOnly | Azt jelzi, hogy csak az első sor vagy az összes sor visszaadása. | Logikai | Nem. Alapértelmezett érték a `true`.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Használja a keresési tevékenység eredménye a következő tevékenységben

A keresési eredmény eredmény abban az esetben a `output` a tevékenységfuttatási eredmény szakasza.

* **Ha `firstRowOnly` értéke `true` (alapértelmezett)**, a kimeneti formátuma a következő kódban látható módon. A keresési eredménye alapján egy rögzített `firstRow` kulcs. Az eredmény a soron következő tevékenységek használatához a mintát `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Ha `firstRowOnly` értéke `false`** , a kimeneti formátuma a következő kódban látható módon. A `count` mező azt jelzi, hogy hány rekordot ad vissza, és részletes jelennek meg a rögzített `value` tömb. Ebben az esetben a keresési tevékenység általában követi a [Foreach tevékenység](control-flow-for-each-activity.md). Átadhatók a `value` tömb a ForEach tevékenység `items` mező szerkezet használatával `@activity('MyLookupActivity').output.value`. Hozzáférés elemeknek a `value` tömb, a következő szintaxissal: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Példa: `@{activity('lookupActivity').output.value[0].tablename}`.

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
Ebben a példában a másolási tevékenység során másol adatokat az Azure SQL Database-példányt az SQL-szereplő Azure Blob Storage tárolóban. Az SQL-tábla neve tárolódik a JSON-fájl, a Blob Storage tárolóban. A keresési tevékenység keresi a futási időben a tábla neve. Ez a megközelítés lehetővé teszi, hogy a JSON nem kell újratelepíteni adatcsatornákat és adathalmazokat dinamikusan módosítani. 

Ebben a példában csak az első sor keresési mutatja be. Az összes sort, és az eredmények láncolt ForEach tevékenység a keresési, tekintse meg a minták [több tábla másolja a tömeges Azure Data Factory használatával](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Folyamat
Ez az adatcsatorna két tevékenységet tartalmaz: *keresési* és *másolási*. 

- A keresési tevékenység LookupDataset, amely az Azure Blob storage helyéről használatára van konfigurálva. A keresési tevékenység ezen a helyen JSON fájlból olvassa be az SQL-tábla neve. 
- A másolási tevékenység során használja a keresési tevékenység (az SQL-tábla neve). A forrás adatkészletben (SourceDataset) a tableName tulajdonságot a keresési tevékenység kimenete használatára van konfigurálva. A másolási tevékenység során az SQL-táblából adatokat az Azure Blob storage SinkDataset tulajdonság által megadott helyre másolja. 


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
A keresési adatkészlet hivatkozik a *sourcetable.json* AzureStorageLinkedService típus által megadott Azure Storage keresési mappában. 

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
A forrás adatkészlet használja a keresési tevékenység, amely az SQL-tábla neve. A másolási tevékenység során másol adatokat az SQL-tábla az Azure Blob storage a fogadó dataset által meghatározott helyre. 

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
A másolási tevékenység során másol az SQL táblázat segítségével adatokat a *filebylookup.csv* fájlt a *csv* az Azure Storage AzureStorageLinkedService tulajdonság által megadott mappában. 

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
Ez a tárfiók a JSON-fájl, az SQL-táblák nevét tartalmazza. 

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
Az Azure SQL Database-példányt a Blob storage másolandó adatokat tartalmazza. 

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

## <a name="next-steps"></a>További lépések
Tekintse meg a többi adat-előállító által támogatott vezérlésfolyam-tevékenységek: 

- [Feldolgozási sor tevékenység végrehajtása](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok tevékenység beolvasása](control-flow-get-metadata-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
