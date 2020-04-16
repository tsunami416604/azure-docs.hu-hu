---
title: T-SQL nézetek a Synapse SQL használatával
description: Tippek a T-SQL nézetek használatához és megoldások fejlesztéséhez a Synapse SQL-rel.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428653"
---
# <a name="t-sql-views-using-synapse-sql"></a>T-SQL nézetek a Synapse SQL használatával
Ebben a cikkben tippeket talál a T-SQL nézetek használatához és a Synapse SQL-vel kapcsolatos megoldások fejlesztéséhez. 

## <a name="why-use-views"></a>Miért érdemes nézeteket használni?

A nézetek számos különböző módon használhatók a megoldás minőségének javítására.  Ez a cikk néhány példát mutat be arra, hogyan gazdagíthatja a megoldást nézetekkel, és tartalmazza a figyelembe veendő korlátozásokat.

### <a name="sql-pool---create-view"></a>SQL-készlet – nézet létrehozása

> [!NOTE]
> **SQL-készlet**: A CREATE VIEW szintaxisa nem szerepel ebben a cikkben. További információt a [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dokumentációjában talál.

### <a name="sql-on-demand-preview---create-view"></a>SQL on-demand (előzetes verzió) – nézet létrehozása

> [!NOTE]
> **SQL on-demand**: A CREATE VIEW szintaxisa nem szerepel ebben a cikkben. További információt a [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dokumentációjában talál.

## <a name="architectural-abstraction"></a>Építészeti absztrakció

Gyakori alkalmazásminta a táblák létrehozása [tábla as select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) használatával történő újralétrehozása, amelyet egy objektum átnevezési minta követ az adatok betöltése közben.

A következő példa új dátumrekordokat ad hozzá egy dátumdimenzióhoz. Figyelje meg, hogyan jön létre először egy új tábla (DimDate_New) a tábla eredeti verziójának lecserélése érdekében.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Ne feledje, hogy ez a megközelítés azt eredményezheti, hogy a táblák megjelennek és eltűnnek a felhasználó nézetéből, és a "tábla nem létezik" hibaüzeneteket tesz. A nézetek segítségével a felhasználók egységes bemutatóréteget kaphatnak, miközben az alapul szolgáló objektumokat átnevezi.

Azáltal, hogy a nézeteken keresztül biztosít hozzáférést az adatokhoz, a felhasználóknak nincs szükségük az alapul szolgáló táblák láthatóságára. A konzisztens felhasználói élmény mellett ez a réteg biztosítja, hogy az elemzési tervezők fejleszthessék az adatmodellt. Az alapul szolgáló táblák továbbfejlesztésének képessége azt jelenti, hogy a tervezők a CTAS segítségével maximalizálhatják a teljesítményt az adatbetöltési folyamat során.

## <a name="performance-optimization"></a>Teljesítményoptimalizálás

A nézetek a táblák közötti teljesítményoptimalizált illesztések kényszerítésére is használhatók. Egy nézet például tartalmazhat egy redundáns terjesztési kulcsot az illesztési feltételek részeként az adatok mozgásának minimalizálása érdekében.

Egy adott lekérdezés kényszerítése vagy a célzásillesztés a T-SQL-nézetek használatának másik előnye. Mint ilyen, a nézetek képesség biztosítja, hogy az illesztések mindig optimális módon történik. Elkerülheti annak szükségességét, hogy a felhasználók emlékezzenek az illesztésük megfelelő felépítésére.

## <a name="limitations"></a>Korlátozások

A Synapse SQL nézetek csak metaadatként tárolódnak. Ennek következtében a következő lehetőségek nem érhetők el:

* Nincs sémakötési lehetőség
* Az alaptáblák nem frissíthetők a nézetben
* A nézetek nem hozhatók létre ideiglenes táblákon keresztül
* Nincs támogatás a EXPAND / NOEXPAND tippekhez
* A Szinapszis SQL-ben nincsenek indexelt nézetek

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [Synapse SQL-fejlesztés áttekintése című témakörben talál.](develop-overview.md)



