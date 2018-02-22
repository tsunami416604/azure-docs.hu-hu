---
title: "Az Azure SQL adatbázis több-bérlős app példa - Wingtip SaaS |} Microsoft Docs"
description: "Ismerje meg, amely használja az Azure SQL Database, a Wingtip SaaS példa több-bérlős mintaalkalmazás használatával"
keywords: "sql database-oktatóanyag"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 2871d2b1208013808958e8a5b0c62fce31af86ec
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Egy több-bérlős Szolgáltatottszoftver-alkalmazást, amely az adatbázis típusú bérlői mintában használja az SQL Database bemutatása

A *Wingtip SaaS* alkalmazás egy több-bérlős mintaalkalmazást. Az alkalmazás használ az adatbázis per bérlői, SaaS-alkalmazás mintát, több bérlő kiszolgálására. Az alkalmazás az Azure SQL Database funkcióit, amelyek lehetővé teszik a Szolgáltatottszoftver-forgatókönyveket, több SaaS tervezési és felügyeleti minták használatával bővíthető. Gyorsan karban lehessen működik és elérhető, a Wingtip SaaS-alkalmazás telepíti, kevesebb mint öt perc alatt!

Alkalmazás forrás kódot és kezelésre szolgáló parancsfájlok érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Mielőtt nekikezdene, tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek felügyeleti parancsfájlok lépéseit.

## <a name="application-architecture"></a>Alkalmazásarchitektúra

A Wingtip SaaS-alkalmazás az adatbázis-/-bérlős modell, és használja az SQL rugalmas készletek hatékonyságának maximalizálása. A létrehozásához és a leképezés bérlők számára az adatokat a katalógus-adatbázist használja. A core Wingtip SaaS-alkalmazás, egy címkészlet, amely három minta bérlők, valamint a katalógus-adatbázis használja. A bővítmények megnyilvánulhat oktatóanyagok a kezdeti telepítés Wingtip SaaS számos befejezése elemzési adatbázis bevezetésével adatbázisok közötti séma felügyeleti stb.


![A Wingtip Szolgáltatottszoftver-architektúra](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Az oktatóanyagok keresztül haladó, és az alkalmazás dolgozik, miközben fontos a Szolgáltatottszoftver-minták összpontosítson, mivel az adatréteg kapcsolódnak. Más szavakkal, koncentráljon az adatrétegre, és ne elemezze túl magát az alkalmazást. Ezek SaaS végrehajtásának megértése mintázatok, kulcs valósít meg ezeket a mintákat a alkalmazások, figyelembe véve a szükséges módosításokat a saját speciális üzleti igényeinek.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL adatbázis Wingtip SaaS oktatóprogramok

Miután telepíti az alkalmazást, megismerkedhet a következő oktatóprogramot kínál, amelyek a kezdeti telepítés épül. Ezek az oktatóanyagok közös SQL-adatbázis, az SQL Data Warehouse és más Azure-szolgáltatások beépített funkciók előnyeit Szolgáltatottszoftver-minták felfedezése. Oktatóanyagok PowerShell-parancsfájlok, a részletes leírást, amely jelentősen egyszerűsítheti a megismeréséhez, és az azonos SaaS felügyeleti minták valósít meg az alkalmazások közé tartoznak.


| Oktatóanyag | Leírás |
|:--|:--|
| [Útmutatás és tippek az Azure SQL Database több-bérlős SaaS alkalmazás – példa](saas-tenancy-wingtip-app-guidance-tips.md) | **KEZDJE ITT!** Töltse le, és futtassa a PowerShell-parancsfájlok készíti elő az alkalmazás részei. |
|[Központi telepítése, és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](saas-dbpertenant-get-started-deploy.md)|  Központi telepítése, és vizsgálja meg a Wingtip SaaS-alkalmazás az Azure-előfizetéshez. |
|[Kiépítés és a katalógus bérlők](saas-dbpertenant-provision-and-catalog.md)| Ismerje meg, hogyan az alkalmazás a bérlők katalógus adatbázis használatával csatlakozik, és hogy a katalógus hogyan leképezi a bérlők adataikat. |
|[Megfigyelés és kezelés teljesítmény](saas-dbpertenant-performance-monitoring.md)| Útmutató: az SQL-adatbázis felügyeleti funkcióinak használatát, és riasztások beállítását, ha a teljesítmény-küszöbérték túllépése. |
|[A figyelőt Naplóelemzés (OMS)](saas-dbpertenant-log-analytics.md) | Használata [Naplóelemzési](../log-analytics/log-analytics-overview.md) nagy mennyiségű erőforrást, figyelheti több készletet között. |
|[Egy egybérlős visszaállítása](saas-dbpertenant-restore-single-tenant.md)| Megtudhatja, hogyan bérlői adatbázis visszaállítása korábbi pontra időben. Párhuzamos adatbázisba, online, így a meglévő bérlő adatbázis lépéseket is szerepelnek. |
|[Bérlői adatbázisséma kezelése](saas-tenancy-schema-management.md)| Tudnivalók a séma, és frissítésének referenciaadatok, minden bérlői adatbázisok között. |
|[Több-bérlős elosztott lekérdezések futtatása](saas-tenancy-cross-tenant-reporting.md) | Hozzon létre egy ad hoc elemzés és valós idejű elosztott lekérdezések futtatása egyetlen bérlő számára.  |
|[Elemzés futtassa a kibontott bérlői adatok](saas-tenancy-tenant-analytics.md) | Bontsa ki a bérlői adatforgalom az egy analytics adatbázis vagy a data warehouse-bA offline elemzési lekérdezések. |


## <a name="next-steps"></a>További lépések

- [Általános útmutatás és tippek, amikor üzembe helyezése és az Wingtip jegyek SaaS-alkalmazás példa](saas-tenancy-wingtip-app-guidance-tips.md)

- [A Wingtip SaaS-alkalmazás központi telepítése](saas-dbpertenant-get-started-deploy.md)
