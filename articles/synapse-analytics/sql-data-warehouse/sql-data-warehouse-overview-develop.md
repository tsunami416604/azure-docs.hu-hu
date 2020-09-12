---
title: Erőforrások a szinapszis SQL-készlet fejlesztéséhez az Azure szinapszis Analyticsben
description: Fejlesztési fogalmak, tervezési döntések, javaslatok és kódolási technikák az Azure szinapszis Analytics szolgáltatáshoz.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 95f712f196c37650b52220c9e34f6cb6b50bff23
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460609"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Tervezési döntések és kódolási technikák a szinapszis SQL-készlethez az Azure szinapszis Analyticsben 
 Ebben a cikkben további forrásokat talál, amelyek segítségével jobban megismerheti az Azure Szinapszisban található SQL-készletek legfontosabb tervezési döntéseit, javaslatait és kódolási módszereit.

## <a name="key-design-decisions"></a>Kulcsfontosságú tervezési döntések
Az alábbi cikkek kiemelik az elosztott adattárház fejlesztésére vonatkozó fogalmakat és tervezési döntéseket az Azure szinapszis SQL Pool funkciójának használatával:

* [kapcsolatok](../sql/connect-overview.md)
* [Egyidejűség](resource-classes-for-workload-management.md)
* [tranzakciók](sql-data-warehouse-develop-transactions.md)
* [felhasználó által definiált sémák](sql-data-warehouse-develop-user-defined-schemas.md)
* [tábla eloszlása](sql-data-warehouse-tables-distribute.md)
* [tábla indexei](sql-data-warehouse-tables-index.md)
* [tábla partíciói](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statisztikák](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Fejlesztési javaslatok és kódolási technikák
Az alábbi cikkekben speciális kódolási technikák, tippek és javaslatok szerepelnek az SQL-készletek fejlesztéséhez:

* [tárolt eljárások](sql-data-warehouse-develop-stored-procedures.md)
* [Címkék](sql-data-warehouse-develop-label.md)
* [kilátással](performance-tuning-materialized-views.md)
* [ideiglenes táblák](sql-data-warehouse-tables-temporary.md)
* [dinamikus SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [hurkolás](sql-data-warehouse-develop-loops.md)
* [csoportosítási beállítások](sql-data-warehouse-develop-group-by-options.md)
* [változó-hozzárendelés](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Következő lépések
További információ: [T-SQL-utasítások](sql-data-warehouse-reference-tsql-statements.md).
