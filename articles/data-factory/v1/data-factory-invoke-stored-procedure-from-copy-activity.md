---
title: Az Azure Data Factory másolási tevékenység tárolt eljárás meghívása |} A Microsoft Docs
description: Ismerje meg, hogyan lehet egy Azure Data Factory másolási tevékenységgel az Azure SQL Database vagy SQL Server tárolt eljárás meghívása.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 35e9347039a7b9939ab4d2719f9738429dec168c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016069"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Azure Data Factory másolási tevékenysége a tárolt eljárás meghívása
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [adatok átalakítása a tárolt eljárási tevékenység használatával a Data Factory](../transform-data-using-stored-procedure.md).


Adatok másolásakor [SQL Server](data-factory-sqlserver-connector.md) vagy [Azure SQL Database](data-factory-azure-sql-connector.md), konfigurálhatja a **SqlSink** a tárolt eljárás meghívása a másolási tevékenység. Előfordulhat, hogy szeretné használni a tárolt eljárást minden olyan további feldolgozás (egyesítés oszlopok keresése értékeket szúr be a több táblákat, stb.) szükséges az adatok a céltábla beszúrása előtt. Ez a szolgáltatás kihasználja [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb675163.aspx). 

A következő minta bemutatja, hogyan kell elindítani a Data Factory-folyamat (a másolási tevékenység) az SQL Server-adatbázis a tárolt eljárás:  

## <a name="output-dataset-json"></a>Kimeneti adatkészlet JSON
A kimeneti adatkészlet JSON, állítsa be a **típus** való: **SqlServerTable**. Állítsa be **AzureSqlTable** egy Azure SQL database használata. Az érték **tableName** tulajdonságának meg kell egyeznie a tárolt eljárás első paraméter neve.  

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
Adja meg a **SqlSink** a következő szakasz a másolási tevékenység JSON-ban. A tárolt eljárás meghívása közben adatok beszúrása a cél-/ fogadóadattár adatbázis, adja meg az értékeket is **SqlWriterStoredProcedureName** és **SqlWriterTableType** tulajdonságait. Ezek a Tulajdonságok leírásáért lásd: [SqlSink szakaszban az SQL Server-összekötő cikkben](data-factory-sqlserver-connector.md#sqlsink).

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
Az adatbázis határoz meg a tárolt eljárás neve megegyezik a **SqlWriterStoredProcedureName**. A tárolt eljárás bemeneti adatokat a forrásadattárból kezeli, és adatot szúr be egy táblába a céladatbázisban. Az első paraméterként a következő tárolt eljárás nevét meg kell egyeznie a tableName JSON (Marketing) adatkészletben történő definiálása okozza.

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
Az adatbázis határoz meg a táblatípus, amelynek a neve megegyezik **SqlWriterTableType**. A séma a tábla típusú meg kell egyeznie a bemeneti adatkészlet sémája.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>További lépések
Tekintse át a következő összekötő cikkek, teljes JSON-példák: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
