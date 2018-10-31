---
title: Alkalmi jelentéskészítő lekérdezések futtatása több Azure SQL-adatbázis |} A Microsoft Docs
description: Alkalmi jelentéskészítő lekérdezések futtatása több SQL-adatbázisban egy több-bérlős alkalmazás példában.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: a5c829ee77f1ba61bf8b1a19488c51dabe2262a0
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241973"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Alkalmi elemzési lekérdezések futtatása több Azure SQL-adatbázis

Ebben az oktatóanyagban, elosztott lekérdezések futtatása teljes készletét bérlői adatbázisokat ad-hoc interaktív jelentések engedélyezéséhez. Ezek a lekérdezések képesek kivonni a Wingtip Tickets SaaS-alkalmazás napi működési adataiban rejtőző fontos értesüléseket. Ehhez az alábbi információkinyerés, további elemzési adatbázis üzembe helyezése a kiszolgáló és a rugalmas lekérdezés használatával engedélyezhető az elosztott lekérdezések.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]

> * Az alkalmi jelentéskészítő adatbázis üzembe helyezése
> * Elosztott lekérdezések futtatása az összes bérlői adatbázison


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezés a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás és megismerése](saas-multitenantdb-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Az SQL Server Management Studio (SSMS) telepítve van. Töltse le és az SSMS telepítése [töltse le az SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc jelentéskészítés minta

![jelentéskészítési adhoc-minta](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-alkalmazások hatalmas mennyiségű bérlői adatok tárolása központilag a felhőben is elemezhet. Az elemzések segítséget nyújt a műveletet, és az alkalmazás használati információk feltárásában. Ezen elemzési funkcióinak fejlesztését, használhatóságuk javítását és más befektetéseket az alkalmazások és szolgáltatások is végigvezeti.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Egyik módszere az, hogy használjon [rugalmas lekérdezés](sql-database-elastic-query-overview.md), ami lehetővé teszi az adatbázisok közös sémával rendelkező elosztott illenek lekérdezése. Ezeknek az adatbázisoknak eltérő erőforráscsoportokban és előfizetésekben szét lehetnek osztva. Még egy közös bejelentkezési adatok kinyerése az összes adatbázishoz hozzáféréssel kell rendelkeznie. Rugalmas lekérdezés használja egyetlen *fő* adatbázis, amelyben definiált külső táblák tükröző táblák vagy nézetek az elosztott (bérlői) adatbázisok. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. Rugalmas lekérdezés a katalógus-adatbázisban a szegmenstérkép használatával az összes bérlői adatbázisok helyének meghatározásához. Telepítés és a lekérdezés magától értetődnek szabványok használatával [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), és támogatja az eszközök, mint például a Power BI és az Excel ad hoc lekérdezését.

Lekérdezések a bérlői adatbázisok közötti elosztásával, a rugalmas lekérdezés éles adatok azonnali betekintést nyújt. Azonban mivel rugalmas lekérdezés lekéri a potenciálisan sok adatbázis adatait, lekérdezések késése néha lehet magasabb, mint a megfelelő, egy egyetlen több bérlős adatbázisban küldött lekérdezések. Győződjön meg arról, a Tervező lekérdezések a visszaadott adat. Rugalmas lekérdezés gyakran leginkább megfelelő, a lekérdezés, ellentétben a gyakran használt épület vagy összetett elemzési lekérdezések vagy jelentések valós idejű adatokat kis mennyiségű. Ha a lekérdezések nem is kell végrehajtani, tekintse meg a [végrehajtási terv](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) milyen a lekérdezés részeként már leküldte a rendszer a távoli adatbázis megtekintéséhez. És felmérheti a visszaadott adatok mennyiségét. Lehet, hogy jobb összetett analitikus feldolgozást igénylő lekérdezések kiszolgálása az internetszolgáltatójuk által a kinyert bérlő adatainak mentése az adatbázisba, amely elemzési lekérdezések van optimalizálva. SQL Database és az SQL Data Warehouse sikerült futtatni az analitikai adatbázis.

Ez a minta Analytics ismertetése a [bérlői elemzések – oktatóanyag](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip Tickets SaaS több-bérlős adatbázis forráskódjához és parancsfájlok

A Wingtip Tickets SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-adattárban. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása.

## <a name="create-ticket-sales-data"></a>Hozzon létre jegyet értékesítési adatok

Lekérdezések futtatásához egy érdekesebb adatkészlet, a jegy-generátor futtatásával hozzon létre jegyet értékesítési adatokat.

1. Az a *PowerShell ISE-ben*, nyissa meg a... \\Tanulási modulok\\működési elemzések\\ad hoc ad hoc jelentéskészítés\\*Demo-AdhocReporting.ps1* szkriptet, és állítsa be a következő értékeket:
   * **$DemoScenario** = 1, **jegyek beszerzése minden helyszínen**.
2. Nyomja meg **F5** futtassa a szkriptet, és jegyeladásokkal létrehozásához. A parancsfájl futása közben továbbra is ebben az oktatóanyagban a lépéseket. A jegy adatainak lekérdezése abban a *az ad hoc az elosztott lekérdezések futtatása* szakaszban, ezért Várjon, amíg a jegy generátor végrehajtásához.

## <a name="explore-the-tenant-tables"></a>Ismerje meg a tenant táblákban 

A Wingtip Tickets SaaS több-bérlős adatbázis alkalmazásban bérlők egy hibrid bérlő felügyeleti modell - bérlői adatok esetén akár egy több-bérlős adatbázis vagy egy egybérlős adatbázis tárolja, és áthelyezhetők a kettő között vannak tárolva. Ha a lekérdezés az összes bérlői adatbázison, fontos, hogy rugalmas lekérdezés az adatok is kezelheti, mint ha a bérlő által horizontálisan skálázott egyetlen logikai adatbázis része. 

Ez a minta elérése érdekében minden tenant táblákban közé tartozik egy *VenueId* oszlop, amely azonosítja a bérlői adatok tartozik. A *VenueId* mint a helyszín neve, de bármely megközelítés kivonatát vezessen be egy egyedi értéket az oszlop használható. Ez a megközelítés akkor jön létre a bérlőkulcsot a katalógus használata hasonló. Tartalmazó táblák *VenueId* Elastic Query lekérdezések párhuzamosíthatja, és küldje le őket a megfelelő távoli bérlői adatbázis le használnak. Ez jelentősen csökkenti a adja vissza, és a teljesítmény növekedését eredményezi, különösen, ha több bérlő számára, amelynek az adatait egyetlen új bérlő-adatbázisokban tárolt adatok mennyisége.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Az ad-hoc elosztott lekérdezésekhez használt adatbázis üzembe helyezése

Ebben a gyakorlatban üzembe helyezi a *adhocreporting* adatbázis. Ez az összes bérlői adatbázison lekérdezések küldésére használatos sémát tartalmazó fő adatbázisban. A meglévő kiszolgáló, a használt mintaalkalmazás felügyelettel kapcsolatos összes adatbázis-kiszolgáló telepítve van az adatbázis.

1. Nyissa meg... \\Tanulási modulok\\működési elemzések\\ad hoc ad hoc jelentéskészítés\\*Demo-AdhocReporting.ps1* a a *PowerShell ISE-ben* és állítsa be a a következő értékeket:
   * **$DemoScenario** = 2, **központi telepítése az Ad hoc elemzési adatbázis**.

2. Nyomja meg **F5** futtassa a szkriptet, és hozzon létre a *adhocreporting* adatbázis.

A következő szakaszban séma hozzáadása az adatbázishoz, elosztott lekérdezések futtatására használható.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Elosztott lekérdezések futtatására a "fő" adatbázis konfigurálása

Ebben a gyakorlatban séma (a külső adatforrás és a külső tábla definíciókat) ad hozzá az ad-hoc jelentési adatbázis, amely lehetővé teszi az összes bérlői adatbázison lekérdezése.

1. Nyissa meg az SQL Server Management Studiót, és a jelentéskészítési Adhoc-adatbázishoz, az előző lépésben létrehozott. Az adatbázis neve nem *adhocreporting*.
2. Nyissa meg a ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* az ssms-ben.
3. Tekintse át az SQL-szkriptet, és vegye figyelembe a következőket:

   Rugalmas lekérdezés adatbázishoz kötődő hitelesítő adatokat használja a bérlői adatbázisok mindegyike eléréséhez. Ez a hitelesítő adat elérhető lesz az összes adatbázist kell, és kell általában biztosítani a minimális jogosultságok szükségesek ahhoz, ezek eseti lekérdezéseket.

    ![hitelesítő adat létrehozása](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   A katalógus-adatbázis segítségével a külső adatforrás, lekérdezések oszlanak meg a lekérdezés futtatásakor a katalógusban regisztrált összes adatbázishoz. Kiszolgálónév nem egyezik az egyes központi telepítések, mert ez a szkript inicializálása a katalógus-adatbázis helye lekérdezi az aktuális kiszolgáló lekérésével (@@servername), a szkript végrehajtása.

    ![külső adatforrás létrehozása](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   A tenant táblákban hivatkozó külső táblák van definiálva **TERJESZTÉSI = SHARDED(VenueId)**. Ez a lekérdezés egy adott irányítja *VenueId* a megfelelő adatbázishoz és javítható a teljesítmény számos, a következő szakaszban látható módon.

    ![külső táblák létrehozása](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A helyi tábla *VenueTypes* , amelyet létrehozott és kitölti a rendszer. A referenciatábla adatok szokás az összes bérlői adatbázisban, így jelölhető ide helyi táblázatként, ki lesz töltve a common data. Bizonyos lekérdezések esetén ez csökkentheti a bérlői adatbázisok között áthelyezett adatok mennyisége és a *adhocreporting* adatbázis.

    ![Tábla létrehozása](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Ha referencia táblák adja meg ezen a módon kikapcsolja, mindenképpen frissítse a táblasémát és adatokat a bérlői adatbázisok frissítésekor.

4. Nyomja meg **F5** futtassa a szkriptet, és inicializálása a *adhocreporting* adatbázis. 

Most már az elosztott lekérdezések futtatását, valamint az összes bérlőre kiterjedő gyűjtsön információt!

## <a name="run-ad-hoc-distributed-queries"></a>Ad-hoc elosztott lekérdezések futtatása

Most, hogy a *adhocreporting* adatbázis állított be, lépjen tovább, és egyes elosztott lekérdezések futtatása. Például, ha a lekérdezés feldolgozása történik szeretné jobban megismerni a végrehajtási terv. 

A végrehajtási terv vizsgálatával, amikor a kurzort a csomag ikonokat részleteiről. 

1. A *SSMS*, nyissa meg... \\Tanulási modulok\\működési elemzések\\ad hoc ad hoc jelentéskészítés\\*Demo-AdhocReportingQueries.sql*.
2. Győződjön meg arról, hogy csatlakozik az **adhocreporting** adatbázis.
3. Válassza ki a **lekérdezés** menüben, majd kattintson **tényleges végrehajtási terv belefoglalása**
4. Jelölje ki a *mely helyszínek jelenleg regisztrált?* lekérdezést, majd nyomja meg **F5**.

   A lekérdezés visszaadja a teljes helyszín listában ábrázoló milyen gyorsan és könnyen az összes bérlőre kiterjedő lekérdezéséhez és az egyes bérlők adatokat adja vissza.

   A terv vizsgálata, és figyelje meg, hogy a teljes költség a távoli lekérdezés, mert folyamatban van egyszerűen fog minden bérlői adatbázis és a helyszín információkat kiválasztásával.

   ![Válassza ki * a dbo. Helyszínek](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Válassza ki a következő lekérdezést, majd nyomja meg **F5**.

   Ez a lekérdezés csatlakozik a bérlői adatbázisok és a helyi adatait *VenueTypes* tábla (helyi, mert van egy táblát a *adhocreporting* adatbázis).

   Nézze meg a terv, és figyelje meg, hogy a költség többsége a távoli lekérdezés mivel tudjuk lekérdezni a minden bérlő helyszín info (dbo. Helyszínek), majd hajtsa végre a gyors helyi csatlakozzon a helyi *VenueTypes* tábla rövid nevének megjelenítéséhez.

   ![Csatlakozás a helyi és távoli adatok](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Most válassza ki a *mely napon lettek a legtöbb jegyek értékesített?* lekérdezést, majd nyomja meg **F5**.

   Ez a lekérdezés hajtja végre egy kicsit összetettebb csatlakoztatása és összesítésének megadására szolgál. Fontos megjegyezni, hogy a feldolgozás a legtöbb távolról történik, de ismét, hogy térnek vissza csak azokat a sorokat kell, minden egyes helyszín összesített jegy értékesítés száma naponta csak egy egyetlen sor visszaadása.

   ![lekérdezés](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Egy ad-hoc jelentési adatbázis üzembe helyezése, és adja hozzá a séma, elosztott lekérdezések futtatását.

Most a [bérlői elemzések – oktatóanyag](saas-multitenantdb-tenant-analytics.md) összetettebb analytics feldolgozási külön analitikai adatbázisba való kinyerésekor adatok feltárására.

## <a name="additional-resources"></a>További források

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
