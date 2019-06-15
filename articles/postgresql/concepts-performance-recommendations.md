---
title: Teljesítménnyel kapcsolatos javaslatok az Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk ismerteti a teljesítmény javaslat funkció az Azure Database for PostgreSQL – egyetlen kiszolgáló.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31d8c0fdf1b4df3ee00f3652c933b4b738384bea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65068843"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Teljesítménnyel kapcsolatos javaslatok az Azure Database for PostgreSQL – egyetlen kiszolgáló

**A következőkre vonatkozik:** Azure Database for PostgreSQL – egyetlen kiszolgáló 9.6 és 10

A teljesítménnyel kapcsolatos javaslatok funkció elemzi az adatbázisokat hozhat létre személyre szabott javaslatok a jobb teljesítmény érdekében. Az ajánlások előállításához, különböző adatbázis jellemzőit, beleértve a sémát az elemzés megvizsgálja. Engedélyezése [Query Store](concepts-query-store.md) használja ki teljesen a teljesítménnyel kapcsolatos javaslatok a szolgáltatás a kiszolgálón. Után minden teljesítmény javaslat megvalósítása, tesztelnie kell a teljesítményt, ezek a módosítások hatásának vizsgálatában. 

## <a name="permissions"></a>Engedélyek
A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

Nyissa meg **teljesítménnyel kapcsolatos javaslatok** származó a **intelligens teljesítmény** a menüsávon a PostgreSQL-kiszolgáló az Azure portál oldalán szakaszában.

![A Teljesítménnyel kapcsolatos javaslatok kezdőlapja](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Válassza ki **elemzés** és a egy adatbázis, amely megkezdi az elemzés válassza. Attól függően, a számítási feladatok a th elemzés végrehajtásához több percig is eltarthat. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés. Elemzés az adatbázis egy részletes vizsgálatot hajt végre. Ajánlott csúcsidőn kívüli időszakokban elemzését. 

A **javaslatok** ablakban megjelennek a javaslatok listája, ha bármelyik találhatók.

![Teljesítmény javaslatok új lap](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Javaslatok a rendszer nem alkalmazza automatikusan. A javaslat alkalmazása, a lekérdezés szövegének másolása, és futtassa azt az ügyfél által választott. Tesztelje a vágólapra, és értékelheti ki az ajánlás figyelése. 

## <a name="recommendation-types"></a>Az ajánlás típusok

Jelenleg két típusú ajánlások támogatottak: *Index létrehozása* és *Drop Index*.

### <a name="create-index-recommendations"></a>Ajánlott indexek létrehozása
*Index létrehozása* ajánlások javaslatot a leggyakrabban a számítási feladat futtatható vagy időigényes lekérdezések felgyorsításához új indexeket. Ez az ajánlás típusához [Query Store](concepts-query-store.md) engedélyezni kell. Query Store lekérdezés adatokat gyűjt, és az elemzés segítségével győződjön meg arról, a javaslat részletes lekérdezési modul és a gyakoriság statisztikák biztosít.

### <a name="drop-index-recommendations"></a>Drop Index javaslatok
Amellett, hogy észlelni a hiányzó indexeket, Azure Database for PostgreSQL elemzi a meglévő indexeket teljesítményét. A ritkán használt vagy redundáns index esetén az elemző azt javasolja, eldobja.


## <a name="next-steps"></a>További lépések
- További információk az Azure Database for PostgreSQL [monitoringjához és finomhangolásához](concepts-monitoring.md).

