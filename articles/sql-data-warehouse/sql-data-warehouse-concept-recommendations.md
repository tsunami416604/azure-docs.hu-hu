---
title: Javaslatok SQL Data Warehouse
description: Tudnivalók a SQL Data Warehouse javaslatairól és azok létrehozásáról
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 09dff2c8ddf5b9038aa715cef02e099ccbc68f8a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685911"
---
# <a name="sql-data-warehouse-recommendations"></a>Javaslatok SQL Data Warehouse

Ez a cikk a SQL Data Warehouse által a Azure Advisor által kiszolgált ajánlásokat ismerteti.  

SQL Data Warehouse ajánlásokat biztosít annak biztosítására, hogy az adattárház konzisztensen legyen optimalizálva a teljesítményre. Az adatraktárral kapcsolatos javaslatok szorosan integrálva vannak a [Azure Advisorekkel](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) , így közvetlenül a [Azure Portalon](https://aka.ms/Azureadvisor)belül biztosíthatja az ajánlott eljárásokat. SQL Data Warehouse elemzi az adattárház aktuális állapotát, gyűjti a telemetria, és az aktív számítási feladatokra vonatkozó ajánlásokat készít a napi ütemben. A támogatott adattárház-javaslatok alább láthatók a javasolt műveletek alkalmazásával együtt.

Ha visszajelzést szeretne küldeni a SQL Data Warehouse Advisorról, vagy bármilyen problémába ütközik, akkor [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Kattintson [ide](https://aka.ms/Azureadvisor) a javaslatok megtekintéséhez! Ez a funkció jelenleg csak a Gen2 adattárházak esetében alkalmazható. 

## <a name="data-skew"></a>Az adattorzítás

Az adatok eldöntése további adatáthelyezést vagy erőforrás-szűk keresztmetszetet eredményezhet a számítási feladatok futtatásakor. Az alábbi dokumentáció ismerteti, hogyan azonosíthatja az adatok eldöntését, és megakadályozhatja, hogy az optimális terjesztési kulcs kiválasztásával megtörténjen.

- [A döntés azonosítása és eltávolítása](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Nem vagy elavult statisztika

Az optimálisnál rosszabb statisztikai adatok súlyos hatással lehetnek a lekérdezési teljesítményre, mivel a SQL Data Warehouse lekérdezés-optimalizáló a legoptimálisabb lekérdezési csomagok előállítására is használható. Az alábbi dokumentáció a statisztikák létrehozásával és frissítésével kapcsolatos ajánlott eljárásokat ismerteti:

- [Táblázat statisztikáinak létrehozása és frissítése](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Ha meg szeretné tekinteni az érintett táblák listáját a javaslatok alapján, futtassa az alábbi [T-SQL-szkriptet](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Az Advisor folyamatosan futtatja ugyanazt a T-SQL-szkriptet a javaslatok létrehozásához.

## <a name="replicate-tables"></a>Táblák replikálása

A replikált táblákra vonatkozó javaslatok esetében az Advisor a következő fizikai jellemzők alapján észleli a táblázatos pályázókat:

- Replikált tábla mérete
- Oszlopok száma
- Tábla eloszlásának típusa
- Partíciók száma

Az Advisor folyamatosan kihasználja a munkaterhelés-alapú heurisztikus feladatokat, például a táblák elérési gyakoriságát, az átlagban visszaadott sorokat, valamint az adatraktár méretének és tevékenységének küszöbértékeit, hogy magas színvonalú javaslatokat generáljon. 

Az alábbiakban az egyes replikált táblákra vonatkozó javaslatok Azure Portalban találhatja meg a munkaterhelés-alapú heurisztikus feladatokat:

- Scan AVG – a táblázatból visszaadott sorok átlagos százaléka az elmúlt hét napban való hozzáférés során
- Gyakori olvasás, nincs frissítés – azt jelzi, hogy a tábla nem frissült az elmúlt hét napban a hozzáférési tevékenység megjelenítése közben.
- Olvasási/frissítési arány – annak a gyakorisága, hogy a táblázat milyen gyakran érhető el, ha az elmúlt hét napban frissül
- Tevékenység – a használatot a hozzáférési tevékenység alapján méri. Ez összehasonlítja a tábla-hozzáférési tevékenységet az adattárházban az elmúlt hét napban megadott átlagos tábla-hozzáférési tevékenységhez képest. 

Az Advisor jelenleg legfeljebb négy replikált tábla jelöltjét jeleníti meg egyszerre a fürtözött oszlopcentrikus indexekkel, amelyek rangsorolják a legmagasabb tevékenységet.

> [!IMPORTANT]
> A replikált tábla javaslata nem teljes körűen igazolható, és nem veszi figyelembe az adatáthelyezési műveleteket. Jelenleg is dolgozunk, de a javaslat alkalmazása után mindig ellenőriznie kell a munkaterhelést. Vegye fel a kapcsolatot sqldwadvisor@service.microsoft.com ha felderíti a visszafejlődés számítási feladatait kiváltó replikált táblázatos javaslatokat. A replikált táblákkal kapcsolatos további információkért tekintse meg az alábbi [dokumentációt](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).
