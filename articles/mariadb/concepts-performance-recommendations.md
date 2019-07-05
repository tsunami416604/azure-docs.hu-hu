---
title: Teljesítménnyel kapcsolatos javaslatok az Azure Database for MariaDB
description: Ez a cikk ismerteti a teljesítmény javaslat funkció az Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a2f9b7597022822272692d20976e1da654b9d524
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462052"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Teljesítménnyel kapcsolatos javaslatok az Azure Database for MariaDB

**A következőkre vonatkozik:** Azure Database for MariaDB 10.2

> [!NOTE]
> Teljesítménnyel kapcsolatos javaslatok az előzetes verzióban.

A teljesítménnyel kapcsolatos javaslatok funkció elemzi az adatbázisokat hozhat létre személyre szabott javaslatok a jobb teljesítmény érdekében. Az ajánlások előállításához, különböző adatbázis jellemzőit, beleértve a sémát az elemzés megvizsgálja. Engedélyezése [Query Store](concepts-query-store.md) használja ki teljesen a teljesítménnyel kapcsolatos javaslatok a szolgáltatás a kiszolgálón. Ha teljesítmény-séma ki, ne tudják bekapcsolni a Query Store lehetővé teszi a performance_schema és a egy részét a szolgáltatás számára szükséges teljesítményt séma eszközöket. Után minden teljesítmény javaslat megvalósítása, tesztelnie kell a teljesítményt, ezek a módosítások hatásának vizsgálatában.

## <a name="permissions"></a>Engedélyek

A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

Nyissa meg **teljesítménnyel kapcsolatos javaslatok** származó a **intelligens teljesítmény** a menüsávon a MariaDB-kiszolgáló az Azure portál oldalán szakaszában.

![A Teljesítménnyel kapcsolatos javaslatok kezdőlapja](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Válassza ki **elemzés** és a egy adatbázis, amely megkezdi az elemzés válassza. Attól függően, a számítási feladatok a az elemzés végrehajtásához több percig is eltarthat. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés. Elemzés az adatbázis egy részletes vizsgálatot hajt végre. Ajánlott csúcsidőn kívüli időszakokban elemzését.

A **javaslatok** ablakban jelennek meg javaslatok, ha találhatók esetleges és a lekérdezés Azonosítóját, a javaslat okozó listáját. A lekérdezés Azonosítóját, a segítségével a [mysql.query_store](concepts-query-store.md#mysqlquery_store) további információ a lekérdezés nézetben.

![Teljesítmény javaslatok új lap](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Javaslatok a rendszer nem alkalmazza automatikusan. A javaslat alkalmazása, a lekérdezés szövegének másolása, és futtassa azt az ügyfél által választott. Tesztelje a vágólapra, és értékelheti ki az ajánlás figyelése.

## <a name="recommendation-types"></a>Az ajánlás típusok

Jelenleg csak *a Create Index* javaslatok támogatottak.

### <a name="create-index-recommendations"></a>Ajánlott indexek létrehozása

*Index létrehozása* ajánlások javaslatot a leggyakrabban a számítási feladat futtatható vagy időigényes lekérdezések felgyorsításához új indexeket. Ez az ajánlás típusához [Query Store](concepts-query-store.md) engedélyezni kell. Query Store lekérdezés adatokat gyűjt, és az elemzés segítségével győződjön meg arról, a javaslat részletes lekérdezési modul és a gyakoriság statisztikák biztosít.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [megfigyelés és finomhangolás](concepts-monitoring.md) MariaDB-hez készült Azure Database-ben.