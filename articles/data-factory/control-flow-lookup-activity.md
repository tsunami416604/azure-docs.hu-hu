---
title: Keresési tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a keresési tevékenység külső forrásból kereshet meg egy értéket. Erre a kimenetre a későbbi tevékenységek is hivatkozhatnak.
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
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 25ed439674fcf7136e29034eb97e0652ae9ba111
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237832"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Keresési tevékenység az Azure Data Factoryban

Keresési tevékenység használható egy adatkészlet lekérése az ADF-támogatott adatforrás valamelyikét.  A következő esetben használható:
- Mely objektumokat (fájlok, táblák stb.) a soron következő tevékenysége, fix kódolása az objektum neve helyett a művelethez használandó dinamikus meghatározásához

Keresési tevékenység olvasása és a egy konfigurációs fájlban, a konfiguráció táblázat vagy a lekérdezés vagy tárolt eljárás végrehajtásának eredménye tartalmát adja vissza.  A keresési tevékenység kimenetét használja a következő másolási vagy Adatátalakítási tevékenységgel, ha a singleton értéke, vagy egy ForEach tevékenység használja, ha attribútumok tömbjét.

## <a name="supported-capabilities"></a>Támogatott képességek

Keresés a következő adatforrások támogatottak. A sorok maximális számát is keresési tevékenység által visszaadott **5000**, és akár **2MB** mérete. És jelenleg a maximális időtartam, a keresési tevékenység az időkorlát lejárta előtt egy óra.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

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

## <a name="type-properties"></a>Tulajdonságok
Name (Név) | Leírás | Típus | Kötelező?
---- | ----------- | ---- | --------
adatkészlet | Az adatkészlet-hivatkozásban biztosít a kereséshez. Ismerje meg a részleteket az "Adatkészlet tulajdonságai" szakaszban minden megfelelő összekötő a cikkben. | Kulcs/érték pár | Igen
source | Tartalmazza az adatkészlet-specifikus forrás tulajdonságait, ugyanaz, mint a másolási tevékenység forrása. Ismerje meg a részleteket az "Másolási tevékenység tulajdonságai" szakaszban minden megfelelő összekötő a cikkben. | Kulcs/érték pár | Igen
firstRowOnly | Azt jelzi, hogy csak az első sort vagy minden sort adja vissza. | Logikai | Nem. Az alapértelmezett szint a `true`.

**Vegye figyelembe a következő szempontokat:**

1. Forrásoszlop ByteArray típus nem támogatott.
2. Struktúra nem támogatott adatkészlet-definícióban. A szöveges formátumú fájlok, használhatja a fejlécsor a oszlop nevét.
3. Ha a keresési forrás egy JSON-fájl a `jsonPathDefinition` a teljes beállítást újra átalakítását a JSON-objektum nem támogatott, objektumok, a rendszer lekéri.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Használja a keresési tevékenység eredménye a soron következő tevékenysége

A keresési eredményt adja vissza a `output` szakaszában a tevékenységfuttatás eredményét.

* **Amikor `firstRowOnly` értékre van állítva `true` (alapértelmezett)**, a kimenet formátuma a következő kódban látható módon. A Keresés eredménye szerint egy rögzített `firstRow` kulcsot. Az eredmény a soron következő tevékenysége használatához használja a következő mintát, `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Amikor `firstRowOnly` értékre van állítva `false`** , a kimenet formátuma a következő kódban látható módon. A `count` mező jelzi, hogy hány rekordok lesznek visszaadva, és részletes értékek szerint egy rögzített `value` tömb. Ebben az esetben a keresési tevékenység általában követ egy [Foreach tevékenység](control-flow-for-each-activity.md). Továbbíthatja a `value` tömb a ForEach tevékenység `items` mezőt minta használatával `@activity('MyLookupActivity').output.value`. Hozzáférés elemeknek a `value` tömböt, használja a következő szintaxist: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Íme egy példa: `@{activity('lookupActivity').output.value[0].tablename}`.

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
Ebben a példában a másolási tevékenység adatokat másol az Azure SQL Database-példány az SQL-táblájába az Azure Blob storage. Az SQL-tábla neve egy JSON-fájlt a Blob storage-ban van tárolva. A keresési tevékenység megkeresi a tábla neve futásidőben. Ez a megközelítés lehetővé teszi, hogy a JSON-adatcsatornákat és adathalmazokat ismételt üzembe helyezése nélkül dinamikusan módosíthatók. 

Ez a példa bemutatja a keresés csak az első sorhoz. Keresés az összes sort, és összekapcsolja az eredményeket a ForEach tevékenység, a mintáiban találhat [több táblák tömeges másolása az Azure Data Factory használatával](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Folyamat
Ez a folyamat két tevékenységet tartalmaz: *keresési* és *másolási*. 

- A keresési tevékenység LookupDataset, amely az Azure Blob storage egy olyan helyre hivatkozik használatára van konfigurálva. A keresési tevékenység beolvassa egy JSON-fájlt ezen a helyen az SQL-tábla neve. 
- A másolási tevékenység használja a keresési tevékenység (az SQL-tábla neve) kimenetét. A tableName tulajdonságot a forrásadatkészlet (SourceDataset) a keresési tevékenység kimenete használatára van konfigurálva. A másolási tevékenység adatokat másol egy Azure Blob Storage-SinkDataset tulajdonság által megadott helyre a SQL-táblát. 


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
A keresési adatkészlet hivatkozik a *sourcetable.json* AzureStorageLinkedService típusa által megadott Azure Storage keresési mappában található fájl. 

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

### <a name="source-dataset-for-the-copy-activity"></a>Forrásadatkészletet a másolási tevékenység
A forrásadatkészlet használja a keresési tevékenység, amely az SQL-tábla neve kimenetét. A másolási tevékenység adatokat másol az Azure Blob storage, a fogadó-adatkészlet által meghatározott helyre a az SQL-táblát. 

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

### <a name="sink-dataset-for-the-copy-activity"></a>Fogadó adatkészlet a másolási tevékenység
A másolási tevékenység adatokat másol az SQL-táblát az *filebylookup.csv* fájlt a *csv* az Azure Storage, az AzureStorageLinkedService tulajdonság által megadott mappába. 

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
Ezt a tárfiókot az SQL-táblák nevére a JSON-fájl tartalmazza. 

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
Az Azure SQL Database-példány tartalmazza az adatokat a Blob storage kell másolni. 

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

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Objektumok közül

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Objektumok tömbje

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="next-steps"></a>További lépések
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenység: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
