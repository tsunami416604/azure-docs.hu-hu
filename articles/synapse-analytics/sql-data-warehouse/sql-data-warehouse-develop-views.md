---
title: T-SQL-nézetek használata
description: Tippek a T-SQL-nézetek használatához és megoldások fejlesztéséhez a szinapszis SQL-készletben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212292"
---
# <a name="views-in-synapse-sql-pool"></a>A szinapszis SQL-készlet nézetei

A nézetek számos különböző módon használhatók a megoldás minőségének javítására.

Az SQL-készlet mind a standard, mind a jelentős nézeteket támogatja. Mindkettő olyan virtuális táblákat hoz létre, amelyek a SELECT kifejezésekkel lettek létrehozva, és a lekérdezéseket logikai táblázatként mutatja

A nézetek összefoglalják a közös adatszámítások összetettségét, és felvesznek egy absztrakt réteget a számítási változásokhoz, így nincs szükség a lekérdezések újraírására.

## <a name="standard-view"></a>Normál nézet

A normál nézet minden alkalommal kiszámítja az adatmegjelenítést, amikor a nézet használatban van. A lemezen nem találhatók adattárolók. A személyek általában szabványos nézeteket használnak olyan eszközként, amely segít megszervezni a logikai objektumokat és lekérdezéseket egy adatbázisban.

Normál nézet használatához a lekérdezésnek közvetlen hivatkozást kell tartalmaznia. További információ: [create View](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dokumentáció.

Az SQL-készlet nézetei csak metaadatokként vannak tárolva. Ennek következtében a következő beállítások nem érhetők el:

* Nincs séma-kötési beállítás
* Az alaptáblák nem frissíthetők a nézeten keresztül
* Ideiglenes táblákon nem hozhatók létre nézetek
* A kibontási/kibontási útmutatók nem támogatottak
* Nincsenek indexelt nézetek az SQL-készletben

A szabványos nézetek segítségével kényszerítheti a táblázatok közötti optimalizált illesztéseket. Egy nézet például belefoglalhatja a redundáns terjesztési kulcsot a csatlakozás feltételeinek részeként az adatáthelyezés csökkentése érdekében.

A nézetek egy másik előnye, hogy egy adott lekérdezést kényszerítenek, vagy a tipphez csatlakoznak. A nézetek ily módon történő használata garantálja, hogy az összekapcsolások mindig optimális módon történnek, elkerülve a felhasználóknak az illesztések helyes szerkezetének megemlékezését.

## <a name="materialized-view"></a>Tényleges táblán alapuló nézet

Egy anyagilag megtekinthető nézet előre kiszámítja, tárolja és karbantartja az adataikat az SQL-készletben, ugyanúgy, mint egy tábla. A rendszer minden alkalommal nem igényel újraszámítást, amikor egy anyagbeli nézetet használ.

Ahogy az adatok betöltődik az alaptáblákba, az SQL-készlet szinkron módon frissíti az anyagilag megjelenő nézeteket.  A lekérdezés-optimalizáló automatikusan központilag telepített, a lekérdezés teljesítményének növelésére szolgáló, a lekérdezésben nem hivatkozott nézeteket használ.  

A nagy mennyiségű nézetből kihasználható lekérdezések összetett lekérdezések (jellemzően lekérdezések összekapcsolással és összesítésekkel) olyan nagyméretű táblákon, amelyek kis eredményhalmazt hoznak létre.  

Az anyag nézet szintaxisának és egyéb követelményeinek részletes ismertetését a következő témakörben találja: [anyagelszámolású nézet létrehozása a Select](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)utasítással.  

A lekérdezések hangolásával kapcsolatos útmutatásért tekintse meg a [Teljesítmény finomhangolása az anyagbeli nézetekkel](performance-tuning-materialized-views.md)című témakört.

## <a name="example"></a>Példa

Gyakori alkalmazási minta a táblázatok újbóli létrehozása a CREATE TABLE AS SELECT (CTAS) használatával, amelyet az objektumok átnevezési mintája követ az adatbetöltése során.  

A következő példa új dátum rekordokat vesz fel egy dátum dimenzióba. Vegye figyelembe, hogy egy új tábla, DimDate_New, először jön létre, majd átnevezve lett a tábla eredeti verziójának helyére.

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Ez a megközelítés azonban azt eredményezheti, hogy a táblák megjelenhetnek, és a "táblázat nem létezik" hibaüzenetek megjelenése mellett nem jelennek meg a felhasználó nézetében.

A nézetek segítségével konzisztens megjelenítési réteget biztosíthat a felhasználók számára, miközben az alapul szolgáló objektumok átnevezve lesznek. Azáltal, hogy a nézetekben hozzáférést biztosít az adathozzáféréshez, a felhasználóknak nincs szükségük a mögöttes táblák láthatóságára.

Ez a réteg egységes felhasználói élményt nyújt, és gondoskodik arról, hogy az adatraktár-tervezők is fejlődjön az adatmodellben. Az alapul szolgáló táblák fejlesztése azt jelenti, hogy a tervezők a CTAS használatával maximalizálják a teljesítményt az betöltési folyamat során.

## <a name="next-steps"></a>További lépések

További fejlesztési tippek: [SQL Pool Development – áttekintés](sql-data-warehouse-overview-develop.md).
