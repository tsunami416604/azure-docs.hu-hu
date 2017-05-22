---
title: "Alkalmi elemzési lekérdezések futtatása több Azure SQL-adatbázisban | Microsoft Docs"
description: "Alkalmi elemzési lekérdezések futtatása több adatbázisban egy több-bérlős alkalmazásban"
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
ms.openlocfilehash: dd41e7f1f131f6c18e03d2434982c3d681342b8b
ms.contentlocale: hu-hu
ms.lasthandoff: 05/12/2017


---
# <a name="run-ad-hoc-analytics-queries-across-all-wtp-tenants"></a>Az összes WTP-bérlőre kiterjedő alkalmi elemzési lekérdezések futtatása

Ennek az oktatóanyagnak a használatával létre fog hozni egy alkalmi elemzési adatbázist, és több lekérdezést fog futtatni az összes bérlőn. Ezek a lekérdezések képesek kivonni a WTP alkalmazás napi működési adataiban rejtőző fontos értesüléseket.

Az alkalmi elemzési lekérdezések futtatásához (több bérlőn) a WTP alkalmazás elemzési adatbázis mellett az [Elastic Query](sql-database-elastic-query-overview.md) szolgáltatást használja.


Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket:

> [!div class="checklist"]

> * Alkalmi elemzési adatbázis üzembe helyezése
> * Elosztott lekérdezések futtatása az összes bérlői adatbázison



Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A WTP alkalmazás üzembe van helyezve. A kevesebb, mint öt perc alatti üzembe helyezéshez lásd: [A WTP SaaS-alkalmazás üzembe helyezése és felfedezése](sql-database-saas-tutorial.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)


## <a name="ad-hoc-analytics-pattern"></a>Alkalmi elemzési minta

Az SaaS-alkalmazások egyik nagy előnye annak a nagy mennyiségű bérlői adatnak a használata, amelyet központilag a felhőben tárol. Ezen adatok segítségével betekintést nyerhet az alkalmazásai, bérlői és azok felhasználói működési és felhasználási módjába, valamint beállításaikba és viselkedésükbe. A talált fontos értesülések segíthetik az alkalmazásai és szolgáltatásai funkcióinak fejlesztését, használhatóságuk javítását és más befektetéseket.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Az egyik lehetőség az Elastic Query használata, amely alkalmi lekérdezést tesz lehetővé közös sémával rendelkező elosztott adatbázisokban. Az Elastic Query egyetlen *központi* adatbázist használ, amelyben az elosztott (bérlői) adatbázisok tábláit tükröző külső adatbázisok vannak meghatározva. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Az Elastic Query horizontális skálázási térképet használ a katalógus-adatbázisban a bérlői adatbázisok helyének megadására. A telepítés és a lekérdezés egyszerű, szokásos T-SQL-t használ, és támogatja az olyan eszközökkel történő alkalmi lekérdezéseket, mint a Power BI és az Excel.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaas](https://github.com/Microsoft/WingtipSaaS) GitHub-adattárban érhető el. A szkriptfájlok a [Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) (Tanulási modulok) mappában találhatók. Töltse le a **Tanulási modulok** mappát a helyi számítógépére, a mappaszerkezetének megőrzésével.

## <a name="deploy-the-database-used-for-ad-hoc-analytics-queries"></a>Az alkalmi elemzési lekérdezésekhez használt adatbázis üzembe helyezése

Ezzel a gyakorlattal üzembe fogja helyezni az összes bérlői adatbázist átfogó alkalmi lekérdezések küldésére használatos sémát tartalmazó alkalmi elemzési adatbázist.

1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Alkalmi elemzések\\*Demo-AdhocAnalytics.ps1* fájlt a *PowerShell ISE* alkalmazásban, és állítsa be a következő értékeket:
   * **$DemoScenario** = 2, **Alkalmi elemzési adatbázis üzembe helyezése**.

1. Nyomja le az **F5** billentyűt a szkript futtatásához és egy új SQL-adatbázis létrehozásához az alkalmi elemzésekhez, majd vegye fel az adatbázist a WTP-katalógusba. A Jegyvásárlások, Jegyek és Helyszínek táblák lekérdezhető külső táblákként lesznek felvéve.
   Ha figyelmeztetéseket kap arról, hogy *Az RPC-kiszolgáló nem érhető el*, az rendben van.


Most már rendelkezik egy *adhocanalytics* adatbázissal, amely használható elosztott lekérdezések futtatására, és a fontos értesülések begyűjtésére az összes bérlőről!

## <a name="run-ad-hoc-analytics-queries"></a>Alkalmi elemzési lekérdezések futtatása

Ebben a gyakorlatban alkalmi elemzési lekérdezéseket futtathat a bérlőre vonatkozó fontos értesülések feltárására a WTP alkalmazásban.

1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Alkalmi elemzések\\*Demo-AdhocAnalyticsQueries.sql* fájlt az SSMS-ben.
1. Győződjön meg róla, hogy kapcsolódik az **adhocanalytics** adatbázishoz
1. Válassza ki azt az egy lekérdezést, amelyet futtatni szeretne, és nyomja le az **F5** billentyűt:

    ![lekérdezés](media/sql-database-saas-tutorial-adhoc-analytics/query.png)




## <a name="next-steps"></a>Következő lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Alkalmi elemzési adatbázis üzembe helyezése
> * Elosztott lekérdezések futtatása az összes bérlői adatbázison

[Log Analytics (OMS) oktatóanyag](sql-database-saas-tutorial-log-analytics.md)

## <a name="additional-resources"></a>További források

* [A Wingtip Tickets Platform (WTP) alkalmazás kezdeti üzembe helyezésére épülő további oktatóanyagok](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)

