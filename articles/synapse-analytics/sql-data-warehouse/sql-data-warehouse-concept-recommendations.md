---
title: Szinapszis SQL-javaslatok
description: Tudnivalók a szinapszis SQL-javaslatairól és azok létrehozásáról
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 17877a1ef5d949fbbee080b6157844ac5b516fe7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633685"
---
# <a name="synapse-sql-recommendations"></a>Szinapszis SQL-javaslatok

Ez a cikk a Azure Advisor által kiszolgált szinapszis SQL-ajánlásokat ismerteti.  

Az SQL Analytics ajánlásokat biztosít az adattárház számítási feladatainak teljesítményre való következetes optimalizálása érdekében. A javaslatok szorosan integrálva vannak a [Azure Advisorekkel](../../advisor/advisor-performance-recommendations.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , így közvetlenül a [Azure Portalon](https://aka.ms/Azureadvisor)belül biztosíthatja az ajánlott eljárásokat. Az SQL Analytics az aktív számítási feladatok telemetria és felületi javaslatait napi szinten gyűjti. A támogatott javaslatok alább láthatók a javasolt műveletek alkalmazásával együtt.

[A javaslatait](https://aka.ms/Azureadvisor) még ma is megtekintheti! Ez a funkció jelenleg csak a Gen2 adattárházak esetében alkalmazható.

## <a name="data-skew"></a>Az adattorzítás

Az adatok eldöntése további adatáthelyezést vagy erőforrás-szűk keresztmetszetet eredményezhet a számítási feladatok futtatásakor. Az alábbi dokumentáció ismerteti, hogyan azonosíthatja az adatok eldöntését, és megakadályozhatja, hogy az optimális terjesztési kulcs kiválasztásával megtörténjen.

- [A döntés azonosítása és eltávolítása](sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)

## <a name="no-or-outdated-statistics"></a>Nem vagy elavult statisztika

Az optimálisnál rosszabb statisztikai adatok súlyosan befolyásolhatják a lekérdezési teljesítményt, mert az SQL-lekérdezés-optimalizáló az optimálisnál rosszabb lekérdezési terveket hoz elő. Az alábbi dokumentáció a statisztikák létrehozásával és frissítésével kapcsolatos ajánlott eljárásokat ismerteti:

- [Táblázat statisztikáinak létrehozása és frissítése](sql-data-warehouse-tables-statistics.md)

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
> A replikált tábla javaslata nem teljes körűen igazolható, és nem veszi figyelembe az adatáthelyezési műveleteket. Jelenleg is dolgozunk, de a javaslat alkalmazása után mindig ellenőriznie kell a munkaterhelést. Vegye fel sqldwadvisor@service.microsoft.com a kapcsolatot, ha a replikált táblázatokra vonatkozó ajánlásokat észlel, amelyek hatására a számítási feladatok visszafejlődés. A replikált táblákkal kapcsolatos további információkért tekintse meg az alábbi [dokumentációt](design-guidance-for-replicated-tables.md#what-is-a-replicated-table).
