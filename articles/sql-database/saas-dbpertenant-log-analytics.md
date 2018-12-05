---
title: Log Analytics használata SQL Database több-bérlős alkalmazással |} A Microsoft Docs
description: Állítsa be, és a Log Analytics használata egy több-bérlős Azure SQL Database SaaS-alkalmazás
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 58a354950e303eda8158ca072870bfecbf33a89f
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890211"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Állítsa be, és a Log Analytics használatához egy több-bérlős SQL Database SaaS-alkalmazással

Ebben az oktatóanyagban beállítása és használata Azure [Log Analytics](/azure/log-analytics/log-analytics-overview) rugalmas készletek és adatbázisok figyelésére. Ebben az oktatóanyagban épül, amely a [teljesítmény figyelése és kezelése oktatóanyag](saas-dbpertenant-performance-monitoring.md). Megjeleníti a Log Analytics használata, mivel megvédi a figyelési és riasztási biztosított az Azure Portalon. A log Analytics támogatja a rugalmas készletek több ezer figyelési és több százezer adatbázist. Log Analytics biztosít egy egyetlen figyelési megoldást, amely több Azure-előfizetések között a különböző alkalmazások és az Azure-szolgáltatások figyelését is integrálhatja.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Telepítse és konfigurálja a Log Analytics.
> * A Log Analytics segítségével készleteket és adatbázisokat figyelhet.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezése és felfedezése a Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás](saas-dbpertenant-get-started-deploy.md).
* Az Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Tekintse meg a [teljesítmény figyelése és kezelése oktatóanyag](saas-dbpertenant-performance-monitoring.md) említett SaaS-forgatókönyveket és a mintákat és azok figyelési megoldást a követelményeknek.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>A Log Analytics használatával adatbázisok és a rugalmas készlet teljesítményének figyelése és kezelése

Az Azure SQL-adatbázis monitorozási és riasztási érhető el adatbázisokhoz és készletekhez az Azure Portalon. A beépített figyelés és riasztás akkor hasznos, de erőforrás-specifikus. Ez azt jelenti, hogy kevésbé alkalmas nagy telepítések figyelésére, vagy adjon meg egy egységesített nézetben minden erőforrásban és előfizetésnél.

Nagy mennyiségű forgatókönyvek esetén használhatja a Log Analytics figyelés és riasztás céljából. A log Analytics egy külön Azure-szolgáltatás, amely lehetővé teszi elemzési diagnosztikai naplók és a egy adott munkaterület potenciálisan sok szolgáltatás összegyűjtött telemetriai keresztül. Log Analytics biztosít beépített lekérdezési nyelvet és adatvizualizációs eszközöket, amelyek lehetővé teszik a működési adatok elemzését. Az SQL Analytics megoldás számos előre definiált rugalmas készlet és -adatbázis figyelési és riasztási nézeteket és lekérdezéseket biztosít. A log Analytics egyéni Nézettervező is biztosít.

Az OMS-munkaterületeket mostantól Log Analytics-munkaterületeknek nevezzük. Az Azure Portalon nyissa meg a log Analytics munkahelyek és elemzési megoldások. Az Azure portal az újabb hozzáférési pont, de elképzelhető, hogy mi az egyes területeken az Operations Management Suite portál mögött.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>A bérlők számára a számítási feladatok szimulálásával diagnosztikai teljesítményadatok létrehozása 

1. A PowerShell ISE-ben nyissa meg a *... \\WingtipTicketsSaaS főkiszolgálóval MultiTenantDb\\tanulási modulok\\alkalmazásteljesítmény-figyelési és felügyeleti\\Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa nyitva ezt a szkriptet, mivel előfordulhat, hogy szeretne több terhelésgenerálási forgatókönyvet során futtatandó ebben az oktatóanyagban.
1. Ha Ön még nem tette, hogy érdekesebb figyelési környezetet a bérlők kötegelt kiépítése. Ez a folyamat pár percet vesz igénybe.

   a. Állítsa be **$DemoScenario = 1**, _bérlők kötegelt kiépítése_.

   b. Futtassa a szkriptet, és üzembe helyezése egy további 17 bérlőt, nyomja meg az F5 billentyűt.

1. Most indítsa el a terhelésgenerátor egy szimulált terhelés futtathatók a bérlők számára.

    a. Állítsa be **$DemoScenario = 2**, _normál intenzitású terhelés létrehozása (KB. 30 DTU)_.

    b. A szkript futtatásához nyomja le az F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazásszkriptek beolvasása

A Wingtip Tickets SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-adattárban. Töltse le és feloldása a Wingtip Tickets PowerShell-parancsfájlok lépéseiért lásd: a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>A Log Analytics és az Azure SQL Analytics megoldás telepítése és konfigurálása

