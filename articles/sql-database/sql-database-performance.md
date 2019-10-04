---
title: A teljesítmény monitorozása és javítása – Azure SQL Database | Microsoft Docs
description: A Azure SQL Database olyan teljesítmény-eszközöket biztosít, amelyek segítségével azonosíthatja azokat a területeket, amelyek javíthatják az aktuális lekérdezési teljesítményt.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 39a100c487588fb717c642036c7713150a95e047
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567077"
---
# <a name="monitor-and-improve-performance"></a>A teljesítmény monitorozása és javítása

Azure SQL Database azonosítja az adatbázis lehetséges problémáit, és javaslatokat tesz a számítási feladatok teljesítményének javítására az intelligens hangolási műveletek és javaslatok biztosításával.

Az adatbázis teljesítményének áttekintéséhez használja az Áttekintés oldalon található **Performance (teljesítmény** ) csempét, vagy navigáljon a "támogatás + hibaelhárítás" szakaszhoz:

   ![Teljesítmény megtekintése](./media/sql-database-performance/entries.png)

A "támogatás + hibaelhárítás" szakaszban a következő lapokat használhatja:


1. A [teljesítmény áttekintése](#performance-overview) az adatbázis teljesítményének figyeléséhez. 
2. [Teljesítménnyel kapcsolatos javaslatok](#performance-recommendations) a számítási feladatok teljesítményének javításához szükséges teljesítménnyel kapcsolatos javaslatok megtalálásához.
3. [Lekérdezési terheléselemző](#query-performance-insight) a leggyakoribb erőforrás-felhasználású lekérdezések kereséséhez.
4. [Automatikus hangolás](#automatic-tuning) , hogy Azure SQL Database automatikusan optimalizálja az adatbázist.

## <a name="performance-overview"></a>Teljesítmény áttekintése

Ez a nézet összegzi az adatbázis teljesítményét, és segítséget nyújt a teljesítmény finomhangolásához és a hibaelhárításhoz. 

![Teljesítmény](./media/sql-database-performance/performance.png)

* A **javaslatok** csempéje az adatbázis hangolási javaslatainak részletezését biztosítja (a három legfontosabb javaslat látható, ha van több). Erre a csempére kattintva a **[teljesítményre vonatkozó javaslatokat](#performance-recommendations)** olvashat. 
* A **hangolási tevékenység** csempe összefoglalja az adatbázis folyamatban lévő és befejezett hangolási műveleteit, így gyors áttekintést nyújt a hangolási tevékenység előzményeiről. Erre a csempére kattintva megtekintheti az adatbázis teljes hangolási előzményeit.
* Az **automatikus hangolás** csempéje az adatbázis [Automatikus hangolási konfigurációját](sql-database-automatic-tuning-enable.md) jeleníti meg (az adatbázisra automatikusan alkalmazott hangolási beállításokat). A csempére kattintva megnyílik az Automation konfigurálása párbeszédpanel.
* Az **adatbázis-lekérdezések** csempe az adatbázis lekérdezési teljesítményének összegzését jeleníti meg (a teljes DTU-használat és a legfelső szintű erőforrás-felhasználású lekérdezések esetében). A csempére kattintva **[lekérdezési terheléselemző](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

Ez az oldal olyan intelligens [hangolási javaslatokat](sql-database-advisor.md) tartalmaz, amelyek javítják az adatbázis teljesítményét. Ezen az oldalon a következő típusú javaslatok láthatók:

* Javaslatok, amelyek alapján létre kell hozni vagy el kell dobni az indexeket.
* Javaslatok a sémával kapcsolatos problémák azonosításához az adatbázisban.
* Javaslatok, amikor a lekérdezések kihasználhatják a paraméteres lekérdezéseket.

![Teljesítmény](./media/sql-database-performance/recommendations.png)

A múltban alkalmazott hangolási műveletek teljes előzményeit is megtalálhatja.

Ismerje meg, hogyan találhatja meg a teljesítményre vonatkozó ajánlásokat az alkalmazás teljesítményének megkeresése [és alkalmazása](sql-database-advisor-portal.md) című cikkben.

## <a name="automatic-tuning"></a>Automatikus finomhangolás

A [teljesítményre vonatkozó javaslatok](sql-database-advisor.md)alkalmazásával az Azure SQL Database-adatbázisok automatikusan is hangolják az adatbázis teljesítményét. További információért olvassa el az [automatikus hangolással](sql-database-automatic-tuning.md)foglalkozó cikket. Az engedélyezéshez olvassa el az [automatikus hangolás engedélyezése](sql-database-automatic-tuning-enable.md)című témakört.

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző

A [lekérdezési terheléselemző](sql-database-query-performance.md) lehetővé teszi, hogy az adatbázis-teljesítménnyel kapcsolatos hibák elhárításához a következőket biztosítson:

* Mélyebb betekintés az adatbázisok erőforrás-(DTU-) felhasználására. 
* A legfelső CPU-t használó lekérdezések, amelyek esetlegesen a jobb teljesítmény érdekében állíthatók be. 
* A lekérdezés részleteinek részletezése. 

  ![teljesítmény-irányítópult](./media/sql-database-query-performance/performance.png)

További információt erről az oldalról a **[lekérdezési terheléselemző használata](sql-database-query-performance.md)** című cikkben talál.

## <a name="additional-resources"></a>További források

* [Azure SQL Database teljesítményre vonatkozó útmutató önálló adatbázisokhoz](sql-database-performance-guidance.md)
* [Mikor érdemes rugalmas készletet használni?](sql-database-elastic-pool-guidance.md)

