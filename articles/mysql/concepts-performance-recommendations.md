---
title: Teljesítménnyel kapcsolatos javaslatok az Azure Database for MySQL-hez
description: Ez a cikk ismerteti a teljesítmény javaslat funkció az Azure Database for MySQL-hez
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 569ef6e9f91fdd728c5d230e2a6c46a7b01e5a62
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078819"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Teljesítménnyel kapcsolatos javaslatok az Azure Database for MySQL-hez

**A következőkre vonatkozik:** Azure Database for MySQL 5.7.

> [!NOTE]
> Teljesítménnyel kapcsolatos javaslatok az előzetes verzióban. Teljesítménnyel kapcsolatos javaslatok támogatása az Azure Portalon tesszük elérhetővé, és előfordulhat, hogy még nem érhető el a régióban.

A teljesítménnyel kapcsolatos javaslatok funkció elemzi az adatbázisokat hozhat létre személyre szabott javaslatok a jobb teljesítmény érdekében. Az ajánlások előállításához, különböző adatbázis jellemzőit, beleértve a sémát az elemzés megvizsgálja. Engedélyezése [Query Store](concepts-query-store.md) használja ki teljesen a teljesítménnyel kapcsolatos javaslatok a szolgáltatás a kiszolgálón. Ha teljesítmény-séma ki, ne tudják bekapcsolni a Query Store lehetővé teszi a performance_schema és a egy részét a szolgáltatás számára szükséges teljesítményt séma eszközöket. Után minden teljesítmény javaslat megvalósítása, tesztelnie kell a teljesítményt, ezek a módosítások hatásának vizsgálatában.

## <a name="permissions"></a>Engedélyek

A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

Nyissa meg **teljesítménnyel kapcsolatos javaslatok** származó a **intelligens teljesítmény** a menüsávon a MySQL-kiszolgáló az Azure portál oldalán szakaszában.

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
- Tudjon meg többet [megfigyelés és finomhangolás](concepts-monitoring.md) az Azure Database for MySQL-hez.