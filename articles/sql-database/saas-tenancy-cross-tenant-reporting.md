---
title: Futtassa a lekérdezéseket jelentéskészítés több Azure SQL-adatbázisban |} A Microsoft Docs
description: Bérlők közötti jelentéskészítés használatával az elosztott lekérdezések.
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: billgib
ms.reviewer: sstein; AyoOlubeko
ms.openlocfilehash: 2b8cc66909fc4f066670424fd9127c311cf618e0
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378047"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Bérlők közötti jelentéskészítés használatával az elosztott lekérdezések

Ebben az oktatóanyagban, elosztott lekérdezések futtatása a bérlő teljes készletét jelentéskészítési adatbázisok. Ezek a lekérdezések képesek kivonni a Wingtip Tickets SaaS-bérlők napi működési adataiban rejtőző fontos értesüléseket. Ehhez a kiszolgáló üzembe helyezése egy további jelentési adatbázis és rugalmas lekérdezés használatával engedélyezhető az elosztott lekérdezések.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * Hogyan helyezhet üzembe egy jelentési adatbázis
> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Hogyan globális nézetek az egyes adatbázisok engedélyezheti a hatékonyabb lekérdezés érdekében bérlőn


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:


* A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezés a Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás és megismerése](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Az SQL Server Management Studio (SSMS) telepítve van. Töltse le és az SSMS telepítése [töltse le az SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Több-bérlős jelentéskészítési minta

![több-bérlős elosztott lekérdezés minta](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Az SaaS-alkalmazások egy lehetőség, hogy a bérlő, a felhőben tárolt adatok hatalmas mennyiségű követésével fontos információkhoz juthat a művelet és az alkalmazás használatának. Ezen elemzési funkcióinak fejlesztését, használhatóságuk javítását és más befektetéseket az alkalmazások és szolgáltatások is végigvezeti.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Egyik módszere az, hogy használjon [rugalmas lekérdezés](sql-database-elastic-query-overview.md), ami lehetővé teszi az adatbázisok közös sémával rendelkező elosztott illenek lekérdezése. Ezeknek az adatbázisoknak eltérő erőforráscsoportokban és előfizetésekben szét lehetnek osztva, de kell osztania egy közös bejelentkezés. Rugalmas lekérdezés használja egyetlen *fő* adatbázis, amelyben definiált külső táblák tükröző táblák vagy nézetek az elosztott (bérlői) adatbázisok. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Rugalmas lekérdezés a katalógus-adatbázisban a szegmenstérkép használatával az összes bérlői adatbázisok helyének meghatározásához. Telepítés és a lekérdezés a fő adatbázis magától értetődnek szabványok használatával [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), és támogatja az eszközök, mint például a Power BI és az Excel lekérdezi.

Lekérdezések a bérlői adatbázisok közötti elosztásával, a rugalmas lekérdezés éles adatok azonnali betekintést nyújt. Rugalmas lekérdezés lekéri a potenciálisan sok adatbázis adatait, mert lekérdezési késés magasabb, mint egy egyetlen több bérlős adatbázisban küldött egyenértékű lekérdezések lehet. Lekérdezések csökkentése érdekében az adatokat, hogy a rendszer visszaadja a fő adatbázis tervezése. Rugalmas lekérdezés gyakran leginkább megfelelő, a lekérdezés, ellentétben a gyakran használt épület vagy összetett elemzési lekérdezések vagy jelentések valós idejű adatokat kis mennyiségű. Ha nem jól lekérdezéseket hajt végre, keresse meg a [végrehajtási terv](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) , a távoli adatbázis leküldte a lekérdezés részét, és a visszaadott adatok mennyiségét. Komplex összesítést készít vagy analitikus feldolgozást igénylő lekérdezések jobban kezeli lehet bérlői adatok oly módon, egy elemzési lekérdezésekhez optimalizált adatbázis vagy a data warehouse-bA. Ez a minta ismertetése a [bérlői elemzések – oktatóanyag](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazásszkriptek beolvasása

A Wingtip Tickets SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-adattárban. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása.

## <a name="create-ticket-sales-data"></a>Hozzon létre jegyet értékesítési adatok

Lekérdezések futtatásához egy érdekesebb adatkészlet, a jegy-generátor futtatásával hozzon létre jegyet értékesítési adatokat.

1. Az a *PowerShell ISE-ben*, nyissa meg a... \\Tanulási modulok\\működési elemzések\\ad hoc ad hoc jelentéskészítés\\*Demo-AdhocReporting.ps1* szkriptet, és állítsa be a következő értéket:
   * **$DemoScenario** = 1, **jegyek beszerzése minden helyszínen**.
2. Nyomja meg **F5** futtassa a szkriptet, és jegyeladásokkal létrehozásához. A parancsfájl futása közben továbbra is ebben az oktatóanyagban a lépéseket. A jegy adatainak lekérdezése abban a *ad hoc elosztott lekérdezések futtatása* szakaszban, ezért Várjon, amíg a jegy generátor végrehajtásához.

## <a name="explore-the-global-views"></a>Fedezze fel a globális nézetek

A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazásban minden bérlői adatbázis van megadva. Az adatbázistáblákban szereplő adatokat, így egyetlen új bérlő szempontjából hatókörét. Azonban minden adatbázis lekérdezésekor fontos, hogy rugalmas lekérdezés az adatok is kezelheti, mint ha a bérlő által horizontálisan skálázott egyetlen logikai adatbázis része. 

Ez a minta a szimulálásához "global" nézetek készletét kerülnek a bérlői adatbázis erre a projektre a bérlő Azonosítóját, a tábla, amely globálisan a rendszer megkérdezi. Például a *VenueEvents* nézet hozzáad egy számított *VenueId* az előre jelzett költségről a oszlopaihoz a *események* tábla. Ehhez hasonlóan a *VenueTicketPurchases* és *VenueTickets* nézetek hozzáadása egy számított *VenueId* oszlop projected azok megfelelő táblából. Ezek a nézetek segítségével rugalmas lekérdezés párhuzamosíthatja, lekérdezések és a leküldéses azokat le a megfelelő távoli bérlői adatbázis-mikor egy *VenueId* oszlop szerepel. Ez jelentősen csökkenti az adatot adja vissza, és számos lekérdezések teljesítményének jelentős növekedését eredményezi. Ezek a globális nézetek lett előre létrehozott, az összes bérlői adatbázisban.

1. Nyissa meg az SSMS és [kapcsolódjon a tenants1 -&lt;felhasználói&gt; kiszolgáló](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Bontsa ki a **adatbázisok**, kattintson a jobb gombbal _contosoconcerthall_, és válassza ki **új lekérdezés**.
1. Futtassa a következő lekérdezéseket a különbség a single-tenant táblákban és a globális nézetek felfedezése:

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

Ezekben a nézetekben a *VenueId* mint a helyszín neve, de bármely megközelítés kivonatát vezessen be egy egyedi érték használható. Ez a megközelítés akkor jön létre a bérlőkulcsot a katalógus használata hasonló.

Megvizsgálhatja a definíciója a *helyszínek* megtekintése:

1. A **Object Explorer**, bontsa ki a **contosoconcerthall** > **nézetek**:

   ![megtekintés](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Kattintson a jobb gombbal **dbo. Helyszínek**.
3. Válassza ki **nézetként parancsfájl** > **LÉTESÍTÉSÉT** > **új Query-szerkesztő ablakban**

Parancsfájl-másik *helyszín* nézet megtekintéséhez, hogyan adnak hozzá a *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Az elosztott lekérdezésekhez használt adatbázis üzembe helyezése

Ebben a gyakorlatban üzembe helyezi a _adhocreporting_ adatbázis. Ez az összes bérlői adatbázison lekérdezések küldésére használatos sémát tartalmazó fő adatbázisban. A meglévő kiszolgáló, a használt mintaalkalmazás felügyelettel kapcsolatos összes adatbázis-kiszolgáló telepítve van az adatbázis.

1. a *PowerShell ISE-ben*, nyissa meg... \\Tanulási modulok\\működési elemzések\\ad hoc ad hoc jelentéskészítés\\*Demo-AdhocReporting.ps1*. 

1. Állítsa be **$DemoScenario = 2**, _jelentési adatbázis központi telepítése az Ad-hoc_.

1. Nyomja meg **F5** futtassa a szkriptet, és hozzon létre a *adhocreporting* adatbázis.

A következő szakaszban séma hozzáadása az adatbázishoz, elosztott lekérdezések futtatására használható.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Elosztott lekérdezések futtatására a "fő" adatbázis konfigurálása

Ebben a gyakorlatban séma (a külső adatforrás és a külső tábla definíciókat) ad hozzá a _adhocreporting_ engedélyezéséhez a lekérdezés az összes bérlői adatbázison.

1. Nyissa meg az SQL Server Management Studiót, és a jelentéskészítési Adhoc adatbázishoz az előző lépésben létrehozott. Az adatbázis neve nem *adhocreporting*.
2. Nyissa meg a ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ az ssms-ben.
3. Tekintse át az SQL-szkript és Megjegyzés:

   Rugalmas lekérdezés adatbázishoz kötődő hitelesítő adatokat használja a bérlői adatbázisok mindegyike eléréséhez. Ez a hitelesítő adat elérhető lesz az összes adatbázist kell, és kell általában biztosítani a minimális jogosultságok szükségesek ahhoz, ezeket a lekérdezéseket.

    ![hitelesítő adat létrehozása](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   A katalógus-adatbázis, a külső adatforrás, a lekérdezések oszlanak meg a lekérdezés futtatásakor a katalógusban regisztrált összes adatbázishoz. Kiszolgáló neve nem egyezik az egyes központi telepítések, mivel ez a szkript a katalógus-adatbázis helye lekérdezi az aktuális kiszolgálóról (@@servername), a szkript végrehajtása.

    ![külső adatforrás létrehozása](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   A globális nézetek hivatkozó külső táblák az előző szakaszban leírt, és meghatározott **TERJESZTÉSI = SHARDED(VenueId)**. Mivel minden egyes *VenueId* maps egyetlen adatbázishoz, ez növeli a teljesítményt számos forgatókönyv a következő szakaszban látható módon.

    ![külső táblák létrehozása](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A helyi tábla _VenueTypes_ , amelyet létrehozott és kitölti a rendszer. A referenciatábla adatok szokás az összes bérlői adatbázisban, így jelölhető ide helyi táblázatként, ki lesz töltve a common data. Bizonyos lekérdezések esetén ez a táblázat a fő adatbázisban definiált kellene csökkentheti át kell helyezni a fő adatbázis adatok mennyisége.

    ![Tábla létrehozása](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Ha referencia táblák adja meg ezen a módon kikapcsolja, mindenképpen frissítse a táblasémát és adatokat a bérlői adatbázisok frissítésekor.

4. Nyomja meg **F5** futtassa a szkriptet, és inicializálása a *adhocreporting* adatbázis. 

Most már az elosztott lekérdezések futtatását, valamint az összes bérlőre kiterjedő gyűjtsön információt!

## <a name="run-distributed-queries"></a>Elosztott lekérdezések futtatása

Most, hogy a *adhocreporting* adatbázis állított be, lépjen tovább, és egyes elosztott lekérdezések futtatása. Például, ha a lekérdezés feldolgozása történik szeretné jobban megismerni a végrehajtási terv. 

A végrehajtási terv vizsgálatával, amikor a kurzort a csomag ikonokat részleteiről. 

Fontos megjegyezni, akkor ez a beállítás **TERJESZTÉSI = SHARDED(VenueId)** mikor van definiálva a külső adatforrás javítja a teljesítményt, számos forgatókönyv esetében. Mivel minden egyes *VenueId* maps egyetlen adatbázishoz, szűrés könnyedén elvégezhető távolról, csak a szükséges adatokat ad vissza.

1. Nyissa meg... \\Tanulási modulok\\működési elemzések\\ad hoc ad hoc jelentéskészítés\\*Demo-AdhocReportingQueries.sql* az ssms-ben.
2. Győződjön meg arról, hogy csatlakozik az **adhocreporting** adatbázis.
3. Válassza ki a **lekérdezés** menüben, majd kattintson **tényleges végrehajtási terv belefoglalása**
4. Jelölje ki a *mely helyszínek jelenleg regisztrált?* lekérdezést, majd nyomja meg **F5**.

   A lekérdezés visszaadja a teljes helyszín lista, így bemutatja, milyen gyorsan és könnyen az összes bérlőre kiterjedő lekérdezéséhez és az egyes bérlők adatokat adja vissza.

   Nézze meg a terv, és figyelje meg, hogy a teljes költség a távoli lekérdezés. Minden bérlői adatbázis távolról hajt végre a lekérdezést, és a központi adatbázisba a helyszín információkat ad vissza.

   ![Válassza ki * a dbo. Helyszínek](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Válassza ki a következő lekérdezést, majd nyomja meg **F5**.

   Ez a lekérdezés csatlakozik a bérlői adatbázisok és a helyi adatait *VenueTypes* tábla (helyi, mert van egy táblát a *adhocreporting* adatbázis).

   Nézze meg a terv, és figyelje meg, hogy a költség többsége a távoli lekérdezés. Minden bérlői adatbázis a helyszín adatokat ad vissza, és elvégzi a helyi csatlakozzon a helyi *VenueTypes* tábla rövid nevének megjelenítéséhez.

   ![Csatlakozás a helyi és távoli adatok](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Most válassza ki a *mely napon lettek a legtöbb jegyek értékesített?* lekérdezést, majd nyomja meg **F5**.

   Ez a lekérdezés hajtja végre egy kicsit összetettebb csatlakoztatása és összesítésének megadására szolgál. Távolról végbe az adatfeldolgozás nagy.  Csak egyetlen sor, amely tartalmazza minden egyes helyszín napi jegy értékesítés száma / nap, a fő adatbázis visszaadása.

   ![lekérdezés](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Jelentéskészítési adatbázis üzembe helyezése, és adja meg a séma, elosztott lekérdezések futtatásához szükséges.


Most a [bérlői elemzések – oktatóanyag](saas-tenancy-tenant-analytics.md) összetettebb analytics feldolgozási külön analitikai adatbázisba való kinyerésekor adatok feltárására.

## <a name="additional-resources"></a>További források

* További [, amint a Wingtip Tickets SaaS adatbázis Per bérlői számára oktatóanyagokkal](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
