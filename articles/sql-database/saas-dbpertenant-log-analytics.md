---
title: "Log Analytics használata SQL Database több-bérlős alkalmazással | Microsoft Docs"
description: "A telepítő és Naplóelemzés (OMS) használja egy több-bérlős Azure SQL adatbázis SaaS-alkalmazáshoz"
keywords: "sql database-oktatóanyag"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: b141ca521ae9c4d9bf6a4be620bc8e5432c52f83
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="set-up-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Állítson be és Naplóelemzés (OMS) használja egy több-bérlős Azure SQL adatbázis SaaS-alkalmazáshoz

Ebben az oktatóanyagban beállítása és használata *Naplóelemzési ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* a rugalmas készletek és adatbázisokat figyeli. Ez az oktatóanyag épít, a [Teljesítményfigyelő és a felügyeleti útmutató](saas-dbpertenant-performance-monitoring.md). Azt illusztrálja, hogyan használandó *Naplóelemzési* révén a figyelés és riasztás a megadott Azure-portálon. Naplóelemzési figyelési ezer rugalmas készletek és a több száz akár több ezer adatbázis támogatja. A Naplóelemzési egy egyetlen figyelési megoldást, amely integrálható a különböző alkalmazások és az Azure-szolgáltatások, az több Azure-előfizetések átívelő figyelésére is biztosít.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Log Analytics (OMS) telepítése és konfigurálása
> * A Log Analytics segítségével készleteket és adatbázisokat figyelhet

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Az SaaS-forgatókönyveknek és -mintáknak és azok figyelési megoldásokkal szemben támasztott követelményekre gyakorolt hatásának a megbeszélését lásd a [Teljesítményfigyelés és kezelés oktatóanyagban](saas-dbpertenant-performance-monitoring.md).

## <a name="monitoring-and-managing-database-and-elastic-pool-performance-with-log-analytics-or-operations-management-suite-oms"></a>Figyelés és Log Analytics vagy az Operations Management Suite (OMS) adatbázis és a rugalmas készlet a teljesítmény kezelése

SQL-adatbázis figyelés és riasztás érhető el az adatbázisok és a készletek az Azure portálon. A beépített figyelés és riasztás kényelmesen használható, de éppen erőforrás-specifikus, akkor megfelelően megfelelő nagyobb telepítések figyelése, vagy az egységes nézetének köszönhetően erőforrásokat és -előfizetések között.

Nagy mennyiségű forgatókönyvek esetén Naplóelemzési alkalmas figyelés és riasztás. A Naplóelemzési egy, külön Azure szolgáltatás, amely lehetővé teszi az elemzés során diagnosztikai naplók és potenciálisan sok szolgáltatások munkaterület összegyűjtött telemetrikus. Naplóelemzési egy lekérdezési nyelv és az adatok a képi megjelenítés olyan eszközöket biztosít operatív adatelemzés lehetővé. Az SQL-elemzési megoldások tartalmaz számos előre definiált rugalmas készlet és adatbázis figyelési és riasztási nézetek és lekérdezések. Az OMS egyéni nézettervező is biztosít.

A Log Analytics-munkahelyek és elemzési megoldások az Azure Portalon és az OMS-ben is megnyithatók. Az Azure Portal az újabb hozzáférési pont, de lehet, hogy egyes területeken az OMS-portál mögött marad.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>A bérlők a munkaterhelést szimulál diagnosztikai teljesítményadatok létrehozása 

1. Az a **PowerShell ISE**, nyissa meg *... \\WingtipTicketsSaaS főkiszolgálós MultiTenantDb\\tanulási modulok\\Teljesítményfigyelő és felügyeleti\\** bemutató-PerformanceMonitoringAndManagement.ps1***. Tartsa nyitva ezt a szkriptet, mivel előfordulhat, hogy az oktatóanyag használata közben szeretne több terhelésgenerálási forgatókönyvet is futtatni.
1. Ha még nem tette meg, kiépítése érdekesebb figyelési környezetet biztosít a bérlők egy tranzakcióköteghez. Ez néhány percet vesz igénybe:
   1. Állítsa be **$DemoScenario = 1**, _bérlő köteg kiépítése_
   1. Futtassa a parancsfájlt, és központi telepítése egy további 17 bérlők, nyomja meg a **F5**.  

1. Most indítsa el a terhelés generátor szimulált teher futtathatnak a bérlők számára.  
    1. Állítsa be **$DemoScenario = 2**, _Generate normál intenzitásának terhelés (KB. 30 DTU)_.
    1. A parancsfájl futtatásához nyomja le az **F5**.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek PowerShell-parancsfájlok lépéseit.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>A Log Analytics és az Azure SQL Analytics megoldás telepítése és konfigurálása

A Log Analytics egy különálló szolgáltatás, amelyet konfigurálni kell. A Naplóelemzési naplóadatokat, telemetriai adatok és a log analytics-munkaterület metrikák gyűjti. A naplóelemzési munkaterület erőforrás, csakúgy, mint más erőforrások, az Azure-ban, és létre kell hozni. Amíg a munkaterületen hozhatók létre ugyanabban az erőforráscsoportban, az alkalmazás nem szükséges általa figyelt, ez gyakran így elérhetővé válnak a legtöbb logika. A Wingtip jegyek alkalmazás esetén a egyetlen erőforráscsoportként működnek biztosítja az alkalmazással együtt törlődik a munkaterületen.

