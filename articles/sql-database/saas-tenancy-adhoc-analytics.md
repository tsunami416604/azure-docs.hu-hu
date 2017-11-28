---
title: "Ad hoc jelentéskészítési lekérdezések futtatása több Azure SQL-adatbázisok közötti |} Microsoft Docs"
description: "Ad hoc jelentéskészítési lekérdezések futtatása egy több-bérlős app példa több SQL-adatbázisoknál."
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
ms.topic: articles
ms.date: 11/13/2017
ms.author: billgib; sstein; AyoOlubeko
ms.openlocfilehash: ddad47ccac57ddbb9387709ababbc5be6bad3462
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Ad hoc elemzési lekérdezések futtatása több Azure SQL-adatbázis

Ebben az oktatóanyagban elosztott lekérdezéseket futtat a bérlő teljes szétosztva adatbázisok ad hoc interaktív jelentés engedélyezése. Ezeket a lekérdezéseket insights megbúvó a Wingtip jegyek SaaS-alkalmazás napi működési adatokat nyerhet ki. Ehhez a kiszolgáló telepítése egy további elemzés adatbázist, és rugalmas lekérdezési használatával engedélyezhető az elosztott lekérdezések.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * A globális nézetek használata az egyes adatbázisok ezek a nézetek engedélyezése bérlők között hatékony lekérdezése
> * Az ad hoc jelentési adatbázis központi telepítése
> * Az összes bérlői az adatbázisok közötti elosztott lekérdezések futtatása



Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:


