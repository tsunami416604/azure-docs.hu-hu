---
title: Egy Azure data warehouse adattárházzal erőforrások |} Microsoft Docs
description: Fejlesztői fogalmak, tervezési döntéseit, javaslatok és az SQL Data Warehouse kódolási eljárások.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: d9a272b2f43e080cd44b7179fe6f9dc55507142b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Tervezési döntések és az SQL Data Warehouse kódolási eljárások
Tekintse meg a fejlesztési cikkeiben jobb megértése érdekében tervezési döntések javaslatok és kódolási eljárások az SQL Data Warehouse keresztül.

## <a name="key-design-decisions"></a>Tervezési döntések
A következő cikkekben fogalmakat és a tervezési döntések egy elosztott data warehouse az SQL Data Warehouse adattárházzal:

* [Kapcsolatok száma][connections]
* [concurrency][concurrency]
* [Tranzakciók][transactions]
* [Felhasználó által definiált sémák][user-defined schemas]
* [elosztása][table distribution]
* [-táblák indexeit][table indexes]
* [tábla partíciók][table partitions]
* [CTAS][CTAS]
* [Statisztika][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Javaslatok és kódolási eljárások
A cikkekben, konkrét programozási módszerek, tippeket és javaslatokat az SQL Data Warehouse adattárházzal:

* [Tárolt eljárások][stored procedures]
* [Címkék][labels]
* [nézetek][views]
* [az ideiglenes táblák][temporary tables]
* [dynamic SQL][dynamic SQL]
* [hurkolás][looping]
* [Beállítások szerint kell csoportosítani][group by options]
* [A változó-hozzárendelés][variable assignment]

## <a name="next-steps"></a>További lépések
Hivatkozás kapcsolatos további információkért lásd: [SQL Data Warehouse T-SQL utasítás](sql-data-warehouse-reference-tsql-statements.md).

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
