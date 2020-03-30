---
title: Adattípusok definiálása
description: Javaslatok a táblaadat-típusok definiálására az Azure SQL Data Warehouse-ban.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 020b6fc08dad0dacb51215eca6f7baf127a9dba6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351316"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Táblaadat-típusok az Azure SQL Data Warehouse-ban
Javaslatok a táblaadat-típusok definiálására az Azure SQL Data Warehouse-ban. 

## <a name="what-are-the-data-types"></a>Mik az adattípusok?

Az SQL Data Warehouse támogatja a leggyakrabban használt adattípusokat. A támogatott adattípusok listáját a CREATE TABLE utasítás [adattípusai](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) című témakörben található. 

## <a name="minimize-row-length"></a>Sorhossz kicsinyítése
Az adattípusok méretének minimalizálása lerövidíti a sorhosszát, ami jobb lekérdezési teljesítményt eredményez. Használja az adatokhoz használható legkisebb adattípust. 

- Kerülje a nagy alapértelmezett hosszúságú karakteroszlopok definiálását. Ha például a leghosszabb érték 25 karakter, akkor az oszlopot VARCHAR(25) néven definiálja. 
- Ne használja az [NVARCHAR][NVARCHAR]-t, ha csak VARCHAR-ra van szüksége.
- Ha lehetséges, az NVARCHAR(4000) vagy a VARCHAR(8000) függvényt használja az NVARCHAR(MAX) vagy a VARCHAR(MAX) helyett.

Ha a táblák betöltéséhez PolyBase külső táblákat használ, a táblasor megadott hossza nem haladhatja meg az 1 MB-ot. Ha egy változó hosszúságú adattal rendelkező sor meghaladja az 1 MB-ot, a sort bcp-vel töltheti be, de a PolyBase segítségével nem.

## <a name="identify-unsupported-data-types"></a>Nem támogatott adattípusok azonosítása
Ha az adatbázist egy másik SQL-adatbázisból telepíti át, előfordulhat, hogy olyan adattípusokat tapasztal, amelyeket az SQL Data Warehouse nem támogat. Ezzel a lekérdezéssel nem támogatott adattípusokat fedezhet fel a meglévő SQL-sémában.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Kerülő megoldások a nem támogatott adattípusokhoz

Az alábbi lista azokat az adattípusokat mutatja be, amelyeket az SQL Data Warehouse nem támogat, és a nem támogatott adattípusok helyett használható alternatívákat biztosít.

| Nem támogatott adattípus | Áthidaló megoldás |
| --- | --- |
| [Geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary között](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Földrajz](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary között](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchia](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Kép](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary között](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Szöveg](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [nszöveg](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Az oszlopok felosztása több erősen beírt oszlopra. |
| [Táblázat](/sql/t-sql/data-types/table-transact-sql) |Átalakítás ideiglenes táblákká. |
| [Időbélyeg](/sql/t-sql/data-types/date-and-time-types) |A [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) és a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) függvény használatához használt átdolgozási kód. Csak az állandók támogatottak alapértelmezettként, ezért current_timestamp nem definiálhatók alapértelmezett megkötésként. Ha a sorverzióértékeket időbélyeggel beírt oszlopból kell áttelepítenie, akkor a [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) vagy [a VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) parancsot használja a NOT NULL vagy null sorverziós verzióértékekhez. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [felhasználó által definiált típus](/sql/relational-databases/native-client/features/using-user-defined-types) |Ha lehetséges, konvertáljon vissza a natív adattípusra. |
| alapértelmezett értékek | Az alapértelmezett értékek csak a konstansok és állandók at támogatják. |


## <a name="next-steps"></a>További lépések
A táblák fejlesztéséről a Táblázat – áttekintés című [témakörben olvashat bővebben.](sql-data-warehouse-tables-overview.md)
