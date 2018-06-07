---
title: Ad hoc jelentéskészítési lekérdezések futtatása több Azure SQL-adatbázisok közötti |} Microsoft Docs
description: Ad hoc jelentéskészítési lekérdezések futtatása egy több-bérlős app példa több SQL-adatbázisoknál.
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: AyoOlubeko
ms.openlocfilehash: 95ea5f2a40bd629384e1bc1f7d707eb30d9eaf5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644273"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Ad hoc elemzési lekérdezések futtatása több Azure SQL-adatbázis

Ebben az oktatóanyagban elosztott lekérdezéseket futtat a bérlő teljes szétosztva adatbázisok ad hoc interaktív jelentés engedélyezése. Ezeket a lekérdezéseket insights megbúvó a Wingtip jegyek SaaS-alkalmazás napi működési adatokat nyerhet ki. Ehhez a információkinyerés, a kiszolgáló telepítése egy további elemzés adatbázist, és rugalmas lekérdezési használatával engedélyezhető az elosztott lekérdezések.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * Az ad hoc jelentési adatbázis központi telepítése
> * Az összes bérlői az adatbázisok közötti elosztott lekérdezések futtatása


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás](saas-multitenantdb-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) telepítve van. Töltse le és telepítse az SSMS [töltse le az SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc jelentések minta

![ad hoc jelentéskészítési minta](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-alkalmazásokhoz elemezheti a hatalmas mennyiségű központilag a felhőben tárolt adatok bérlő. Az elemzések fedi fel a művelet és az alkalmazás használatát. Ezek insights szolgáltatás fejlesztési, használhatóság fejlesztések és egyéb befektetések az alkalmazások és szolgáltatások is ismerteti.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Egyik módszer az, hogy használjon [rugalmas lekérdezési](sql-database-elastic-query-overview.md), amely lehetővé teszi, hogy a közös séma adatbázisok elosztott készleteit között lekérdezése. Ezeket az adatbázisokat is szétosztását másik erőforráscsoport-sablonok és előfizetések között. Még egy közös bejelentkezési bontsa ki az adatokat az adatbázisból hozzáféréssel kell rendelkeznie. Rugalmas lekérdezés használja egyetlen *head* , amelyben külső táblázatokat adatbázis, amely a táblák vagy nézetek az elosztott (bérlői) adatbázisok tükrözik. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Rugalmas lekérdezési a katalógus-adatbázis a shard térkép összes bérlői adatbázis helyének meghatározásához használja. A telepítő és a lekérdezés is egyszerű szabványos [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), és támogatja a Power BI és Excel alkalmi lekérdezése.

A bérlői adatbázisok közötti lekérdezések elosztásával rugalmas lekérdezési élő termelési adatok azonnali betekintést biztosít. Azonban rugalmas lekérdezési potenciálisan sok adatbázisok adatait kéri le, mert lekérdezés-késleltetés néha nál is magasabb lehet egy több-bérlős adatbázisba küldött egyenértékű lekérdezések. Győződjön meg arról, a Tervező lekérdezésekre minimalizálása érdekében a visszaadott adatokat. Rugalmas lekérdezési gyakran leginkább megfelelő, a kis adatmennyiségek valós idejű, szemben a gyakran használt épület vagy összetett elemzési lekérdezések vagy jelentések lekérdezésre. Ha a lekérdezések nem is kell végrehajtani, tekintse meg a [végrehajtási terv](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) a lekérdezés részét a távoli adatbázis leküldött megjelenítéséhez. És felmérheti, mennyi adatot ad vissza. Előfordulhat, hogy bonyolult elemzésfeldolgozási jobb igénylő lekérdezések a kibontott bérlői adatok elemzési lekérdezések optimalizált adatbázisba történő mentése szolgálja ki. SQL-adatbázis és az SQL Data Warehouse a sikerült ilyen az analytics-adatbázist tárolni.

Ebben a mintában az elemzés az ismertetése a [bérlői analytics oktatóanyag](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Az alkalmazás forráskódjához Wingtip jegyek SaaS több-bérlős adatbázis és a parancsfájlok

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.

## <a name="create-ticket-sales-data"></a>A jegy értékesítési adatok létrehozása

Ennél is érdekesebb megoldást adatkészlet lekérdezéseinek futtatásához, a szolgáltatásjegy-generátor futtatásával hozzon létre jegy értékesítési adatait.

1. Az a *PowerShell ISE*, nyissa meg a... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReporting.ps1* parancsfájlt, és állítsa be a következő értékeket:
   * **$DemoScenario** = 1, **események minden helyszínek, a jegyektől beszerzési**.
2. Nyomja le az **F5** futtassa a parancsfájlt, és a jegyet értékesítési generálásához. A parancsfájl futása közben, a lépések az ebben az oktatóanyagban. A jegy adatokat lekérdezni a *futtassa az ad hoc elosztott lekérdezések* területen, várjon, amíg befejeződik a jegy generátor.

## <a name="explore-the-tenant-tables"></a>A tenant táblákban felfedezés 

A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazásban bérlők egy hibrid bérlői felügyeleti modell - bérlő adatok esetén vagy egy több-bérlős vagy egy bérlői adatbázist tárolja, és helyezheti át a kettő között vannak tárolva. Az összes bérlői az adatbázisok közötti lekérdezésekor fontos, hogy rugalmas lekérdezési az adatok is kezelheti, mintha a bérlő szilánkos egyetlen logikai adatbázis része. 

Ebben a mintában eléréséhez összes bérlői tábla tartalmaz egy *VenueId* oszlop, amely azonosítja, amely az adatok bérlői tartozik. A *VenueId* mint bevezetni az oszlop egy egyedi értéket a helyszínére nevét, de bármely megközelítés kivonatát használható. A megoldás, hasonlóan ahhoz, ahogy a bérlői kulcs használatát a katalógus értékei. Táblázatot tartalmazó *VenueId* lekérdezések parallelize, és küldje le őket a megfelelő távoli bérlői adatbázis le rugalmas Query használják. Ez jelentősen csökkenti az adatmennyiséget ad vissza, és a teljesítmény növekedését eredményezi, különösen akkor, ha nincsenek a több bérlő, amelyek esetén az egybérlős adatbázisok tárolják.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Az alkalmi elosztott lekérdezések az adatbázis központi telepítése

Ebben a gyakorlatban telepíti a *adhocreporting* adatbázis. Ez az a központi adatbázis, amely tartalmazza az összes bérlői az adatbázisok közötti lekérdezéshez használt séma. Az adatbázis telepítve van a meglévő kiszolgáló, amely a mintaalkalmazást-felügyelettel kapcsolatos összes adatbázisának használt kiszolgáló.

1. Nyissa meg... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReporting.ps1* a a *PowerShell ISE* és állítsa be a a következő értékeket:
   * **$DemoScenario** = 2, **központi telepítése az Ad hoc analytics adatbázis**.

2. Nyomja le az **F5** futtassa a parancsfájlt, és hozzon létre a *adhocreporting* adatbázis.

A következő szakaszban séma hozzáadása az adatbázishoz, elosztott lekérdezések futtatására használható.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Az elosztott lekérdezések futtatásához a "head" adatbázis konfigurálása

Ebben a gyakorlatban hozzáadja az ad hoc jelentési adatbázis, amely lehetővé teszi, hogy minden bérlő az adatbázisok közötti lekérdezése séma (a külső adatforrás és a külső tábla-definíciók).

1. Nyissa meg az SQL Server Management Studio eszközt, és az előző lépésben létrehozott ad hoc jelentési adatbázis kapcsolódjon. Az adatbázis neve nem *adhocreporting*.
2. Nyissa meg a ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* szolgáltatáshoz az ssms.
3. Tekintse át az SQL-parancsfájlt, és vegye figyelembe a következőket:

   Rugalmas lekérdezés eléréséhez a bérlő-adatbázisok mindegyike esetében egy adatbázishoz kötődő hitelesítő adatot használja. Ezeket a hitelesítő adatokat kell lennie az összes adatbázis elérhető, és kell általában rendelkeznek a minimális jogosultságok szükségesek ahhoz, e ad hoc lekérdezéseket.

    ![hitelesítő adatok létrehozása](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   A katalógus-adatbázis segítségével a külső adatforrásaként, lekérdezések a lekérdezés futásakor a katalógusban regisztrált összes adatbázis terjesztése. Kiszolgáló neve nem egyezik az egyes központi telepítések, mert az inicializálási parancsfájlja a katalógus-adatbázis helyét lekérdezi az aktuális kiszolgáló lekérésével (@@servername) Ha a parancsfájl végrehajtása.

    ![külső adatforrás létrehozása](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   A tenant táblákban hivatkozó külső táblázatokat **TERJESZTÉSI = SHARDED(VenueId)**. Ez egy lekérdezést egy adott irányítja *VenueId* a megfelelő adatbázishoz, és javítja a teljesítményt a sok forgatókönyvben, a következő szakaszban ismertetett módon.

    ![külső táblák létrehozása](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A helyi tábla *VenueTypes* , amely létre és töltődik fel. A táblázatban az összes bérlői adatbázis közös, így ide helyi táblaként, ki van töltve a közös adatok ábrázolhatók. Egyes lekérdezések esetén ez csökkentheti a bérlő adatbázisok közötti áthelyezése adatmennyiség és a *adhocreporting* adatbázis.

    ![tábla létrehozása](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Ha hivatkozási táblák ezen a módon kikapcsolja, feltétlenül frissítése a következő tábla sémáját és az adatokat, amikor frissíti a bérlő adatbázisok.

4. Nyomja le az **F5** futtassa a parancsfájlt, és inicializálni a *adhocreporting* adatbázis. 

Most elosztott lekérdezések futtatását, valamint insights összegyűjtése minden bérlők között!

## <a name="run-ad-hoc-distributed-queries"></a>Ad hoc elosztott lekérdezések futtatása

Most, hogy a *adhocreporting* adatbázis állított be, lépjen tovább, és néhány elosztott lekérdezések futtatása. Például a végrehajtási terv kra, ha a lekérdezés feldolgozása zajlik. 

További részletek a terv ikonok vizsgálatakor ellenőrizze a végrehajtási terv, mutasson. 

1. A *SSMS*, nyissa meg... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReportingQueries.sql*.
2. Biztosan csatlakozni tudjanak a **adhocreporting** adatbázis.
3. Válassza ki a **lekérdezés** menüre, majd **tényleges végrehajtási terv tartalmazza**
4. Jelölje ki a *mely helyszínek jelenleg regisztrált?* lekérdezést, és nyomja le az **F5**.

   A lekérdezés visszaadja a teljes helyszínére lista, hogyan ábrázoló, és könnyen már egyetlen bérlő számára átfogó lekérdezése, és térjen vissza az adatokat az egyes bérlők.

   Vizsgálja meg a terv és a teljes költség van-e a távoli lekérdezés, mert azt még egyszerűen az egyes bérlői adatbázis is, majd válassza a helyszínére információkat.

   ![Válassza ki * a dbo. Helyszínek](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Válassza ki a következő lekérdezést, és nyomja le az **F5**.

   Ez a lekérdezés csatlakozik a bérlő adatbázisokból és a helyi adatok *VenueTypes* tábla (helyi, mert van egy táblát a *adhocreporting* adatbázis).

   Vizsgálja meg a terv és a legtöbb költsége van-e a távoli lekérdezés mivel azt a bérlő helyszínére info (dbo. Helyszínek), majd hajtsa végre a gyors helyi csatlakozzon a helyi *VenueTypes* tábla a következő felhasználóbarát név megjelenítéséhez.

   ![Csatlakozás a helyi és távoli adatokkal](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Most válassza ki a *mely napján volt a legtöbb a jegyektől eladott?* lekérdezést, és nyomja le az **F5**.

   Ez a lekérdezés egy kicsit bonyolultabb csatlakoztatása és összesítési beállítások végzi. Fontos megjegyezni, hogy távolról történik, az adatfeldolgozás nagy, de ebben az esetben azt érdekében ismét csak azokat a sorokat, igazolnia kell, az egyes helyszínekkel összesített jegy pénztári száma naponta csak egyetlen sort ad vissza.

   ![lekérdezés](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Az ad hoc jelentési adatbázis központi telepítése, és adja hozzá a séma elosztott lekérdezések futtatásához.

Most a [bérlői Analytics oktatóanyag](saas-multitenantdb-tenant-analytics.md) való kibontása adatokba összetettebb elemzés feldolgozásának külön analytics adatbázisába.

## <a name="additional-resources"></a>További források

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
