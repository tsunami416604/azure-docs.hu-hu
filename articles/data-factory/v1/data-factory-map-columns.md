---
title: Az Azure Data Factoryben az adatkészlet oszlopok leképezése |} Microsoft Docs
description: Megtudhatja, hogyan Forrásoszlopok leképezése cél oszlopok.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1c8ea748297b8fe4052b60f08b8f00b1e41275fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620357"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>A dataset Forrásoszlopok leképezése cél adatkészlet oszlopok
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. 

Adja meg, hogyan megadott oszlopoknak a "szerkezetének" forrás tábla térkép oszlop szerepel a fogadó tábla "structure" oszlopleképezés is használható. A **columnMapping** tulajdonság érhető el a **typeProperties** a másolási tevékenység szakasza.

Oszlop leképezése a következő szituációkat ismerteti:

* A forrás adatkészlet-szerkezetekben összes oszlop összes oszlop szerepel a fogadó adatkészlet-szerkezetekben van leképezve.
* A forrás adatkészlet-szerkezetekben oszlopok egy részét az összes oszlop szerepel a fogadó adatkészlet-szerkezetekben van leképezve.

Hiba feltételek, amelyek kivételt a következők:

* Kevesebb oszlopot vagy több oszlop szerepel a "structure" fogadó tábla mint a leképezésben megadott.
* Ismétlődő leképezés.
* SQL-lekérdezés eredménye nem rendelkezik a leképezésben megadott oszlopnév.

> [!NOTE]
> A következő mintákat az Azure SQL és az Azure Blob, de bármely adattároló, amely támogatja a téglalap alakú adatkészletek vonatkoznak. Állítsa be úgy a DataSet adatkészlet és a társított szolgáltatás definíciók példákban az adatok a megfelelő adatforrás.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Oszlopleképezés, az Azure SQL-ből az Azure-blobhoz 1 – minta
Ez a példa a bemeneti táblájának struktúrája, és egy Azure SQL adatbázis SQL táblázat mutat.

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

Ez a példa a kimeneti táblához tartozik egy struktúra, és az Azure blob Storage tárolóban lévő blob mutat.

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

A következő JSON egy folyamaton belül határozza meg a másolási tevékenység. A forrás oszlop szerepel a fogadó leképezve oszlopokat (**columnMappings**) használatával a **fordító** tulajdonság.

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
**Oszlop-hozzárendelési folyamat:**

![Oszlop-hozzárendelési folyamat](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Az SQL-lekérdezést az Azure SQL-ből az Azure-blobhoz oszlopleképezés 2 – minta
Ez a példa egy SQL-lekérdezés segítségével adatok kinyerése az Azure SQL helyett egyszerűen adja meg a táblanevet és az oszlop neve "structure" szakasz. 

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
Ebben az esetben a lekérdezés eredményeinek először képezi le a "structure" forrás megadott oszlopok. A következő forrás "structure" oszlopok vannak leképezve oszlop szerepel a fogadó "structure" columnMappings megadott szabályait.  Tegyük fel, hogy a lekérdezés 5 oszlopok, két további oszlop, mint a forrás "structure" adja vissza.

**Oszlop-hozzárendelési folyamat**

![Oszlop leképezése adatfolyam-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>További lépések
Olvassa el a másolási tevékenység az oktatóanyag: 

- [A Blob Storage az SQL Database-adatok másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
