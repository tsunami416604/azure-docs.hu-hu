---
title: Keressünk tevékenységet az Azure Data Factoryban
description: Ismerje meg, hogyan kereshet meg egy külső forrásból származó értéket a Lookup tevékenység segítségével. Erre a kimenetre a következő tevékenységek is hivatkozhatnak.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 08cc7ce8f306095a66bc0f8cf74dff8c8b551ecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440486"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Keressünk tevékenységet az Azure Data Factoryban

A keresési tevékenység bármely Azure Data Factory által támogatott adatforrásból lekérheti az adatkészletet. Használja a következő esetben:
- Dinamikusan határozza meg, hogy mely objektumokon működjön egy későbbi tevékenységben, ahelyett, hogy az objektum nevét keményen kódolnák. Néhány objektumpélda a fájlok és a táblák.

A hirdetési tevékenység beolvassa és visszaadja egy konfigurációs fájl vagy tábla tartalmát. Azt is visszaadja az eredményt a lekérdezés végrehajtása vagy a tárolt eljárás. A kimenet a lookup tevékenység egy későbbi másolási vagy átalakítási tevékenység, ha egy singleton érték. A kimenet használható foreach tevékenység, ha ez egy sor attribútumok.

## <a name="supported-capabilities"></a>Támogatott képességek

A következő adatforrások támogatottak a lookup tevékenységhez. A legtöbb sor, amelyet a lookup tevékenység visszaadhat, 5000, legfeljebb 2 MB méretű. Jelenleg a leghosszabb időtartam a lookup tevékenység időtúllépést megelőzően egy óra.

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

## <a name="type-properties"></a>Típus tulajdonságai

Név | Leírás | Típus | Kötelező?
---- | ----------- | ---- | --------
Adatkészlet | A keresett adatkészlet hivatkozását tartalmazza. További részletek az **adatkészlet tulajdonságai** szakaszminden megfelelő összekötő cikkben. | Kulcs/érték pár | Igen
source | Adatkészlet-specifikus forrástulajdonságokat tartalmaz, amelyek megegyeznek a Másolási tevékenység forrásával. Részletek a **Másolási tevékenység tulajdonságai** szakasz minden megfelelő összekötő cikkben. | Kulcs/érték pár | Igen
firstRowOnly | Azt jelzi, hogy csak az első vagy az összes sort adja vissza. | Logikai | Nem. A mező alapértelmezett értéke: `true`.

> [!NOTE]
> 
> * **A ByteArray** típusú forrásoszlopok nem támogatottak.
> * **A struktúra** nem támogatott az adatkészlet-definíciókban. Szövegformátumú fájlok esetén a fejlécsor segítségével adja meg az oszlop nevét.
> * Ha a keresőforrás JSON-fájl, `jsonPathDefinition` a JSON-objektum átformálásának beállítása nem támogatott. A program a teljes objektumot beolvassa.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>A keresgaszíntevékenység eredményének használata egy későbbi tevékenységben

A rendszer a tevékenység `output` futtatási eredményének szakaszában adja vissza a találateredményt.

* **Ha `firstRowOnly` `true` a beállítás (alapértelmezett) van megállítva,** a kimeneti formátum a következő kódban látható módon jelenik meg. A keresgalista eredmény `firstRow` rögzített kulcs alatt van. Ha az eredményt a következő tevékenységben `@{activity('MyLookupActivity').output.firstRow.TableName}`szeretné használni, használja a mintáját.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Ha `firstRowOnly` a `false`beállítás **a , a kimeneti formátum a következő kódban látható módon jelenik meg. A `count` mező azt jelzi, hogy hány rekordot ad vissza. A részletes értékek rögzített `value` tömb alatt jelennek meg. Ebben az esetben a lookup tevékenységet egy [Foreach tevékenység](control-flow-for-each-activity.md)követi. A tömböt a `value` használatával `items` adja át a `@activity('MyLookupActivity').output.value`ForEach tevékenység mezőnek a használatával. A tömb elemeinek `value` eléréséhez használja a `@{activity('lookupActivity').output.value[zero based index].propertyname}`következő szintaxist: . Például: `@{activity('lookupActivity').output.value[0].tablename}`.

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

