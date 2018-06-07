---
title: Naplóelemzési használhatja egy SQL-adatbázis több-bérlős alkalmazással |} Microsoft Docs
description: Állítson be és Naplóelemzési használja egy több-bérlős Azure SQL adatbázis SaaS-alkalmazáshoz
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 38adf3dd2be0770dd815644ece452a82bc98baf9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645317"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>Állítson be és Naplóelemzési használja egy több-bérlős SQL adatbázis SaaS-alkalmazáshoz

Ebben az oktatóanyagban beállítását, és használja az Azure [Naplóelemzési](/azure/log-analytics/log-analytics-overview) rugalmas készletek és adatbázisok figyelése. Ez az oktatóanyag épít, a [teljesítmény figyelése és kezelése az oktatóanyag](saas-dbpertenant-performance-monitoring.md). Azt illusztrálja, hogyan Naplóelemzési használata révén a figyelési és riasztási megadott Azure-portálon. Naplóelemzési figyelési ezer rugalmas készletek és a több száz akár több ezer adatbázis támogatja. A Naplóelemzési egy egyetlen figyelési megoldást, amely integrálható a különböző alkalmazások és az Azure-szolgáltatásokat az több Azure-előfizetések átívelő figyelésére is biztosít.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Telepítse és konfigurálja a Naplóelemzési.
> * Log Analytics segítségével figyelheti a készletek és adatbázisokat.

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek Szolgáltatottszoftver-adatbázis egy bérlő alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS-adatbázis egy bérlő alkalmazás](saas-dbpertenant-get-started-deploy.md).
* Az Azure PowerShell telepítve van. További információkért lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

Tekintse meg a [teljesítmény figyelése és kezelése az oktatóanyag](saas-dbpertenant-performance-monitoring.md) információt a Szolgáltatottszoftver-forgatókönyvek és megoldások és hatásuk a figyelési megoldás vonatkozó követelmények.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Megfigyelés és kezelés Naplóelemzési adatbázis és a rugalmas készlet teljesítmény

Az Azure SQL Database figyelés és riasztás érhető el az adatbázisok és a készletek az Azure portálon. A beépített figyelés és riasztás akkor hasznos, de erőforrás-specifikus. Ez azt jelenti, hogy megfelelően megfelelő nagyobb telepítések figyelése, vagy meg kell egyesítve láthassák erőforrásokat és -előfizetések között.

Nagy mennyiségű forgatókönyvek esetén használható Naplóelemzési figyelés és riasztás. A Naplóelemzési egy külön Azure szolgáltatás, amely lehetővé teszi az analytics diagnosztikai naplók és potenciálisan sok szolgáltatások munkaterület összegyűjtött telemetrikus keresztül. Naplóelemzési beépített lekérdezési nyelv és az adatok a képi megjelenítés olyan eszközöket biztosít, amelyek lehetővé teszik az operatív adatelemzés. Az SQL-elemzési megoldások a több előre meghatározott rugalmas készlet és adatbázis-figyelési és riasztási nézetek és lekérdezések biztosít. A Naplóelemzési emellett egy egyéni adatforrásnézet-tervezőből.

Napló elemzési munkaterületekkel és elemzési megoldásokat nyissa meg az Azure portálon, és az Operations Management Suite. Az Azure-portál az újabb-hozzáférési pontnak, de előfordulhat, hogy az egyes területeken Operations Management Suite portálját mögött.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>A bérlők a munkaterhelést szimulál diagnosztikai teljesítményadatok létrehozása 

1. Nyissa meg a PowerShell ISE *... \\WingtipTicketsSaaS főkiszolgálós MultiTenantDb\\tanulási modulok\\Teljesítményfigyelő és felügyeleti\\bemutató-PerformanceMonitoringAndManagement.ps1*. Ez a parancsfájl tartsa nyitva, mert ez az oktatóanyag során generációs forgatókönyvek a terhelés számos futtatni kívánt.
2. Ön még nem tette volna meg, ha használhatók a bérlők annak a figyelési környezetben ennél is érdekesebb megoldást egy tranzakcióköteghez. Ez a folyamat néhány percet vesz igénybe.

   a. Állítsa be **$DemoScenario = 1**, _kiépíteni a bérlő köteg_.

   b. Futtassa a parancsfájlt, és egy további 17 bérlők telepítése, nyomja le az F5 billentyűt.

3. Most indítsa el a terhelés generátor szimulált teher futtathatnak a bérlők számára.

    a. Állítsa be **$DemoScenario = 2**, _Generate normál intenzitásának terhelés (KB. 30 DTU)_.

    b. A parancsfájl futtatásához nyomja le az F5 billentyűt.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Töltse le és feloldása a Wingtip jegyek PowerShell-parancsfájlok lépéseiért lásd: a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>A Naplóelemzési és az Azure SQL elemzési megoldás telepítése és konfigurálása

