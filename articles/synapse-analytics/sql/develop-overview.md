---
title: Erőforrások a szinapszis SQL-funkciók fejlesztéséhez
description: Fejlesztési fogalmak, tervezési döntések, javaslatok és kódolási technikák a szinapszis SQL-hez.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: d47b4847a12b63532e44a8a1a47101dd065f811b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446595"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Tervezési döntések és kódolási technikák a szinapszis SQL-funkciókhoz az Azure szinapszis Analyticsben
Ebben a cikkben a szinapszis SQL-hez készült dedikált SQL Pool és kiszolgáló nélküli SQL Pool függvények erőforrásainak listáját találja. Az ajánlott cikkek két szakaszra oszlanak: a legfontosabb tervezési döntések, a fejlesztési és kódolási technikák.

A cikk célja, hogy segítsen az optimális technikai megközelítés kialakításában az Azure szinapszis Analytics szolgáltatásban található szinapszis SQL-összetevőkhöz.

## <a name="key-design-decisions"></a>Kulcsfontosságú tervezési döntések
Az alábbi cikkek kiemelik a szinapszisok SQL-fejlesztésével kapcsolatos fogalmakat és tervezési döntéseket:

| Cikk | dedikált SQL-készlet | kiszolgáló nélküli SQL-készlet |
| ------- | -------- | ------------- |
| [Kapcsolatok](connect-overview.md)                    | Igen | Igen |
| [Erőforrás-osztályok és Egyidejűség](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Igen    | Nem |
| [Tranzakciók](develop-transactions.md)              | Igen | Nem |
| [Felhasználó által definiált sémák](develop-user-defined-schemas.md) | Igen | Igen |
| [a táblaelosztással](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Igen | Nem |
| [táblaindexekkel](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Igen | Nem |
| [Tábla partíciói](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Igen | Nem |
| [Statisztika](develop-tables-statistics.md)            | Igen | Igen |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Igen | Nem |
| [Külső táblák](develop-tables-external-tables.md) | Igen | Igen |
| [CETAS](develop-tables-cetas.md)                     | Igen | Igen |


## <a name="recommendations"></a>Javaslatok

Az alábbiakban megtalálhatja azokat az alapvető cikkeket, amelyek kiemelik az egyes kódolási technikákat, tippeket és javaslatokat a fejlesztéshez:

| Cikk | dedikált SQL-készlet | kiszolgáló nélküli SQL-készlet |
| ------- | -------- | ------------- |
| [Tárolt eljárások](develop-stored-procedures.md)  | Igen                | Nem                      |
| [Címkék](develop-label.md)                           | Igen                | Nem                      |
| [Nézetek](develop-views.md)                             | Igen                | Igen                     |
| [ideiglenes táblákkal](develop-tables-temporary.md)       | Igen                | Igen                     |
| [Dinamikus SQL](develop-dynamic-sql.md)                 | Igen                | Igen                     |
| [Hurkolás](develop-loops.md)                         | Igen                | Igen                     |
| [Csoportosítás beállítások szerint](develop-group-by-options.md)       | Igen                | Nem                      |
| [Változó-hozzárendelés](develop-variable-assignment.md) | Igen                | Igen                     |

## <a name="next-steps"></a>Következő lépések
További információ: [SQL Pool T-SQL-utasítások](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

