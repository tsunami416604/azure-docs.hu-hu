---
title: Futtassa a kibontott adatok több-bérlős analytics |} Microsoft Docs
description: Több-bérlős elemzési lekérdezések kibontani a több Azure SQL Database adatbázist adatokkal.
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: anjangsh; billgib; genemi
ms.openlocfilehash: 68057a2ae5925aa16288844759a34592aa7c7573
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644960"
---
# <a name="cross-tenant-analytics-using-extracted-data"></a>Több-bérlős analytics kibontott adatok használata

Ebben az oktatóanyagban, végezze el a teljes analytics forgatókönyvhöz. A forgatókönyv bemutatja, hogyan engedélyezheti a analytics a vállalkozások intelligens vonatkozó döntések meghozatalában. Minden bérlő adatbázisból beolvasott adatok felhasználásával, segítségével analytics betekintést nyerhet bérlői viselkedést és alkalmazás-használat. Ebben a forgatókönyvben a három lépést foglal magában: 

1.  **Bontsa ki** minden bérlő adatbázis adatait és **terhelés** az analytics-tárolóba.
2.  **A kibontott adatok átalakítása** analytics feldolgozásra.
3.  Használjon **üzleti intelligencia** kimenő hasznos insights, amelyek révén is ismerteti. megrajzolásához eszközök. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozza létre a bérlőt, elemzés tárolja az adatokat nyerhet ki.
> - Rugalmas feladat segítségével kinyeri az adatokat az egyes bérlői adatbázisok az analytics-tárolóba.
> - A kibontott adatok (csillag sémába átszervez) optimalizálásához.
> - Az elemzési adatbázis lekérdezése.
> - Használja a Power BI az adatok vizuális jelölje ki a bérlői adatforgalom trendeket, és lehetővé teszi a javításai javaslat.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Kapcsolat nélküli bérlői analytics minta

Több-bérlős SaaS-alkalmazásokhoz általában rendelkeznek bérlői adatok a felhőben tárolt hatalmas mennyiségű. Ezeket az adatokat biztosít a művelet észrevételeket gazdag forrását és az alkalmazás használatát, és a bérlők viselkedését. Ezek insights szolgáltatás fejlesztési használhatóság fejlesztések és egyéb befektetések az alkalmazás és a platform is ismerteti.

Az összes bérlőre vonatkozó adatok elérése használata egyszerű, amikor az adatok csak egy több-bérlős adatbázisban. De a hozzáférés összetettebb, amikor adatbázisok több ezer léptékű pontjain. Tame összetettségét, valamint a tranzakciós adatok elemzési lekérdezések gyakorolt hatásának minimalizálása érdekében egy módja az adatok kinyerése a célra készült elemzés adatbázis vagy adatraktár.

Ez az oktatóanyag a Wingtip jegyek SaaS-alkalmazás teljes analytics forgatókönyv mutat. Első, *rugalmas feladatok* adatok kinyerése minden egyes bérlői adatbázisához, és töltse be az átmeneti tárolási táblákból analytics tárolóban. Az analytics-tároló egy SQL-adatbázis vagy egy SQL Data Warehouse sikerült lennie. A nagyméretű adatok kiolvasásához [Azure Data Factory](../data-factory/introduction.md) ajánlott.