* A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) telepítve van. Töltse le és telepítse az SSMS [töltse le az SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc jelentések minta

![ad hoc jelentéskészítési minta](media/saas-tenancy-adhoc-analytics/adhocreportingpattern_dbpertenant.png)

Az SaaS-alkalmazások kiváló lehetőségek közül azt a képességet hatalmas adatmennyiség bérlői központilag a felhőben tárolt segítségével betekintést nyerhet a művelet és az alkalmazás használatának. Ezek insights szolgáltatás fejlesztési, használhatóság fejlesztések és egyéb befektetések az alkalmazások és szolgáltatások is ismerteti.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Egyik módszer az, hogy használjon [rugalmas lekérdezési](sql-database-elastic-query-overview.md), amely lehetővé teszi, hogy a közös séma adatbázisok elosztott készleteit között lekérdezése. Ezek az adatbázisok másik erőforráscsoport-sablonok és előfizetések legyen elosztva, de kell egy közös bejelentkezési megosztaniuk. Rugalmas lekérdezés használja egyetlen *head* , amelyben külső táblázatokat adatbázis, amely a táblák vagy nézetek az elosztott (bérlői) adatbázisok tükrözik. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Rugalmas lekérdezési a katalógus-adatbázis a shard térkép összes bérlői adatbázis helyének meghatározásához használja. A telepítő és a lekérdezés is egyszerű szabványos [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), és támogatja a Power BI és Excel alkalmi lekérdezése.

A bérlői adatbázisok közötti lekérdezések elosztásával rugalmas lekérdezési élő termelési adatok azonnali betekintést biztosít. Azonban rugalmas lekérdezési potenciálisan sok adatbázisok adatait kéri le, mert lekérdezés-késleltetés néha nál is magasabb lehet egy több-bérlős adatbázisba küldött egyenértékű lekérdezések. Győződjön meg arról, a Tervező lekérdezésekre minimalizálása érdekében a visszaadott adatokat. Rugalmas lekérdezési gyakran leginkább megfelelő, a kis adatmennyiségek valós idejű, szemben a gyakran használt épület vagy összetett elemzési lekérdezések vagy jelentések lekérdezésre. Ha nem jól lekérdezések hajtható végre, tekintse meg a [végrehajtási terv](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) , a lekérdezés részét a távoli adatbázis leküldött, és mennyi adatot ad vissza. Lehet, hogy bonyolult elemzésfeldolgozási jobb igénylő lekérdezések bizonyos esetekben bérlői adatok csomagolja ki dedikált adatbázis vagy adatraktár-elemzési lekérdezések optimalizálva szolgálja ki. Ebben a mintában az ismertetése a [bérlői analytics oktatóanyag](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.

## <a name="create-ticket-sales-data"></a>A jegy értékesítési adatok létrehozása

Ennél is érdekesebb megoldást adatkészlet lekérdezéseinek futtatásához, a szolgáltatásjegy-generátor futtatásával hozzon létre jegy értékesítési adatait.

1. Az a *PowerShell ISE*, nyissa meg a... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReporting.ps1* parancsfájlt, és állítsa be a következő értékeket:
   * **$DemoScenario** = 1, **események minden helyszínek, a jegyektől beszerzési**.
2. Nyomja le az **F5** futtassa a parancsfájlt, és a jegyet értékesítési generálásához. A parancsfájl futása közben, a lépések az ebben az oktatóanyagban. A jegy adatokat lekérdezni a *elosztott ad hoc lekérdezéseket futtathat* területen, várjon, amíg befejeződik a jegy generátor.

## <a name="explore-the-global-views"></a>Fedezze fel a globális nézetek

A Wingtip jegyek SaaS adatbázis / bérlői alkalmazásban mindegyik bérlő kap egy adatbázis. A táblák szereplő adatokat, így egyetlen bérlői szempontjából hatókörét. Azonban minden az adatbázisok közötti lekérdezésekor fontos, hogy rugalmas lekérdezési az adatok is kezelheti, mintha a bérlő szilánkos egyetlen logikai adatbázis része. 

Ebben a mintában szimulálása, "global" nézetet kínál hozzáadódnak a bérlő adatbázis azokat a táblákat, amelyek a rendszer megkérdezi a globálisan projekt egy bérlő azonosítója. Például a *VenueEvents* nézet hozzáadása egy számított *VenueId* a tervezett oszlopokra a *események* tábla. Hasonlóképpen a *VenueTicketPurchases* és *VenueTickets* nézetek hozzáadása egy számított *VenueId* oszlop tervezett a megfelelő táblát. Ezek a nézetek által használt rugalmas lekérdezés lekérdezések és leküldéses azokat le a megfelelő távoli bérlő adatbázis-mikor parallelize egy *VenueId* oszlop. Ez jelentősen csökkenti az adatmennyiséget ad vissza, és sok lekérdezések teljesítményének jelentős növekedését eredményezi. Ezek a globális nézetek már előre létrehozott összes bérlői adatbázisokban.

1. Nyissa meg a szolgáltatáshoz az SSMS és [csatlakozni a tenants1 -&lt;felhasználói&gt; server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
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

1. A **Object Explorer**, bontsa ki a **contosoconcerthall** > **nézetek**:

   ![nézetek](media/saas-tenancy-adhoc-analytics/views.png)

2. Kattintson a jobb gombbal **dbo. Helyszínek**.
3. Válassza ki **nézetként parancsfájl** > **hozhat létre** > **új Lekérdezésszerkesztő ablak**

Parancsfájl-másik *helyszínére* nézeteit hogyan fel a *VenueId*.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Az alkalmi elosztott lekérdezések az adatbázis központi telepítése

Ebben a gyakorlatban telepíti a *adhocreporting* adatbázis. Ez az a központi adatbázis, amely tartalmazza az összes bérlői az adatbázisok közötti lekérdezéshez használt séma. Az adatbázis telepítve van a meglévő kiszolgáló, amely a mintaalkalmazást-felügyelettel kapcsolatos összes adatbázisának használt kiszolgáló.

1. Nyissa meg... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReporting.ps1* a a *PowerShell ISE* és állítsa be a a következő értékeket:
   * **$DemoScenario** = 2, **Alkalmi elemzési adatbázis üzembe helyezése**.

2. Nyomja le az **F5** futtassa a parancsfájlt, és hozzon létre a *adhocreporting* adatbázis.

A következő szakaszban séma hozzáadása az adatbázishoz, elosztott lekérdezések futtatására használható.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Az elosztott lekérdezések futtatásához a "head" adatbázis konfigurálása

Ebben a gyakorlatban hozzáadja az ad hoc elemzési adatbázis, amely lehetővé teszi, hogy minden bérlő az adatbázisok közötti lekérdezése séma (a külső adatforrás és a külső tábla-definíciók).

1. Nyissa meg az SQL Server Management Studio eszközt, és az előző lépésben létrehozott ad hoc jelentési adatbázis kapcsolódjon. Az adatbázis neve nem *adhocreporting*.
2. Nyissa meg a ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* szolgáltatáshoz az ssms.
3. Nézze át az SQL-parancsfájlt és a Megjegyzés:

   Rugalmas lekérdezés eléréséhez a bérlő-adatbázisok mindegyike esetében egy adatbázishoz kötődő hitelesítő adatot használja. Ezeket a hitelesítő adatokat kell lennie az összes adatbázis elérhető, és kell általában rendelkeznek a minimális jogosultságok szükségesek ahhoz, e ad hoc lekérdezéseket.

    ![hitelesítő adatok létrehozása](media/saas-tenancy-adhoc-analytics/create-credential.png)

   A katalógus-adatbázis segítségével a külső adatforrásaként, lekérdezések a lekérdezés futásakor a katalógusban regisztrált összes adatbázis terjesztése. Kiszolgáló neve nem egyezik az egyes központi telepítések, mert az inicializálási parancsfájlja a katalógus-adatbázis helyét lekérdezi az aktuális kiszolgáló lekérésével (@@servername) Ha a parancsfájl végrehajtása.

    ![külső adatforrás létrehozása](media/saas-tenancy-adhoc-analytics/create-external-data-source.png)

   A külső táblákra hivatkozó a globális nézetek, az előző szakaszban leírt, és definiálva **TERJESZTÉSI = SHARDED(VenueId)**. Mivel minden egyes *VenueId* képeződik le egy adatbázist, ez növeli a teljesítményt számos forgatókönyv a következő szakaszban látható.

    ![külső táblák létrehozása](media/saas-tenancy-adhoc-analytics/external-tables.png)

   A helyi tábla *VenueTypes* , amely létre és töltődik fel. A táblázatban az összes bérlői adatbázis közös, így ide helyi táblaként, ki van töltve a közös adatok ábrázolhatók. Egyes lekérdezések esetén ez csökkentheti a bérlő adatbázisok közötti áthelyezése adatmennyiség és a *adhocreporting* adatbázis.

    ![tábla létrehozása](media/saas-tenancy-adhoc-analytics/create-table.png)

   Ha hivatkozási táblák ezen a módon kikapcsolja, feltétlenül frissítése a következő tábla sémáját és az adatokat, amikor frissíti a bérlő adatbázisok.

4. Nyomja le az **F5** futtassa a parancsfájlt, és inicializálni a *adhocreporting* adatbázis. 

Most elosztott lekérdezések futtatását, valamint insights összegyűjtése minden bérlők között!

## <a name="run-ad-hoc-distributed-queries"></a>Ad hoc elosztott lekérdezések futtatása

Most, hogy a *adhocreporting* adatbázis állított be, lépjen tovább, és néhány elosztott lekérdezések futtatása. Például a végrehajtási terv kra, ha a lekérdezés feldolgozása zajlik. 

További részletek a terv ikonok vizsgálatakor ellenőrizze a végrehajtási terv, mutasson. 

Fontos megjegyezni, hogy beállítás **TERJESZTÉSI = SHARDED(VenueId)** amikor definiálva van a külső adatforrás javítja a teljesítményt a sok forgatókönyvben. Mivel minden egyes *VenueId* képeződik le egy adatbázist, szűrés könnyen végezhető el távolról, csak a szükséges adatokat ad vissza.

1. Nyissa meg... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReportingQueries.sql* szolgáltatáshoz az ssms.
2. Biztosan csatlakozni tudjanak a **adhocreporting** adatbázis.
3. Válassza ki a **lekérdezés** menüre, majd **tényleges végrehajtási terv tartalmazza**
4. Jelölje ki a *mely helyszínek jelenleg regisztrált?* lekérdezést, és nyomja le az **F5**.

   A lekérdezés visszaadja a teljes helyszínére listájában ábrázoló milyen gyors és egyszerű lekérdezése összes bérlők között, és térjen vissza az adatokat az egyes bérlők.

   Vizsgálja meg a terv és, hogy a teljes költség van-e a távoli lekérdezés, mert minden egyes bérlői adatbázisához kezeli a saját lekérdezés és a helyszínére adatait adja vissza.

   ![Válassza ki * a dbo. Helyszínek](media/saas-tenancy-adhoc-analytics/query1-plan.png)

5. Válassza ki a következő lekérdezést, és nyomja le az **F5**.

   Ez a lekérdezés csatlakozik a bérlő adatbázisokból és a helyi adatok *VenueTypes* tábla (helyi, mert van egy táblát a *adhocreporting* adatbázis).

   Vizsgálja meg a terv és a legtöbb költsége van-e a távoli lekérdezés. Az egyes bérlői adatbázisok a helyszínére vonatkozó adatokkal tér vissza, és elvégzi a helyi csatlakozzon a helyi *VenueTypes* tábla a következő felhasználóbarát név megjelenítéséhez.

   ![Csatlakozás a helyi és távoli adatokkal](media/saas-tenancy-adhoc-analytics/query2-plan.png)

6. Most válassza ki a *mely napján volt a legtöbb a jegyektől eladott?* lekérdezést, és nyomja le az **F5**.

   Ez a lekérdezés egy kicsit bonyolultabb csatlakoztatása és összesítési beállítások végzi. Fontos megjegyezni, hogy távolról történik, az adatfeldolgozás nagy, de ebben az esetben csak a sorok beolvasása szükséges, az egyes helyszínekkel összesített jegy pénztári száma naponta egy sor.

   ![lekérdezés](media/saas-tenancy-adhoc-analytics/query3-plan.png)


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Az ad hoc jelentési adatbázis központi telepítése, és adja hozzá a séma elosztott lekérdezések futtatásához.


Most a [bérlői Analytics oktatóanyag](saas-tenancy-tenant-analytics.md) való kibontása adatokba összetettebb elemzés feldolgozásának külön analytics adatbázisába.

## <a name="additional-resources"></a>További források

* További [oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
