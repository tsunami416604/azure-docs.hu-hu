---
title: Az Azure Synapse Analytics adattárházának fejlesztéséhez szükséges erőforrások
description: Fejlesztési koncepciók, tervezési döntések, ajánlások és kódolási technikák az SQL Data Warehouse számára.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa0f5fd631dfa3e4deca4853c27a667fcf312fec
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350280"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Tervezési döntések és kódolási technikák egy adattárházhoz az Azure Synapse Analytics szolgáltatásban 
 Ebben a cikkben további forrásokat talál, amelyek segítenek jobban megérteni az Azure Synapse-ban lévő adatraktár kulcsfontosságú tervezési döntéseit, javaslatait és kódolási technikáit.

## <a name="key-design-decisions"></a>Legfontosabb tervezési döntések
Az alábbi cikkek az Azure Synapse SQL Analytics-funkciójának használatával az elosztott adattárház fejlesztésére vonatkozó fogalmakat és tervezési döntéseket emelik ki:

* [Kapcsolatok](sql-data-warehouse-connect-overview.md)
* [Konkurencia](resource-classes-for-workload-management.md)
* [Tranzakciók](sql-data-warehouse-develop-transactions.md)
* [felhasználó által definiált sémák](sql-data-warehouse-develop-user-defined-schemas.md)
* [tábla elosztása](sql-data-warehouse-tables-distribute.md)
* [táblaindexek](sql-data-warehouse-tables-index.md)
* [táblapartíciók](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [statisztika](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Fejlesztési ajánlások és kódolási technikák
Az alábbi cikkek konkrét kódolási technikákat, tippeket és javaslatokat tartalmaznak az SQL Analytics adattárházának fejlesztéséhez:

* [tárolt eljárások](sql-data-warehouse-develop-stored-procedures.md)
* [Címkék](sql-data-warehouse-develop-label.md)
* [Kilátás nyílik](sql-data-warehouse-develop-views.md)
* [ideiglenes táblák](sql-data-warehouse-tables-temporary.md)
* [dinamikus SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [Hurkolás](sql-data-warehouse-develop-loops.md)
* [csoportosítás beállítások szerint](sql-data-warehouse-develop-group-by-options.md)
* [változó hozzárendelés](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>További lépések
További információt a [T-SQL utasítások című témakörben talál.](sql-data-warehouse-reference-tsql-statements.md)
