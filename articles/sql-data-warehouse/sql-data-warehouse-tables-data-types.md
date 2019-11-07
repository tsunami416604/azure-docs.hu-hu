---
title: Adattípusok definiálása
description: Javaslatok táblázatos adattípusok definiálásához a Azure SQL Data Warehouseban.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a1d88d8fabd5ff32cc1ca5cca4e2a57a86c8dcb3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692483"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Táblázat adattípusai a Azure SQL Data Warehouseban
Javaslatok táblázatos adattípusok definiálásához a Azure SQL Data Warehouseban. 

## <a name="what-are-the-data-types"></a>Mik az adattípusok?

SQL Data Warehouse a leggyakrabban használt adattípusokat támogatja. A támogatott adattípusok listáját lásd: [adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) a CREATE TABLE utasításban. 

## <a name="minimize-row-length"></a>Sor hosszának csökkentése
Az adattípusok méretének minimalizálása lerövidíti a sor hosszát, ami jobb lekérdezési teljesítményt eredményez. Használja az adataihoz használható legkisebb adattípust. 

- Kerülje a nagyméretű alapértelmezett hosszúságú karakteres oszlopok definiálását. Ha például a leghosszabb érték 25 karakter, akkor adja meg az oszlopot VARCHAR-ként (25). 
- Kerülje a [NVARCHAR] [NVARCHAR] használatát, ha csak VARCHAR-ra van szüksége.
- Ha lehetséges, használjon NVARCHAR (4000) vagy VARCHAR (8000) értéket a NVARCHAR (MAX) vagy a VARCHAR (MAX) helyett.

Ha a táblák betöltéséhez a-alapú külső táblákat használ, a tábla sorainak megadott hossza nem haladhatja meg az 1 MB-ot. Ha a változó hosszúságú adatokkal rendelkező sorok 1 MB-nál nagyobb méretűek, a sort betöltheti a BCP-vel, de nem a Base értékkel.

## <a name="identify-unsupported-data-types"></a>Nem támogatott adattípusok azonosítása
Ha egy másik SQL-adatbázisból telepíti át az adatbázist, akkor előfordulhat, hogy a SQL Data Warehouse nem támogatott adattípusokat észlel. Ezzel a lekérdezéssel derítheti fel a nem támogatott adattípusokat a meglévő SQL-sémában.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Megkerülő megoldások nem támogatott adattípusok esetén

A következő lista azokat az adattípusokat tartalmazza, amelyeket a SQL Data Warehouse nem támogat, és a nem támogatott adattípusok helyett használható alternatívákat biztosít.

| Nem támogatott adattípus | Áthidaló megoldás |
| --- | --- |
| [geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [földrajz](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [lemezkép](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [szöveg](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Oszlop felosztása több erősen gépelt oszlopba. |
| [tábla](/sql/t-sql/data-types/table-transact-sql) |Ideiglenes táblákra konvertál. |
| [időbélyeg](/sql/t-sql/data-types/date-and-time-types) |A kód újradolgozása a [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) és a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) függvény használatához. Alapértelmezés szerint csak állandók támogatottak, ezért a CURRENT_TIMESTAMP nem definiálható alapértelmezett korlátozásként. Ha a sor verziószámát egy időbélyeg típusú oszlopból kell áttelepítenie, akkor a [bináris](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) vagy a [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) értéket használja a not NULL vagy a null értékű sor verziószámához. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [felhasználó által definiált típus](/sql/relational-databases/native-client/features/using-user-defined-types) |Ha lehetséges, váltson vissza a natív adattípusra. |
| alapértelmezett értékek | Az alapértelmezett értékek csak a literálokat és az állandókat támogatják. |


## <a name="next-steps"></a>További lépések
További információ a táblázatok létrehozásáról: [táblázat – áttekintés](sql-data-warehouse-tables-overview.md).
