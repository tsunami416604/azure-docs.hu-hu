---
title: Adatkészlet-oszlopok leképezése az Azure Data Factoryban
description: További információ a forrásoszlopok céloszlopokhoz való hozzárendeléséhez.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6eb7012e28319ee6cc86de5ee56090743d681068
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923869"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Forrásadatkészlet-oszlopok hozzárendelése céladatkészlet-oszlopokhoz
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Az oszlopleképezés segítségével megadhatja, hogy a forrástábla "szerkezetében" megadott oszlopok hogyan feleljenek meg a fogadótábla "struktúrájában" megadott oszlopokhoz. A **columnMapping** tulajdonság a Másolás tevékenység **typeProperties** szakaszában érhető el.

Az oszlopleképezés a következő eseteket támogatja:

* A forrásadatkészlet-struktúra összes oszlopa le van képezve a fogadó adatkészlet-struktúra összes oszlopára.
* A forrásadatkészlet-struktúra oszlopainak egy részhalmaza le van képezve a fogadó adatkészlet-struktúra összes oszlopához.

A következők a kivételt eredményező hibafeltételek:

* A leképezésben megadottnál kevesebb oszlop vagy több oszlop lehet a fogadótábla "struktúrájában".
* Duplikált leképezés.
* Az SQL-lekérdezés eredményének nincs megadva oszlopneve a leképezésben.

> [!NOTE]
> A következő minták az Azure SQL és az Azure Blob, de minden olyan adattárban, amely támogatja a téglalap alakú adatkészletek. Állítsa be az adatkészletet és a csatolt szolgáltatásdefiníciókat a példákban, hogy a megfelelő adatforrás adataira mutasson.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>1. minta – oszlopleképezés az Azure SQL-ről az Azure blobra
Ebben a példában a bemeneti tábla rendelkezik egy struktúrával, és egy Azure SQL-adatbázis egy SQL-táblára mutat.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Ebben a példában a kimeneti tábla rendelkezik egy struktúrával, és egy Azure blob storage blobjára mutat.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

A következő JSON egy másolási tevékenységet határoz meg egy folyamatban. A **forrásoszlopok** leképezve oszlopok a fogadó (**columnMappings**) a Translator tulajdonság használatával.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Oszlopleképezési folyamat:**

![Oszlopleképezési folyamat](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>2. minta – oszlopleképezés SQL-lekérdezéssel az Azure SQL-ből az Azure blobba
Ebben a példában egy SQL-lekérdezés t használ adatok kinyerésére az Azure SQL helyett egyszerűen adja meg a tábla nevét és az oszlopneveket a "struktúra" szakaszban. 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
Ebben az esetben a lekérdezés eredményei először a forrás "struktúrájában" megadott oszlopokhoz vannak leképezve. Ezután a forrás "struktúra" oszlopai a fogadó "struktúra" oszlopaihoz vannak rendelve a columnMappings mezőben megadott szabályokkal.  Tegyük fel, hogy a lekérdezés 5 oszlopot ad vissza, két oszlopot, mint a forrás "struktúrájában" megadottak.

**Oszlopleképezési folyamat**

![Oszlopleképezési folyamat-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>További lépések
A Másolási tevékenység használatával kapcsolatos oktatóanyagról a következő cikket ismerteti: 

- [Adatok másolása a Blob Storage szolgáltatásból az SQL-adatbázisba](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
