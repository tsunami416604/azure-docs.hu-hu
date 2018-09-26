---
title: Figyelheti és javíthatja a teljesítményt – Azure SQL Database |} A Microsoft Docs
description: Az Azure SQL Database teljesítményét eszközöket biztosít, amely javítja a jelenlegi lekérdezési teljesítményt területeknek az azonosítására szolgál.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 35af3a926ab3c3a3046eab921e86b6610edc309c
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162956"
---
# <a name="monitor-and-improve-performance"></a>Figyelheti és javíthatja a teljesítményt
Az Azure SQL Database azonosítja a lehetséges problémákat az adatbázisban, és teendőket javasol, amely növelheti a számítási feladat teljesítményét azáltal, hogy intelligens hangolási műveletek és javaslatok.

Tekintse át az adatbázis teljesítményét, használja a **teljesítmény** csempét – Áttekintés lapon, vagy navigáljon "Támogatás + hibaelhárítás" szakaszban:

   ![Teljesítmény megtekintése](./media/sql-database-performance/entries.png)

Az a "Támogatás + hibaelhárítás" szakaszban a következő lapokon is használhatja:


1. [Teljesítmény áttekintése](#performance-overview) az adatbázis teljesítményének figyelése. 
2. [Teljesítménnyel kapcsolatos javaslatok](#performance-recommendations) található teljesítménnyel kapcsolatos javaslatok, amely növelheti a számítási feladatok teljesítményére.
3. [Lekérdezési Terheléselemző](#query-performance-insight) felső erőforrásigényes lekérdezések található.
4. [Az automatikus hangolás](#automatic-tuning) ahhoz, hogy az Azure SQL Database automatikusan optimalizálja adatbázisát.

## <a name="performance-overview"></a>Teljesítmény áttekintése
Ebben a nézetben áttekintést kaphat az adatbázis teljesítményét, és segítséget nyújt a teljesítmény finomhangolásához és a hibaelhárítás. 

![Teljesítmény](./media/sql-database-performance/performance.png)

* A **javaslatok** csempe nyújt információkat a finomhangolási javaslatai az adatbázis (első három javaslatok láthatók van-e további). Az erre a csempére kattintva megnyílik  **[teljesítménnyel kapcsolatos javaslatok](#performance-recommendations)**. 
* A **tevékenység hangolása** csempe a folyamatban levő és befejezett hangolási műveletek az adatbázis, a tevékenység hangolása előzményeit gyors betekintést biztosít összegzését tartalmazza. Erre a csempére kattintva megnyílik az adatbázis teljes hangolási előzmények megtekintését.
* A **automatikus finomhangolási** csempén látható a [automatikus hangolási konfiguráció](sql-database-automatic-tuning-enable.md) az adatbázis (hangolási beállításokat, amelyek automatikusan érvénybe lépnek az adatbázishoz). Erre a csempére kattintva megnyílik az automation-konfiguráció párbeszédpanel.
* A **adatbázis-lekérdezések** csempe a lekérdezési teljesítmény az adatbázis (teljes dtu-k használatát és a felső erőforrásigényes lekérdezések) összegzését jeleníti meg. Az erre a csempére kattintva megnyílik  **[lekérdezési Terheléselemző](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
Ez az oldal nyújt intelligens [finomhangolási javaslatai](sql-database-advisor.md) , javíthatja az adatbázis teljesítményét. A következő típusú ajánlások ezen a lapon láthatók:

* Melyik indexek létrehozása, vagy dobja el a javaslatokat.
* Javaslatok az azonosított kapcsolatos hibák elhárítása az adatbázisban.
* Javaslatok a lekérdezések a paraméteres lekérdezések hasznosíthatja.

![Teljesítmény](./media/sql-database-performance/recommendations.png)

Hangolási műveletek korábban alkalmazott teljes előzményeit is megtalálhatja.

Ismerje meg, hogyan keresse meg az alkalmazás a teljesítménnyel kapcsolatos javaslatok [keresse meg és teljesítménnyel kapcsolatos javaslatok alkalmazása](sql-database-advisor-portal.md) cikk.

## <a name="automatic-tuning"></a>Automatikus hangolás
Az Azure SQL Database automatikusan hangolhassa adatbázis teljesítményének alkalmazásával [teljesítménnyel kapcsolatos javaslatok](sql-database-advisor.md). További tudnivalókért olvassa el a [automatikus finomhangolási cikk](sql-database-automatic-tuning.md). Az engedélyezéshez, olvassa el a [az automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
[Lekérdezési Terheléselemző](sql-database-query-performance.md) lehetővé teszi, hogy kevesebb időt azáltal, hogy adatbázis-teljesítmény hibaelhárítása:

* Mélyebb betekintést az adatbázisok (DTU) erőforrás-felhasználásáról. 
* A legnagyobb Processzorigényű lekérdezések, amelyek vélhetően hangolásával a jobb teljesítmény érdekében. 
* Lehetővé teszi a lekérdezés részleteinek feltárásához. 

  ![teljesítmény-irányítópult](./media/sql-database-query-performance/performance.png)

Erre a lapra vonatkozó további információkat tekinthet meg a cikk  **[lekérdezési Terheléselemző használata](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>További források
* [Útmutató az önálló adatbázisok az Azure SQL Database teljesítményének növeléséhez](sql-database-performance-guidance.md)
* [Mikor ajánlott a rugalmas készlet használata?](sql-database-elastic-pool-guidance.md)