A Naplóelemzési egy külön szolgáltatás, amelyet be kell állítani. A Naplóelemzési naplóadatokat, telemetriai adatok és metrikák Naplóelemzési munkaterület gyűjti. További erőforrások az Azure-ban, mint például a Naplóelemzési munkaterület kell létrehozni. A munkaterület nem szükséges, az általa figyelt alkalmazások ugyanabban az erőforráscsoportban hozható létre. Ez így gyakran az így a legtöbb, ha. A Wingtip jegyek alkalmazás esetén egyetlen erőforráscsoportként működnek segítségével győződjön meg arról, hogy a munkaterület törlése alkalmazásával.

1. Nyissa meg a PowerShell ISE *... \\WingtipTicketsSaaS főkiszolgálós MultiTenantDb\\tanulási modulok\\Teljesítményfigyelő és felügyeleti\\Analytics jelentkezzen\\bemutató-LogAnalytics.ps1*.
2. A parancsfájl futtatásához nyomja le az F5 billentyűt.

Most már ki tudja nyitni a Naplóelemzési az Azure-portálon vagy az Operations Management Suite portálját. A Naplóelemzési munkaterület gyűjthet, és láthatóvá tegye azt néhány percet vesz igénybe. A továbbiakban hagyja a rendszer diagnosztikai adatok összegyűjtése, a ennél is érdekesebb megoldást a szolgáltatás. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Készletek és adatbázisok figyelése a Log Analytics és az SQL Analytics megoldással


Ebben a gyakorlatban nyissa meg a Naplóelemzési és az Operations Management Suite portálját az adatbázisok és a készletek gyűjtött telemetriai adatok közül.

1. Keresse meg a [Azure-portálon](https://portal.azure.com). Válassza ki **minden szolgáltatás** Naplóelemzési megnyitásához. Majd keresse meg a szolgáltatáshoz.

   ![Nyissa meg a Naplóelemzési](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. Válassza ki a munkaterület nevű _wtploganalytics -&lt;felhasználói&gt;_.

3. Válassza az **Áttekintés** lehetőséget a Log Analytics megoldás megnyitásához az Azure Portalon.

   ![Áttekintés](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Eltarthat néhány percig, a megoldás csak aktív. 

4. Válassza ki a **Azure SQL elemzés** csempére kattintva nyissa meg azt.

    ![Áttekintés csempe](media/saas-dbpertenant-log-analytics/overview.png)

5. A nézetek a megoldás az oldalt, a saját belső görgetősáv alján görgessen. Ha szükséges, frissítse az oldalt.

6. Az összefoglalás lapon megismeréséhez válassza ki a csempéket, vagy az egyes adatbázisokat egy részletes explorer megnyitásához.

    ![Napló elemzések irányítópultján](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. Módosítsa a szűrőt az időtartományt módosításához. A jelen oktatóanyag esetében válassza ki a **elmúlt 1 óra**.

    ![időszűrője](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. Válasszon ki egy egyetlen adatbázist felfedezése, mely a lekérdezés használati és az adatbázishoz tartozó metrikákat.

    ![adatbázis elemzés](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. A szoftverhasználati mérési adatok megtekintéséhez görgessen jobbra a analytics lap.
 
     ![adatbázis metrikák](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. Görgessen az analytics lap bal oldalán, és válassza ki a kiszolgáló csempe az a **erőforrás Info** listája.  

    ![Erőforrások Info listája](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Megnyílik egy oldal, amely a készletek és az adatbázis a kiszolgálón.

    ![a készletek és adatbázisok kiszolgáló](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. Válasszon ki egy készletet. A készlet lapon görgessen jobbra a készlet metrikák megjelenítéséhez. 

    ![készlet metrikák](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. A Naplóelemzési munkaterület válassza **OMS-portálon** van a munkaterület megnyitásához.

    ![Az Operations Management Suite portálját csempe](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Az Operations Management Suite-portálon ismerje meg a napló és a metrika adatok további a munkaterületen. 

Figyelés és riasztás a Log Analyticshez alapulnak lekérdezések az adatokat a munkaterületen eltérően a riasztási definiált az egyes erőforrások az Azure portálon keresztül. Riasztások sablon a lekérdezések, egy riasztást, amely felett az összes adatbázisra, nem pedig adatbázisonként definiáló egy definiálhat. Lekérdezések csak a munkaterület érhető el az adatok korlátozza.

Lekérdezése, és állítson be riasztásokat a Naplóelemzési használatáról további információk: [Naplóelemzési a riasztási szabályok használata](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

A Naplóelemzési SQL-adatbázis költségek a munkaterületen adatok mennyisége alapján. Ebben az oktatóanyagban létrehozott egy szabad munkaterület, amely korlátozódik 500 MB / nap. Ezt a határértéket elérése után adatok nem kerülnek a munkaterületen.


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Telepítse és konfigurálja a Naplóelemzési.
> * Log Analytics segítségével figyelheti a készletek és adatbázisokat.

Próbálja meg a [bérlői analytics oktatóanyag](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>További források

* [További oktatóprogramot kínál, amelyek a kezdeti Wingtip jegyek SaaS adatbázis / bérlői alkalmazás központi telepítésének létrehozása](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
