---
title: T-SQL-nézetek használata a Azure SQL Data Warehouseban | Microsoft Docs
description: Tippek a Azure SQL Data Warehouse T-SQL-nézeteinek használatához a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 1fd406243f0f2f5339c4170c4ec17286fcf2ef6d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901711"
---
# <a name="views-in-azure-sql-data-warehouse"></a>A Azure SQL Data Warehouse nézetei
A nézetek számos különböző módon használhatók a megoldás minőségének javítására. 

Azure SQL Data Warehouse támogatja a szabványos és a jelentős nézeteket. Mindkettő olyan virtuális táblákat hoz létre, amelyek a SELECT kifejezésekkel lettek létrehozva, és a lekérdezéseket logikai táblázatként mutatja A nézetek összefoglalják a közös adatszámítások összetettségét, és felvesznek egy absztrakt réteget a számítási változásokhoz, így nincs szükség a lekérdezések újraírására.

## <a name="standard-view"></a>Normál nézet
A normál nézet minden alkalommal kiszámítja az adatmegjelenítést, amikor a nézet használatban van. A lemezen nem találhatók adattárolók. A személyek általában szabványos nézeteket használnak olyan eszközként, amely segít megszervezni a logikai objektumokat és lekérdezéseket egy adatbázisban. Normál nézet használatához a lekérdezésnek közvetlen hivatkozást kell tartalmaznia. További információ: [create View](/sql/t-sql/statements/create-view-transact-sql) dokumentáció.

A SQL Data Warehouse nézetei csak metaadatokként vannak tárolva. Ennek következtében a következő beállítások nem érhetők el:
* Nincs séma-kötési beállítás
* Az alaptáblák nem frissíthetők a nézeten keresztül
* Ideiglenes táblákon nem hozhatók létre nézetek
* A kibontási/kibontási útmutatók nem támogatottak
* Nincsenek indexelt nézetek a SQL Data Warehouse

A szabványos nézetek segítségével kényszerítheti a táblázatok közötti optimalizált illesztéseket. Egy nézet például belefoglalhatja a redundáns terjesztési kulcsot a csatlakozás feltételeinek részeként az adatáthelyezés csökkentése érdekében. A nézetek egy másik előnye, hogy egy adott lekérdezést kényszerítenek, vagy a tipphez csatlakoznak. A nézetek ily módon történő használata garantálja, hogy az összekapcsolások mindig optimális módon történnek, elkerülve a felhasználóknak az illesztések helyes szerkezetének megemlékezését.

## <a name="materialized-view"></a>Tényleges táblán alapuló nézet
Egy anyagilag megtekinthető nézet előre kiszámítja, tárolja és karbantartja az adataikat Azure SQL Data Warehouse ugyanúgy, mint egy tábla. A rendszer minden alkalommal nem igényel újraszámítást, amikor egy anyagbeli nézetet használ. Mivel az adatok betöltődik az alaptáblákba, Azure SQL Data Warehouse szinkron módon frissíti az anyagbeli nézeteket.  A lekérdezés-optimalizáló automatikusan központilag telepített, a lekérdezés teljesítményének növelésére szolgáló, a lekérdezésben nem hivatkozott nézeteket használ.  A nagy mennyiségű nézetből kihasználható lekérdezések összetett lekérdezések (jellemzően lekérdezések összekapcsolással és összesítésekkel) olyan nagyméretű táblákon, amelyek kis eredményhalmazt hoznak létre.  

Az anyag nézet szintaxisának és egyéb követelményeinek részletes ismertetését a következő témakörben találja: [anyagelszámolású nézet létrehozása a Select](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)utasítással.  

A lekérdezések hangolásával kapcsolatos útmutatásért tekintse meg a [Teljesítmény finomhangolása az anyagbeli nézetekkel](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)című témakört. 

## <a name="example"></a>Példa
Gyakori alkalmazási minta a táblázatok újbóli létrehozása a CREATE TABLE AS SELECT (CTAS) használatával, amelyet egy objektum átnevezési mintája követ, az adatbetöltések során.  A következő példa új dátum rekordokat vesz fel egy dátum dimenzióba. Figyelje meg, hogy a rendszer először hozza létre az új táblát, a DimDate_New, majd átnevezi a tábla eredeti verziójának helyére.

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
Ez a megközelítés azonban azt eredményezheti, hogy a táblák megjelenhetnek, és nem jelennek meg a felhasználó nézetében, valamint a "táblázat nem létezik" hibaüzenetek. A nézetek segítségével konzisztens megjelenítési réteget biztosíthat a felhasználók számára, miközben az alapul szolgáló objektumokat átnevezi. Azáltal, hogy a nézetekben hozzáférést biztosít az adathozzáféréshez, a felhasználóknak nincs szükségük a mögöttes táblák láthatóságára. Ez a réteg egységes felhasználói élményt nyújt, és gondoskodik arról, hogy az adatraktár-tervezők is fejlődjön az adatmodellben. Az alapul szolgáló táblák fejlesztése azt jelenti, hogy a tervezők a CTAS használatával maximalizálják a teljesítményt az betöltési folyamat során.   

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).


