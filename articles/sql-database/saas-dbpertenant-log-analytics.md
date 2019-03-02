---
title: Használja az Azure Monitor naplóira egy SQL Database több-bérlős alkalmazással |} A Microsoft Docs
description: Beállítása és használata az Azure Monitor naplóira egy több-bérlős Azure SQL Database SaaS-alkalmazáshoz
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
ms.date: 01/25/2019
ms.openlocfilehash: b283f2691d2cb3135007a752348a8d9759e870f5
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214163"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Beállítása és használata az Azure Monitor naplóira egy több-bérlős SQL Database SaaS-alkalmazással

Ebben az oktatóanyagban beállítása és használata [naplózza az Azure Monitor](/azure/log-analytics/log-analytics-overview) rugalmas készletek és adatbázisok figyelésére. Ebben az oktatóanyagban épül, amely a [teljesítmény figyelése és kezelése oktatóanyag](saas-dbpertenant-performance-monitoring.md). Megjeleníti a Azure Monitor naplóira használata, mivel megvédi a figyelési és riasztási biztosított az Azure Portalon. Az Azure Monitor naplózza a rugalmas készletek több ezer és több százezer adatbázist támogatja. Az Azure Monitor naplóira biztosít egy egyetlen figyelési megoldást, amely több Azure-előfizetések között a különböző alkalmazások és az Azure-szolgáltatások figyelését is integrálhatja.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Telepítse és konfigurálja az Azure Monitor naplóira.
> * Használja az Azure Monitor naplózza a készletek és adatbázisok figyelésére.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezése és felfedezése a Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás](saas-dbpertenant-get-started-deploy.md).
* Az Azure PowerShell telepítve van. További információért lásd [az Azure PowerShell használatának első lépéseit](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Tekintse meg a [teljesítmény figyelése és kezelése oktatóanyag](saas-dbpertenant-performance-monitoring.md) említett SaaS-forgatókönyveket és a mintákat és azok figyelési megoldást a követelményeknek.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Az Azure Monitor naplóira adatbázisok és a rugalmas készlet teljesítményének figyelése és kezelése

Az Azure SQL-adatbázis monitorozási és riasztási érhető el adatbázisokhoz és készletekhez az Azure Portalon. A beépített figyelés és riasztás akkor hasznos, de erőforrás-specifikus. Ez azt jelenti, hogy kevésbé alkalmas nagy telepítések figyelésére, vagy adjon meg egy egységesített nézetben minden erőforrásban és előfizetésnél.

Nagy mennyiségű forgatókönyvek esetén használhatja az Azure Monitor naplóira figyelés és riasztás céljából. Az Azure Monitor egy külön Azure-szolgáltatás, amely lehetővé teszi elemzési diagnosztikai naplók és a egy adott munkaterület potenciálisan sok szolgáltatás összegyűjtött telemetriai keresztül. Az Azure Monitor naplóira biztosít beépített lekérdezési nyelvet és adatvizualizációs eszközöket, amelyek lehetővé teszik a működési adatok elemzését. Az SQL Analytics megoldás számos előre definiált rugalmas készlet és -adatbázis figyelési és riasztási nézeteket és lekérdezéseket biztosít. Az Azure Monitor naplóira egyéni Nézettervező is biztosít.

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

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Log Analytics-munkaterületet és az Azure SQL Analytics megoldás telepítése és konfigurálása

Az Azure Monitor az külön szolgáltatás, amelyet be kell állítani. Az Azure Monitor gyűjti naplóadatokat, telemetriai adatok és metrikák naplózza a Log Analytics-munkaterületen. További erőforrások az Azure-ban, mint a Log Analytics-munkaterületen kell létrehozni. A munkaterületet nem szükséges, mint az alkalmazások figyeli ugyanabban az erőforráscsoportban kell létrehozni. Ezért gyakran ez a legésszerűbb azonban. A Wingtip Tickets alkalmazás egyetlen erőforráscsoportot használ, hogy a munkaterületet törölték az alkalmazással.

1. A PowerShell ISE-ben nyissa meg a *... \\WingtipTicketsSaaS főkiszolgálóval MultiTenantDb\\tanulási modulok\\alkalmazásteljesítmény-figyelési és felügyeleti\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. A szkript futtatásához nyomja le az F5.

Most megnyithatja az Azure Monitor naplózza az Azure Portalon. A Log Analytics-munkaterületet a telemetriai adatok gyűjtésére, és látható néhány percet vesz igénybe. A továbbiakban hagyja a rendszer gyűjtse az diagnosztikai adatokat, annál több érdekes, a felhasználói élményt. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Log Analytics-munkaterületet és az SQL Analytics megoldás segítségével készleteket és adatbázisokat figyelhet


Ebben a gyakorlatban nyissa meg az Azure Portalon, és tekintse meg az adatbázisokhoz és készletekhez gyűjtött telemetriai adatokat a Log Analytics-munkaterületet.

1. Keresse fel az [Azure Portalt](https://portal.azure.com). Válassza ki **minden szolgáltatás** , nyissa meg a Log Analytics-munkaterületet. Ezután keresse meg a Log Analytics.

   ![Nyissa meg a Log Analytics-munkaterület](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Válassza ki a munkaterületet nevű _wtploganalytics -&lt;felhasználói&gt;_.

1. Válassza ki **áttekintése** a log analytics megoldás megnyitásához az Azure Portalon.

   ![Áttekintés](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Eltarthat néhány percig, amíg a megoldás aktívvá válik. 

1. Válassza ki a **Azure SQL Analytics** csempére annak megnyitásához.

    ![Áttekintés csempe](media/saas-dbpertenant-log-analytics/overview.png)

1. A nézetek a megoldás az oldalra, a saját belső görgetősáv alján görgessen. Ha szükséges, frissítse az oldalt.

1. Ismerje meg az Összegzés lapon, jelölje ki a csempék vagy az önálló adatbázisok a Lehatolás megnyitása.

    ![Log analytics-irányítópult](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Módosítsa a szűrőt beállítást módosíthatja az időtartományt. A jelen oktatóanyag esetében válassza ki a **elmúlt 1 órára**.

    ![Időszűrő](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Válassza ki az egyes adatbázisok megismerése a lekérdezések használata és az adatbázishoz tartozó mérőszámokat.

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

Monitorozási és riasztási az Azure monitorban naplók alapulnak lekérdezések az adatok a munkaterületen, ellentétben a riasztási meghatározott egyes erőforrások az Azure Portalon keresztül. Riasztások alapozva lekérdezéseket, definiálhat egy riasztást, amely minden adatbázis ahelyett, hogy adatbázisonként meghatározó egy keresztül. Lekérdezések csak a munkaterületen elérhető adatok korlátozza.

Lekérdezése és riasztásokat állíthat be az Azure Monitor naplóira használatáról további információkért lásd: [naplózza a riasztási szabályok az Azure Monitor használata](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Az Azure Monitor-naplókban a SQL Database díjszabását a munkaterületen található adatmennyiség alapján. Ebben az oktatóanyagban létrehozott egy ingyenes munkaterületet, amely korlátja 500 MB / nap. Ez a korlát elérése után adatok nem kerülnek a munkaterületre.


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Telepítse és konfigurálja az Azure Monitor naplóira.
> * Használja az Azure Monitor naplózza a készletek és adatbázisok figyelésére.

Próbálja ki a [bérlői elemzések – oktatóanyag](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>További források

* [További oktatóanyagokat szeretnénk a hozhat létre a Wingtip Tickets SaaS bérlőnkénti adatbázis alkalmazás kezdeti üzembe helyezése](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor-naplók](../azure-monitor/insights/azure-sql.md)
