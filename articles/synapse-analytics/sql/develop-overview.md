---
title: Erőforrások a szinapszis SQL-funkciók fejlesztéséhez
description: Fejlesztési fogalmak, tervezési döntések, javaslatok és kódolási technikák a szinapszis SQL-hez.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 85b0137f8d89def2f38ffe82199950c9158888d0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070050"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Tervezési döntések és kódolási technikák a szinapszis SQL-funkciókhoz az Azure szinapszis Analyticsben
Ebből a cikkből megtudhatja, hogyan használhatók az SQL Pool és az SQL on-demand (előzetes verzió) funkciói a szinapszis SQL-ben. Az ajánlott cikkek két szakaszra oszlanak: a legfontosabb tervezési döntések, a fejlesztési és kódolási technikák.

Ennek a cikknek a célja, hogy segítséget nyújtson az optimális technikai megközelítés kialakításában a szinapszis Analytics szolgáltatásban található szinapszis SQL-összetevőkhöz.

## <a name="key-design-decisions"></a>Kulcsfontosságú tervezési döntések
Az alábbi cikkek kiemelik a szinapszisok SQL-fejlesztésével kapcsolatos fogalmakat és tervezési döntéseket:

| Cikk | SQL-készlet | Igény szerinti SQL |
| ------- | -------- | ------------- |
| [Kapcsolatok](connect-overview.md)                    | Yes | Igen |
| [Erőforrás-osztályok és Egyidejűség](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | Nem |
| [Tranzakciók](develop-transactions.md)              | Yes | Nem |
| [Felhasználó által definiált sémák](develop-user-defined-schemas.md) | Yes | Igen |
| [a táblaelosztással](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Yes | Nem |
| [táblaindexekkel](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Yes | Nem |
| [Tábla partíciói](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | Nem |
| [Statisztika](develop-tables-statistics.md)            | Yes | Igen |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | Nem |
| [Külső táblák](develop-tables-external-tables.md) | Yes | Igen |
| [CETAS](develop-tables-cetas.md)                     | Yes | Igen |


## <a name="recommendations"></a>Javaslatok

Az alábbiakban megtalálhatja azokat az alapvető cikkeket, amelyek kiemelik az egyes kódolási technikákat, tippeket és javaslatokat a fejlesztéshez:

| Cikk | SQL-készlet | Igény szerinti SQL |
| ------- | -------- | ------------- |
| [Tárolt eljárások](develop-stored-procedures.md)  | Yes                | Nem                      |
| [Címkék](develop-label.md)                           | Yes                | Nem                      |
| [Nézetek](develop-views.md)                             | Yes                | Igen                     |
| [ideiglenes táblákkal](develop-tables-temporary.md)       | Yes                | Igen                     |
| [Dinamikus SQL](develop-dynamic-sql.md)                 | Yes                | Igen                     |
| [Hurkolás](develop-loops.md)                         | Yes                | Igen                     |
| [Csoportosítás beállítások szerint](develop-group-by-options.md)       | Yes                | Nem                      |
| [Változó-hozzárendelés](develop-variable-assignment.md) | Yes                | Igen                     |

## <a name="next-steps"></a>Következő lépések
További információ: [SQL Pool T-SQL-utasítások](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

