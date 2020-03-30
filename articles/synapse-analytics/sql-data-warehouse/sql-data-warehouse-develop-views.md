---
title: T-SQL nézetek használata
description: Tippek a T-SQL-nézetek használatához az Azure SQL Data Warehouse-ban megoldások fejlesztéséhez.
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
ms.openlocfilehash: 9eccc225c5473291b7535339ff64a036730894ae
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351516"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Nézetek az Azure SQL Data Warehouse-ban
A nézetek számos különböző módon használhatók a megoldás minőségének javítására. 

Az Azure SQL Data Warehouse támogatja a szabványos és materializált nézeteket. Mindkettő SELECT kifejezéssel létrehozott virtuális tábla, amelyet logikai táblaként mutatnak be a lekérdezéseknek. Nézetek beágyazza a gyakori adatszámítás összetettségét, és adjunk hozzá egy absztrakciós réteget a számítási módosításokhoz, így nincs szükség a lekérdezések újraírására.

## <a name="standard-view"></a>Normál nézet
A szabványos nézet minden alkalommal kiszámítja az adatokat, amikor a nézetet használja. Nincsenek adatok a lemezen. A személyek általában szabványos nézeteket használnak olyan eszközként, amely segít az adatbázis logikai objektumainak és lekérdezéseinek rendszerezésében. Szabványos nézet használatához a lekérdezésnek közvetlenül hivatkoznia kell rá. További információt a [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) dokumentációjában talál.

Az SQL Data Warehouse nézetei csak metaadatként tárolódnak. Következésképpen a következő lehetőségek nem állnak rendelkezésre:
* Nincs sémakötési beállítás
* Az alaptáblák nem frissíthetők a nézeten keresztül
* Nem hozhatók létre nézetek ideiglenes táblákon keresztül
* A EXPAND / NOEXPAND tippek nem támogatottak
* Nincsenek indexelt nézetek az SQL Data Warehouse-ban

A szabványos nézetek a táblák közötti teljesítményoptimalizált illesztések kényszerítésére használhatók. Egy nézet például tartalmazhat egy redundáns terjesztési kulcsot az illesztési feltételek részeként az adatok mozgásának minimalizálása érdekében. A nézet másik előnye lehet egy adott lekérdezés vagy illesztési tipp kényszerítése. A nézetek ily módon történő használata garantálja, hogy az illesztések mindig optimális módon történnek, elkerülve annak szükségességét, hogy a felhasználók emlékezzenek az illesztésük megfelelő konstrukciójára.

## <a name="materialized-view"></a>Tényleges táblán alapuló nézet
Egy materializált nézet előre kiszámítja, tárolja, és az Azure SQL Data Warehouse-ban tárolja az adatait, mint egy tábla. Materializált nézet használatközben nincs szükség újraszámításra. Ahogy az adatok betöltődnek az alaptáblákba, az Azure SQL Data Warehouse szinkron módon frissíti a materializált nézeteket.  A lekérdezés-optimalizáló automatikusan telepített materializált nézeteket használ a lekérdezés teljesítményének javítása érdekében, még akkor is, ha a lekérdezés nem hivatkozik a nézetekre.  A materializált nézetekből leginkább hasznos lekérdezések összetett lekérdezések (általában illesztésekkel és összesítésekkel rendelkező lekérdezések) olyan nagy táblákon, amelyek kis eredményhalmazt eredményeznek.  

A materializált nézet szintaxisával és egyéb követelményeivel kapcsolatos részletekért olvassa el [a MATERIALIZÁLT NÉZET LÉTREHOZÁSA A HOGY SELECT LEHETŐSÉGET.](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)  

A lekérdezéshangolási útmutatásért jelölje be [a Teljesítményhangolás materializált nézetekkel jelölőnégyzetet.](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views) 

## <a name="example"></a>Példa
Gyakori alkalmazásminta a táblák létrehozása tábla as select (CTAS) használatával történő újralétrehozása, amelyet egy objektum átnevezési minta követ az adatok betöltése közben.  A következő példa új dátumrekordokat ad hozzá egy dátumdimenzióhoz. Figyelje meg, hogyan jön létre először egy új tábla (DimDate_New) a tábla eredeti verziójának lecserélése érdekében.

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
Ez a megközelítés azonban azt eredményezheti, hogy a táblák megjelennek és eltűnnek a felhasználó nézetéből, valamint "a tábla nem létezik" hibaüzenetek. A nézetek segítségével a felhasználók egységes megjelenítési réteget kaphatnak, miközben az alapul szolgáló objektumokat átnevezték. Azáltal, hogy a nézeteken keresztül biztosít hozzáférést az adatokhoz, a felhasználóknak nincs szükségük az alapul szolgáló táblák láthatóságára. Ez a réteg egységes felhasználói élményt nyújt, miközben biztosítja, hogy az adattárház-tervezők fejleszthetik az adatmodellt. Az alapul szolgáló táblák továbbfejlesztése azt jelenti, hogy a tervezők a CTAS segítségével maximalizálhatják a teljesítményt az adatbetöltési folyamat során.   

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).


