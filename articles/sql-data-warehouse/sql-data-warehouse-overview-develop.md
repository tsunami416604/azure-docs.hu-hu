---
title: Az Azure-beli adattárház fejlesztéséhez használható forrásanyagok |} A Microsoft Docs
description: Fejlesztői fogalmak, tervezési döntéseket, javaslatokat és az SQL Data Warehouse programozási technikákat.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 08/29/2018
ms.author: kevinvngo
ms.reviewer: igorstan
ms.openlocfilehash: 8c04e0409faa3b63a8a2957284ac7aa96740ae03
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468376"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Tervezési döntéseket és a kódolási módszereket az SQL Data warehouse-hoz
Figyelje a fejlesztéssel kapcsolatos cikket jobb megértése érdekében hozott kulcsfontosságú, a javaslatokat és a kódolási módszereket az SQL Data Warehouse segítségével.

## <a name="key-design-decisions"></a>Fő tervezési döntéseket hozhat
A következő cikkek jelölje ki a fogalmakat és a egy elosztott data warehouse az SQL Data Warehouse fejlesztéséhez tervezési döntéseket:

* [Kapcsolatok][connections]
* [concurrency][concurrency]
* [Tranzakciók][transactions]
* [felhasználó által definiált sémák][user-defined schemas]
* [a táblaelosztással][table distribution]
* [táblaindexekkel][table indexes]
* [Táblapartíciók][table partitions]
* [CTAS][CTAS]
* [statisztika][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>Javaslatok és a kódolási módszereket
Ezek a cikkek jelöljön ki adott programozási technikákat, tippeket és javaslatokat az SQL Data Warehouse fejlesztéséhez:

* [Tárolt eljárások][stored procedures]
* [labels][labels]
* [Nézetek][views]
* [Az ideiglenes táblák][temporary tables]
* [dynamic SQL][dynamic SQL]
* [hurkolás][looping]
* [Csoportosítás beállítások szerint][group by options]
* [változó-hozzárendelés][variable assignment]

## <a name="next-steps"></a>További lépések
Hivatkozás kapcsolatos további információkért lásd: [SQL Data Warehouse a T-SQL utasításokkal](sql-data-warehouse-reference-tsql-statements.md).

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
