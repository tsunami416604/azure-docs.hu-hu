---
title: Azure-beli adattárház fejlesztéséhez szükséges erőforrások
description: Fejlesztési fogalmak, tervezési döntések, javaslatok és kódolási technikák a SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a6cb7937bdd5dea9eb1a48b2b350db9077431fe0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645612"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Tervezési döntések és kódolási technikák a SQL Data Warehouse
Tekintse át ezeket a fejlesztési cikkeket, hogy jobban megértse a legfontosabb tervezési döntéseket, ajánlásokat és kódolási technikákat SQL Data Warehouse.

## <a name="key-design-decisions"></a>Kulcsfontosságú tervezési döntések
A következő cikkek kiemelik az elosztott adattárház SQL Data Warehouse használatával történő fejlesztésével kapcsolatos fogalmakat és kialakítási döntéseket:

* [kapcsolatok][connections]
* [Egyidejűség][concurrency]
* [tranzakciók][transactions]
* [felhasználó által definiált sémák][user-defined schemas]
* [tábla eloszlása][table distribution]
* [tábla indexei][table indexes]
* [tábla partíciói][table partitions]
* [CTAS][CTAS]
* [statisztika][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Fejlesztési javaslatok és kódolási technikák
Ezek a cikkek konkrét kódolási technikákat, tippeket és javaslatokat mutatnak a SQL Data Warehouse fejlesztéséhez:

* [tárolt eljárások][stored procedures]
* [Címkék][labels]
* [kilátással][views]
* [ideiglenes táblák][temporary tables]
* [dinamikus SQL][dynamic SQL]
* [hurkolás][looping]
* [csoportosítási beállítások][group by options]
* [változó-hozzárendelés][variable assignment]

## <a name="next-steps"></a>További lépések
További információ: [SQL Data Warehouse T-SQL-utasítások](sql-data-warehouse-reference-tsql-statements.md).

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
