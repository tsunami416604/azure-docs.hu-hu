---
title: T-SQL-nézetek használata az Azure SQL Data Warehouse |} Microsoft Docs
description: Tippek a T-SQL-nézetek használata az Azure SQL Data Warehouse adattárházzal történő, megoldások.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 28280a067e7008c20361e0a0041c81ba84e7f74c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="views-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse nézetek
Tippek a T-SQL-nézetek használata az Azure SQL Data Warehouse adattárházzal történő, megoldások. 

## <a name="why-use-views"></a>Nézetek miért érdemes használni?
Nézetek számos különböző módon minőségének a megoldás is használható.  Ez a cikk funkciógazdagabbá teheti a megoldás a nézetek, valamint a korlátozásokat, amelyeket figyelembe kell venni néhány példák mutatja be.

> [!NOTE]
> NÉZET létrehozása szintaxisának nem ez a cikk tárgyalja. További információkért lásd: a [NÉZET létrehozása](/sql/t-sql/statements/create-view-transact-sql) dokumentációját.
> 
> 

## <a name="architectural-abstraction"></a>Az architektúra absztrakciós
A közös alkalmazásminta újra létre kell hoznia táblák létrehozása tábla AS kiválasztása (CTAS) egy objektum átnevezése mintát, miközben az adatok betöltése után.

Az alábbi példa új dátum rekordok hozzáadása a dátum dimenzió. Vegye figyelembe, hogyan új tábla, DimDate_New, először hozzák létre, és lecseréli az eredeti verzióját tartalmazza a majd átnevezték.

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

Ez a módszer azonban táblák jelenik meg, és egy felhasználó, valamint a "tábla nem létezik" hibaüzenet eltűnik eredményez. Nézetek segítségével biztosít a felhasználók egy egységes megjelenítési réteg az alapul szolgáló objektumok váltják fel. Nézetek adatokhoz való hozzáférést biztosít, a felhasználóknak nem kell látható az alapul szolgáló táblákhoz. Ez a réteg egységes felhasználói élményt nyújt, közben is győződjön meg arról, hogy az adatraktár tervezők fejleszteni az adatmodellt. Is lehetővé azt fejleszteni az alapul szolgáló táblák azt jelenti, hogy a tervezők CTAS segítségével maximalizálhatja a teljesítményt az adatok betöltése a folyamat során.   

## <a name="performance-optimization"></a>Teljesítményoptimalizálás
Nézetek is kényszeríteni optimalizált teljesítményt illesztést a táblák között állítható be. Például egy nézet építhessék be a redundáns terjesztési kulcs adatmozgás minimalizálása érdekében a csatlakozó feltétel részeként. Egy másik előnye, hogy egy nézet egy adott lekérdezés vagy csatlakozó mutató kényszerített lehet. Nézetek használata ezen a módon kikapcsolja garantálja, hogy összekapcsolások mindig megtörténik a felhasználóknak jegyezze meg a megfelelő konstruktor a táblákra elkerülése optimális módon.

## <a name="limitations"></a>Korlátozások
Az SQL Data Warehouse nézetek, csak a metaadatok tárolódnak. Ennek következtében a következő beállítások nem érhetők el:

* Nincs kötés séma beállítás
* Alaptáblát keresztül a nézet nem frissíthető
* Nézetek nem hozható létre az ideiglenes táblák
* Nem támogatott a KIBONTÁS / NOEXPAND mutató
* Nincsenek az SQL Data Warehouse nem indexelt nézetek

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).