Ezt követően az összesített adatok olyan készlete alakul [csillag-séma](https://www.wikipedia.org/wiki/Star_schema) táblákat. A táblák egy központi ténytábla és a kapcsolódó dimenziótáblák áll.  A jegyektől Wingtip:

- A csillag-séma a központi ténytábla jegy adatokat tartalmaz.
- A dimenziótáblák helyszínek, események, az ügyfelek leíró, és dátumokat vásároljon.

A központi tény- és dimenziótáblák táblák együtt hatékony elemzésfeldolgozási engedélyezése. Ebben az oktatóanyagban használt csillag-séma a következő ábrán látható:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Végezetül az analytics tároló le kell kérdezni használatával **Power bi** jelölje ki a bérlő viselkedést és a Wingtip jegyek alkalmazás használatát. Lekérdezéseket futtat, amely:
 
- Az egyes helyszínekkel relatív időben népszerűvé vált megjelenítése
- Jelölje ki a minták jegy értékesítési különböző események esetén
- Különböző helyszínek relatív sikeres eladási ki az esemény megjelenítése

Mindegyik bérlő hogyan használja a szolgáltatás ismertetése felfedezése, mely beállítások monetizing a szolgáltatást, és javítják a szolgáltatás segítségével sikeresebb bérlők szolgál. Ez az oktatóanyag, amely a bérlői adataiból adatokat is insights típusú alapvető példákat tartalmaz.

## <a name="setup"></a>Beállítás

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás központi telepítése. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](saas-dbpertenant-get-started-deploy.md)
- A Wingtip jegyek SaaS adatbázis / bérlői parancsfájlok és az alkalmazás [forráskód](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) letöltődnek a Githubról. Lásd: Töltse le az utasításokat. Ügyeljen arra, hogy *feloldása a zip-fájl* tartalmának kibontása előtt. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.
- A Power BI Desktop telepítve van. [A Power BI Desktop letöltése](https://powerbi.microsoft.com/downloads/)
- A köteg további bérlő van kiépítve, tekintse meg a [ **rendelkezés bérlők oktatóanyag**](saas-dbpertenant-provision-and-catalog.md).
- Egy feladat fiókot és a feladat fiók adatbázis létrejött. Tekintse meg a megfelelő lépéseket a [ **séma felügyeleti oktatóanyag**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>A bemutató-adatok létrehozása

Ebben az oktatóanyagban elemzés jegy értékesítési adatait történik. Az aktuális lépésben a bérlőknek hozhat létre jegy adatokat.  Ezek az adatok később ki kell olvasni, elemzés céljára. *Győződjön meg arról, ellátta a bérlő kötegelt a korábban ismertetett módon, hogy rendelkezik egy jelentéssel bíró adatmennyiség*. Elég nagy mennyiségű adatot is elérhetővé teheti a számos különböző jegy minták megvásárlását.

1. Nyissa meg a PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*, és a következő értéket:
    - **$DemoScenario** = **1** beszerzési jegyek összes helyszínek, események
2. Nyomja le az **F5** futtassa a parancsfájlt, és meg minden egyes helyszínekkel eseményt előzmények megvásárlásáról jegy létrehozását.  A parancsfájl futtatása létrehozni a jegyektől tízezreit néhány percig.

### <a name="deploy-the-analytics-store"></a>Az elemzés tároló telepítése
Gyakran nincsenek számos tranzakciós adatbázisok, amelyek együtt a bérlő adatok tárolásához. A sok tartalmazó tranzakciós adatbázisok áttelepítése egy analytics tárolóba kell összesíteni a bérlői adatforgalom. Az összesítés lehetővé teszi, hogy az adatok hatékony lekérdezések. Az oktatóanyag egy Azure SQL Database adatbázist fogja tárolni az összesített adatokat.

A következő lépésekben az analytics tárolóba, melynek neve telepít **tenantanalytics**. Előre definiált-táblázatot, amely fel van töltve az oktatóanyag későbbi részében is központi telepítése:
1. Nyissa meg a PowerShell ISE *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Állítsa be az $DemoScenario változót a parancsfájl felel meg a választott analytics tároló:
    - SQL-adatbázis nélkül oszlop áruház használatához állítsa **$DemoScenario** = **2**
    - Az oszlop store SQL-adatbázis használatához állítsa **$DemoScenario** = **3**  
3. Nyomja le az **F5** a bemutató-parancsfájl futtatásához (meghív a *telepítés-TenantAnalytics<XX>.ps1* parancsfájl) amely hoz létre a bérlői analytics tároló. 

Most, hogy az alkalmazás telepítve, és feltölti azt a bérlő érdekes adatokkal, használjon [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) csatlakozni **tenants1-dpt -&lt;felhasználói&gt;**  és **katalógus-dpt -&lt;felhasználói&gt;**  bejelentkezési kiszolgálók = *fejlesztői*, jelszó = *P@ssword1*. Tekintse meg a [bevezető oktatóanyag](saas-dbpertenant-wingtip-app-overview.md) további útmutatást.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

Az Object Explorer hajtsa végre a következő lépéseket:

1. Bontsa ki a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló.
2. Bontsa ki az adatbázisok csomópontot, és a bérlői adatbázisok listájának megtekintéséhez.
3. Bontsa ki a *katalógus-dpt -&lt;felhasználói&gt;*  kiszolgáló.
4. Győződjön meg arról, hogy látni az analytics-tároló és a jobaccount adatbázis.

Tekintse meg a következő adatbázis elemek az SSMS Object Explorer az analytics tároló csomópont kibontásával:

- Táblák **TicketsRawData** és **EventsRawData** a tenant adatbázisból származó nyers kinyert adat tárolására.
- A csillag-séma táblák **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, és **dim_Dates** .
- A következő tárolt eljárást a nyers adatok tábla a csillag-séma táblák feltöltésére használt eszköz.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Adatok kinyerése 

### <a name="create-target-groups"></a>Célcsoportok létrehozása 

A folytatás előtt győződjön meg arról, a feladat fiók és jobaccount adatbázis telepítette. A következő lépéseket készletben rugalmas feladatok szolgál az adatok kinyerése érdekében minden egyes bérlő adatbázisból, és az adatok tárolásához analytics tárolójában. A második feladat majd shreds az adatokat, és tárolja a csillag-séma táblákba. Ezek a feladatok két nevezetesen futtatásához két különböző célcsoportokhoz, **TenantGroup** és **AnalyticsGroup**. A kinyerési feladat fut a TenantGroup, amely tartalmazza az összes bérlői adatbázis. A aprítási feladat fut a AnalyticsGroup, amely csak az analytics-tároló tartalmazza. A cél csoportok létrehozása a következő lépések segítségével:

1. A szolgáltatáshoz az SSMS, csatlakozzon a **jobaccount** adatbázis-katalógus-dpt -&lt;felhasználói&gt;.
2. Nyissa meg a szolgáltatáshoz az ssms, *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Módosítsa a @User változó a parancsfájl elején cseréje <User> a Wingtip SaaS-alkalmazás telepítésekor használt felhasználói értékkel.
4. Nyomja le az **F5** , amely a két célcsoportok hoz létre a parancsfájl futtatásához.

### <a name="extract-raw-data-from-all-tenants"></a>Nyers adatok kinyerése egyetlen bérlő számára

Nagy mennyiségű adatok módosítások gyakrabban fordulhatnak elő a *ügyfél és a jegyet* adatainak mint *esemény és helyszínére* adatokat. Ezért fontolja meg, külön-külön és gyakrabban beolvashatja az esemény-és helyszínére, mint a jegyet és a felhasználói adatok kibontása. Ebben a szakaszban határozza meg, és két külön feladatok ütemezése:

- Bontsa ki a jegyet és a felhasználói adatokat.
- Bontsa ki az esemény-és helyszínére.

Minden feladat kinyeri az adatokat, és a analytics tárolóba küldi azt. Nincs külön feladatot az sémába analytics csillag-shreds a kibontott adatok.

1. SSMS, csatlakoznia kell a **jobaccount** katalógusban adatbázis-dpt -&lt;felhasználói&gt; kiszolgáló.
2. Nyissa meg a szolgáltatáshoz az ssms, *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Módosítsa @User a parancsfájlt, és cserélje ki tetején <User> a Wingtip SaaS-alkalmazás telepítésekor használt felhasználónévhez 
4. Nyomja le az F5 billentyűt a parancsfájl által létrehozott, és futtatja a feladatot, amely kinyeri belőlük az egyes bérlői adatbázisok az jegyek és az ügyfelek adatokat. A feladat menti az adatokat az analytics-tárolóba.
5. A lekérdezés a TicketsRawData tábla az adatbázisban tenantanalytics, győződjön meg arról, hogy a tábla az adataival tölti fel jegyek egyetlen bérlő számára.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Ismételje meg az előző lépéseket, kivéve a idő csere **\ExtractTickets.sql** rendelkező **\ExtractVenuesEvents.sql** a 2. lépés.

Az új események és helyszínek adatokkal az összes bérlő analytics tárolójában EventsRawData táblázat sikeresen fut a feladat tölti fel. 

## <a name="data-reorganization"></a>Adatok átszervezési

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Kinyert adat zúzására szolgálnak a csillag-séma táblák feltöltése

A következő lépés, hogy zúzására szolgálnak a kibontott nyers adatokat az elemzési lekérdezések optimalizált táblák egy készlete. A csillag-séma szolgál. Egy központi ténytábla érvényes egyéni jegy értékesítési rögzíti. Más táblák feltöltött helyszínek, eseményeket és az ügyfelek kapcsolódó adatokat. És dimenziótáblák idő. 

Az oktatóanyag ezen részében adja meg, és futtatni egy feladatot, amely egyesíti a kibontott nyers adatokat az adatokat a csillag-séma táblák. A merge feladat befejezése után a nyers adatok törlődnek, készen áll a következő bérlő adatok feltöltését táblák elhagyása bontsa ki a feladatot.

1. A szolgáltatáshoz az SSMS, csatlakozzon a **jobaccount** adatbázis-katalógus-dpt -&lt;felhasználói&gt;.
2. Nyissa meg a szolgáltatáshoz az ssms, *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Nyomja le az **F5** futtassa a parancsfájlt egy feladatot, amely meghívja a sp_ShredRawExtractedData meghatározásához tárolt eljárást az analytics-tárolóban.
4. Hagyjon elegendő időt a feladat végrehajtása sikeresen befejeződött.
    - Ellenőrizze a **életciklus** jobs.jobs_execution táblázat a feladat állapotának. Győződjön meg arról, hogy a feladat **sikeres** a folytatás előtt. A sikeres kísérletek a következő diagram hasonló adatait jeleníti meg:

![felaprítással](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Az adatok feltárása

### <a name="visualize-tenant-data"></a>Bérlői adatok megjelenítése

A csillag-séma tábla összes a jegy értékesítési adatokat biztosít a elemzése szükséges. Könnyebb tekintse meg a nagy méretű adatkészletekhez trendeket, grafikusan megjelenítheti azt kell.  Ebben a szakaszban megismerheti, hogyan használható **Power BI** módosíthatók, és a bérlői adatok kibontott, és megszervezni megjelenítése.

A Power bi-ba, és importálhatja a korábban létrehozott nézeteket, tegye a következőket:

1. Indítsa el a Power BI desktopban.
2. Válassza ki az otthoni szalagon **adatok beolvasása**, és válassza ki **több...** a menüből.
3. Az a **adatok beolvasása** ablakban jelölje be az Azure SQL Database.
4. Az adatbázis-bejelentkezési ablakban adja meg a kiszolgálónév (katalógus-dpt -&lt;felhasználói&gt;. database.windows.net). Válassza ki **importálási** a **adatok csatlakozási mód**, majd kattintson az OK gombra. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Válassza ki **adatbázis** a bal oldali ablaktáblán, majd adjon meg felhasználónevet = *fejlesztői*, és írja be a jelszó = *P@ssword1*. Kattintson a **Connect** (Csatlakozás) gombra.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Az a **Navigator** analytics adatbázis ablaktábla, válassza ki a csillag-séma táblákat: fact_Tickets, dim_Events, dim_Venues, dim_Customers és dim_Dates. Válassza ki **terhelés**. 

Gratulálunk! Az adatok sikeresen töltött Power BI-bA. Most elindíthatja felfedezése érdekes képi megjelenítés érdekében betekintést nyerhet a bérlők számára. Ezután végezze el hogyan analytics engedélyezheti, hogy az adatvezérelt javaslatok Wingtip jegyek üzleti csoporthoz adja meg. A javaslatok segítségével az üzleti modell és a felhasználói élmény optimalizálása érdekében.

Indítsa el elemzésével jegy értékesítési adatait, hogy használatának változása a helyszínek között. Válassza a következő beállításokat a Power BI a sávdiagram az egyes helyszínekkel által jegyek teljes száma ábrázolásához. A jegy generátor véletlenszerű variációjának, mert az eredmények eltérő lehet.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

A fenti ábra megerősíti, hogy a jegyektől által az egyes helyszínekkel száma függ. A jegyektől több értékesítő helyszínek, amely kevesebb a jegyektől eladásra helyszínek-nál több fokozottan használ a szolgáltatás. Előfordulhat, hogy az erőforrás-elosztás különböző bérlői igények szerint testre szabni itt lehetőséget.

További elemezheti pontozásával megállapíthatjuk, hogyan jegy értékesítési időbeli eltérők lehetnek. Válassza a következő beállításokat a Power BI 60 napon minden nap eladott jegyek száma ábrázolásához.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

A fenti diagram megjeleníti a jegy egyes helyszínek értékesítési csúcs. Ezek a teljesítményt a képet, hogy egyes helyszínek előfordulhat, hogy lehet fel rendszererőforrásokat aránytalanul megerősítése. Eddig nincs nincs nyilvánvaló minta esetén fordulhat elő, a teljesítményt.

Ezután további vizsgálni kívánt csúcs pénztári napjainkban jelentőségét. Ha tegye ezeket csúcsait után kerül sor jegyek keresse fel a pénztári? A jegyektől naponta értékesített megrajzolásához, válassza a következő beállításokat a Power bi-ban.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

A fenti ábra azt mutatja, hogy egyes helyszínek jegyek számos eladásra pénztári első napján. Amint jegyek keresse fel a pénztári, ezek helyszínek, úgy tűnik, hogy egy mad sürgős. A tevékenység által néhány helyszínek kapacitásnövelés hatással lehet a szolgáltatás más bérlők számára.

Tovább részletezhető az adatok ismételt használatával ellenőrizheti a mad sürgős teljesül-e az összes esemény ezek helyszínek üzemelteti. Előző ábrák akkor megfigyelhető, hogy a Contoso energiaoptimalizálást egyszerre Hall jegyek számos értékesít, illetve hogy Contoso is van egy csúcs jegy Sales bizonyos napon. A Power BI beállításokkal a Contoso energiaoptimalizálást egyszerre Hall, értékesítési trendeket a eseményeinek minden egyes előtérbe összegző jegy értékesítési megrajzolásához lejátszása. Hajtsa végre az összes esemény ugyanezt a pénztári mintát követik?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

A Contoso energiaoptimalizálást egyszerre Hall előző ábra mutatja, hogy a mad sürgős történik meg az összes esemény. A szűrő beállításokkal pénztári trendekről a licencekkel kapcsolatos egyéb helyszínek megjelenítéséhez lejátszása.

Minták értékesítési jegy betekintést vezethet Wingtip jegyek üzleti modelljüknek optimalizálása érdekében. Helyett egyaránt díjszabási egyetlen bérlő számára, lehet, hogy a Wingtip kell vezetnie más-más teljesítménybeli szintű szolgáltatási szinteket. Nagyobb helyszínek, amely naponta több jegyek el kell egy magasabb szolgáltatásiszint-szerződéssel (SLA) és magasabb szintű használható sikerült választhatják. E helyszínek rendelkezhetnek a hozzájuk tartozó adatbázisok címkészlet, amely nagyobb adatbázis-erőforrás korlátok helyezve. Egyes szolgáltatásszinteken sikerült óránkénti értékesítési kiosztását, rendelkező haladja meg a lemezfoglalási kiszabott további díjakat. Nagyobb helyszínek, amelyek az értékesítési rendszeres felszakadásáig előnyös az magasabb szinteket, és Wingtip jegyek is hatékonyabbá pénzt a szolgáltatás.

Egyes Wingtip jegyek ügyfelek panaszkodnak mert ugyanakkor, hogy kihívást jelent eladásra elég a jegyektől szolgáltatás költségeinek igazolására. Lehet, hogy az ezen insights van a helyszínek underperforming jegy értékesítés növelése lehetőséget. Magasabb értékesítési volna növelje észlelt a szolgáltatást. Fact_Tickets kattintson jobb gombbal, majd válassza ki **új mérték**. Adja meg az alábbi kifejezés a következő új néven mértékhez **AverageTicketsSold**:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

Válassza ki a következő képi megjelenítések beállításai az egyes helyszínekkel azok relatív sikerességének meghatározásához által százalékos jegyek megrajzolásához.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

A fenti ábra bemutatja, hogy annak ellenére, hogy a legtöbb helyszínek több mint 80 %-a jegyek eladásra, néhány vannak tud lépést töltse ki a több mint fele a munkaállomásokat. Az értékek is jelölje be a jegyektől egyes helyszínekkel eladott maximális és minimális aránya az lejátszása.

Korábban meg felderíteni, hogy a jegy értékesítési általában a kiszámítható minták kövesse az elemzési mélyíteni. Ez a felderítés helyszínek program jegy értékesítési underperforming dinamikus árképzési ajánlásával Wingtip jegyek súgó előfordulhat, hogy legyen. Ez a felderítési azt mutatja, tervezni a machine learning technikák előre jelezni az egyes eseményekhez jegy értékesítési lehetőséget. Előrejelzés is sikerült megállapítani az ajánlat kedvezményeket jegy értékesítési bevétel gyakorolt hatás. A Power BI Embedded sikerült integrálni kell egy esemény felügyeleti alkalmazás. Az integráció segíthet az előre jelzett értékesítés és a különböző kedvezményeket hatásának megjelenítéséhez. Az alkalmazás segíthet az megtervezi a egy közvetlenül által analytics megjelenített alkalmazni kívánt optimális kedvezményeket.

A WingTip alkalmazás bérlői adatait trendeket megfigyelhető volt. Az alkalmazás megfelelően tájékoztatják a Szolgáltatottszoftver-alkalmazásszállítók számára az üzleti döntések más módokon is fontolóra. A bérlők igényeinek jobban elemeket, a szállítók is. Remélhetőleg ebben az oktatóanyagban van felszerelve, bérlői adatokon építve a vállalatok számára az adatvezérelt döntések elemzés végrehajtásához szükséges eszközök.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Előre definiált csillagséma táblák bérlői analytics adatbázis telepítése
> - Rugalmas feladat használja az adatok kinyerése a tenant adatbázisból
> - A kibontott adatok egyesítése tábla a csillag-séma készült elemzés
> - Az elemzési adatbázis lekérdezése 
> - Figyelje meg a bérlői adatok a trendek az adatok vizuális Power BI segítségével 

Gratulálunk!

## <a name="additional-resources"></a>További források

- További [oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Rugalmas feladat](sql-database-elastic-jobs-overview.md).
