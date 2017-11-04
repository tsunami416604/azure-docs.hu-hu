---
title: "Az Azure Data Factory másolási tevékenység tárolt eljárás meghívása |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet meghívni egy Azure Data Factory másolási tevékenység során az Azure SQL Database vagy az SQL Server tárolt eljárást."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 460785d0d3f8b3d8a0a53d544788cb1a74db8c00
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>A másolási tevékenység során az Azure Data Factory tárolt eljárás meghívása
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [tárolt eljárási tevékenység adat-előállítóban 2-es verzióját használó adatok](../transform-data-using-stored-procedure.md).


Az adatok másolásakor [SQL Server](data-factory-sqlserver-connector.md) vagy [Azure SQL Database](data-factory-azure-sql-connector.md), konfigurálhatja a **SqlSink** a másolási tevékenység meghívni a tárolt eljárást. Érdemes lehet használni a tárolt eljárást a további feldolgozás (egyesítés oszlopok, keresés, szúrás több táblák, stb.) az adatok a céltáblázatba való beszúrása előtt meg kell adni. Ez a szolgáltatás kihasználja a [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb675163.aspx). 

A következő példa bemutatja, hogyan hívhatnak meg a Data Factory-folyamathoz (másolási tevékenység) SQL Server-adatbázisban tárolt eljárást:  

## <a name="output-dataset-json"></a>JSON kimeneti adatkészlet
A kimeneti adatkészlet JSON, állítsa be a **típus** való: **SqlServerTable**. Állítsa az értékét **AzureSqlTable** Azure SQL-adatbázis használata. A következő **tableName** tulajdonságának meg kell egyeznie a tárolt eljárás első paraméter neve.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>A másolási tevékenység JSON SqlSink szakasz
Adja meg a **SqlSink** a másolási tevékenység során JSON szakasz az alábbiak szerint. A tárolt eljárás hívása közben adatok beszúrása a fogadó vagy a cél-adatbázis, adja meg az értékeket is **SqlWriterStoredProcedureName** és **SqlWriterTableType** tulajdonságait. Ezek a tulajdonságok leírását lásd: [SqlSink szakasz az SQL Server-összekötő cikkben](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Tárolt eljárás meghatározása 
Az adatbázisban, adja meg a tárolt eljárás neve megegyezik a **SqlWriterStoredProcedureName**. A tárolt eljárás kezeli a forrás adattárból bemeneti adatokat, és adatok beszúrása egy táblázatba a céladatbázisban. Az első paraméter a következő tárolt eljárás nevét meg kell egyeznie a tableName definiálva az adatkészlet JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Tábla típusának megadása
Az adatbázisban azonos nevű tábla típusának azonosítására **SqlWriterTableType**. A tábla típusú sémát, meg kell egyeznie a bemeneti adatkészlet sémája.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Következő lépések
Tekintse át a következő összekötő, amely annak teljes JSON-példák: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
