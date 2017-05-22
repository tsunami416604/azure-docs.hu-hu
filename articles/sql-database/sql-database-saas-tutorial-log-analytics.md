---
title: "Log Analytics használata SQL Database több-bérlős alkalmazással | Microsoft Docs"
description: "Log Analytics (OMS) beállítása és használata az Azure SQL Database mintául szolgáló Wingtip Tickets (WTP) alkalmazásával"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4ff4519ca40f036d58f82993db78fe08aa7d5733
ms.contentlocale: hu-hu
ms.lasthandoff: 05/12/2017


---
# <a name="setup-and-use-log-analytics-oms-with-the-wtp-sample-saas-app"></a>A Log Analytics (OMS) beállítása és használata a WTP minta SaaS-alkalmazással

Ebben az oktatóanyagban a *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* beállítását és a WTP alkalmazással a rugalmas készletek és adatbázisok figyelésére történő használatát ismertetjük. A [Teljesítményfigyelés és -kezelés oktatóanyagra](sql-database-saas-tutorial-performance-monitoring.md) épül, és bemutatja a *Log Analytics* szolgáltatásnak az Azure Portalon biztosított figyelési és riasztási szolgáltatás kiegészítésére történő használatnak módját. A Log Analytics különösen alkalmas kiterjedt figyelésre és riasztásra, mert több száz készlet és több százezer adatbázis használatát is támogatja. Egyetlen figyelési megoldásként is szolgál, amely képes több Azure-előfizetésben is integrálni a különböző alkalmazások és Azure-szolgáltatások figyelését.

Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Log Analytics (OMS) telepítése és konfigurálása
> * A Log Analytics segítségével készleteket és adatbázisokat figyelhet

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A WTP alkalmazás üzembe van helyezve. A kevesebb, mint öt perc alatti üzembe helyezéshez lásd: [A WTP SaaS-alkalmazás üzembe helyezése és felfedezése](sql-database-saas-tutorial.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Az SaaS-forgatókönyveknek és -mintáknak és azok figyelési megoldásokkal szemben támasztott követelményekre gyakorolt hatásának a megbeszélését lásd a [Teljesítményfigyelés és kezelés oktatóanyagban](sql-database-saas-tutorial-performance-monitoring.md).

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Teljesítmény figyelése és kezelése a Log Analyticsban (OMS)

Az SQL Database esetében a figyelés és riasztás rendelkezésre áll az adatbázisokhoz és a készletekhez. Ez a beépített figyelés és riasztás erőforrás-specifikus, és kényelmes kisszámú erőforrás esetén, de kevésbé alkalmas nagy telepítések figyelésére, vagy egységes nézet létrehozására a különböző erőforrásokról és előfizetésekről.

Nagy mennyiségű erőforrás esetén a Log Analytics használható. Ez egy különálló Azure-szolgáltatás, amely Log Analytics-munkahelyen gyűjtött diagnosztikai naplókhoz és telemetriához biztosít elemzési szolgáltatást, számos szolgáltatásból képes telemetriai adatok gyűjtésére, és használható lekérdezések indítására, valamint riasztások beállítására. A Log Analytics beépített lekérdezési nyelvet és adatvizualizációs eszközöket biztosít, amelyek lehetővé teszik a működési adatok elemzését és vizualizálását. Az SQL Analytics megoldás számos előre definiált rugalmas készlet- és adatbázis-figyelést, valamint riasztási nézeteket és lekérdezéseket biztosít, és szükség szerint lehetővé teszi a saját eseti lekérdezéseinek hozzáadását és mentését. Az OMS egyéni nézettervező is biztosít.

A Log Analytics-munkahelyek és elemzési megoldások az Azure Portalon és az OMS-ben is megnyithatók. Az Azure Portal az újabb hozzáférési pont, de lehet, hogy egyes területeken az OMS-portál mögött marad.

### <a name="start-the-load-generator-to-create-data-to-analyze"></a>Indítsa el a terhelésgenerátort az elemzésre szánt adatok létrehozásához

1. Nyissa meg a **Demo-PerformanceMonitoringAndManagement.ps1**fájlt a **PowerShell ISE** alkalmazásban. Tartsa nyitva ezt a szkriptet, mivel előfordulhat, hogy az oktatóanyag használata közben szeretne több terhelésgenerálási forgatókönyvet is futtatni.
1. Ha ötnél kevesebb bérlővel rendelkezik, építse ki a bérlők egy kötegét, hogy érdekesebb figyelési környezetet tudjon kialakítani:
   1. Állítsa be a **$DemoScenario = 1,** **Bérlői köteg kiépítése** értéket
   1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

1. Állítsa be **$DemoScenario** = 2, **Normál intenzitású terhelés generálása (hozzávetőlegesen 40 DTU)** értéket.
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaas](https://github.com/Microsoft/WingtipSaaS) GitHub-adattárban érhető el. A szkriptfájlok a [Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) (Tanulási modulok) mappában találhatók. Töltse le a **Tanulási modulok** mappát a helyi számítógépére, a mappaszerkezetének megőrzésével.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>A Log Analytics és az Azure SQL Analytics megoldás telepítése és konfigurálása

A Log Analytics egy különálló szolgáltatás, amelyet konfigurálni kell. A Log Analytics naplóadatokat, telemetriai adatok és metrikákat gyűjt egy naplóelemzési munkaterületre. A munkaterület egy erőforrás, mint az Azure egyéb erőforrásai, és létre kell hozni. Bár a munkaterületet nem szükséges ugyanabban az erőforráscsoportban létrehozni, mint az általa figyelt alkalmazás(oka)t, általában ez a legésszerűbb. A WTP alkalmazás esetében ez lehetővé teszi a munkaterület egyszerű törlését az erőforráscsoport törlésével.

1. Nyissa meg a ...\\Tanulási modulok\\Teljesítményfigyelés és -kezelés\\Log Analytics\\*Demo-LogAnalytics.ps1* fájlt a **PowerShell ISE** alkalmazásban.
1. A szkript futtatásához nyomja le az **F5** billentyűt.

Ekkor meg kell tudnia nyitni a Log Analytics-et az Azure Portalon (vagy az OMS-portálon). A telemetriai adatok összegyűjtése és megjelenítése a Log Analytics-munkaterületen eltarthat néhány percig. Minél tovább hagyja, hogy a rendszer gyűjtse az adatokat, annál érdekesebb lesz az eredmény. Eljött a pillanat, hogy magához vegyen valamilyen frissítőt, csak hagyja nyugodtan futni a terhelésgenerátort!


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Készletek és adatbázisok figyelése a Log Analytics és az SQL Analytics megoldással


Ebben a gyakorlatban meg fogja nyitni a Log Analytics megoldást és az OMS-portált a WTP-adatbázisokhoz és készletekhez gyűjtött telemetriai adatok megtekintéséhez.

1. Keresse meg az [Azure Portalt](https://portal.azure.com), és nyissa meg a Log Analytics megoldást, ehhez kattintson a További szolgáltatások elemre, majd keressen rá a Log Analytics kifejezésre:

   ![Log Analytics megnyitása](media/sql-database-saas-tutorial-log-analytics/log-analytics-open.png)

1. Válassza ki a *wtploganalytics-&lt;USER&gt;* nevű munkahelyet.

1. Válassza az **Áttekintés** lehetőséget a Log Analytics megoldás megnyitásához az Azure Portalon.
   ![áttekintés-hivatkozás](media/sql-database-saas-tutorial-log-analytics/click-overview.png)

    **FONTOS**: eltarthat néhány percig, amíg a megoldás aktívvá válik. Legyen türelemmel!

1. Kattintson az Azure SQL Analytics csempére a megnyitásához.

    ![Áttekintés](media/sql-database-saas-tutorial-log-analytics/overview.png)

    ![analytics](media/sql-database-saas-tutorial-log-analytics/analytics.png)

1. A nézet a megoldás panelen oldalra görgethető az alul található saját görgetősávjával (szükség esetén frissítse a panelt).

1. A különböző nézetek felfedezéséhez kattintson rájuk, vagy kattintson rá az egyes erőforrásokra a részletező böngésző megnyitásához, ahol használhatja a bal felső sarokban található időcsúszkát, vagy rákattinthat egy függőleges oszlopra, hogy egy szűkebb időszeletre koncentrálhasson. Ebben a nézetben kiválaszthat különálló adatbázisokat vagy készleteket, hogy az adott erőforrásokra koncentrálhasson:

    ![diagram](media/sql-database-saas-tutorial-log-analytics/chart.png)

1. Ha visszatér a megoldás panelre, és teljesen jobbra görgeti a nézetet látni fog néhány mentett lekérdezést, amelyeket kattintással megnyithat és átnézhet. Kísérletezhet ezek módosításával, majd mentheti a létrehozott és érdekesnek talált lekérdezéseket, melyeket aztán újra megnyitva más erőforrásokkal használhat.

1. Visszatérve a Log Analytics-munkaterület paneljére válassza az OMS-portált, ha a megoldást itt szeretné megnyitni.

    ![oms](media/sql-database-saas-tutorial-log-analytics/oms.png)

1. Az OMS-portálon riasztásokat konfigurálhat. Kattintson az adatbázis DTU nézetében a riasztás részre.

1. A megjelenő Naplóbeli keresés nézetben láthatja a metrikáknak megfelelő oszlopdiagramot.

    ![naplóbeli keresés](media/sql-database-saas-tutorial-log-analytics/log-search.png)

1. Ha az eszköztáron a Riasztás elemre kattint, akkor látni fogja és módosíthatja a riasztás konfigurációját.

    ![riasztási szabály hozzáadása](media/sql-database-saas-tutorial-log-analytics/add-alert.png)

A Log Analytics-ben és az OMS-ben a figyelés és a riasztás a munkahelyen található adatok lekérdezésén alapul, az egyes erőforráspaneleken viszont a riasztások erőforrás-specifikusak. Tehát meghatározhat olyan riasztást, amely az összes adatbázist figyeli, ahelyett, hogy adatbázisonként meg kellene adni egyet. Vagy írhat olyan riasztást, amely összetett lekérdezést használ több erőforrástípusban. A lekérdezéseket csak a munkaterületen elérhető adatok korlátozzák.

Az SQL Database-ben a Log Analytics díjszabása a munkaterületen található adatmennyiségtől függ. Ennek az oktatóanyagnak a használatával ingyenes munkaterületet hozott létre, amelynek a napi korlátja 500 MB. Miután elérte ezt a korlátot, a további adatok nem kerülnek a munkaterületre.


## <a name="next-steps"></a>Következő lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Log Analytics (OMS) telepítése és konfigurálása
> * A Log Analytics segítségével készleteket és adatbázisokat figyelhet

[Bérlői elemzések – oktatóanyag](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>További források

* [A Wingtip Tickets Platform (WTP) alkalmazás kezdeti üzembe helyezésére épülő további oktatóanyagok](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)