A log Analytics egy külön szolgáltatás, amelyet be kell állítani a. A log Analytics naplóadatokat, a telemetriai adatok és a egy Log Analytics-munkaterület metrikákat gyűjt. További erőforrások az Azure-ban, mint a Log Analytics-munkaterületen kell létrehozni. A munkaterületet nem szükséges, mint az alkalmazások figyeli ugyanabban az erőforráscsoportban kell létrehozni. Ezért gyakran ez a legésszerűbb azonban. A Wingtip Tickets alkalmazás egyetlen erőforráscsoportot használ, hogy a munkaterületet törölték az alkalmazással.

1. A PowerShell ISE-ben nyissa meg a *... \\WingtipTicketsSaaS főkiszolgálóval MultiTenantDb\\tanulási modulok\\alkalmazásteljesítmény-figyelési és felügyeleti\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. A szkript futtatásához nyomja le az F5.

Most megnyithatja a Log Analytics az Azure Portalon. A Log Analytics-munkaterületet a telemetriai adatok gyűjtésére, és látható néhány percet vesz igénybe. A továbbiakban hagyja a rendszer gyűjtse az diagnosztikai adatokat, annál több érdekes, a felhasználói élményt. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Készletek és adatbázisok figyelése a Log Analytics és az SQL Analytics megoldással


Ebben a gyakorlatban az Azure Portalon, és tekintse meg az adatbázisokhoz és készletekhez gyűjtött telemetriai adatokat a Log Analytics megnyitása.

1. Keresse fel az [Azure Portalt](https://portal.azure.com). Válassza ki **minden szolgáltatás** Log Analytics megnyitásához. Ezután keresse meg a Log Analytics.

   ![Nyissa meg a Log Analytics](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Válassza ki a munkaterületet nevű _wtploganalytics -&lt;felhasználói&gt;_.

1. Válassza az **Áttekintés** lehetőséget a Log Analytics megoldás megnyitásához az Azure Portalon.

   ![Áttekintés](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Eltarthat néhány percig, amíg a megoldás aktívvá válik. 

1. Válassza ki a **Azure SQL Analytics** csempére annak megnyitásához.

    ![Áttekintés csempe](media/saas-dbpertenant-log-analytics/overview.png)

1. A nézetek a megoldás az oldalra, a saját belső görgetősáv alján görgessen. Ha szükséges, frissítse az oldalt.

1. Ismerje meg az Összegzés lapon, jelölje ki a csempék vagy az önálló adatbázisok a Lehatolás megnyitása.

    ![Log Analytics-irányítópult](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Módosítsa a szűrőt beállítást módosíthatja az időtartományt. A jelen oktatóanyag esetében válassza ki a **elmúlt 1 órára**.

    ![Időszűrő](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Válasszon egy önálló adatbázist, a lekérdezések használata és az adatbázishoz tartozó mérőszámok megismeréséhez.

    ![Adatbázis-elemzés](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Használati metrikák megtekintéséhez görgessen jobbra az elemzési lap.
 
     ![Adatbázis-metrikák](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Görgessen az elemzési lap bal oldalán, és válassza ki a kiszolgáló csempét a **erőforrásadatok** listája.  

    ![Rendszererőforrás-lista adatai](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Megnyílik egy oldal, amely bemutatja a készletek és adatbázisok a kiszolgálón.

    ![A készletek és adatbázisok kiszolgáló](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Válassza ki a készlet. A készlet megnyíló lapon görgessen jobbra a készlet metrikákat. 

    ![Készletekkel kapcsolatos metrikák](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Vissza a Log Analytics-munkaterületen, válassza ki **OMS-portálon** , nyissa meg a munkaterület létezik.

    ![Log Analytics-munkaterület](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

A Log Analytics munkaterületen áttekintheti a napló- és metrikaadatokat adatok további. 

Monitorozási és riasztási a Log Analytics alapulnak lekérdezések az adatok a munkaterületen, ellentétben a riasztási meghatározott egyes erőforrások az Azure Portalon keresztül. Riasztások alapozva lekérdezéseket, definiálhat egy riasztást, amely minden adatbázis ahelyett, hogy adatbázisonként meghatározó egy keresztül. Lekérdezések csak a munkaterületen elérhető adatok korlátozza.

Lekérdezése és riasztásokat állíthat be a Log Analytics használatával további információkért lásd: [használata a Log Analytics riasztási szabályai](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Log Analytics díjszabása a munkaterületen található adatmennyiség alapján SQL Database díjszabását. Ebben az oktatóanyagban létrehozott egy ingyenes munkaterületet, amely korlátja 500 MB / nap. Ez a korlát elérése után adatok nem kerülnek a munkaterületre.


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Telepítse és konfigurálja a Log Analytics.
> * A Log Analytics segítségével készleteket és adatbázisokat figyelhet.

Próbálja ki a [bérlői elemzések – oktatóanyag](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>További források

* [További oktatóanyagokat szeretnénk a hozhat létre a Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás kezdeti üzembe helyezése](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../azure-monitor/insights/azure-sql.md)
