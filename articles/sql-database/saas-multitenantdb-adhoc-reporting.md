---
title: Ad hoc jelentési lekérdezések több adatbázisban
description: Ad hoc jelentési lekérdezések futtatása több SQL-adatbázisban például egy több-bérlős alkalmazásban.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 10/30/2018
ms.openlocfilehash: c0d1829c52041446b4feb43d8af262265e2680fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822188"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Ad hoc elemzési lekérdezések futtatása több Azure SQL-adatbázisban

Ebben az oktatóanyagban elosztott lekérdezéseket futtat a bérlői adatbázisok teljes készletében az ad hoc interaktív jelentések engedélyezéséhez. Ezek a lekérdezések kinyerhetik a Wingtip Tickets SaaS alkalmazás napi működési adataiba temetett elemzéseket. Ezek a kinyerések, üzembe helyez egy további elemzési adatbázist a katalóguskiszolgálóra, és rugalmas lekérdezés használatával az elosztott lekérdezések engedélyezéséhez.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> 
> * Ad hoc jelentési adatbázis telepítése
> * Elosztott lekérdezések futtatása az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése és feltárása című témakört.](saas-multitenantdb-get-started-deploy.md)
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Az SQL Server Management Studio (SSMS) telepítve van. Az SSMS letöltéséhez és telepítéséhez olvassa el [az SQL Server Management Studio (SSMS) letöltése című témakört.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc jelentési minta

![adhoc jelentési minta](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Az SaaS-alkalmazások elemezhetik a központilag a felhőben tárolt hatalmas mennyiségű bérlői adatot. Az elemzések betekintést nyújtanak az alkalmazás működésébe és használatába. Ezek az elemzések irányíthatják a funkciók fejlesztését, a használhatóság javítását és az alkalmazásokba és szolgáltatásokba történő egyéb befektetéseket.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Az egyik módszer az [elastic Query](sql-database-elastic-query-overview.md)használata, amely lehetővé teszi a lekérdezést a közös sémával rendelkező adatbázisok elosztott készletei között. Ezek az adatbázisok különböző erőforráscsoportok és előfizetések között oszthatók. Mégis egy közös bejelentkezési hozzáférést kell biztosítani az összes adatbázisból származó adatok kinyeréséhez. A Rugalmas lekérdezés egy *egyfejű* adatbázist használ, amelyben olyan külső táblák vannak definiálva, amelyek tükrözik a táblákat vagy nézeteket az elosztott (bérlői) adatbázisokban. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Rugalmas lekérdezés a katalógus-adatbázis szegmensleképezése segítségével határozza meg az összes bérlői adatbázis helyét. A telepítés és a lekérdezés egyszerű a szabványos [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)használatával, és támogatja az ad hoc lekérdezést olyan eszközökről, mint a Power BI és az Excel.

Lekérdezések elosztása a bérlői adatbázisok között, rugalmas lekérdezés azonnali betekintést nyújt az élő termelési adatok. Azonban rugalmas lekérdezés lekéri az adatokat potenciálisan sok adatbázis, lekérdezési késés néha magasabb lehet, mint az egy több-bérlős adatbázisba küldött egyenértékű lekérdezések. Ügyeljen arra, hogy a visszaadott adatok minimalizálása érdekében tervezzen lekérdezéseket. Rugalmas lekérdezés gyakran a legalkalmasabb a lekérdezése kis mennyiségű valós idejű adatok, szemben a gyakran használt vagy összetett elemzési lekérdezések vagy jelentések létrehozása. Ha a lekérdezések nem teljesítenek jól, tekintse meg a [végrehajtási tervet,](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) hogy a lekérdezés melyik részét lenyomták a távoli adatbázisba. És mérje fel, hogy mennyi adatot ad vissza. Az összetett analitikus feldolgozást igénylő lekérdezések jobban kiszolgálhatók, ha a kinyert bérlői adatokat egy elemzési lekérdezésekre optimalizált adatbázisba menti. Az SQL Database és az SQL Data Warehouse ilyen elemzési adatbázist üzemeltethet.

Ez a minta az elemzési ismerteti a [bérlői elemzési oktatóanyag.](saas-multitenantdb-tenant-analytics.md)

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás forráskódjának és parancsfájljainak beszereznie

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájlok és az alkalmazás forráskód érhető el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub tárház. Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip Jegyek SaaS-parancsfájlok letöltéséhez és feloldásához szükséges lépésekhez.

## <a name="create-ticket-sales-data"></a>Jegyértékesítési adatok létrehozása

Ha egy érdekesebb adatkészletre szeretne lekérdezéseket futtatni, hozzon létre jegyértékesítési adatokat a jegygenerátor futtatásával.

1. A *PowerShell ISE-ben*nyissa meg a ... \\Tanulási modulok\\Operatív\\elemzés Adhoc Reporting\\*Demo-AdhocReporting.ps1* parancsfájlt, és állítsa be a következő értékeket:
   * **$DemoScenario** = 1, **Jegyek vásárlása eseményekminden helyszínen**.
2. Nyomja le **az F5 billentyűt** a parancsfájl futtatásához és a jegyértékesítés létrehozásához. A parancsfájl futása közben folytassa az oktatóanyag lépéseit. A jegyadatok lekérdezése az *ad hoc elosztott lekérdezések futtatása* szakaszban történik, ezért várja meg, amíg a jegygenerátor befejeződik.

## <a name="explore-the-tenant-tables"></a>A bérlői táblák felfedezése 

A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás, bérlők egy hibrid bérlőfelügyeleti modell tárolja, ahol a bérlői adatok vagy egy több-bérlős adatbázisban vagy egy bérlői adatbázisban, és áthelyezhető a kettő között. Az összes bérlői adatbázis lekérdezése során fontos, hogy rugalmas lekérdezés az adatokat úgy kezelheti, mintha egyetlen logikai adatbázis része, bérlő által szilánkos. 

Ennek a mintának a elérése érdekében minden bérlői tábla tartalmaz egy *VenueId* oszlopot, amely azonosítja, hogy az adatok melyik bérlőhöz tartoznak. A *VenueId* a helyszín nevének kivonataként kerül kiszámításra, de bármilyen megközelítés használható az oszlop egyedi értékének bevezetésére. Ez a megközelítés hasonló a bérlői kulcs számítási módja a katalógusban való használatra. *A VenueId* tartalmazó táblákat a Rugalmas lekérdezés a lekérdezések párhuzamosítására és a megfelelő távoli bérlői adatbázisba való lenyomásával használja. Ez jelentősen csökkenti a visszaadott adatok mennyiségét, és növeli a teljesítményt, különösen akkor, ha több bérlő, amelynek adatait egyetlen bérlői adatbázisoktárolja.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Az ad hoc elosztott lekérdezésekhez használt adatbázis telepítése

Ez a gyakorlat az *adhocreporting* adatbázist telepíti. Ez a fő adatbázis, amely tartalmazza a séma lekérdezéséhez használt az összes bérlői adatbázisok. Az adatbázis a meglévő katalóguskiszolgálóra van telepítve, amely a mintaalkalmazás összes felügyeleti adatbázisához használt kiszolgáló.

1. Nyit... \\Tanulási modulok\\működési\\elemzésa\\adhoc*reporting-adhocReporting.ps1* a *PowerShell ISE* és állítsa be a következő értékeket:
   * **$DemoScenario** = 2, **Ad hoc elemzési adatbázis telepítése.**

2. Nyomja le **az F5 billentyűt** a parancsfájl futtatásához és az *adhocreporting adatbázis létrehozásához.*

A következő szakaszban sémát ad hozzá az adatbázishoz, hogy az elosztott lekérdezések futtatásához használható legyen.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>A "head" adatbázis konfigurálása elosztott lekérdezések futtatásához

Ez a gyakorlat sémát (a külső adatforrást és külső tábladefiníciókat) ad hozzá az ad hoc jelentéskészítési adatbázishoz, amely lehetővé teszi az összes bérlői adatbázis lekérdezését.

1. Nyissa meg az SQL Server Management Studio alkalmazást, és csatlakozzon az előző lépésben létrehozott Adhoc jelentési adatbázishoz. Az adatbázis neve *adhocreporting*.
2. Nyissa meg a ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql-t* az SSMS-ben.
3. Tekintse át az SQL-parancsfájlt, és vegye figyelembe az alábbiakat:

   Rugalmas lekérdezés egy adatbázis-hatókörrel rendelkező hitelesítő adatok at a bérlői adatbázisok eléréséhez. Ennek a hitelesítő adatnak minden adatbázisban elérhetőnek kell lennie, és általában meg kell adni az ad hoc lekérdezések engedélyezéséhez szükséges minimális jogokat.

    ![hitelesítő adat létrehozása](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   A katalógus-adatbázis külső adatforrásként való használatával a lekérdezések a lekérdezés futtatásakor a katalógusban regisztrált összes adatbázisba kerülnek. Mivel a kiszolgálónevek minden központi telepítésnél eltérőek, ez az inicializálási@servernameparancsfájl a parancsfájl végrehajtását kiszolgáló aktuális kiszolgáló (@ ) beolvasásával kapja meg a katalógusadatbázis helyét.

    ![külső adatforrás létrehozása](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   A bérlői táblákat referencia külső táblák at **a DISTRIBUTION = SHARDED(VenueId)** értékkel definiálják. Ez egy adott VenueId lekérdezését a megfelelő adatbázisba *irányítja,* és számos forgatókönyv esetében javítja a teljesítményt a következő szakaszban látható módon.

    ![külső táblák létrehozása](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A létrehozott és feltöltött helyi table *VenueTypes.* Ez a referencia-adattábla minden bérlői adatbázisban közös, így itt helyi táblaként jeleníthető meg, és a közös adatokkal tölthető fel. Egyes lekérdezések esetében ez csökkentheti a bérlői adatbázisok és az *adhocreporting-adatbázis* között áthelyezett adatok mennyiségét.

    ![tábla létrehozása](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Ha ilyen módon hivatkozási táblákat is tartalmaz, a bérlői adatbázisok frissítésekénél mindenképpen frissítse a táblasémát és az adatokat.

4. Nyomja le **az F5 billentyűt** a parancsfájl futtatásához és az adhocreporting adatbázis inicializálásához. *adhocreporting* 

Most már futtathatja az elosztott lekérdezéseket, és összegyűjtheti az összes bérlő közötti elemzéseket!

## <a name="run-ad-hoc-distributed-queries"></a>Ad hoc elosztott lekérdezések futtatása

Most, hogy az *adhocreporting* adatbázis be van állítva, folytassa, és futtasson néhány elosztott lekérdezést. Adja meg a végrehajtási tervet a lekérdezés feldolgozási helyének jobb megértése érdekében. 

A végrehajtási terv vizsgálatakor mutasson a terv ikonjaira a részletekért. 

1. Az *SSMS*, nyitott ... \\Tanulási modulok\\működési\\elemzésa\\adhoc reporting*demo-adhocReportingQueries.sql*.
2. Győződjön meg arról, hogy csatlakozik az **adhocreporting** adatbázishoz.
3. Válassza a **Lekérdezés** menüt, és kattintson **a Tényleges végrehajtási terv vel való belefoglalás gombra.**
4. Jelölje ki a *Mely helyszínek vannak jelenleg regisztrálva?* lekérdezés, és nyomja le az **F5 billentyűt.**

   A lekérdezés a teljes helyszínlistát adja vissza, amely bemutatja, hogy milyen gyors és egyszerű az összes bérlő lekérdezése és az egyes bérlőktől származó adatok visszaadása.

   Vizsgálja meg a tervet, és ellenőrizze, hogy a teljes költség a távoli lekérdezés, mert egyszerűen megy minden bérlői adatbázis, és kiválasztja a helyszín adatait.

   ![SELECT * FROM dbo. Helyszínek](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Jelölje ki a következő lekérdezést, és nyomja **le az F5 billentyűt.**

   Ez a lekérdezés egyesíti a bérlői adatbázisok és a helyi *VenueTypes* tábla adatait (helyi, mivel ez egy tábla az *adhocreporting* adatbázisban).

   Vizsgálja meg a tervet, és győződje meg, hogy a költségek többsége a távoli lekérdezés, mert lekérdezzük az egyes bérlők helyszíninfo (dbo. Helyszínek), majd csinál egy gyors helyi illesztés -val a helyi *VenueTypes tábla* -hoz bemutatás a barátságos név.

   ![Csatlakozás távoli és helyi adatokhoz](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Most válassza ki a *Melyik napon volt a legtöbb eladott jegy?* lekérdezés, és nyomja meg az **F5**.

   Ez a lekérdezés egy kicsit összetettebb illesztést és összesítést tesz le. Fontos megjegyezni, hogy a feldolgozás nagy része távolról történik, és ismét csak azokat a sorokat hozzuk vissza, amelyekre szükségünk van, és csak egy sort adnak vissza az egyes helyszínek összesített jegyértékesítési számához naponta.

   ![lekérdezés](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> 
> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Telepítsen egy ad hoc jelentési adatbázist, és adjon hozzá sémát az elosztott lekérdezések futtatásához.

Most próbálja meg a [Bérlő Analytics oktatóanyag](saas-multitenantdb-tenant-analytics.md) az adatok kinyerése egy külön elemzési adatbázis összetettebb elemzési feldolgozás.

## <a name="additional-resources"></a>További források

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
