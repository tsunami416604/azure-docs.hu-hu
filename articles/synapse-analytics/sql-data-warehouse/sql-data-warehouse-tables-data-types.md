---
title: Táblázatos adattípusok a dedikált SQL-készletben
description: Javaslatok táblázatos adattípusok definiálásához a dedikált SQL-készletben.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 5bb1d10978171b93ee697b37ee9ac0702d3f898c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313071"
---
# <a name="table-data-types-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Táblázatos adattípusok a dedikált SQL-készlethez az Azure szinapszis Analyticsben 

Ebben a cikkben a táblázatos adattípusok a dedikált SQL-készletben való definiálásával kapcsolatos javaslatok szerepelnek.

## <a name="supported-data-types"></a>Támogatott adattípusok

A dedikált SQL-készlet a leggyakrabban használt adattípusokat támogatja. A támogatott adattípusok listáját lásd: [adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true#DataTypes) a CREATE TABLE utasításban.

## <a name="minimize-row-length"></a>Sor hosszának csökkentése

Az adattípusok méretének minimalizálása lerövidíti a sor hosszát, ami jobb lekérdezési teljesítményt eredményez. Használja az adataihoz használható legkisebb adattípust.

- Kerülje a nagyméretű alapértelmezett hosszúságú karakteres oszlopok definiálását. Ha például a leghosszabb érték 25 karakter, akkor adja meg az oszlopot VARCHAR-ként (25).
- Kerülje a [NVARCHAR](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatát, ha csak varchar-ra van szüksége.
- Ha lehetséges, használjon NVARCHAR (4000) vagy VARCHAR (8000) értéket a NVARCHAR (MAX) vagy a VARCHAR (MAX) helyett.

Ha a táblák betöltéséhez a-alapú külső táblákat használ, a táblázat megadott hossza nem haladhatja meg az 1 MB-ot. Ha a változó hosszúságú adatokkal rendelkező sorok 1 MB-nál nagyobb méretűek, a sort betöltheti a BCP-vel, de nem a Base értékkel.

## <a name="identify-unsupported-data-types"></a>Nem támogatott adattípusok azonosítása

Ha másik SQL-adatbázisból telepíti át az adatbázist, olyan adattípusokat talál, amelyek nem támogatottak a dedikált SQL-készletben. A következő lekérdezés használatával derítheti fel a nem támogatott adattípusokat a meglévő SQL-sémában:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Megkerülő megoldások nem támogatott adattípusok esetén

A következő lista azokat az adattípusokat tartalmazza, amelyeket a dedikált SQL-készlet nem támogat, és hasznos alternatívákat biztosít a nem támogatott adattípusokhoz.

| Nem támogatott adattípus | Áthidaló megoldás |
| --- | --- |
| [geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [földrajz](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(4000) |
| [lemezkép](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [szöveg](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Oszlop felosztása több erősen gépelt oszlopba. |
| [table](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Ideiglenes táblákra konvertál. |
| [időbélyeg](/sql/t-sql/data-types/date-and-time-types) |A [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) és a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) függvény használatára vonatkozó kód újradolgozása. Alapértelmezés szerint csak állandók támogatottak, így current_timestamp nem lehet alapértelmezett korlátozásként definiálni. Ha a sor verziószámát egy időbélyeg típusú oszlopból kell áttelepítenie, a [bináris](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) vagy a [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) értéket használja a Not Null vagy a null sor verziószámának értékéhez. |
| [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [felhasználó által definiált típus](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Ha lehetséges, váltson vissza a natív adattípusra. |
| alapértelmezett értékek | Az alapértelmezett értékek csak a literálokat és az állandókat támogatják. |

## <a name="next-steps"></a>Következő lépések

További információ a táblázatok létrehozásáról: [táblázat – áttekintés](sql-data-warehouse-tables-overview.md).
