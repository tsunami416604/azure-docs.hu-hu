---
title: "Log Analytics használata SQL Database több-bérlős alkalmazással | Microsoft Docs"
description: "A telepítő és Naplóelemzés (OMS) használja egy több-bérlős Azure SQL adatbázis SaaS-alkalmazáshoz"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: billgib; sstein
ms.openlocfilehash: 43d46e6a31ee05add33da59348a1d180c4078f97
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="setup-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>A telepítő és Naplóelemzés (OMS) használja egy több-bérlős Azure SQL adatbázis SaaS-alkalmazáshoz

Ebben az oktatóanyagban beállítása és használata *Naplóelemzési ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* a rugalmas készletek és adatbázisokat figyeli. Ez az oktatóanyag épít, a [Teljesítményfigyelő és a felügyeleti útmutató](sql-database-saas-tutorial-performance-monitoring.md). Azt illusztrálja, hogyan használandó *Naplóelemzési* révén a figyelés és riasztás a megadott Azure-portálon. A Naplóelemzési alkalmas figyelés és riasztás léptékű, mert több száz készletek és a több száz akár több ezer adatbázis támogatja. Egyetlen figyelési megoldásként is szolgál, amely képes több Azure-előfizetésben is integrálni a különböző alkalmazások és Azure-szolgáltatások figyelését.

Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Log Analytics (OMS) telepítése és konfigurálása
> * A Log Analytics segítségével készleteket és adatbázisokat figyelhet

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS-alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](sql-database-saas-tutorial.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Az SaaS-forgatókönyveknek és -mintáknak és azok figyelési megoldásokkal szemben támasztott követelményekre gyakorolt hatásának a megbeszélését lásd a [Teljesítményfigyelés és kezelés oktatóanyagban](sql-database-saas-tutorial-performance-monitoring.md).

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Teljesítmény figyelése és kezelése a Log Analyticsban (OMS)

Az SQL Database esetében a figyelés és riasztás rendelkezésre áll az adatbázisokhoz és a készletekhez. Ez a beépített figyelés és riasztás erőforrás-specifikus, és kényelmes kisszámú erőforrás esetén, de kevésbé alkalmas nagy telepítések figyelésére, vagy egységes nézet létrehozására a különböző erőforrásokról és előfizetésekről.

Nagy mennyiségű erőforrás esetén a Log Analytics használható. Ez egy különálló Azure-szolgáltatás, amely Log Analytics-munkahelyen gyűjtött diagnosztikai naplókhoz és telemetriához biztosít elemzési szolgáltatást, számos szolgáltatásból képes telemetriai adatok gyűjtésére, és használható lekérdezések indítására, valamint riasztások beállítására. A Log Analytics beépített lekérdezési nyelvet és adatvizualizációs eszközöket biztosít, amelyek lehetővé teszik a működési adatok elemzését és vizualizálását. Az SQL-elemzési megoldások számos előre definiált rugalmas készlet és figyelés és riasztás nézetek és lekérdezések adatbázis biztosít, és lehetővé teszi, hogy adja hozzá a saját ad hoc lekérdezéseket, és mentse őket szükség szerint. Az OMS egyéni nézettervező is biztosít.

A Log Analytics-munkahelyek és elemzési megoldások az Azure Portalon és az OMS-ben is megnyithatók. Az Azure Portal az újabb hozzáférési pont, de lehet, hogy egyes területeken az OMS-portál mögött marad.

### <a name="create-data-by-starting-the-load-generator"></a>Adatok létrehozásához a terhelés generátor indítása 

1. Nyissa meg a **Demo-PerformanceMonitoringAndManagement.ps1**fájlt a **PowerShell ISE** alkalmazásban. Tartsa nyitva ezt a szkriptet, mivel előfordulhat, hogy az oktatóanyag használata közben szeretne több terhelésgenerálási forgatókönyvet is futtatni.
1. Ha kevesebb mint öt bérlők, rendelkezni a bérlők számára adja meg a érdekes még egy kötegelt figyelési környezetben:
   1. Állítsa be a **$DemoScenario = 1,** **Bérlői köteg kiépítése** értéket
   1. A parancsfájl futtatásához nyomja le az **F5**.

1. Állítsa be **$DemoScenario** = 2, **Generate normál intenzitásának terhelés (KB. 40 DTU)**.
1. A parancsfájl futtatásához nyomja le az **F5**.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaas](https://github.com/Microsoft/WingtipSaaS) GitHub-adattárban érhető el. A szkriptfájlok a [Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) (Tanulási modulok) mappában találhatók. Töltse le a **Tanulási modulok** mappát a helyi számítógépére, a mappaszerkezetének megőrzésével.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>A Log Analytics és az Azure SQL Analytics megoldás telepítése és konfigurálása

A Log Analytics egy különálló szolgáltatás, amelyet konfigurálni kell. A Log Analytics naplóadatokat, telemetriai adatok és metrikákat gyűjt egy naplóelemzési munkaterületre. A munkaterület egy erőforrás, mint az Azure egyéb erőforrásai, és létre kell hozni. Bár a munkaterületet nem szükséges ugyanabban az erőforráscsoportban létrehozni, mint az általa figyelt alkalmazás(oka)t, általában ez a legésszerűbb. A Wingtip SaaS-alkalmazások esetén lehetővé teszi a munkaterület egyszerűen törölni kell az erőforráscsoport egyszerűen törölni az alkalmazással.

1. Nyissa meg a ...\\Tanulási modulok\\Teljesítményfigyelés és -kezelés\\Log Analytics\\*Demo-LogAnalytics.ps1* fájlt a **PowerShell ISE** alkalmazásban.
1. A parancsfájl futtatásához nyomja le az **F5**.

Ezen a ponton a nyitott Naplóelemzési tudja az Azure-portál (vagy az OMS-portállal) kell lennie. Telemetria kell gyűjteni a Naplóelemzési munkaterület, és láthatóvá válnak néhány percet vesz igénybe. A továbbiakban hagyja a rendszer a ennél is érdekesebb megoldást a szolgáltatás adatokat gyűjt. Eljött a pillanat, hogy magához vegyen valamilyen frissítőt, csak hagyja nyugodtan futni a terhelésgenerátort!


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Készletek és adatbázisok figyelése a Log Analytics és az SQL Analytics megoldással


Ebben a gyakorlatban nyissa meg a Naplóelemzési és az OMS-portálon az adatbázisok és a készletek alatt gyűjtött telemetriai adatok közül.

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

* [További oktatóprogramot kínál, amelyek a kezdeti Wingtip SaaS-alkalmazás telepítésében épül](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
