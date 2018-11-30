---
title: Keresési tevékenység az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a keresési tevékenység külső forrásból kereshet meg egy értéket. Ez a kimenet a következő tevékenységek további lehet hivatkozni.
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
ms.openlocfilehash: e682b3780c26da9cf2398e93adc32cb107127d9c
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426791"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Keresési tevékenység az Azure Data Factoryban

Keresési tevékenység az Azure Data Factory által támogatott adatforrások bármelyikéből kérheti le egy adatkészletet. Használhatja az alábbi forgatókönyvet:
- Mely objektumokat a soron következő tevékenysége, sorszámozás az objektum neve helyett a művelethez használandó dinamikus meghatározásához. Néhány objektum például fájlok és a táblák.

Keresési tevékenység beolvassa, és a egy konfigurációs fájlban vagy a tábla tartalmát adja vissza. Emellett egy lekérdezést, vagy a tárolt eljárás végrehajtása eredményét adja vissza. A keresési tevékenység kimenetét egy további példányt vagy Adatátalakítási tevékenységgel használható, Egypéldányos érték. A kimeneti egy ForEach tevékenység használható, ha az attribútumok tömbjét.

## <a name="supported-capabilities"></a>Támogatott képességek

Keresési tevékenység a következő adatforrások támogatottak. A sorok, keresési tevékenység által visszaadható legnagyobb száma az 5 000, akár 2 MB méretű. A leghosszabb időtartamú a keresési tevékenység az időkorlát lejárta előtt jelenleg egy óra.

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
Adatkészlet | Az adatkészlet-hivatkozásban biztosít a kereséshez. A részletek a **adatkészlet tulajdonságai** minden megfelelő összekötő cikk szakaszában talál. | Kulcs/érték pár | Igen
source | Tartalmazza az adatkészlet-specifikus forrás tulajdonságait, ugyanaz, mint a másolási tevékenység forrása. A részletek a **másolási tevékenység tulajdonságai** szakasz mindegyik megfelelő összekötő a cikkben. | Kulcs/érték pár | Igen
firstRowOnly | Azt jelzi, hogy csak az első sort vagy minden sort adja vissza. | Logikai | Nem. A mező alapértelmezett értéke: `true`.

> [!NOTE]

> * Az oszlopok forrás **ByteArray** típusa nem támogatott.
> * **Struktúra** nem támogatott adatkészlet-definíciókban. A szöveges formátumú fájlok esetében a fejlécsor használatával adja meg az oszlop neve.
> * Ha a keresési forrás egy JSON-fájlt a `jsonPathDefinition` beállítása átalakításakor a JSON-objektum nem támogatott. A teljes objektumokat a rendszer lekéri.

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

* **Amikor `firstRowOnly` értékre van állítva `false`** , a kimenet formátuma a következő kódban látható módon. A `count` mező jelzi, hogy hány rekordok lesznek visszaadva. Részletes értékek szerint egy rögzített `value` tömb. Ebben az esetben a keresési tevékenység követ egy [Foreach tevékenység](control-flow-for-each-activity.md). Át kell adnia a `value` tömb a ForEach tevékenység `items` mezőt minta használatával `@activity('MyLookupActivity').output.value`. Hozzáférés elemeknek a `value` tömböt, használja a következő szintaxist: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Például: `@{activity('lookupActivity').output.value[0].tablename}`.

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

### <a name="copy-activity-example"></a>Másolási tevékenység példa
Ebben a példában a másolási tevékenység adatokat másol az Azure SQL Database-példány az SQL-táblájába az Azure Blob storage. Az SQL-tábla neve egy JSON-fájlt a Blob storage-ban van tárolva. A keresési tevékenység megkeresi a tábla neve futásidőben. JSON-ban Ez a módszer használatával dinamikusan módosul. Nem kell újratelepíteni adatcsatornákat és adathalmazokat. 

Ez a példa bemutatja a keresés csak az első sorhoz. Keresés az összes sort, és összekapcsolja az eredményeket a ForEach tevékenység, a mintáiban találhat [több táblák tömeges másolása az Azure Data Factory használatával](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Folyamat
Ez a folyamat két tevékenységet tartalmaz: a keresési és másolási. 

- A keresési tevékenység használatára van konfigurálva **LookupDataset**, amely az Azure Blob storage egy olyan helyre hivatkozik. A keresési tevékenység beolvassa egy JSON-fájlt ezen a helyen az SQL-tábla neve. 
- Másolási tevékenység használja a keresési tevékenység, amely az SQL-tábla neve kimenetét. A **tableName** tulajdonságot a **SourceDataset** a keresési tevékenység kimenete használatára van konfigurálva. Másolja a tevékenység adatokat másol az SQL-táblát egy olyan helyre, az Azure Blob storage-ban. A hely által meghatározott a **SinkDataset** tulajdonság. 

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
A **keresési** adatkészlet a **sourcetable.json** által megadott Azure Storage keresési mappában található fájl a **AzureStorageLinkedService** típusa. 

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

### <a name="source-dataset-for-copy-activity"></a>**Forrás** adatkészletet másolási tevékenységhez
A **forrás** adatkészletet használja a keresési tevékenység, amely az SQL-tábla neve kimenetét. Másolja a tevékenység adatokat másol az SQL-tábla egy olyan helyre, az Azure Blob storage-ban. A hely által meghatározott a **fogadó** adatkészlet. 

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

### <a name="sink-dataset-for-copy-activity"></a>**Fogadó** adatkészletet másolási tevékenységhez
A másolási tevékenység adatokat másol az SQL-táblát az **filebylookup.csv** fájlt a **csv** az Azure Storage-mappát. A fájl szerint van megadva a **AzureStorageLinkedService** tulajdonság. 

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

## <a name="limitations-and-workarounds"></a>Korlátozások és a lehetséges megoldások

Az alábbiakban néhány korlátozást a keresési tevékenység és a javasolt megoldások.

| Korlátozás | Áthidaló megoldás |
|---|---|
| A keresési tevékenység legfeljebb 5000 sort, és legfeljebb 2 MB méretű rendelkezik. | Amennyiben a külső folyamat ismétel-e egy belső folyamatot, amely lekéri az adatokat, hogy a sorok maximális száma és mérete nem haladja meg két szinten folyamat tervezése. |
| | |

## <a name="next-steps"></a>További lépések
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [ForEach tevékenység](control-flow-for-each-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
