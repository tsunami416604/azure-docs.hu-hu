---
title: Futtassa a lekérdezések reporting több Azure SQL-adatbázisok közötti |} Microsoft Docs
description: Kereszt-bérlő használó elosztott lekérdezések.
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptuals
ms.date: 04/01/2018
ms.author: billgib
ms.reviewer: sstein; AyoOlubeko
ms.openlocfilehash: 4738f20ad647e65abffdb65ef350c3c0ad4d2f8f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646102"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Kereszt-bérlő használó elosztott lekérdezések

Ebben az oktatóanyagban elosztott lekérdezéseket futtat a bérlő teljes szétosztva a jelentési adatbázist. Ezeket a lekérdezéseket insights megbúvó a Wingtip jegyek Szolgáltatottszoftver-bérlő napi működési adatokat nyerhet ki. Ehhez a kiszolgáló telepítése egy további jelentéskészítési adatbázis, és rugalmas lekérdezési használatával engedélyezhető az elosztott lekérdezésekben.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * A jelentéskészítési adatbázis központi telepítése
> * Az összes bérlői az adatbázisok közötti elosztott lekérdezések futtatása
> * Hogyan globális nézetek az egyes adatbázisok engedélyezheti a bérlők közötti hatékony lekérdezése


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:


* A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) telepítve van. Töltse le és telepítse az SSMS [töltse le az SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Jelentési kereszt-bérlő minta

![több-bérlős elosztott lekérdezés minta](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

SaaS-alkalmazásokhoz az egyetlen lehetőség bérlői adatok a felhőben tárolt hatalmas mennyiségű segítségével betekintést nyerhet a művelet és az alkalmazás használatát. Ezek insights szolgáltatás fejlesztési, használhatóság fejlesztések és egyéb befektetések az alkalmazások és szolgáltatások is ismerteti.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Egyik módszer az, hogy használjon [rugalmas lekérdezési](sql-database-elastic-query-overview.md), amely lehetővé teszi, hogy a közös séma adatbázisok elosztott készleteit között lekérdezése. Ezek az adatbázisok másik erőforráscsoport-sablonok és előfizetések legyen elosztva, de kell egy közös bejelentkezési megosztaniuk. Rugalmas lekérdezés használja egyetlen *head* , amelyben külső táblázatokat adatbázis, amely a táblák vagy nézetek az elosztott (bérlői) adatbázisok tükrözik. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Rugalmas lekérdezési a katalógus-adatbázis a shard térkép összes bérlői adatbázis helyének meghatározásához használja. A telepítő és a központi adatbázis lekérdezése egyszerű szabványos [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), és támogatja a Power BI és Excel lekérdezése.

A bérlői adatbázisok közötti lekérdezések elosztásával rugalmas lekérdezési élő termelési adatok azonnali betekintést biztosít. Rugalmas lekérdezés lekéri a potenciálisan sok adatbázisok, lekérdezés késés nagyobb, mint egy több-bérlős adatbázisba küldött egyenértékű lekérdezések válhat. Tervezze meg a lekérdezéseket, amelyekkel lecsökkentheti a központi adatbázisba visszaadott adatokat. Rugalmas lekérdezési gyakran leginkább megfelelő, a kis adatmennyiségek valós idejű, szemben a gyakran használt épület vagy összetett elemzési lekérdezések vagy jelentések lekérdezésre. Ha nem jól lekérdezések hajtható végre, tekintse meg a [végrehajtási terv](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) , a lekérdezés részét a távoli adatbázis kerül, és mennyi adatot ad vissza. Komplex összesítést készít vagy elemzésfeldolgozási igénylő lekérdezések bérlői adatok csomagolja ki egy adatbázis vagy adatraktár elemzési lekérdezések optimalizálva jobb leírók is lehet. Ebben a mintában az ismertetése a [bérlői analytics oktatóanyag](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.

## <a name="create-ticket-sales-data"></a>A jegy értékesítési adatok létrehozása

Ennél is érdekesebb megoldást adatkészlet lekérdezéseinek futtatásához, a szolgáltatásjegy-generátor futtatásával hozzon létre jegy értékesítési adatait.

1. Az a *PowerShell ISE*, nyissa meg a... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReporting.ps1* parancsfájlt, és a következő értéket:
   * **$DemoScenario** = 1, **események minden helyszínek, a jegyektől beszerzési**.
2. Nyomja le az **F5** futtassa a parancsfájlt, és a jegyet értékesítési generálásához. A parancsfájl futása közben, a lépések az ebben az oktatóanyagban. A jegy adatokat lekérdezni a *elosztott ad hoc lekérdezéseket futtathat* területen, várjon, amíg befejeződik a jegy generátor.

## <a name="explore-the-global-views"></a>Fedezze fel a globális nézetek

A Wingtip jegyek SaaS adatbázis / bérlői alkalmazásban mindegyik bérlő kap egy adatbázis. A táblák szereplő adatokat, így egyetlen bérlői szempontjából hatókörét. Azonban minden az adatbázisok közötti lekérdezésekor fontos, hogy rugalmas lekérdezési az adatok is kezelheti, mintha a bérlő szilánkos egyetlen logikai adatbázis része. 

Ebben a mintában szimulálása, "global" nézetet kínál hozzáadódnak a bérlő adatbázis azokat a táblákat, amelyek a rendszer megkérdezi a globálisan projekt egy bérlő azonosítója. Például a *VenueEvents* nézet hozzáadása egy számított *VenueId* a tervezett oszlopokra a *események* tábla. Hasonlóképpen a *VenueTicketPurchases* és *VenueTickets* nézetek hozzáadása egy számított *VenueId* oszlop tervezett a megfelelő táblát. Ezek a nézetek által használt rugalmas lekérdezés lekérdezések és leküldéses azokat le a megfelelő távoli bérlő adatbázis-mikor parallelize egy *VenueId* oszlop. Ez jelentősen csökkenti az adatmennyiséget ad vissza, és sok lekérdezések teljesítményének jelentős növekedését eredményezi. Ezek a globális nézetek már előre létrehozott összes bérlői adatbázisokban.

1. Nyissa meg a szolgáltatáshoz az SSMS és [csatlakozni a tenants1 -&lt;felhasználói&gt; server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Bontsa ki a **adatbázisok**, kattintson a jobb gombbal _contosoconcerthall_, és válassza ki **új lekérdezés**.
1. Futtassa az alábbi lekérdezéseket felfedezése, mely a különbség a single-tenant táblákban és a globális nézetek:

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

   ![megtekintés](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Kattintson a jobb gombbal **dbo. Helyszínek**.
3. Válassza ki **nézetként parancsfájl** > **hozhat létre** > **új Lekérdezésszerkesztő ablak**

Parancsfájl-másik *helyszínére* nézeteit hogyan fel a *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Az elosztott lekérdezéseknél használt adatbázis központi telepítése

Ebben a gyakorlatban telepíti a _adhocreporting_ adatbázis. Ez az a központi adatbázis, amely tartalmazza az összes bérlői az adatbázisok közötti lekérdezéshez használt séma. Az adatbázis telepítve van a meglévő kiszolgáló, amely a mintaalkalmazást-felügyelettel kapcsolatos összes adatbázisának használt kiszolgáló.

1. a *PowerShell ISE*, nyissa meg... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReporting.ps1*. 

1. Állítsa be **$DemoScenario = 2**, _jelentési adatbázis központi telepítése az Ad hoc_.

1. Nyomja le az **F5** futtassa a parancsfájlt, és hozzon létre a *adhocreporting* adatbázis.

A következő szakaszban séma hozzáadása az adatbázishoz, elosztott lekérdezések futtatására használható.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Az elosztott lekérdezések futtatásához a "head" adatbázis konfigurálása

Ebben a gyakorlatban ad hozzá (a külső adatforrás és a külső tábla-definíciók) séma a _adhocreporting_ adatbázis ahhoz, hogy minden bérlő az adatbázisok közötti lekérdezése.

1. Nyissa meg az SQL Server Management Studio eszközt, és az előző lépésben létrehozott ad hoc jelentési adatbázis kapcsolódjon. Az adatbázis neve nem *adhocreporting*.
2. Nyissa meg a ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ szolgáltatáshoz az ssms.
3. Nézze át az SQL-parancsfájlt és a Megjegyzés:

   Rugalmas lekérdezés eléréséhez a bérlő-adatbázisok mindegyike esetében egy adatbázishoz kötődő hitelesítő adatot használja. Ezeket a hitelesítő adatokat kell lennie az összes adatbázis elérhető, és kell általában rendelkeznek a minimális jogosultságokat kell ahhoz, hogy ezeket a lekérdezéseket.

    ![hitelesítő adatok létrehozása](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   A katalógus adatbázissal külső adatforrásaként lekérdezések terjesztése a lekérdezés futásakor a katalógusban regisztrált összes adatbázisra. Kiszolgáló neve nem egyezik az egyes központi telepítések, mivel ezt a parancsfájlt a katalógus-adatbázis helyét lekérdezi az aktuális kiszolgálóról (@@servername) Ha a parancsfájl végrehajtása.

    ![külső adatforrás létrehozása](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   A külső táblákra hivatkozó a globális nézetek, az előző szakaszban leírt, és definiálva **TERJESZTÉSI = SHARDED(VenueId)**. Mivel minden egyes *VenueId* képeződik le egy adatbázist, ez növeli a teljesítményt számos forgatókönyv a következő szakaszban látható.

    ![külső táblák létrehozása](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A helyi tábla _VenueTypes_ , amely létre és töltődik fel. A táblázatban az összes bérlői adatbázis közös, így ide helyi táblaként, ki van töltve a közös adatok ábrázolhatók. Egyes lekérdezések esetén ez a táblázat a központi adatbázisban definiált rendelkező csökkentheti át kell helyezni a központi adatbázisba adatmennyiséget.

    ![tábla létrehozása](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Ha hivatkozási táblák ezen a módon kikapcsolja, feltétlenül frissítése a következő tábla sémáját és az adatokat, amikor frissíti a bérlő adatbázisok.

4. Nyomja le az **F5** futtassa a parancsfájlt, és inicializálni a *adhocreporting* adatbázis. 

Most elosztott lekérdezések futtatását, valamint insights összegyűjtése minden bérlők között!

## <a name="run-distributed-queries"></a>Az elosztott lekérdezések futtatása

Most, hogy a *adhocreporting* adatbázis állított be, lépjen tovább, és néhány elosztott lekérdezések futtatása. Például a végrehajtási terv kra, ha a lekérdezés feldolgozása zajlik. 

További részletek a terv ikonok vizsgálatakor ellenőrizze a végrehajtási terv, mutasson. 

Fontos megjegyezni, hogy beállítás **TERJESZTÉSI = SHARDED(VenueId)** amikor definiálva van a külső adatforrás javítja a teljesítményt a sok forgatókönyvben. Mivel minden egyes *VenueId* képeződik le egy adatbázist, szűrés könnyen végezhető el távolról, csak a szükséges adatokat ad vissza.

1. Nyissa meg... \\Tanulási modulok\\működési Analytics\\ad hoc Reporting\\*bemutató-AdhocReportingQueries.sql* szolgáltatáshoz az ssms.
2. Biztosan csatlakozni tudjanak a **adhocreporting** adatbázis.
3. Válassza ki a **lekérdezés** menüre, majd **tényleges végrehajtási terv tartalmazza**
4. Jelölje ki a *mely helyszínek jelenleg regisztrált?* lekérdezést, és nyomja le az **F5**.

   A lekérdezés visszaadja a teljes helyszínére listájában ábrázoló milyen gyors és egyszerű lekérdezése összes bérlők között, és térjen vissza az adatokat az egyes bérlők.

   Vizsgálja meg a terv és a teljes költség van-e a távoli lekérdezésben. Minden egyes bérlői adatbázisához távolról végrehajtja a lekérdezést, és a központi adatbázisba annak helyszínére adatait adja vissza.

   ![Válassza ki * a dbo. Helyszínek](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Válassza ki a következő lekérdezést, és nyomja le az **F5**.

   Ez a lekérdezés csatlakozik a bérlő adatbázisokból és a helyi adatok *VenueTypes* tábla (helyi, mert van egy táblát a *adhocreporting* adatbázis).

   Vizsgálja meg a terv és a legtöbb költsége van-e a távoli lekérdezés. Az egyes bérlői adatbázisok a helyszínére vonatkozó adatokkal tér vissza, és elvégzi a helyi csatlakozzon a helyi *VenueTypes* tábla a következő felhasználóbarát név megjelenítéséhez.

   ![Csatlakozás a helyi és távoli adatokkal](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Most válassza ki a *mely napján volt a legtöbb a jegyektől eladott?* lekérdezést, és nyomja le az **F5**.

   Ez a lekérdezés egy kicsit bonyolultabb csatlakoztatása és összesítési beállítások végzi. A feldolgozás a legtöbb távolról történik.  Csak egyetlen sor, egyes helyszínekkel napi jegy pénztári száma napi tartalmazó központi adatbázisba visszaadása.

   ![lekérdezés](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * A jelentéskészítési adatbázis központi telepítése, és adja meg a séma, elosztott lekérdezések futtatásához szükséges.


Most a [bérlői Analytics oktatóanyag](saas-tenancy-tenant-analytics.md) való kibontása adatokba összetettebb elemzés feldolgozásának külön analytics adatbázisába.

## <a name="additional-resources"></a>További források

* További [oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