### <a name="copy-activity-example"></a>Példa a Tevékenység másolása
Ebben a példában a Copy Activity adatokat másol egy SQL-táblából az Azure SQL Database-példányban az Azure Blob storage.In this example, Copy Activity copys data from a SQL table in your Azure SQL Database instance to Azure Blob storage. Az SQL-tábla nevét egy JSON-fájl tárolja a Blob storage-ban. A Lookup tevékenység futásidőben megkeresi a tábla nevét. A JSON dinamikusan módosul ezzel a módszerrel. Nem kell újratelepíteni e folyamatok at vagy adatkészleteket. 

Ez a példa csak az első sor felkelőját mutatja be. Az összes sor kereséséhez és az eredmények ForEach tevékenységgel való láncolásához tekintse meg a mintákat [több tábla másolása tömegesen az Azure Data Factory használatával.](tutorial-bulk-copy.md)

### <a name="pipeline"></a>Folyamat
Ez a folyamat két tevékenységet tartalmaz: a keresést és a másolást. 

- A lookup tevékenység a **LookupDataset**használatára van konfigurálva, amely az Azure Blob storage egy helyére hivatkozik. A lookup tevékenység beolvassa az SQL tábla nevét egy JSON-fájlból ezen a helyen. 
- A Másolási tevékenység a lookup tevékenység kimenetét használja, amely az SQL-tábla neve. A **SourceDataset** **táblanév** tulajdonsága úgy van konfigurálva, hogy a lookup tevékenység kimenetét használja. A Copy Activity adatokat az SQL-táblából az Azure Blob storage egyik helyére másolja. A helyet a **SinkDataset** tulajdonság határozza meg. 

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

### <a name="lookup-dataset"></a>Adatkészlet felkereshető
A **keresési** adatkészlet az **Azure StorageStorageLinkedService** típus által megadott Azure Storage keresési mappában található **sourcetable.json** fájl. 

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

### <a name="source-dataset-for-copy-activity"></a>**Forrásadatkészlet** a másolási tevékenységhez
A **forrásadatkészlet** a lookup tevékenység kimenetét használja, amely az SQL-tábla neve. A Copy Activity adatokat erről az SQL-táblából az Azure Blob storage egy helyére másolja. A helyet a **fogadó** adatkészlet határozza meg. 

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

### <a name="sink-dataset-for-copy-activity"></a>**A** másolási tevékenység hez vezető adatkészlet
A Copy Activity adatokat az SQL-táblából másolja az Azure Storage **csv** mappájában lévő **filebylookup.csv** fájlba. A fájlt az **AzureStorageLinkedService** tulajdonság határozza meg. 

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
Ez a tárfiók tartalmazza a JSON-fájlt az SQL-táblák nevével. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>"
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database társított szolgáltatás
Ez az Azure SQL Database-példány a Blob storage-ba másolandó adatokat tartalmazza. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;"
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Objektumok halmaza

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

## <a name="limitations-and-workarounds"></a>Korlátozások és kerülő megoldások

Az alábbiakban a külső tevékenység néhány korlátozását és a javasolt kerülő megoldásokat olvashatja.

| Korlátozás | Áthidaló megoldás |
|---|---|
| A lookup tevékenység legfeljebb 5000 sort tartalmaz, és legfeljebb 2 MB-os méretű. | Tervezzen egy kétszintű folyamatot, amelyben a külső csővezeték egy belső csővezetéken halad át, amely olyan adatokat olvas be, amelyek nem haladják meg a maximális sorokat vagy méretet. |
| | |

## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamattevékenységeket: 

- [Folyamattevékenység végrehajtása](control-flow-execute-pipeline-activity.md)
- [ForEach tevékenység](control-flow-for-each-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
