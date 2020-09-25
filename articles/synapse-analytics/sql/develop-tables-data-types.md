---
title: Táblázat adattípusai a szinapszis SQL-ben
description: Javaslatok a Table adattípusok definiálásához a szinapszis SQL-ben.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: dec5d73c0c121a1e4995bd66500fc08fde3f2f10
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288749"
---
# <a name="table-data-types-in-synapse-sql"></a>Táblázat adattípusai a szinapszis SQL-ben

Ebből a cikkből megtudhatja, hogyan határozhat meg táblázatos adattípusokat a szinapszis SQL-ben. 

## <a name="data-types"></a>Adattípusok

A szinapszis SQL a leggyakrabban használt adattípusokat támogatja. A támogatott adattípusok listáját lásd: [adattípusok](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes&preserve-view=true) a CREATE TABLE utasításban. 

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
| [geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [földrajz](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(4000) |
| [lemezkép](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [szöveg](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Oszlop felosztása több erősen gépelt oszlopba. |
| [tábla](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Ha az SQL-készletet használja, az ideiglenes táblákra válthat. SQL (előzetes verzió) használata esetén érdemes lehet az [CETAS](../sql/develop-tables-cetas.md)használatával tárolni az adattárolást. |
| [időbélyeg](/sql/t-sql/data-types/date-and-time-types) |A [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) és a [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) függvény használatára vonatkozó kód újradolgozása. Alapértelmezés szerint csak állandók támogatottak, ezért current_timestamp nem definiálható alapértelmezett korlátozásként. Ha a sor verziószámát egy időbélyeg típusú oszlopból kell áttelepítenie, akkor a [bináris](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) vagy a [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)(8) értéket használja a not NULL vagy a null értékű sor verziószámához. |
| [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| [felhasználó által definiált típus](/sql/relational-databases/native-client/features/using-user-defined-types&preserve-view=true) |Ha lehetséges, váltson vissza a natív adattípusra. |
| alapértelmezett értékek | Az alapértelmezett értékek csak a literálokat és az állandókat támogatják. |

## <a name="next-steps"></a>Következő lépések

További információ a táblázatok létrehozásáról: [táblázat – áttekintés](develop-overview.md).
