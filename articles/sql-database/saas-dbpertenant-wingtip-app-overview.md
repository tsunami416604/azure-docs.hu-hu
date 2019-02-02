---
title: Az Azure SQL Database több-bérlős példa – a Wingtip SaaS |} A Microsoft Docs
description: Ismerje meg az Azure SQL Database, a Wingtip SaaS-példa több-bérlős mintaalkalmazás segítségével
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: bbebfca1684cad600d7ef0cc1a4cff2201fdc07e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566146"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Egy több-bérlős SaaS-alkalmazás a bérlőnkénti adatbázis mintát használ, az SQL Database bemutatása

A Wingtip SaaS-alkalmazás egy több-bérlős mintaalkalmazás. Az alkalmazás a bérlőnkénti adatbázis SaaS-alkalmazásmintát használ több bérlő kiszolgálásához. Az alkalmazás bemutatja az Azure SQL Database funkcióit, amelyek lehetővé teszik az SaaS-forgatókönyveket használatával több SaaS kialakításokat és felügyeleti mintákat. Gyorsan elsajátíthatja a használatát, hogy a Wingtip SaaS-alkalmazás üzembe helyez, kevesebb mint öt perc alatt.

Forrás kód és a felügyelet alkalmazásszkriptek érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-adattárban. Mielőtt elkezdené, tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip Tickets felügyeleti parancsfájlokat.

## <a name="application-architecture"></a>Alkalmazásarchitektúra

A Wingtip SaaS-alkalmazás a bérlőnkénti adatbázis modellt használja. Rugalmas SQL-készleteket használ a hatékonyság. Kiépítés és leképezés bérlők számára az adataikhoz, a katalógus-adatbázist használnak. A mag Wingtip SaaS-alkalmazás három minta bérlővel rendelkező készlet, valamint a katalógus-adatbázist használja. A katalógus és a bérlői kiszolgálók a DNS-aliasokat vannak kiépítve. Ezek az aliasok használhatók egy hivatkozást a Wingtip alkalmazás által használt aktív erőforrások karbantartása. Ezek az aliasok frissítve lett, hogy a vész-helyreállítási oktatóanyagok helyreállítási erőforrások mutasson. A Wingtip SaaS oktatóanyagok eredményeket az első üzembe helyezés bővítmények számos befejezése. A bővítmények például elemzési adatbázisok és adatbázisok közötti Sémakezelés jelennek meg.


![A Wingtip SaaS-architektúra](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Nyissa meg az oktatóanyagok, és az alkalmazás használata, összpontosíthat a SaaS-minták az adatréteg valamelyikéhez kapcsolódnak. Más szóval az adatréteg összpontosíthat, és nem overanalyze maga az alkalmazás. Ezek SaaS végrehajtásának megértése minták megvalósításának ezeknek a mintáknak az alkalmazásaiban kulcs. Is érdemes lehet saját speciális üzleti igényeinek szükséges minden módosítást.

## <a name="sql-database-wingtip-saas-tutorials"></a>Az SQL Database – a Wingtip SaaS-oktatóanyagok

Miután telepíti az alkalmazást, Fedezze fel az alábbi oktatóanyagok, amely az első üzembe helyezés épülnek. Ezekben az oktatóanyagokban ismerje meg a gyakori SaaS-minták az SQL Database, Azure SQL Data warehouse-bA és más Azure-szolgáltatások beépített funkcióinak kihasználását. Oktatóanyagok a PowerShell-szkripteket részletes leírásokkal közé tartozik. A magyarázatokat egyszerűbben megértése és megvalósítása az azonos SaaS-felügyeleti mintáknak az alkalmazásaiban.


| Oktatóanyag | Leírás |
|:--|:--|
| [Például az SQL Database több-bérlős SaaS alkalmazás tanácsokat és javaslatokat](saas-tenancy-wingtip-app-guidance-tips.md) | Töltse le és futtassa a PowerShell-parancsfájlok készíti elő az alkalmazás részei. |
|[Üzembe helyezése és megismerése a Wingtip SaaS-alkalmazás](saas-dbpertenant-get-started-deploy.md)|  Üzembe helyezése, és ismerje meg a Wingtip SaaS-alkalmazás az Azure-előfizetésében. |
|[Kiépítéssel és katalogizálással bérlők](saas-dbpertenant-provision-and-catalog.md)| Ismerje meg, hogy az alkalmazás bérlők használatával csatlakozik egy katalógus-adatbázist, és hogyan a katalógus leképezi a adataikat bérlők. |
|[Teljesítményének figyelése és kezelése](saas-dbpertenant-performance-monitoring.md)| Megtudhatja, hogyan használhatja az SQL Database figyelési funkcióit, és riasztásokat állíthat be, amikor a teljesítmény-küszöbérték túllépése. |
|[Monitorozás az Azure Log Analytics](saas-dbpertenant-log-analytics.md) | Ismerje meg, hogyan használható [Log Analytics](../log-analytics/log-analytics-overview.md) több készlet figyelése a nagy mennyiségű erőforrást. |
|[Egyetlen bérlő visszaállítása](saas-dbpertenant-restore-single-tenant.md)| Ismerje meg, hogy a bérlői adatbázis visszaállítása egy korábbi időpontra időben. Is megtudhatja, hogyan állíthatja vissza egy párhuzamos adatbázis, amely a meglévő bérlői adatbázis online hagyja. |
|[Bérlői adatbázis séma kezelése](saas-tenancy-schema-management.md)| Megtudhatja, hogyan séma frissítésére, és frissíti a referenciaadatokat összes bérlői adatbázison. |
|[Több-bérlős elosztott lekérdezések futtatása](saas-tenancy-cross-tenant-reporting.md) | Hozzon létre egy alkalmi elemzési adatbázist, és valós idejű elosztott lekérdezések futtatása az összes bérlőre kiterjedő.  |
|[Elemzések futtatására a kinyert bérlő adatai](saas-tenancy-tenant-analytics.md) | Bontsa ki a bérlő adatai, egy elemzési adatbázis, sem az adattárházra offline elemzési lekérdezések. |


## <a name="next-steps"></a>További lépések

- [A Wingtip Tickets SaaS-alkalmazás példával vagy központi tanácsokat és általános](saas-tenancy-wingtip-app-guidance-tips.md)
- [A Wingtip SaaS-alkalmazás üzembe helyezése](saas-dbpertenant-get-started-deploy.md)
