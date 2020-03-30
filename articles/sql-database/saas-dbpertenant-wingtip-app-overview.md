---
title: Több-bérlős alkalmazás példa - Wingtip SaaS
description: Ismerje meg az Azure SQL Database-t használó több-bérlős mintaalkalmazás használatával a Wingtip SaaS például
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 46cdcd5f768278dbc729f48e450c68a63be604be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256496"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Bevezetés egy több-bérlős SaaS-alkalmazásba, amely a bérlőnkénti adatbázis-mintát használja az SQL Database-rel

A Wingtip SaaS-alkalmazás egy minta több-bérlős alkalmazás. Az alkalmazás a bérlőnkénti adatbázis-SaaS-alkalmazásmintát használja több bérlő kiszolgálásához. Az alkalmazás bemutatja az Azure SQL Database olyan funkcióit, amelyek számos SaaS-tervezési és felügyeleti minta használatával engedélyezik az SaaS-forgatókönyveket. A Wingtip SaaS alkalmazás gyors üzembe helyezése és gyors üzembe helyezése érdekében kevesebb mint öt perc alatt üzembe helyezhető.

Az alkalmazás forráskódja és felügyeleti parancsfájljai a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárházban érhetők el. Mielőtt elkezdené, tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets felügyeleti parancsfájlok letöltésének és feloldásának lépéseit.

## <a name="application-architecture"></a>Alkalmazásarchitektúra

A Wingtip SaaS alkalmazás a bérlőnkénti adatbázis-modellt használja. Sql rugalmas készletek et használ a hatékonyság maximalizálása érdekében. A bérlők kiépítéséhez és hozzárendeléséhez az adataikhoz egy katalógus-adatbázist használ. Az alapvető Wingtip SaaS-alkalmazás három mintabérlővel, valamint a katalógus-adatbázissal rendelkező készletet használ. A katalógus- és bérlői kiszolgálók DNS-aliasokkal vannak kiépítve. Ezek az aliasok a Wingtip alkalmazás által használt aktív erőforrásokra mutató hivatkozás fenntartására szolgálnak. Ezek az aliasok frissülnek, hogy a vész-helyreállítási oktatóanyagok helyreállítási erőforrásokra mutassanak. A Wingtip SaaS számos oktatóanyagának befejezése a kezdeti üzembe helyezéshez bővítményeket eredményez. A rendszer bővítményeket, például analitikus adatbázisokat és adatbázisközi sémakezelést vezet be.


![Wingtip SaaS architektúra](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Az oktatóanyagok on-át, és az alkalmazással való munka során összpontosítson a SaaS-mintákra, mivel azok az adatréteghez kapcsolódnak. Más szóval összpontosítson az adatrétegre, és ne elemezze túl magát az alkalmazást. Ezek a SaaS-minták megvalósításának megértése kulcsfontosságú ezek a minták megvalósítása az alkalmazásokban. Is vegye figyelembe a szükséges módosításokat az Ön konkrét üzleti követelményeknek.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS oktatóanyagok

Az alkalmazás üzembe helyezése után fedezze fel az alábbi oktatóanyagokat, amelyek a kezdeti üzembe helyezésre épülnek. Ezek az oktatóanyagok olyan gyakori SaaS-mintákat tárnak fel, amelyek kihasználják az SQL Database, az Azure SQL Data Warehouse és más Azure-szolgáltatások beépített szolgáltatásait. Az oktatóanyagok közé tartoznak a PowerShell-parancsfájlok részletes magyarázatokkal. A magyarázatok egyszerűsítik az alkalmazások ban lévő SaaS-felügyeleti minták megértését és megvalósítását.


| Oktatóanyag | Leírás |
|:--|:--|
| [Útmutató és tippek az SQL Database több-bérlős SaaS-alkalmazáshoz példa](saas-tenancy-wingtip-app-guidance-tips.md) | PowerShell-parancsfájlok letöltése és futtatása az alkalmazás egyes részeinek előkészítéséhez. |
|[A Wingtip SaaS alkalmazás üzembe helyezése és felfedezése](saas-dbpertenant-get-started-deploy.md)|  Üzembe helyezheti és felfedezheti a Wingtip SaaS-alkalmazást az Azure-előfizetésével. |
|[Kiépítési és katalógus-bérlők](saas-dbpertenant-provision-and-catalog.md)| Megtudhatja, hogy az alkalmazás hogyan csatlakozik a bérlőkhöz egy katalógus-adatbázis használatával, és hogyan képezi le a katalógus a bérlőket az adataikhoz. |
|[Teljesítmény figyelése és kezelése](saas-dbpertenant-performance-monitoring.md)| Ismerje meg, hogyan használhatja az SQL Database figyelési funkcióit, és hogyan állíthat be riasztásokat a teljesítményküszöbértékek túllépése esetén. |
|[Monitorozás az Azure Monitor naplóival](saas-dbpertenant-log-analytics.md) | Ismerje meg, hogyan [használhatja](../log-analytics/log-analytics-overview.md) az Azure Monitor naplók nagy mennyiségű erőforrás figyelésére több készlet között. |
|[Egyetlen bérlő visszaállítása](saas-dbpertenant-restore-single-tenant.md)| Ismerje meg, hogyan állíthatja vissza a bérlői adatbázist egy korábbi időpontra. Azt is megtudhatja, hogyan lehet visszaállítani egy párhuzamos adatbázisba, amely a meglévő bérlői adatbázis online állapotban marad. |
|[Bérlői adatbázis sémájának kezelése](saas-tenancy-schema-management.md)| Ismerje meg, hogyan frissítheti a sémát, és frissítheti a referenciaadatokat az összes bérlői adatbázisban. |
|[Több bérlőre osztott elosztott lekérdezések futtatása](saas-tenancy-cross-tenant-reporting.md) | Hozzon létre egy ad hoc elemzési adatbázist, és valós idejű elosztott lekérdezéseket futtasson az összes bérlő között.  |
|[Elemzés futtatása kinyert bérlői adatokon](saas-tenancy-tenant-analytics.md) | Bérlői adatok kinyerése elemzési adatbázisba vagy adattárházba offline elemzési lekérdezésekhez. |


## <a name="next-steps"></a>További lépések

- [Általános útmutatás és tippek a Wingtip Tickets SaaS alkalmazás telepítéseés használata során példa](saas-tenancy-wingtip-app-guidance-tips.md)
- [A Wingtip SaaS-alkalmazás üzembe helyezése](saas-dbpertenant-get-started-deploy.md)
