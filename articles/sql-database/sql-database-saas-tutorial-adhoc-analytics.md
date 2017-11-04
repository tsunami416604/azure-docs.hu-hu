---
title: "Alkalmi elemzési lekérdezések futtatása több Azure SQL-adatbázisban | Microsoft Docs"
description: "Ad hoc elemzési lekérdezések futtatása egy több-bérlős app példa több SQL-adatbázisoknál."
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
ms.date: 06/23/2017
ms.author: billgib; sstein
ms.openlocfilehash: 849f0570fb1550f6c3676fc070d0f862450ade9a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Több Azure SQL adatbázis alkalmi elemzési lekérdezések futtatása

Ebben az oktatóanyagban elosztott lekérdezések teljes szétosztva több-bérlős adatbázisok alkalmi analytics engedélyezéséhez futtassa. Rugalmas lekérdezés segítségével engedélyezése az elosztott lekérdezések, amelyhez egy további elemzés adatbázis központi telepítése (a kiszolgáló). Ezeket a lekérdezéseket insights megbúvó a Wingtip SaaS-alkalmazás napi működési adatokat nyerhet ki.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * A globális nézetek használata az egyes adatbázisok ezek a nézetek engedélyezése bérlők között hatékony lekérdezése
> * Az ad hoc analytics adatbázis központi telepítése
> * Az összes bérlői az adatbázisok közötti elosztott lekérdezések futtatása



Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS-alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](sql-database-saas-tutorial.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) telepítve van. Töltse le és telepítse az SSMS [töltse le az SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-analytics-pattern"></a>Ad hoc analytics minta

Az SaaS-alkalmazások kiváló lehetőségek egyik bérlői adatok a felhőben tárolt központilag hatalmas mennyiségű használja. Ezen adatok segítségével betekintést nyerhet a művelet és az alkalmazás használatát. Ezek insights szolgáltatás fejlesztési, használhatóság fejlesztések és egyéb befektetések az alkalmazások és szolgáltatások is ismerteti.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Egyik módszer az, hogy használjon [rugalmas lekérdezési](sql-database-elastic-query-overview.md), amely lehetővé teszi, hogy a közös séma adatbázisok elosztott készleteit között lekérdezése. Rugalmas lekérdezés használja egyetlen *head* , amelyben külső táblázatokat adatbázis, amely a táblák vagy nézetek az elosztott (bérlői) adatbázisok tükrözik. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Az Elastic Query horizontális skálázási térképet használ a katalógus-adatbázisban a bérlői adatbázisok helyének megadására. A telepítő és a lekérdezés is egyszerű szabványos [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), és támogatja az ad hoc az eszközök, például a Power BI és Excel lekérdezése.

A bérlői adatbázisok közötti lekérdezések elosztásával rugalmas lekérdezési élő termelési adatok azonnali betekintést biztosít. Azonban rugalmas lekérdezési potenciálisan sok adatbázisok adatait kéri le, mert lekérdezés-késleltetés néha nál is magasabb lehet egy több-bérlős adatbázisba küldött egyenértékű lekérdezések. Győződjön meg arról, a Tervező lekérdezésekre minimalizálása érdekében a visszaadott adatokat. Rugalmas lekérdezési gyakran leginkább megfelelő, a kis adatmennyiségek valós idejű, szemben a gyakran használt épület vagy összetett elemzési lekérdezések vagy jelentések lekérdezésre. Ha nem jól lekérdezések hajtható végre, tekintse meg a [végrehajtási terv](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) , a lekérdezés részét a távoli adatbázis leküldött, és mennyi adatot ad vissza. Lehet, hogy bonyolult elemzésfeldolgozási jobb igénylő lekérdezések bizonyos esetekben bérlői adatok csomagolja ki dedikált adatbázis vagy adatraktár-elemzési lekérdezések optimalizálva szolgálja ki. Ebben a mintában az ismertetése a [bérlői analytics oktatóanyag](sql-database-saas-tutorial-tenant-analytics.md). 

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Szolgáltatottszoftver-parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-tárház. [Töltse le a Wingtip Szolgáltatottszoftver-parancsfájlok lépéseket](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="create-ticket-sales-data"></a>A jegy értékesítési adatok létrehozása

Ennél is érdekesebb megoldást adatkészlet lekérdezéseinek futtatásához, a szolgáltatásjegy-generátor futtatásával hozzon létre jegy értékesítési adatait.

1. Az a *PowerShell ISE*, nyissa meg a... \\Tanulási modulok\\működési Analytics\\ad hoc Analytics\\*bemutató-AdhocAnalytics.ps1* parancsfájlt, és állítsa be a következő értékeket:
   * **$DemoScenario** = 1, **események minden helyszínek, a jegyektől beszerzési**.
2. Nyomja le az **F5** futtassa a parancsfájlt, és a jegyet értékesítési generálásához. A parancsfájl futása közben, a lépések az ebben az oktatóanyagban. A jegy adatokat lekérdezni a *elosztott ad hoc lekérdezéseket futtathat* területen, várjon, amíg befejeződik a jegy generátor.

## <a name="explore-the-global-views"></a>Fedezze fel a globális nézetek

A Wingtip SaaS-alkalmazás-t egy bérlő-adatbázis-modell, így a bérlő adatbázis sémát single-bérlő szempontjából. Egy tábla létezik bérlői-specifikus adatait *helyszínére*, amely mindig egyetlen sort, és valósul meg egy halommemóriában, egy elsődleges kulcs nélkül. A séma levő többi táblázat nem kell létesítenie a *helyszínére* táblázatra, mert a szokásos kétséges soha nem bármely mely bérlői tartozik az adatokat.

Azonban minden az adatbázisok közötti lekérdezésekor fontos, hogy rugalmas lekérdezési az adatok is kezelheti, mintha a bérlő szilánkos egyetlen logikai adatbázis része. Ebben a mintában szimulálása, "global" nézetet kínál hozzáadódnak a bérlő adatbázis erre a projektre a bérlő azonosítója azokat a táblákat, amelyek a rendszer megkérdezi a globálisan. Például a *VenueEvents* nézet hozzáadása egy számított *VenueId* a tervezett oszlopokra a *események* tábla. A külső tábla a központi adatbázis meghatározásával keresztül *VenueEvents* (ahelyett, hogy az alapul szolgáló *események* táblázat), rugalmas lekérdezés nem képes alapján illesztések leküldhetik *VenueId* , azok minden egyes távoli adatbázishoz (nem a központi adatbázis) párhuzamos hajtható végre. Ez jelentősen csökkenti a visszaadott adatokat, így ez sok lekérdezések teljesítményének jelentős növelését. Ezek a globális nézetek volt előre létrehozott összes bérlői adatbázisokban (és a *basetenantdb*).

1. Nyissa meg a szolgáltatáshoz az SSMS és [csatlakozni a tenants1 -&lt;felhasználói&gt; server](sql-database-wtp-overview.md#explore-database-schema-and-execute-sql-queries-using-ssms).
2. Bontsa ki a **adatbázisok**, kattintson a jobb gombbal **contosoconcerthall**, és válassza ki **új lekérdezés**.
3. Futtassa az alábbi lekérdezéseket felfedezése, mely a különbség a single-tenant táblákban és a globális nézetek:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Ezekben a nézetekben a *VenueId* mint helyszínére nevét, de bármely megközelítés kivonatát bevezetni egyedi érték használható. A megoldás, hasonlóan ahhoz, ahogy a bérlői kulcs használatát a katalógus értékei.

Megvizsgálhatja a definícióját a *helyszínek* megtekintése:

1. A **Object Explorer**, bontsa ki a **contosoconcethall** > **nézetek**:

   ![nézetek](media/sql-database-saas-tutorial-adhoc-analytics/views.png)

2. Kattintson a jobb gombbal **dbo. Helyszínek**.
3. Válassza ki **nézetként parancsfájl** > **hozhat létre** > **új Lekérdezésszerkesztő ablak**

Parancsfájl-másik *helyszínére* nézeteit hogyan fel a *VenueId*.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Alkalmi elosztott lekérdezések az adatbázis központi telepítése

Ebben a gyakorlatban telepíti a *adhocanalytics* adatbázis. Ez az a központi adatbázis, amely tartalmazza az összes bérlői az adatbázisok közötti lekérdezéshez használt séma. Az adatbázis telepítve van a meglévő kiszolgáló, amely a mintaalkalmazást-felügyelettel kapcsolatos összes adatbázisának használt kiszolgáló.

1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Alkalmi elemzések\\*Demo-AdhocAnalytics.ps1* fájlt a *PowerShell ISE* alkalmazásban, és állítsa be a következő értékeket:
   * **$DemoScenario** = 2, **Alkalmi elemzési adatbázis üzembe helyezése**.

2. Nyomja le az **F5** futtassa a parancsfájlt, és hozzon létre a *adhocanalytics* adatbázis.

A következő szakaszban séma hozzáadása az adatbázishoz, elosztott lekérdezések futtatására használható.

## <a name="configure-the-database-for-running-distributed-queries"></a>Konfigurálja az adatbázist, elosztott lekérdezések futtatásához

Ebben a gyakorlatban hozzáadja az ad hoc elemzési adatbázis, amely lehetővé teszi, hogy minden bérlő az adatbázisok közötti lekérdezése séma (a külső adatforrás és a külső tábla-definíciók).

1. Nyissa meg az SQL Server Management Studio eszközt, és az előző lépésben létrehozott ad hoc Analytics adatbázis kapcsolódjon. Az adatbázis neve nem *adhocanalytics*.
2. Nyissa meg a ...\Learning Modules\Operational Analytics\Adhoc Analytics\ *Initialize-AdhocAnalyticsDB.sql* szolgáltatáshoz az ssms.
3. Tekintse át az SQL-parancsfájlt, és vegye figyelembe a következőket:

   Rugalmas lekérdezés eléréséhez a bérlő-adatbázisok mindegyike esetében egy adatbázishoz kötődő hitelesítő adatot használja. Ezeket a hitelesítő adatokat kell lennie az összes adatbázis elérhető, és kell általában rendelkeznek a minimális jogosultságok szükségesek ahhoz a ad hoc lekérdezéseket.

    ![hitelesítő adatok létrehozása](media/sql-database-saas-tutorial-adhoc-analytics/create-credential.png)

   A külső adatforráshoz, használja a bérlői shard leképezését a katalógus adatbázisban van definiálva. Segítségével a külső adatforrásaként, lekérdezések terjesztése a lekérdezés futásakor a katalógusban regisztrált összes adatbázisra. Kiszolgáló neve nem egyezik az egyes központi telepítések, mert az inicializálási parancsfájlja a katalógus-adatbázis helyét lekérdezi az aktuális kiszolgáló lekérésével (@@servername) Ha a parancsfájl végrehajtása.

    ![külső adatforrás létrehozása](media/sql-database-saas-tutorial-adhoc-analytics/create-external-data-source.png)

   A külső táblákra hivatkozó a globális nézetek, az előző szakaszban leírt, és definiálva **TERJESZTÉSI = SHARDED(VenueId)**. Mivel minden egyes *VenueId* képeződik le egy adatbázist, ez növeli a teljesítményt számos forgatókönyv a következő szakaszban látható.

    ![külső táblák létrehozása](media/sql-database-saas-tutorial-adhoc-analytics/external-tables.png)

   A helyi tábla *VenueTypes* , amely létre és töltődik fel. A táblázatban az összes bérlői adatbázis közös, így ide helyi táblaként, ki van töltve a közös adatok ábrázolhatók. Az egyes lekérdezések Ez csökkentheti a bérlő adatbázisok közötti áthelyezése adatok mennyisége és a *adhocanalytics* adatbázis.

    ![tábla létrehozása](media/sql-database-saas-tutorial-adhoc-analytics/create-table.png)

   Ha hivatkozási táblák ezen a módon kikapcsolja, feltétlenül frissítése a következő tábla sémáját és az adatokat, amikor frissíti a bérlő adatbázisok.

4. Nyomja le az **F5** futtassa a parancsfájlt, és inicializálni a *adhocanalytics* adatbázis. 

Most elosztott lekérdezések futtatását, valamint insights összegyűjtése minden bérlők között!

## <a name="run-ad-hoc-distributed-queries"></a>Ad hoc elosztott lekérdezések futtatása

Most, hogy a *adhocanalytics* adatbázis állított be, lépjen tovább, és néhány elosztott lekérdezések futtatása. Például a végrehajtási terv kra, ha a lekérdezés feldolgozása zajlik. 

További részletek a terv ikonok vizsgálatakor ellenőrizze a végrehajtási terv, mutasson. 

Fontos megjegyezni, hogy beállítás **TERJESZTÉSI = SHARDED(VenueId)** Ha meg van adva a külső adatforrás, hogy javítja a teljesítményt a sok forgatókönyvben. Mivel minden egyes *VenueId* képeződik le egy adatbázist, szűrés könnyen végezhető el távolról, csak a szükséges adatokat ad vissza.

1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Alkalmi elemzések\\*Demo-AdhocAnalyticsQueries.sql* fájlt az SSMS-ben.
2. Biztosan csatlakozni tudjanak a **adhocanalytics** adatbázis.
3. Válassza ki a **lekérdezés** menüre, majd **tényleges végrehajtási terv tartalmazza**
4. Jelölje ki a *mely helyszínek jelenleg regisztrált?* lekérdezést, és nyomja le az **F5**.

   A lekérdezés visszaadja a teljes helyszínére lista, hogyan ábrázoló, és könnyen már egyetlen bérlő számára átfogó lekérdezése, és térjen vissza az adatokat az egyes bérlők.

   Vizsgálja meg a terv és a teljes költség van-e a távoli lekérdezés, mert azt még egyszerűen az egyes bérlői adatbázis is, majd válassza a helyszínére információkat.

   ![Válassza ki * a dbo. Helyszínek](media/sql-database-saas-tutorial-adhoc-analytics/query1-plan.png)

5. Válassza ki a következő lekérdezést, és nyomja le az **F5**.

   Ez a lekérdezés csatlakozik a bérlő adatbázisokból és a helyi adatok *VenueTypes* tábla (helyi, mert van egy táblát a *adhocanalytics* adatbázis).

   Vizsgálja meg a terv és a legtöbb költsége van-e a távoli lekérdezés mivel azt a bérlő helyszínére info (dbo. Helyszínek), majd hajtsa végre a gyors helyi csatlakozzon a helyi *VenueTypes* tábla a következő felhasználóbarát név megjelenítéséhez.

   ![Csatlakozás a helyi és távoli adatokkal](media/sql-database-saas-tutorial-adhoc-analytics/query2-plan.png)

6. Most válassza ki a *mely napján volt a legtöbb a jegyektől eladott?* lekérdezést, és nyomja le az **F5**.

   Ez a lekérdezés egy kicsit bonyolultabb csatlakoztatása és összesítési beállítások végzi. Fontos megjegyezni, hogy távolról történik, az adatfeldolgozás nagy, de ebben az esetben azt érdekében ismét csak azokat a sorokat, igazolnia kell, az egyes helyszínekkel összesített jegy pénztári száma naponta csak egyetlen sort ad vissza.

   ![lekérdezés](media/sql-database-saas-tutorial-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Az ad hoc analytics adatbázis központi telepítése, és adja hozzá a séma elosztott lekérdezések futtatásához.


Most a [bérlői Analytics oktatóanyag](sql-database-saas-tutorial-tenant-analytics.md) való kibontása adatokba összetettebb analytics feldolgozásra külön analytics adatbázishoz...

## <a name="additional-resources"></a>További források

* További [oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
