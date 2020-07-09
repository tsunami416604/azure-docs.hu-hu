---
title: Keresési tevékenység a Azure Data Factory
description: Ismerje meg, hogyan kereshet meg egy külső forrásból származó értéket a keresési tevékenység használatával. Ezt a kimenetet a sikeres tevékenységek továbbra is hivatkozhatják.
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
ms.openlocfilehash: 02abdaf46ca2af6c96d3b5e8d4ce5876831bd415
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81417999"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Keresési tevékenység a Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A keresési tevékenység lekérheti az adatkészletet a Azure Data Factory által támogatott adatforrásokból. A következő esetekben használja:
- Dinamikusan határozza meg, hogy mely objektumok fognak működni egy későbbi tevékenységben, ahelyett, hogy az objektum nevét rögzítette. Néhány objektum például fájlok és táblák.

A keresési tevékenység beolvassa és visszaadja egy konfigurációs fájl vagy tábla tartalmát. Egy lekérdezés vagy tárolt eljárás végrehajtásának eredményét is visszaadja. A keresési tevékenység kimenete egy későbbi másolási vagy átalakítási tevékenységben is használható, ha ez egy egyedi érték. A kimenet egy ForEach tevékenységben használható, ha az attribútumok tömbje.

## <a name="supported-capabilities"></a>Támogatott képességek

A keresési tevékenység a következő adatforrásokat támogatja. A keresési tevékenység által visszaadott legnagyobb számú sor 5 000, legfeljebb 2 MB méretű lehet. Jelenleg az időkorlát előtt a keresési tevékenység leghosszabb időtartama egy óra.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

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

Name | Leírás | Típus | Kötelező?
---- | ----------- | ---- | --------
adatkészlet | A keresés adatkészlet-hivatkozását adja meg. Az egyes kapcsolódó összekötők című cikk **adatkészlet tulajdonságai** szakaszában talál részleteket. | Kulcs/érték pár | Igen
source | Adatkészlet-specifikus forrás tulajdonságokat tartalmaz, ugyanazokat a másolási tevékenység forrását. A részletek a **másolási tevékenység tulajdonságai** szakaszban olvashatók a kapcsolódó összekötők cikkeiben. | Kulcs/érték pár | Igen
firstRowOnly | Azt jelzi, hogy csak az első sort vagy az összes sort kívánja-e visszaadni. | Logikai | Nem. A mező alapértelmezett értéke: `true`.

> [!NOTE]
> 
> * A **ByteArray** típusú forrásoldali oszlopok nem támogatottak.
> * A **struktúra** nem támogatott az adatkészlet-definíciókban. Szöveges formátumú fájlok esetében a fejlécsor használatával adja meg az oszlop nevét.
> * Ha a keresési forrás egy JSON-fájl, a `jsonPathDefinition` JSON-objektum átformálásának beállítása nem támogatott. A rendszer lekéri a teljes objektumot.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>A keresési tevékenység eredményének használata egy későbbi tevékenységben

A keresési eredmény a `output` tevékenység futtatási eredményének szakaszában lesz visszaadva.

* **Ha a `firstRowOnly` értéke `true` (alapértelmezett)**, a kimeneti formátum a következő kódban látható. A keresési eredmény egy rögzített kulcs alatt található `firstRow` . Ha a következő tevékenység eredményét szeretné használni, használja a mintázatát `@{activity('MyLookupActivity').output.firstRow.TableName}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* Ha a értéke, a kimeneti formátum a következő kódban látható. ** `firstRowOnly` `false` ** Egy `count` mező jelzi, hogy a rendszer hány rekordot ad vissza. A részletes értékek egy rögzített tömb alatt jelennek meg `value` . Ilyen esetben a keresési tevékenységet egy [foreach-tevékenység](control-flow-for-each-activity.md)követi. A `value` tömböt a foreach tevékenység mezőjébe kell átadni `items` a mintázatának használatával `@activity('MyLookupActivity').output.value` . A tömb elemeinek eléréséhez `value` használja a következő szintaxist: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Például: `@{activity('lookupActivity').output.value[0].tablename}`.

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

### <a name="copy-activity-example"></a>Példa másolási tevékenységre
Ebben a példában a másolási tevékenység az Azure SQL Database-példányban található SQL-táblából másolja át az adatait az Azure Blob Storage-ba. Az SQL-tábla neve egy JSON-fájlban tárolódik a blob Storage-ban. A keresési tevékenység futásidőben megkeresi a tábla nevét. A JSON dinamikusan módosul a módszer használatával. Nem kell újratelepítenie a folyamatokat vagy adatkészleteket. 

Ez a példa csak az első sor keresését mutatja be. Az összes sorhoz való kereséshez, valamint az eredmények ForEach-tevékenységgel való láncolásához tekintse meg a [több táblázat másolása tömegesen a Azure Data Factory használatával](tutorial-bulk-copy.md)című témakör mintáit.

### <a name="pipeline"></a>Folyamat
Ez a folyamat két tevékenységet tartalmaz: keresés és másolás. 

- A keresési tevékenység a **LookupDataset**használatára van konfigurálva, amely az Azure Blob Storage-ban található helyre hivatkozik. A keresési tevékenység beolvassa az SQL-táblázat nevét egy olyan JSON-fájlból, amely ezen a helyen található. 
- A másolási tevékenység a keresési tevékenység kimenetét használja, amely az SQL-tábla neve. A **SourceDataset** **Táblanév** tulajdonsága a keresési tevékenység kimenetének használatára van konfigurálva. A másolási tevékenység az SQL-táblából másolja az adatait az Azure Blob Storage egyik helyére. A helyet a **SinkDataset** tulajdonság határozza meg. 

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
A **keresési** adatkészlet a **AzureStorageLinkedService** típus által megadott Azure Storage keresési mappában található fájl **sourcetable.js** . 

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

### <a name="source-dataset-for-copy-activity"></a>Másolási tevékenység **forrás** -adatkészlete
A **forrás** -adatkészlet a keresési tevékenység kimenetét használja, amely az SQL-tábla neve. A másolási tevékenység az adott SQL-táblából másolja át az adatait az Azure Blob Storage egyik helyére. **A helyet a fogadó adatkészlet adja** meg. 

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

### <a name="sink-dataset-for-copy-activity"></a>Másolási tevékenység fogadó **adatkészlete**
A másolási tevékenység átmásolja az adatait az SQL-táblából az Azure Storage **CSV** -mappájában lévő **filebylookup.csv** fájlba. A fájlt a **AzureStorageLinkedService** tulajdonság határozza meg. 

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
Ez a Storage-fiók tartalmazza az SQL-táblák nevét tartalmazó JSON-fájlt. 

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
Ez a Azure SQL Database-példány tartalmazza a blob Storage-ba másolandó adatfájlokat. 

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

### <a name="sourcetablejson"></a>sourcetable.jsbekapcsolva

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

## <a name="limitations-and-workarounds"></a>Korlátozások és megkerülő megoldások

Íme néhány korlátozás a keresési tevékenységhez és a javasolt megkerülő megoldásokhoz.

| Korlátozás | Áthidaló megoldás |
|---|---|
| A keresési tevékenység legfeljebb 5 000 sort tartalmaz, és legfeljebb 2 MB méretű. | Tervezzen olyan kétszintű folyamatot, amelyben a külső folyamat egy belső folyamaton keresztül történik, amely nem haladja meg a maximálisan megengedett sorokat vagy méretet. |
| | |

## <a name="next-steps"></a>További lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [ForEach tevékenység](control-flow-for-each-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