1. Az a **PowerShell ISE**, nyissa meg *... \\WingtipTicketsSaaS főkiszolgálós MultiTenantDb\\tanulási modulok\\Teljesítményfigyelő és felügyeleti\\Analytics jelentkezzen\\** bemutató-LogAnalytics.ps1***.
1. A parancsfájl futtatásához nyomja le az **F5**.

Ezen a ponton a nyitott Naplóelemzési tudja az Azure-portál (vagy az OMS-portállal) kell lennie. Telemetria kell gyűjteni a Naplóelemzési munkaterület, és láthatóvá válnak néhány percet vesz igénybe. A továbbiakban hagyja a rendszer a ennél is érdekesebb megoldást a szolgáltatás diagnosztikai adatok összegyűjtése. Eljött a pillanat, hogy magához vegyen valamilyen frissítőt, csak hagyja nyugodtan futni a terhelésgenerátort!

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Készletek és adatbázisok figyelése a Log Analytics és az SQL Analytics megoldással


Ebben a gyakorlatban nyissa meg a Naplóelemzési és az OMS-portálon az adatbázisok és a készletek alatt gyűjtött telemetriai adatok közül.

1. Keresse meg a [Azure-portálon](https://portal.azure.com) és megnyitásához kattintson a Naplóelemzési **minden szolgáltatás**, majd keresse meg a Naplóelemzési:

   ![Log Analytics megnyitása](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Válassza ki a munkaterület nevű _wtploganalytics -&lt;felhasználói&gt;_.

1. Válassza az **Áttekintés** lehetőséget a Log Analytics megoldás megnyitásához az Azure Portalon.

   ![áttekintés-hivatkozás](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Szükség lehet néhány perc elteltével a megoldás nem aktív. Legyen türelemmel!

1. Kattintson az Azure SQL Analytics csempére a megnyitásához.

    ![Áttekintés](media/saas-dbpertenant-log-analytics/overview.png)

    ![analytics](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. A nézetek a megoldás az oldalt, görgessen a saját belső görgetősáv alján (frissítse a lapot, ha szükséges).

1. Az összefoglalás lapon megismerkedhet a csempéket, vagy egy részletes explorer megnyitásához egyedi adatbázis kattintva.

1. Módosítsa a szűrőt időintervallumát - e oktatóanyag kivételezési módosítandó beállítás _elmúlt 1 óra_

    ![időszűrője](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Válassza ki a lekérdezés használatát és az adatbázishoz tartozó metrikákat felfedezése egy adatbázist.

    ![adatbázis elemzés](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. A használati metrikák görgessen a analytics lap jobb.
 
     ![adatbázis metrikák](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Görgessen az analytics lap bal oldalán, majd kattintson a kiszolgáló csempére az erőforrás adatait listában. Ekkor megnyílik egy oldal, a készletek és adatbázisokat megjelenítő a kiszolgálón. 

     ![Erőforrás-adatai](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

 
     ![a készletek és adatbázisok kiszolgáló](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. A kiszolgálón, amely megnyíló lapon látható a készletek és a kiszolgálón lévő adatbázis kattintson a készlet.  A készlet lapon görgessen jobbra a készlet metrikák megjelenítéséhez.  

     ![készlet metrikák](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)



1. Vissza a Naplóelemzési munkaterület válassza **OMS-portálon** van a munkaterület megnyitásához.

    ![oms](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Az OMS-portálon ismerje meg a napló és a metrika adatok további a munkaterületen.  

A figyelés és riasztás Naplóelemzés és az OMS lekérdezéseken alapul az adatokat a munkaterületen eltérően a riasztási definiált az egyes erőforrások az Azure portálon keresztül. Riasztások sablon a lekérdezések, egy riasztást, amely felett az összes adatbázisra, nem pedig adatbázisonként definiáló egy definiálhat. A lekérdezéseket csak a munkaterületen elérhető adatok korlátozzák.

További információ az OMS használatával lekérdezése, és állítson be riasztásokat, a megtekintéséhez [Naplóelemzési a riasztási szabályok használata](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Az SQL Database-ben a Log Analytics díjszabása a munkaterületen található adatmennyiségtől függ. Ebben az oktatóanyagban létrehozott egy szabad munkaterület, amely korlátozódik 500 MB / nap. Felső határ elérésekor adatok nem kerülnek a munkaterületen.


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Log Analytics (OMS) telepítése és konfigurálása
> * A Log Analytics segítségével készleteket és adatbázisokat figyelhet

[Bérlői elemzések – oktatóanyag](saas-dbpertenant-log-analytics.md)

## <a name="additional-resources"></a>További források

* [További oktatóprogramot kínál, amelyek a kezdeti Wingtip jegyek SaaS adatbázis / bérlői alkalmazástelepítés épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
