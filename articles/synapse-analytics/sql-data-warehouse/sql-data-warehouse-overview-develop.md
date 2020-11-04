---
title: Erőforrások egy dedikált SQL-készlet fejlesztéséhez az Azure szinapszis Analytics szolgáltatásban
description: Fejlesztési fogalmak, tervezési döntések, javaslatok és kódolási technikák egy dedikált SQL-készlethez az Azure szinapszis Analyticsben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f158655cd7aeba647480325966c7f0cfcf65b13c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322134"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Tervezési döntések és kódolási technikák egy dedikált SQL-készlethez az Azure szinapszis Analyticsben 

 Ebben a cikkben további forrásokat talál, amelyek segítenek jobban megérteni a legfontosabb tervezési döntéseket, javaslatokat és kódolási technikákat egy dedikált SQL-készlethez az Azure Szinapszisban.

## <a name="key-design-decisions"></a>Kulcsfontosságú tervezési döntések

Az alábbi cikkek kiemelik azokat az alapfogalmakat és kialakítási döntéseket, amelyekkel elosztott adattárházat fejleszthet az Azure szinapszis dedikált SQL Pool funkciójának használatával:

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

A következő cikkekben speciális kódolási technikák, tippek és javaslatok szerepelnek a dedikált SQL-készlet fejlesztéséhez:

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
