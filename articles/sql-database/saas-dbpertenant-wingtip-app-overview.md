---
title: Az Azure SQL adatbázis több-bérlős app példa - Wingtip SaaS |} Microsoft Docs
description: Ismerje meg, amely használja az Azure SQL Database, a Wingtip SaaS példa több-bérlős mintaalkalmazás használatával
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 563d82076721a669069ba3e36df84a050188813c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Egy több-bérlős Szolgáltatottszoftver-alkalmazást, amely az adatbázis-/-bérlő mintát használ, az SQL-adatbázis bemutatása

A Wingtip SaaS-alkalmazás egy több-bérlős mintaalkalmazást. Az alkalmazás az adatbázis-/-bérlő SaaS alkalmazás mintát használ, több bérlő kiszolgálására. Az alkalmazás az Azure SQL Database funkcióit, amelyek lehetővé teszik a Szolgáltatottszoftver-forgatókönyvek több SaaS tervezési és felügyeleti minták használatával bővíthető. Gyorsan karban lehessen működik és elérhető, a Wingtip SaaS-alkalmazás öt percen belül telepíti.

Alkalmazás forrás kódot és kezelésre szolgáló parancsfájlok érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Mielőtt nekikezdene, tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek felügyeleti parancsfájlok lépéseit.

## <a name="application-architecture"></a>Alkalmazásarchitektúra

A Wingtip SaaS-alkalmazás az adatbázis-/-bérlő modellt használ. SQL rugalmas készletek hatékonyságának maximalizálása használ. A létrehozásához és a leképezés bérlők számára az adatokat a katalógus-adatbázist használja. A core Wingtip SaaS-alkalmazás egy címkészlet, amely három minta bérlők, valamint a katalógus-adatbázis használja. A kezdeti telepítés bővítmények Wingtip SaaS oktatóanyagok eredményez számos befejezése. Új, például elemzési adatbázisok és az adatbázisok közötti séma felügyeleti bővítmények.


![A Wingtip Szolgáltatottszoftver-architektúra](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Az oktatóanyagok keresztül halad, és az alkalmazás használata, összpontosítani a Szolgáltatottszoftver-minták mivel az adatréteg kapcsolódnak. Ez azt jelenti az adatréteg összpontosít, és az alkalmazás maga nem overanalyze. Ezek SaaS végrehajtásának megértése minták, valósít meg ezeket a mintákat az alkalmazások billentyűt. Is érdemes lehet a saját üzleti követelmények teljesítéséhez szükséges módosításokat.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL adatbázis Wingtip SaaS oktatóprogramok

Miután telepítette az alkalmazást, megismerkedhet a következő oktatóprogramot kínál, amelyek a kezdeti telepítés létrehozása. Ezek az oktatóanyagok közös SQL-adatbázis, az Azure SQL Data Warehouse és a más Azure-szolgáltatások beépített funkciók előnyeit Szolgáltatottszoftver-minták felfedezése. Oktatóanyagok tartalmaz részletes leírást a PowerShell-parancsfájlok. A magyarázatot ismertetése és az alkalmazások a azonos SaaS felügyeleti minták végrehajtásának egyszerűsítése érdekében.


| Oktatóanyag | Leírás |
|:--|:--|
| [Útmutatás és tippek, például az SQL-adatbázis több-bérlős SaaS alkalmazás](saas-tenancy-wingtip-app-guidance-tips.md) | Töltse le, és futtassa a PowerShell-parancsfájlok készíti elő az alkalmazás részei. |
|[Központi telepítése, és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](saas-dbpertenant-get-started-deploy.md)|  Központi telepítése, és vizsgálja meg a Wingtip SaaS-alkalmazás Azure-előfizetéséhez. |
|[Kiépítés és a katalógus bérlők](saas-dbpertenant-provision-and-catalog.md)| Ismerje meg, hogyan az alkalmazás csatlakozzon a bérlők a katalógus-adatbázis használatával, és hogy a katalógus hogyan leképezi a bérlők számára az adatokat. |
|[Megfigyelés és kezelés teljesítmény](saas-dbpertenant-performance-monitoring.md)| Megtudhatja, hogyan használja az SQL-adatbázis figyelési szolgáltatásokat, és állítson be riasztásokat, ha a teljesítmény-küszöbérték túllépése. |
|[Az Azure Naplóelemzés (az Operations Management Suite) figyelője](saas-dbpertenant-log-analytics.md) | Ismerje meg, hogyan használható [Naplóelemzési](../log-analytics/log-analytics-overview.md) nagy mennyiségű erőforrást figyeléséhez több készletet között. |
|[Egy egybérlős visszaállítása](saas-dbpertenant-restore-single-tenant.md)| Megtudhatja, hogyan bérlői adatbázis visszaállítása korábbi pontra időben. Elsajátíthatja egy párhuzamos adatbázisban, így a meglévő bérlő adatbázis online visszaállítása. |
|[Bérlői adatbázisséma kezelése](saas-tenancy-schema-management.md)| Tudnivalók a séma és frissítésének referenciaadatok összes bérlői adatbázis között. |
|[Több-bérlős elosztott lekérdezések futtatása](saas-tenancy-cross-tenant-reporting.md) | Ad hoc analytics adatbázist létrehozni, és valós idejű elosztott lekérdezések futtatása egyetlen bérlő számára.  |
|[Elemzés futtassa a kibontott bérlői adatok](saas-tenancy-tenant-analytics.md) | Bontsa ki a bérlői adatforgalom az egy analytics adatbázis vagy a data warehouse-bA offline elemzési lekérdezések. |


## <a name="next-steps"></a>További lépések

- [Általános útmutatás és tippek, amikor központilag, és használja a Wingtip jegyek SaaS-alkalmazás példát](saas-tenancy-wingtip-app-guidance-tips.md)
- [A Wingtip SaaS-alkalmazás központi telepítése](saas-dbpertenant-get-started-deploy.md)
