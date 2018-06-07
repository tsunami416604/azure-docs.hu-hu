---
title: Figyelheti és javíthatja a teljesítményt – az Azure SQL Database |} Microsoft Docs
description: Az Azure SQL Database teljesítményét eszközöket biztosít, az aktuális lekérdezés teljesítményének területeit azonosították.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 7d1fa8d05de11ed68bedbc91fbe22aa64b25372e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650383"
---
# <a name="monitor-and-improve-performance"></a>Figyelheti és javíthatja a teljesítményt
Azure SQL Database az adatbázis potenciális problémákat ismertet, és javíthatja a teljesítményt a munkaterhelés intelligens hangolási műveletek és javaslatok biztosításával teendőket.

Tekintse át az adatbázis teljesítményét, használja a **teljesítmény** csempét – Áttekintés lapon vagy navigáljon a "Támogatási + hibaelhárítás" szakaszban:

   ![Nézetek teljesítménye](./media/sql-database-performance/entries.png)

Az a "Támogatási + hibaelhárítás" szakaszban, a következő lapok használható:


1. [Teljesítmény – áttekintés](#performance-overview) az adatbázis teljesítményének figyelése. 
2. [Teljesítmény javaslatok](#performance-recommendations) javíthatja a teljesítményt a munkaterhelés teljesítmény javaslatokat kereséséhez.
3. [Lekérdezési Terheléselemzőhöz](#query-performance-insight) erőforrásigényes lekérdezések felső erőforrás található.
4. [Automatikus hangolása](#automatic-tuning) ahhoz, hogy az Azure SQL Database automatikusan optimalizálhatja az adatbázist.

## <a name="performance-overview"></a>Teljesítmény – áttekintés
Ebben a nézetben az adatbázis teljesítményének összegzését tartalmazza, és teljesítményének hangolása és hibaelhárítási segítséget. 

![Teljesítmény](./media/sql-database-performance/performance.png)

* A **javaslatok** csempe nyújt információkat a hangolása az adatbázishoz ajánlott (a három legfontosabb javaslatok láthatók Ha nincsenek további). Ez a csempe kattintva időt vesz igénybe, hogy  **[teljesítmény javaslatok](#performance-recommendations)**. 
* A **tevékenység hangolása** csempe a folyamatban levő és befejezett hangolása műveletek az adatbázishoz, felkínálva tevékenység hangolása előzményeinek megtekintésében gyors összegzését tartalmazza. Ez a csempe kattintva viszi az adatbázis teljes hangolási előzményeinek megtekintése.
* A **automatikus hangolása** csempe megjeleníti a [automatikus hangolása konfigurációs](sql-database-automatic-tuning-enable.md) (hangolási lehetőségeket, amelyek a rendszer automatikusan alkalmazza az adatbázis) az adatbázis számára. Ez a csempe kattintva megnyithatja az automation konfigurációs párbeszédpanel.
* A **adatbázis-lekérdezésre** csempe a lekérdezési teljesítményt, az adatbázis (összesített DTU használati és felső erőforrás erőforrásigényes lekérdezések) összegzését jeleníti meg. Ez a csempe kattintva időt vesz igénybe, hogy  **[lekérdezési Terheléselemző](#query-performance-insight)**.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
Ezen a lapon biztosít intelligens [javaslatok hangolása](sql-database-advisor.md) , amely az adatbázis a jobb teljesítmény érdekében. A következő típusú javaslatok ezen az oldalon láthatók:

* Mely indexek létrehozása, vagy dobja el javaslatait.
* Javaslatok az séma problémák felmerültek az adatbázisban.
* Javaslatok az lekérdezéseket is kihasználhatja a paraméteres lekérdezések le.

![Teljesítmény](./media/sql-database-performance/recommendations.png)

Nyomon követheti a múltban alkalmazott műveletek hangolása is tájékozódhat.

Megtudhatja, hogyan található apply teljesítmény javaslatok az [keresse meg és teljesítmény javaslatok alkalmazása](sql-database-advisor-portal.md) cikk.

## <a name="automatic-tuning"></a>Automatikus hangolás
Az Azure SQL-adatbázisok automatikusan hangolhassa adatbázis teljesítményének alkalmazásával [teljesítmény javaslatok](sql-database-advisor.md). További tudnivalókért olvassa el a [automatikus hangolási cikk](sql-database-automatic-tuning.md). Az engedélyezéshez, olvassa el a [automatikus hangolása engedélyezése](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Lekérdezési terheléselemző
[Lekérdezési Terheléselemzőhöz](sql-database-query-performance.md) lehetővé teszi, hogy az adatbázis teljesítményét, adja meg a hibaelhárítási kevesebb időt:

* Az adatbázisok (DTU) erőforrás-felhasználás mélyebb betekintést. 
* A felső CPU erőforrásigényes lekérdezések, amely potenciálisan a jobb teljesítmény kell beállítani. 
* Részletekbe menően tárhatják fel a részletek a lekérdezés képessége. 

  ![teljesítmény irányítópult](./media/sql-database-query-performance/performance.png)

Ez a lap további információt talál a cikk a  **[használata a lekérdezési Terheléselemző](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>További források
* [Az önálló adatbázisok Azure SQL Database teljesítményét útmutatást](sql-database-performance-guidance.md)
* [Ha egy rugalmas készlethez használ?](sql-database-elastic-pool-guidance.md)

