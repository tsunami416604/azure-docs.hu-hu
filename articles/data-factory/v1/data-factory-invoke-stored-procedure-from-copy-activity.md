---
title: Tárolt eljárás meghívása az Azure Data Factory Copy Activity-ból
description: Megtudhatja, hogyan hívhatja meg a tárolt eljárást az Azure SQL Database-ben, vagy az SQL Server egy Azure Data Factory másolási tevékenység.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d05c2b03a0c498144f37c9b6205053120a596b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924096"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Tárolt eljárás meghívása másolási tevékenységből az Azure Data Factoryban
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [adatok átalakítása tárolt eljárástevékenység használatával a Data Factory alkalmazásban című témakört.](../transform-data-using-stored-procedure.md)


Adatok másolásakor [az SQL Server](data-factory-sqlserver-connector.md) vagy az Azure SQL [Database,](data-factory-azure-sql-connector.md)beállíthatja az **SqlSink** másolási tevékenység meghívására egy tárolt eljárást. Előfordulhat, hogy a tárolt eljárást bármilyen további feldolgozás végrehajtására használja (oszlopok egyesítése, értékek keresése, több táblába való beillesztés stb.) szükséges, mielőtt adatokat szúrna be a céltáblába. Ez a szolgáltatás kihasználja a [Tábla értékű paraméterek et.](https://msdn.microsoft.com/library/bb675163.aspx) 

A következő minta bemutatja, hogyan lehet meghívni egy sql server-adatbázisban tárolt eljárást egy Data Factory-folyamatból (másolási tevékenység):  

## <a name="output-dataset-json"></a>Kimeneti adatkészlet JSON
A JSON kimeneti **adatkészletben** állítsa a következő típust: **SqlServerTable**. Állítsa be **az AzureSqlTable** egy Azure SQL-adatbázissal való használatra. A **tableName** tulajdonság értékének meg kell egyeznie a tárolt eljárás első paraméterének nevével.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>SqlSink szakasz a JSON másolási tevékenységben
Adja meg az **SqlSink** szakaszt a JSON másolási tevékenységben az alábbiak szerint. Ha tárolt eljárást szeretne meghívni, miközben adatokat szúr be a fogadó/cél adatbázisba, adja meg az **SqlWriterStoredProcedureName** és az **SqlWriterTableType** tulajdonságok értékeit. Ezeknek a tulajdonságoknak a leírását [az SQL Server összekötőről szóló cikk SqlSink című szakaszában olvashatja.](data-factory-sqlserver-connector.md#sqlsink)

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

## <a name="stored-procedure-definition"></a>Tárolt eljárás definíciója 
Az adatbázisban definiálja a tárolt eljárást az **SqlWriterStoredProcedureName**nevű fájlnevével. A tárolt eljárás kezeli a forrásadattárból származó bemeneti adatokat, és adatokat szúr be a céladatbázis egy táblájába. A tárolt eljárás első paraméterének nevének meg kell egyeznie a JSON (Marketing) adatkészletben definiált táblanévvel.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Táblatípus-definíció
Az adatbázisban adja meg az **SqlWriterTableType**nevű táblatípust. A táblatípus sémájának meg kell egyeznie a bemeneti adatkészlet sémájával.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>További lépések
Tekintse át a következő összekötő cikkeket, amelyek a teljes JSON-példák: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
