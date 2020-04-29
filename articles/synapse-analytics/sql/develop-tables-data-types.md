---
title: Adattípusok definiálása
description: Javaslatok a Table adattípusok definiálásához a szinapszis SQL-ben.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 312c58a7df09ebe3e0bdf0749f902199723a331b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429004"
---
# <a name="table-data-types-in-synapse-sql"></a>Táblázat adattípusai a szinapszis SQL-ben

Javaslatok a Table adattípusok definiálásához a szinapszis SQL-ben. 

## <a name="what-are-the-data-types"></a>Mik az adattípusok?

A szinapszis SQL a leggyakrabban használt adattípusokat támogatja. A támogatott adattípusok listáját lásd: [adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) a CREATE TABLE utasításban. 

## <a name="minimize-row-length"></a>Sor hosszának csökkentése

Az adattípusok méretének minimalizálása lerövidíti a sor hosszát, ami jobb lekérdezési teljesítményt eredményez. Használja az adataihoz használható legkisebb adattípust.

- Kerülje a nagyméretű alapértelmezett hosszúságú karakteres oszlopok definiálását. Ha például a leghosszabb érték 25 karakter, akkor adja meg az oszlopot VARCHAR-ként (25).
- Kerülje a [NVARCHAR] [NVARCHAR] használatát, ha csak VARCHAR-ra van szüksége.
- Ha lehetséges, használjon NVARCHAR (4000) vagy VARCHAR (8000) értéket a NVARCHAR (MAX) vagy a VARCHAR (MAX) helyett.

> [!NOTE]
> Ha az SQL-készlet tábláinak betöltéséhez használ albase külső táblákat, a tábla sorainak megadott hossza nem haladhatja meg az 1 MB-ot. Ha a változó hosszúságú adatokkal rendelkező sorok 1 MB-nál nagyobb méretűek, a sort betöltheti a BCP-vel, de nem a Base értékkel.

## <a name="identify-unsupported-data-types"></a>Nem támogatott adattípusok azonosítása

Ha egy másik SQL-adatbázisból telepíti át az adatbázist, a szinapszis SQL-ben nem támogatott adattípusok jelenhetnek meg. Ezzel a lekérdezéssel derítheti fel a nem támogatott adattípusokat a meglévő SQL-sémában.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Megkerülő megoldások nem támogatott adattípusok esetén

A következő lista azokat az adattípusokat tartalmazza, amelyeket a szinapszis SQL nem támogat, és a nem támogatott adattípusok helyett használható alternatívákat is biztosít.

| Nem támogatott adattípus | Áthidaló megoldás |
| --- | --- |
| [geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [földrajz](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [lemezkép](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [szöveg](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Oszlop felosztása több erősen gépelt oszlopba. |
| [tábla](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Ha az SQL-készletet használja, az ideiglenes táblákra válthat. SQL (előzetes verzió) használata esetén érdemes lehet az [CETAS](../sql/develop-tables-cetas.md)használatával tárolni az adattárolást. |
| [időbélyeg](/sql/t-sql/data-types/date-and-time-types) |A [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) és a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) függvény használatára vonatkozó kód újradolgozása. Alapértelmezés szerint csak állandók támogatottak, ezért current_timestamp nem definiálható alapértelmezett korlátozásként. Ha a sor verziószámát egy időbélyeg típusú oszlopból kell áttelepítenie, akkor a [bináris](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) vagy a [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)(8) értéket használja a not NULL vagy a null értékű sor verziószámához. |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [felhasználó által definiált típus](/sql/relational-databases/native-client/features/using-user-defined-types) |Ha lehetséges, váltson vissza a natív adattípusra. |
| alapértelmezett értékek | Az alapértelmezett értékek csak a literálokat és az állandókat támogatják. |

## <a name="next-steps"></a>További lépések

További információ a táblázatok létrehozásáról: [táblázat – áttekintés](develop-overview.md).
