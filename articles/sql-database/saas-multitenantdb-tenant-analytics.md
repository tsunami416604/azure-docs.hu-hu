---
title: Elemzési lekérdezések futtatása
description: Több-bérlős alkalmazásban több Azure SQL Database-adatbázisból kinyert adatok használatával több Azure SQL Database-adatbázisból kinyert adatok használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
ms.date: 09/19/2018
ms.openlocfilehash: 067afd09f942b8062825553a3cf90f715e8d3938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822149"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Több-bérlős elemzés kinyert adatok használatával – több-bérlős alkalmazás
 
Ebben az oktatóanyagban egy több-bérlős megvalósítás teljes elemzési forgatókönyvét vezeti be. A forgatókönyv bemutatja, hogy az elemzés hogyan teheti lehetővé a vállalkozások számára az intelligens döntések meghozatalát. A szilánkos adatbázisból kinyert adatok használatával elemzéssegítségével betekintést nyerhet a bérlői viselkedésbe, beleértve a wingtip jegyek SaaS-mintaalkalmazás használatát. Ez a forgatókönyv három lépésből áll: 

1.  **Adatok kinyerése** az egyes bérlői adatbázisokból egy elemzési tárolóba.
2.  **Optimalizálja a kinyert adatokat** elemzési feldolgozásra.
3.  **Az Üzletiintelligencia-eszközök** segítségével hasznos elemzéseket készíthet, amelyek irányíthatják a döntéshozatalt. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozza létre a bérlői elemzési tárolót az adatok kibontásához.
> - Rugalmas feladatok használatával az egyes bérlői adatbázisokból adatokat nyerhet ki az elemzési tárolóba.
> - Optimalizálja a kinyert adatokat (átszervezi egy csillag-séma).
> - Az elemzési adatbázis lekérdezése.
> - A Power BI adatmegjelenítéshez használja a bérlői adatok trendjeinek kiemelését és a fejlesztésekre vonatkozó javaslatok at.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Offline bérlői elemzési minta

A fejlesztés alatt álló SaaS-alkalmazások hatalmas mennyiségű, a felhőben tárolt bérlői adathoz férhetnek hozzá. Az adatok az alkalmazás működésével és használatával, valamint a bérlők viselkedésével kapcsolatos részletes információk gazdag forrását biztosítják. Ezek az elemzések irányíthatják a funkciók fejlesztését, a használhatóság javítását és az alkalmazásba és a platformba történő egyéb beruházásokat.

Az adatok elérése az összes bérlő egyszerű, ha az összes adat csak egy több-bérlős adatbázisban található. De a hozzáférés bonyolultabb, ha több ezer adatbázis között osztott. Az összetettség megszelídítésének egyik módja az adatok kinyerése egy elemzési adatbázisba vagy egy adattárházba. Ezután lekérdezi az adatraktárt, hogy az összes bérlő jegyadataiból információkat gyűjtsön.

Ez az oktatóanyag egy teljes elemzési forgatókönyvet mutat be ehhez a minta SaaS-alkalmazáshoz. Először is, rugalmas feladatok az egyes bérlői adatbázisokból származó adatok kinyerésének ütemezésére szolgálnak. Az adatokat a rendszer egy elemzési tárba küldi. Az elemzési tároló lehet SQL-adatbázis vagy SQL Data Warehouse. A nagyméretű adatok kinyerése, [az Azure Data Factory](../data-factory/introduction.md) dicséretes.

