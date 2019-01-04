---
title: Az Azure Data Factoryban adatkészletoszlopok leképezése |} A Microsoft Docs
description: Ismerje meg, hogyan képezhet le forrásoszlopok céloszlopok.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1b009ac2ca42e9804b88989b55b2e73524732550
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017463"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Adatkészlet céloszlopok forrás adatkészletoszlopok leképezése
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Adja meg, hogyan oszlopokban a "struktúrában megadott" forrás tábla térkép oszlopok a "struktúrában" fogadó tábla megadott oszlop-hozzárendelés használható. A **columnMapping** tulajdonság érhető el a **typeProperties** szakasz látható a másolási tevékenység.

Oszlop-hozzárendelés a következő eseteket támogatja:

* A forrás adatkészlet-szerkezetekben minden oszlop összes oszlopot a fogadó adatkészlet-szerkezetekben vannak leképezve.
* A forrás adatkészlet-szerkezetekben az oszlopok egy része lesz leképezve a fogadó adatkészlet-szerkezetekben összes oszlopot.

A kivétel eredményező hiba feltételek a következők:

* Kevesebb oszlopot vagy több oszlop szerepel a "" fogadó tábla szerkezetének, mint a leképezésben megadott.
* Kettős hozzárendelés.
* SQL-lekérdezés eredménye nem rendelkezik olyan oszlopnevet, amely a leképezés van megadva.

> [!NOTE]
> A következő minták az Azure SQL és az Azure Blob, de alkalmazhatók minden adattárhoz, amely támogatja a téglalap alakú adatkészlet. Módosítsa az adatkészlet és a példákat, mutasson az adatok a megfelelő adatforrást a kapcsolódószolgáltatás-definíció.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Oszlop leképezése az Azure SQL az Azure-blobba 1 – minta
Ebben a példában a bemeneti tábla struktúrája, és a egy Azure SQL database az SQL-táblájára mutat.

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

Ebben a példában a kimeneti tábla egy struktúrája, és a egy Azure blob Storage-blobba mutat.

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

A következő JSON egy másolási tevékenységet a folyamat határozza meg. Az oszlopok hozzárendelve fogadó oszlopai forrásból (**leképezésekben**) használatával a **Translator** tulajdonság.

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

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Az Azure blob SQL-lekérdezést az Azure SQL-címtársítás oszlop 2 – minta
Ebben a példában egy SQL-lekérdezés segítségével adatok kinyerése az Azure SQL helyett egyszerűen adja meg a táblázat neve és az oszlop neve "struktúra" szakaszban. 

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
A lekérdezési eredmények ebben az esetben először vannak leképezve "struktúrában" forrás megadott oszlopra. Ezután "struktúra" forrás oszlopai vannak leképezve fogadó "struktúra" oszlopai leképezésekben megadott szabályoknak.  Tegyük fel, hogy a lekérdezés visszaadja az 5 oszlopokhoz, és két új oszlop, mint a forrás "struktúrában" megadott.

**Oszlop-hozzárendelési folyamat**

![Flow – 2. oszlopot leképezés](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>További lépések
Olvassa el a másolási tevékenység használatával oktatóanyagot: 

- [Adatok másolása Blob Storage-ból SQL-adatbázis](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
