---
title: Lekérdezések jelentése több adatbázisban
description: Bérlőközi jelentéskészítés elosztott lekérdezések használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
ms.date: 01/25/2019
ms.openlocfilehash: c863946934df9990c14e49ef1a0a82bbc55b27c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822082"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Bérlők közötti jelentéskészítés elosztott lekérdezésekkel

Ebben az oktatóanyagban elosztott lekérdezéseket futtat a bérlői adatbázisok teljes készletében a jelentések hez. Ezek a lekérdezések kinyerhetik a Wingtip Jegyek SaaS-bérlők napi működési adataiba temetett elemzéseket. Ehhez egy további jelentési adatbázist telepít a katalóguskiszolgálóra, és rugalmas lekérdezéssel engedélyezheti az elosztott lekérdezéseket.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> 
> * Jelentési adatbázis telepítése
> * Elosztott lekérdezések futtatása az összes bérlői adatbázisban
> * Hogyan teszik lehetővé az egyes adatbázisok globális nézetei a bérlők közötti hatékony lekérdezést?


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:


* A Wingtip jegyek SaaS-adatbázis bérlőnként alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS-adatbázis bérlőnkénti telepítése és feltárása című témakört.](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Az SQL Server Management Studio (SSMS) telepítve van. Az SSMS letöltéséhez és telepítéséhez olvassa el [az SQL Server Management Studio (SSMS) letöltése című témakört.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="cross-tenant-reporting-pattern"></a>Több-bérlős jelentéskészítési minta

![át- és felosztott lekérdezésminta](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Az SaaS-alkalmazásokkal az egyik lehetőség a felhőben tárolt hatalmas mennyiségű bérlői adat felhasználása az alkalmazás működésébe és használatába való betekintést nyerésre. Ezek az elemzések irányíthatják a funkciók fejlesztését, a használhatóság javítását és az alkalmazásokba és szolgáltatásokba történő egyéb befektetéseket.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Az egyik módszer az [elastic Query](sql-database-elastic-query-overview.md)használata, amely lehetővé teszi a lekérdezést a közös sémával rendelkező adatbázisok elosztott készletei között. Ezek az adatbázisok különböző erőforráscsoportok és előfizetések között is eloszthatók, de meg kell osztaniuk egy közös bejelentkezést. A Rugalmas lekérdezés egy *egyfejű* adatbázist használ, amelyben olyan külső táblák vannak definiálva, amelyek tükrözik a táblákat vagy nézeteket az elosztott (bérlői) adatbázisokban. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Rugalmas lekérdezés a katalógus-adatbázis szegmensleképezése segítségével határozza meg az összes bérlői adatbázis helyét. A főadatbázis beállítása és lekérdezése egyszerű a szabványos [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)használatával, és támogatja az olyan eszközöklekérdezését, mint a Power BI és az Excel.

Lekérdezések elosztása a bérlői adatbázisok között, rugalmas lekérdezés azonnali betekintést nyújt az élő termelési adatok. Rugalmas lekérdezés lekéri az adatokat potenciálisan sok adatbázis, lekérdezési késés magasabb lehet, mint az egy több-bérlős adatbázisba küldött egyenértékű lekérdezések. Lekérdezések tervezése a fő adatbázisba visszaadott adatok minimalizálása érdekében. Rugalmas lekérdezés gyakran a legalkalmasabb a lekérdezése kis mennyiségű valós idejű adatok, szemben a gyakran használt vagy összetett elemzési lekérdezések vagy jelentések létrehozása. Ha a lekérdezések nem teljesítenek jól, tekintse meg a [végrehajtási tervet,](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) hogy a lekérdezés melyik része kerül leküldéses a távoli adatbázisba, és mennyi adatot ad vissza. Az összetett összesítést vagy analitikus feldolgozást igénylő lekérdezések jobban kezelhetők lehetnek a bérlői adatok adatbázisba vagy elemzési lekérdezésekre optimalizált adattárházba történő kinyerésével. Ezt a mintát a [bérlői elemzési oktatóanyag ismerteti.](saas-tenancy-tenant-analytics.md) 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis bérlőnkénti alkalmazásparancsfájljainak beszereznie

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájlok és az alkalmazás forráskód érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub tárház. Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip Jegyek SaaS-parancsfájlok letöltéséhez és feloldásához szükséges lépésekhez.

## <a name="create-ticket-sales-data"></a>Jegyértékesítési adatok létrehozása

Ha egy érdekesebb adatkészletre szeretne lekérdezéseket futtatni, hozzon létre jegyértékesítési adatokat a jegygenerátor futtatásával.

1. A *PowerShell ISE-ben*nyissa meg a ... \\Tanulási modulok\\operatív\\elemzés adhoc reporting\\*demo-adhocreporting.ps1* script, és állítsa be a következő értéket:
   * **$DemoScenario** = 1, **Jegyek vásárlása eseményekminden helyszínen**.
2. Nyomja le **az F5 billentyűt** a parancsfájl futtatásához és a jegyértékesítés létrehozásához. A parancsfájl futása közben folytassa az oktatóanyag lépéseit. A jegyadatok lekérdezése az *ad hoc elosztott lekérdezések futtatása* szakaszban történik, ezért várja meg, amíg a jegygenerátor befejeződik.

## <a name="explore-the-global-views"></a>Fedezze fel a globális nézeteket

A Wingtip jegyek SaaS-adatbázis bérlőnként alkalmazás, minden bérlő kap egy adatbázist. Így az adatbázistáblákban lévő adatok hatóköre egyetlen bérlő szemszögéből történik. Azonban az összes adatbázis lekérdezések, fontos, hogy rugalmas lekérdezés az adatokat úgy kezelheti, mintha egy bérlő által skálázott egyetlen logikai adatbázis része. 

A minta szimulálása érdekében a "globális" nézetek egy készlete kerül a bérlői adatbázisba, amely bérlői azonosítót vetít a globálisan lekérdezett táblák mindegyikébe. A *VenueEvents* nézet például hozzáad egy számított *VenueId azonosítót* az *Események* táblából kivetített oszlopokhoz. Hasonlóképpen a *VenueTicketPurchases* és *venuetickets* nézetek hozzá egy kiszámított *VenueId* oszlop a megfelelő táblákból kivetített. Ezeket a nézeteket a Rugalmas lekérdezés a lekérdezések párhuzamosítására és leküldése a megfelelő távoli bérlői adatbázisba, ha egy *VenueId* oszlop van jelen. Ez jelentősen csökkenti a visszaadott adatok mennyiségét, és számos lekérdezés teljesítményének jelentős növekedését eredményezi. Ezek a globális nézetek már előre létre az összes bérlői adatbázisokban.

1. Nyissa meg az SSMS-t, és [csatlakozzon a&lt;bérlőkhöz1-&gt; USER kiszolgáló](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Bontsa ki **az Adatbázisok csomópontot,** kattintson a jobb gombbal _a contosoconcerthall_elemre, és válassza az **Új lekérdezés parancsot.**
1. Az egybérlős táblák és a globális nézetek közötti különbség feltárásához futtassa a következő lekérdezéseket:

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

Ezekben a nézetekben a *VenueId* a helyszín nevének kivonataként kerül kiszámításra, de bármilyen megközelítés használható egy egyedi érték bevezetésére. Ez a megközelítés hasonló a bérlői kulcs számítási módja a katalógusban való használatra.

A helyszínek nézet meghatározásának *vizsgálata:*

1. Az **Objektumkezelőben**bontsa ki **a contosoconcerthall** > **Nézetek :**

   ![Kilátás nyílik](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Kattintson a jobb gombbal **dbo. Helyszínek**.
3. **Parancsfájlnézet** > kiválasztása LÉTREHOZÁS**ÚJ lekérdezésszerkesztőablakként** **CREATE To** > 

Script bármely más *helyszín* nézetek, hogy hogyan adja hozzá a *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Az elosztott lekérdezésekhez használt adatbázis telepítése

Ez a gyakorlat az _adhocreporting_ adatbázist telepíti. Ez a fő adatbázis, amely tartalmazza a séma lekérdezéséhez használt az összes bérlői adatbázisok. Az adatbázis a meglévő katalóguskiszolgálóra van telepítve, amely a mintaalkalmazás összes felügyeleti adatbázisához használt kiszolgáló.

1. a *PowerShell ISE*, nyitott ... \\Tanulási modulok\\operatív\\elemzés adhoc\\*jelentési demo-adhocReporting.ps1*. 

1. Állítsa be **$DemoScenario = 2**, _Ad hoc jelentési adatbázis telepítése_.

1. Nyomja le **az F5 billentyűt** a parancsfájl futtatásához és az *adhocreporting adatbázis létrehozásához.*

A következő szakaszban sémát ad hozzá az adatbázishoz, hogy az elosztott lekérdezések futtatásához használható legyen.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>A "head" adatbázis konfigurálása elosztott lekérdezések futtatásához

Ez a gyakorlat sémát (a külső adatforrást és külső tábladefiníciókat) ad hozzá az _adhocreporting_ adatbázishoz, hogy lehetővé tegye az összes bérlői adatbázis lekérdezését.

1. Nyissa meg az SQL Server Management Studio alkalmazást, és csatlakozzon az előző lépésben létrehozott Adhoc Reporting adatbázishoz. Az adatbázis neve *adhocreporting*.
2. Nyissa meg a ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql-t_ az SSMS-ben.
3. Tekintse át az SQL-parancsfájlt, és jegyezze fel:

   Rugalmas lekérdezés egy adatbázis-hatókörrel rendelkező hitelesítő adatok at a bérlői adatbázisok eléréséhez. Ennek a hitelesítő adatnak minden adatbázisban elérhetőnek kell lennie, és általában meg kell adni a lekérdezések engedélyezéséhez szükséges minimális jogokat.

    ![hitelesítő adat létrehozása](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Mivel a katalógus-adatbázis külső adatforrásként van, a lekérdezések a lekérdezés futtatásakor a katalógusban regisztrált összes adatbázisba kerülnek. Mivel a kiszolgálónevek minden központi telepítésnél eltérőek, ez a parancsfájl@servernamea katalógusadatbázis helyét aparancsfájl végrehajtását kiszolgálótól (@ ) kapja meg.

    ![külső adatforrás létrehozása](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Az előző szakaszban ismertetett és a DISTRIBUTION = **SHARDED(VenueId)** parancikán definiált globális nézetekre hivatkozó külső táblák. Mivel minden *VenueId* leképezi az egyes adatbázisok, ez javítja a teljesítményt számos forgatókönyv, ahogy a következő szakaszban látható.

    ![külső táblák létrehozása](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A létrehozott és feltöltött helyi table _VenueTypes._ Ez a referencia-adattábla minden bérlői adatbázisban közös, így itt helyi táblaként jeleníthető meg, és a közös adatokkal tölthető fel. Egyes lekérdezések esetén a tábla a fő adatbázisban definiálva csökkentheti a fő adatbázisba áthelyezendő adatok mennyiségét.

    ![tábla létrehozása](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Ha ilyen módon hivatkozási táblákat is tartalmaz, a bérlői adatbázisok frissítésekénél mindenképpen frissítse a táblasémát és az adatokat.

4. Nyomja le **az F5 billentyűt** a parancsfájl futtatásához és az adhocreporting adatbázis inicializálásához. *adhocreporting* 

Most már futtathatja az elosztott lekérdezéseket, és összegyűjtheti az összes bérlő közötti elemzéseket!

## <a name="run-distributed-queries"></a>Elosztott lekérdezések futtatása

Most, hogy az *adhocreporting* adatbázis be van állítva, folytassa, és futtasson néhány elosztott lekérdezést. Adja meg a végrehajtási tervet a lekérdezés feldolgozási helyének jobb megértése érdekében. 

A végrehajtási terv vizsgálatakor mutasson a terv ikonjaira a részletekért. 

Fontos megjegyezni, hogy **a DISTRIBUTION = SHARDED(VenueId)** beállítás a külső adatforrás definiálásakor számos esetben javítja a teljesítményt. Mivel az egyes *VenueId* leképezi az egyes adatbázisok, szűrés könnyen elvégezhető távolról, csak a szükséges adatokat adja vissza.

1. Nyit... \\Tanulási modulok\\működési\\elemzés adhoc\\*jelentési demo-adhocReportingQueries.sql* SSMS.Learning Modules Operational Analytics Adhoc Reporting Demo-AdhocReportingQueries.sql in SSMS.
2. Győződjön meg arról, hogy csatlakozik az **adhocreporting** adatbázishoz.
3. Válassza a **Lekérdezés** menüt, és kattintson **a Tényleges végrehajtási terv vel való belefoglalás gombra.**
4. Jelölje ki a *Mely helyszínek vannak jelenleg regisztrálva?* lekérdezés, és nyomja le az **F5 billentyűt.**

   A lekérdezés a teljes helyszínlistát adja vissza, bemutatva, hogy milyen gyorsan és egyszerűen lehet lekérdezni az összes bérlőt, és adatokat visszaadni az egyes bérlőktől.

   Vizsgálja meg a tervet, és ellenőrizze, hogy a teljes költség a távoli lekérdezésben van-e. Minden bérlői adatbázis távolról hajtja végre a lekérdezést, és visszaadja a helyszín adatait a fő adatbázisba.

   ![SELECT * FROM dbo. Helyszínek](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Jelölje ki a következő lekérdezést, és nyomja **le az F5 billentyűt.**

   Ez a lekérdezés egyesíti a bérlői adatbázisok és a helyi *VenueTypes* tábla adatait (helyi, mivel ez egy tábla az *adhocreporting* adatbázisban).

   Vizsgálja meg a tervet, és ellenőrizze, hogy a költségek többsége a távoli lekérdezés. Minden bérlői adatbázis visszaadja a helyszín adatait, és helyi illesztést hajt végre a helyi *VenueTypes* táblával a rövid név megjelenítéséhez.

   ![Csatlakozás távoli és helyi adatokhoz](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Most válassza ki a *Melyik napon volt a legtöbb eladott jegy?* lekérdezés, és nyomja meg az **F5**.

   Ez a lekérdezés egy kicsit összetettebb illesztést és összesítést tesz le. A feldolgozás nagy része távolról történik.  Csak egy sor, amely tartalmazza az egyes helyszínek napi jegy értékesítési száma naponta, vissza kerül a fej adatbázisba.

   ![lekérdezés](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> 
> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Telepítsen egy jelentési adatbázist, és határozza meg az elosztott lekérdezések futtatásához szükséges sémát.


Most próbálja meg a [Bérlő Analytics oktatóanyag](saas-tenancy-tenant-analytics.md) az adatok kinyerése egy külön elemzési adatbázis összetettebb elemzési feldolgozás.

## <a name="additional-resources"></a>További források

* További [oktatóanyagok, amelyek a Wingtip jegyek SaaS-adatbázis bérlőnként alkalmazásra épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
