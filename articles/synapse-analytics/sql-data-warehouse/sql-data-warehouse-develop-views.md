---
title: T-SQL nézetek használata
description: Tippek a T-SQL nézetek használatához és megoldások fejlesztéséhez a Synapse SQL-készletben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633388"
---
# <a name="views-in-synapse-sql-pool"></a>Nézetek a Szinapszis SQL-készletében

A nézetek számos különböző módon használhatók a megoldás minőségének javítására.

Az SQL-készlet támogatja a szabványos és materializált nézeteket is. Mindkettő SELECT kifejezéssel létrehozott virtuális tábla, amelyet logikai táblaként mutatnak be a lekérdezéseknek.

Nézetek beágyazza a gyakori adatszámítás összetettségét, és adjunk hozzá egy absztrakciós réteget a számítási módosításokhoz, így nincs szükség a lekérdezések újraírására.

## <a name="standard-view"></a>Normál nézet

A szabványos nézet minden alkalommal kiszámítja az adatokat, amikor a nézetet használja. Nincsenek adatok a lemezen. A személyek általában szabványos nézeteket használnak olyan eszközként, amely segít az adatbázis logikai objektumainak és lekérdezéseinek rendszerezésében.

Szabványos nézet használatához a lekérdezésnek közvetlenül hivatkoznia kell rá. További információt a [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dokumentációjában talál.

Az SQL-készletben lévő nézetek csak metaadatként tárolódnak. Ennek következtében a következő lehetőségek nem érhetők el:

* Nincs sémakötési beállítás
* Az alaptáblák nem frissíthetők a nézetben
* A nézetek nem hozhatók létre ideiglenes táblákon keresztül
* A EXPAND / NOEXPAND tippek nem támogatottak
* Nincsenek indexelt nézetek az SQL-készletben

A szabványos nézetek a táblák közötti teljesítményoptimalizált illesztések kényszerítésére használhatók. Egy nézet például tartalmazhat egy redundáns terjesztési kulcsot az illesztési feltételek részeként az adatok mozgásának minimalizálása érdekében.

A nézet másik előnye lehet egy adott lekérdezés vagy illesztési tipp kényszerítése. A nézetek ily módon történő használata garantálja, hogy az illesztések mindig optimális módon történnek, elkerülve annak szükségességét, hogy a felhasználók emlékezzenek az illesztésük megfelelő konstrukciójára.

## <a name="materialized-view"></a>Tényleges táblán alapuló nézet

A materializált nézet előre kiszámítja, tárolja, és az adatokat az SQL-készletben tartja, mint egy tábla. Materializált nézet használatközben nincs szükség újraszámításra.

Ahogy az adatok betöltődnek az alaptáblákba, az SQL-készlet szinkron módon frissíti a materializált nézeteket.  A lekérdezés-optimalizáló automatikusan telepítve materializált nézeteket használ a lekérdezés teljesítményének javítása érdekében, még akkor is, ha a lekérdezés nem hivatkozik a nézetekre.  

A materializált nézetekből leginkább hasznos lekérdezések összetett lekérdezések (általában illesztésekkel és összesítésekkel rendelkező lekérdezések) olyan nagy táblákon, amelyek kis eredményhalmazt eredményeznek.  

A materializált nézet szintaxisával és egyéb követelményeivel kapcsolatos részletekért olvassa el [a MATERIALIZÁLT NÉZET LÉTREHOZÁSA A HOGY SELECT LEHETŐSÉGET.](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

A lekérdezéshangolási útmutatásért jelölje be [a Teljesítményhangolás materializált nézetekkel jelölőnégyzetet.](performance-tuning-materialized-views.md)

## <a name="example"></a>Példa

Gyakori alkalmazásminta a táblák létrehozása tábla as select (CTAS) használatával történő újralétrehozása, amelyet egy objektum átnevezési minta követ az adatok betöltése közben.  

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Ez a megközelítés azonban azt eredményezheti, hogy a táblák megjelennek és eltűnnek a felhasználó nézetéből, valamint "tábla nem létezik" hibaüzenetek kiadásával.

A nézetek segítségével a felhasználók egységes bemutatóréteget kaphatnak, miközben az alapul szolgáló objektumokat átnevezi. Azáltal, hogy a nézeteken keresztül biztosít hozzáférést az adatokhoz, a felhasználóknak nincs szükségük az alapul szolgáló táblák láthatóságára.

Ez a réteg egységes felhasználói élményt nyújt, miközben biztosítja, hogy az adattárház-tervezők fejleszthetik az adatmodellt. Az alapul szolgáló táblák továbbfejlesztése azt jelenti, hogy a tervezők a CTAS segítségével maximalizálhatják a teljesítményt az adatbetöltési folyamat során.

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket az [SQL-készlet fejlesztésének áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)
