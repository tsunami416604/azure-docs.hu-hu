---
title: Erőforrások az adattárház fejlesztéséhez az Azure szinapszis Analytics szolgáltatásban
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
ms.openlocfilehash: 0f8967eab1be8351c50035d8afa784aa7afd79e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153315"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Tervezési döntések és kódolási technikák egy adattárházhoz az Azure szinapszis Analyticsben 
 Ebben a cikkben további forrásokat talál, amelyek segítségével jobban megismerheti a legfontosabb tervezési döntéseket, javaslatokat és kódolási technikákat az Azure Szinapszisban található adattárházak esetében.

## <a name="key-design-decisions"></a>Kulcsfontosságú tervezési döntések
Az alábbi cikkek a következő cikkekben ismertetik az elosztott adattárház fejlesztésével kapcsolatos fogalmakat és kialakítási döntéseket az Azure szinapszis SQL Analytics szolgáltatásával:

* [kapcsolatok](sql-data-warehouse-connect-overview.md)
* [Egyidejűség](resource-classes-for-workload-management.md)
* [tranzakciók](sql-data-warehouse-develop-transactions.md)
* [felhasználó által definiált sémák](sql-data-warehouse-develop-user-defined-schemas.md)
* [tábla eloszlása](sql-data-warehouse-tables-distribute.md)
* [tábla indexei](sql-data-warehouse-tables-index.md)
* [tábla partíciói](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statisztika](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Fejlesztési javaslatok és kódolási technikák
Az alábbi cikkekben konkrét kódolási technikák, tippek és javaslatok szerepelnek az adatraktár SQL Analytics szolgáltatással történő fejlesztéséhez:

* [tárolt eljárások](sql-data-warehouse-develop-stored-procedures.md)
* [Címkék](sql-data-warehouse-develop-label.md)
* [kilátással](sql-data-warehouse-develop-views.md)
* [ideiglenes táblák](sql-data-warehouse-tables-temporary.md)
* [dinamikus SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [hurkolás](sql-data-warehouse-develop-loops.md)
* [csoportosítási beállítások](sql-data-warehouse-develop-group-by-options.md)
* [változó-hozzárendelés](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Következő lépések
További információ: [T-SQL-utasítások](sql-data-warehouse-reference-tsql-statements.md).