Ezután az összesített adatok at akaszd egy sor [csillag-séma](https://www.wikipedia.org/wiki/Star_schema) táblák. A táblák egy központi ténytáblából és a kapcsolódó dimenziótáblákból állnak:

- A csillagséma központi ténytáblája jegyadatokat tartalmaz.
- A dimenziótáblák a helyszínekre, eseményekre, vevőkre és vásárlási dátumokra vonatkozó adatokat tartalmaznak.

A központi és a dimenziótáblák együttesen hatékony analitikai feldolgozást tesznek lehetővé. Az oktatóanyagban használt csillagséma az alábbi képen látható:
 
![Starschema](media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Végül a rendszer lekérdezi a csillagséma-táblákat. A lekérdezés eredményei vizuálisan jelennek meg, hogy kiemelje a bérlői viselkedéssel és az alkalmazás használatával kapcsolatos elemzéseket. Ezzel a csillagsémával olyan lekérdezéseket futtathat, amelyek segítenek az alábbi elemek felderítésén:

- Ki vásárol jegyeket és melyik helyszínről.
- Rejtett minták és trendek a következő területeken:
    - A jegyek eladása.
    - A relatív népszerűsége minden helyszínen.

Annak megértése, hogy az egyes bérlők konzisztensen használják a szolgáltatást, lehetőséget ad arra, hogy az igényeiknek megfelelő szolgáltatási csomagokat hozzon létre. Ez az oktatóanyag alapvető példákat nyújt a bérlői adatokból összenyerhető elemzési adatokra.

## <a name="setup"></a>Telepítés

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése és feltárása című témakört.](saas-multitenantdb-get-started-deploy.md)
- A Wingtip SaaS-parancsfájlok és az alkalmazás [forráskódja](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) a GitHubról töltődik le. Ügyeljen arra, hogy *feloldja a zip fájl blokkolását,* mielőtt kibontana annak tartalmát. Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip Jegyek SaaS-parancsfájlok letöltéséhez és feloldásához szükséges lépésekhez.
- A Power BI Desktop telepítve van. [A Power BI Desktop letöltése](https://powerbi.microsoft.com/downloads/)
- A további bérlők kötege ki van építve, lásd: A [**bérlők kiépítése oktatóanyag.**](saas-multitenantdb-provision-and-catalog.md)
- Létrehozott egy feladatügynök és egy feladatügynök-adatbázist. Tekintse meg a megfelelő lépéseket a [**Sémakezelés oktatóanyagában.**](saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent)

### <a name="create-data-for-the-demo"></a>Adatok létrehozása a bemutatóhoz

Ebben az oktatóanyagban a jegyértékesítési adatok elemzése történik. Az aktuális lépésben az összes bérlő jegyadatait hozza létre.  Később ezeket az adatokat elemzésre kinyerik. *Győződjön meg arról, hogy kiépítette a bérlők kötegét a korábban leírtak szerint, hogy jelentős mennyiségű adatkal rendelkezzen.* A kellően nagy mennyiségű adat számos különböző jegyvásárlási mintát tehet ki.

1. A **PowerShell ISE-ben**nyissa meg *a ...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1 webhelyet,* és állítsa be a következő értéket:
    - **$DemoScenario** = **1** Jegyek minden helyszínen történő megvásárlása
2. Nyomja meg **az F5** billentyűt a szkript futtatásához, és hozzon létre jegyvásárlási előzményeket minden eseményhez minden helyszínen.  A parancsfájl több percig fut, hogy több tízezer jegyet hozzon létre.

### <a name="deploy-the-analytics-store"></a>Az elemzési tároló üzembe helyezése
Gyakran számos tranzakciós szilánkos adatbázisok, amelyek együttesen az összes bérlői adatokat. A bérlői adatokat a szilánkos adatbázisból egyetlen elemzési tárolóba kell összesíteni. Az összesítés lehetővé teszi az adatok hatékony lekérdezését. Ebben az oktatóanyagban egy Azure SQL Database-adatbázis az összesített adatok tárolására szolgál.

A következő lépésekben üzembe helyezi az analytics-tárolót, amelyet **tenantanalytics-nek**neveznek. Az oktatóanyag későbbi részében feltöltött előre definiált táblákat is üzembe helyez:
1. A PowerShell ISE-ben nyissa meg *a ...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1 webhelyet.* 
2. Állítsa be a $DemoScenario változót a parancsfájlban, hogy megfeleljen a választott analytics store. Tanulási célokra az oszlopcentrikus nélküli SQL-adatbázis ajánlott.
    - Ha oszlopcentrikus nélkül szeretné használni az SQL adatbázist, állítsa **be $DemoScenario** = **2**
    - Az SQL-adatbázis oszlopcentrikussal való használatához állítsa be **$DemoScenario** = **3**  
3. Nyomja meg az **F5** billentyűt a demóparancsfájl futtatásához (amely meghívja a *Deploy-TenantAnalytics\<XX>.ps1* parancsfájlt), amely létrehozza a bérlői elemzési tárolót. 

Most, hogy telepítette az alkalmazást, és érdekes bérlői adatokkal töltötte meg, az [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) segítségével csatlakoztassa a **tenants1-mt-\<Felhasználói\> ** és **katalógus-mt-\<Felhasználói\> ** kiszolgálókat a Login = *developer*, Password = *P\@ssword1*használatával.

![architectureOverView](media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

Az Objektumkezelőben hajtsa végre az alábbi lépéseket:

1. Bontsa ki a *bérlők1-mt-\<Felhasználói\> * kiszolgáló.
2. Bontsa ki az adatbázisok csomópontját, és tekintse meg a több bérlőt tartalmazó *bérlők1* adatbázist.
3. Bontsa ki a *\<katalógus-mt- Felhasználó\> * kiszolgáló.
4. Ellenőrizze, hogy megjelenik-e az analytics-tároló és a feladatfiók-adatbázis.

Tekintse meg a következő adatbáziselemeket az SSMS Object Explorerben az analytics-tároló csomópont jának kibővítésével:

- A **TicketsRawData** és **az EventsRawData** táblák nyers kinyert adatokat tartanak a bérlői adatbázisokból.
- A csillagsématáblák **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**és **dim_Dates**.
- A **sp_ShredRawExtractedData** tárolt eljárás a csillagséma-táblák feltöltésére szolgál a nyers adattáblákból.

![tenantAnalytics](media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Adatok kinyerése 

### <a name="create-target-groups"></a>Célcsoportok létrehozása 

A folytatás előtt győződjön meg arról, hogy telepítette a feladatfiók és a feladatfiók adatbázis. A következő lépésekben rugalmas feladatok segítségével kinyerheti az adatokat a szilánkos bérlők adatbázisából, és az adatok tárolására az analytics-tárolóban. Ezután a második feladat megsemmisíti az adatokat, és a csillagséma tábláiba tárolja. Ez a két feladat két különböző célcsoporton fut, nevezetesen a **TenantGroup** és **az AnalyticsGroup csoporton.** A kibontási feladat fut a TenantGroup, amely tartalmazza az összes bérlői adatbázisok. Az aprítási feladat fut az AnalyticsGroup, amely csak az analytics-áruház. Hozza létre a célcsoportokat a következő lépésekkel:

1. Az SSMS-ben csatlakozzon a **jobaccount** adatbázishoz a katalógus-mt-\<Felhasználó .\>
2. Az SSMS-ben nyissa meg *a ...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Módosítsa @User a változót a parancsfájl `<User>` tetején, és cserélje ki a Wingtip Jegyek SaaS több-bérlős adatbázis-alkalmazás telepítésekor használt felhasználói értékkel.
4. Nyomja **le az F5 billentyűt** a két célcsoportot létrehozó parancsfájl futtatásához.

### <a name="extract-raw-data-from-all-tenants"></a>Nyers adatok kinyerése az összes bérlőből

A tranzakciók gyakrabban fordulhatnak elő a *jegy- és ügyféladatok,* mint az *esemény- és helyszínadatok* esetében. Ezért fontolja meg a jegy- és ügyféladatok külön-külön és gyakrabban történő kinyerését, mint az esemény- és helyszínadatok kinyerése. Ebben a szakaszban két külön feladatot határoz meg és ütemez:

- Jegyek és ügyféladatok kinyerése.
- Esemény- és helyszínadatok kinyerése.

Minden feladat kinyeri az adatait, és felteszi az analytics-tárolóba. Van egy külön feladat foszlány a kinyert adatokat az analytics star-schema.

1. Az SSMS-ben csatlakozzon a **feladatfiók** adatbázisához a katalógus-mt-\<Felhasználói\> kiszolgálón.
2. Az SSMS-ben nyissa meg *a ...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Módosítsa @User a parancsfájl tetején, és `<User>` cserélje le a wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás telepítésekor használt felhasználónévre. 
4. Nyomja le **az F5** billentyűt a parancsfájl futtatásához, amely létrehozza és futtatja a feladatot, amely kinyeri a jegyeket és az ügyfelek adatait az egyes bérlői adatbázisokból. A feladat menti az adatokat az analytics tárolóba.
5. A TicketsRawData tábla a tenantanalytics adatbázisban, győződjön meg arról, hogy a tábla feltölti a jegyek adatait az összes bérlő.

![jegykivonatok](media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Ismételje meg az előző lépéseket, kivéve, hogy ezúttal cserélje le **a \ExtractTickets.sql** programot **a \ExtractVenuesEvents.sql** 2.

A feladat sikeres futtatása feltölti az EventsRawData táblát az analytics tárolóban az összes bérlő új eseményeivel és helyszínekkel kapcsolatos információkkal. 

## <a name="data-reorganization"></a>Adatok átszervezése

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>A kinyert adatok foszata csillagséma-táblák feltöltéséhez

A következő lépés a kinyert nyers adatok foszlány egy táblakészlet, amely optimalizált elemzési lekérdezések. Csillagséma használatos. A központi ténytábla az egyes jegyértékesítési rekordokat tartalmazza. A dimenziótáblák at a helyszínekre, eseményekre, vevőkre és vásárlási dátumokra vonatkozó adatokkal töltik fel. 

Az oktatóanyag ebben a szakaszában definiáljon és futtasson egy feladatot, amely egyesíti a kinyert nyers adatokat a csillag-sématáblákban lévő adatokkal. Az egyesítési feladat befejezése után a nyers adatok törlődnek, így a táblák készen állnak a következő bérlői adatkinyerési feladat által feltöltésre.

1. Az SSMS-ben csatlakozzon a **jobaccount** adatbázishoz a katalógus-mt-\<Felhasználó .\>
2. Az SSMS fájlban nyissa meg *a ...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql fájlt.*
3. Nyomja le **az F5** billentyűt a parancsfájl futtatásához egy olyan feladat meghatározásához, amely meghívja a sp_ShredRawExtractedData tárolt eljárást az analytics-tárolóban.
4. Hagyjon elég időt a feladat sikeres futtatására.
    - Ellenőrizze a jobs **Életciklus** oszlopában.jobs_execution a feladat állapotát. A folytatás előtt győződjön **meg arról,** hogy a feladat sikeres volt. A sikeres futtatás a következő diagramhoz hasonló adatokat jelenít meg:

![aprításMunka](media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Adatok feltárása

### <a name="visualize-tenant-data"></a>Bérlői adatok megjelenítése

A csillagséma-táblázatban szereplő adatok az elemzéshez szükséges összes jegyértékesítési adatot biztosítják. A nagy adatkészletek trendjeinek könnyebb megtekintéséhez grafikusan kell vizualizálnia.  Ebben a szakaszban megtudhatja, hogyan kezelheti és jelenítheti meg a **bérlői** adatokat a Power BI használatával a kinyert és rendezett bérlői adatok kal.

Az alábbi lépésekkel csatlakozhat a Power BI-hoz, és importálhatja a korábban létrehozott nézeteket:

1. Indítsa el a Power BI desktopot.
2. A Kezdőlap menüszalagon válassza az **Adatok bekerülése**lehetőséget, és válassza az **Egyebek lehetőséget...** a menüből.
3. Az **Adatok bekerülése** ablakban válassza az Azure SQL Database lehetőséget.
4. Az adatbázis bejelentkezési ablakában adja meg a kiszolgáló\<\>nevét (katalógus-mt- User .database.windows.net). Válassza az **Adatkapcsolat izolátus** **mód lehetőséget,** majd kattintson az OK gombra. 

    ![powerBISignIn](media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Válassza **az Adatbázis** elemet a bal oldali ablaktáblában, majd írja be a felhasználónév = *fejlesztő*, és írja be a jelszót = *P\@ssword1*. Kattintson a **Csatlakozás** gombra.  

    ![AdatbázisSignIn](media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. A **Navigátor** ablaktáblában az elemzési adatbázis alatt jelölje ki a csillagsématáblákat: fact_Tickets, dim_Events, dim_Venues, dim_Customers és dim_Dates. Ezután válassza **a Betöltés**lehetőséget. 

Gratulálunk! Sikeresen betöltötte az adatokat a Power BI-ba. Most már elkezdheti felfedezni az érdekes vizualizációkat, hogy betekintést nyerjen a bérlőkbe. Ezután bemutatja, hogy az analitika hogyan teheti lehetővé, hogy adatalapú javaslatokat nyújtson a Wingtip Tickets üzleti csapatának. A javaslatok segíthetnek az üzleti modell és az ügyfélélmény optimalizálásában.

Először elemezheti a jegyértékesítési adatokat, hogy láthassa a felhasználás változásait a helyszínek között. A Power BI következő beállításaival ábrázolja az egyes helyszínek által értékesített jegyek teljes számát tartalmazó sávdiagramot. A jegygenerátor véletlenszerű változása miatt az eredmények eltérőek lehetnek.
 
![Összes TicketsByVenues](media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Az előző minta terület megerősíti, hogy az egyes helyszínek által értékesített jegyek száma változó. A több jegyet értékesítő helyszínek nagyobb mértékben használják a szolgáltatást, mint a kevesebb jegyet értékesítő helyszínek. Itt lehetőség lehet az erőforrás-elosztás taszthatására a különböző bérlői igények nek megfelelően.

Tovább elemezheti az adatokat, hogy lássa, hogyan változnak a jegyeladások az idő múlásával. A Power BI következő beállításaival ábrázolja a naponta 60 napig értékesített jegyek teljes számát.
 
![Eladó versusdátum](media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Az előző diagram egyes helyszínekjegyértékesítési csúcsát jeleníti meg. Ezek a tüskék megerősítik azt az elképzelést, hogy egyes helyszínek aránytalanul nagy mértékben fogyasztják a rendszer erőforrásait. Eddig nincs nyilvánvaló minta, amikor a tüskék fordulnak elő.

Ezután tovább szeretné vizsgálni ezeknek a csúcsértékesítési napoknak a jelentőségét. Mikor történnek ezek a csúcsok a jegyek eladása után? A naponta eladott jegyek nyomtatásához válassza az alábbi lehetőségeket a Power BI-ban.

![SaleDayDistribution](media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Az előző telek azt mutatja, hogy egyes helyszínek eladni egy csomó jegyet az első napon az értékesítés. Amint a jegyek etetik ezeken a helyszíneken, úgy tűnik, hogy őrült rohanás van. Ez a néhány helyszín által leadott tevékenység hatással lehet a szolgáltatás más bérlők számára.

Az adatok at újra beáshatja, hogy lássa, ez az őrült rohanás igaz-e az ezek a helyszínek által üzemeltetett összes eseményre. A korábbi telkek, megfigyelte, hogy a Contoso Concert Hall eladja a jegyeket, és hogy a Contoso is van egy tüske a jegyeladások bizonyos napokon. A Power BI lehetőségeivel a Contoso Concert Hall összesített jegyeladásait tervezhet, és az egyes események értékesítési trendjeire összpontosítva. Minden esemény ugyanazt az értékesítési mintát követi?

![ContosoSales](media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

Az előző telek contoso koncertterem azt mutatja, hogy az őrült rohanás nem történik meg minden esemény. Játssz a szűrőopciókkal, hogy láthasd a más helyszínek értékesítési trendjeit.

A jegyértékesítési mintákba való betekintést vezethet Wingtip jegyek optimalizálni az üzleti modell. Ahelyett, hogy az összes bérlő egyenlő töltése, talán Wingtip kell bevezetnie a különböző számítási méretű szolgáltatási szintek. A nagyobb helyszínek, amelyeknek naponta több jegyet kell eladniuk, magasabb szintű, magasabb szolgáltatásiszint-szerződéssel (SLA) rendelkező magasabb szintű szintet kínálhatnak. Ezek a helyszínek lehet, hogy az adatbázisok at a készlet magasabb adatbázisonkénti erőforráskorlátok. Minden szolgáltatási szint rendelkezhet egy óránkénti értékesítési allokációval, amely további díjakat számíthat fel a felosztás túllépése miatt. A nagyobb helyszínek, amelyek időszakos értékesítési sorozatokkal rendelkeznek, a magasabb szintek előnyeit élveznék, és a Wingtip jegyek hatékonyabban bevételt hozhatnak a szolgáltatásból.

Eközben néhány Wingtip Jegyek ügyfelek panaszkodnak, hogy küzdenek, hogy eladja elég jegyet, hogy igazolja a szolgáltatás költségeit. Talán ezekben a felismerésekben lehetőség van arra, hogy növeljék a jegyeladásokat az alulteljesítő helyszíneken. A magasabb eladások növelnék a szolgáltatás vélt értékét. Kattintson a jobb gombbal fact_Tickets, és válassza az **Új mérték parancsot.** Adja meg a következő kifejezést az **AverageTicketsSold**nevű új mértékhez:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

A következő vizualizációs beállítások kiválasztásával meg szeretné határozni az egyes helyszínek által értékesített jegyek százalékos arányát a relatív sikerük meghatározásához.

![analyticsViews](media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Az előző telek azt mutatja, hogy bár a legtöbb helyszínen eladni több mint 80%-a jegyeket, néhány küzd, hogy töltse ki több mint a fele a helyek. Játssz a Values Well játékkal, hogy kiválasztd az egyes helyszínekre eladott jegyek maximális vagy minimális százalékát.

Korábban elmélyítette az elemzést, hogy felfedezze, hogy a jegyeladások általában kiszámítható mintákat követnek. Ez a felfedezés lehetővé teheti a Wingtip Jegyek számára, hogy dinamikus árképzésajánlásával növeljék a jegyeladásokat alulteljesítő helyszínek. Ez a felfedezés felfedheti a lehetőséget, hogy gépi tanulási technikákat alkalmazzon az egyes események jegyértékesítésének előrejelzésére. Előrejelzéseket lehetne tenni a jegyértékesítésre vonatkozó kedvezmények árbevétele imitálásának a bevételre gyakorolt hatásáról is. A Power BI Embedded integrálható egy eseménykezelő alkalmazásba. Az integráció segíthet az előre jelzett értékesítések és a különböző engedmények hatásának megjelenítésében. Az alkalmazás segíthet kidolgozni egy optimális kedvezményt kell alkalmazni közvetlenül az elemzési kijelzőn.

Megfigyelte a bérlői adatok trendjeit a Wingtip Jegyek SaaS több-bérlős adatbázis-alkalmazásból. Az alkalmazás más módokon is tájékoztathatja az SaaS-alkalmazások szállítóinak üzleti döntéseit. Az eladók jobban tudják kielégíteni bérlőik igényeit. Remélhetőleg ez az oktatóanyag olyan eszközökkel rendelkezik, amelyek szükségesek a bérlői adatok elemzéséhez, hogy a vállalkozások adatvezérelt döntéseket hozhassanak.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Bérlői elemzési adatbázis telepítése előre definiált csillagsématáblákkal
> - Rugalmas feladatok használatával adatokat nyerhet ki az összes bérlői adatbázisból
> - A kinyert adatok egyesítése a jelentésekhez tervezett csillagséma tábláiba
> - Elemzési adatbázis lekérdezése 
> - A Power BI használata adatmegjelenítéshez a bérlői adatok tendenciáinak megfigyeléséhez 

Gratulálunk!

## <a name="additional-resources"></a>További források

További [oktatóanyagok, amelyek a Wingtip SaaS alkalmazásra épülnek.](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) 
- [Rugalmas feladatok](elastic-jobs-overview.md).
- [Több-bérlős elemzés kinyert adatok használatával – egybérlős alkalmazás](saas-tenancy-tenant-analytics.md) 