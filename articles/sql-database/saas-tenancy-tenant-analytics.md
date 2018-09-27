---
title: Kinyert adatok segítségével több-bérlős elemzések futtatására |} A Microsoft Docs
description: Egy egybérlős alkalmazást több Azure SQL Database adatbázis kinyert adatok segítségével a több-bérlős elemzési lekérdezések.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: anjangsh,billgib,genemi
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: bd766dfb712921a57dd23c4fdecc25dd623eb833
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393264"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>A kinyert adatoknak - egybérlős alkalmazást több-bérlős analytics használatával
 
Ebben az oktatóanyagban vezeti végig egy teljes körű elemzési forgatókönyve egy egybérlős implementatoin is. A forgatókönyv azt mutatja be, hogyan engedélyezheti a analytics a cégek számára az intelligens döntések. Minden bérlői adatbázis kinyert adatok segítségével, használhatja analytics betekintést bérlő viselkedése, beleértve azok a Wingtip Tickets SaaS-mintaalkalmazás használatát. Ebben a forgatókönyvben három lépésből áll: 

1.  **Csomagolja ki** minden bérlői adatbázis adatait és **terhelés** -analytics store-bA.
2.  **A kibontott adatok átalakítása** analytics feldolgozás céljából.
3.  Használat **üzleti intelligencia** rajzolja meg a döntéshozatalhoz is útmutató hasznos insights eszközök. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozza létre a bérlőt, analytics, az adatokat tárol.
> - Rugalmas feladatok használatával az adatok kinyerése az egyes bérlői adatbázisok analytics tárolójába.
> - Optimalizálhatja a kinyert adatok (reorganize csillagséma be).
> - A lekérdezés az analitikai adatbázis.
> - Használja a Power BI-adatvizualizáció javaslat javításai, és jelölje ki a bérlő adatainak trendeket.

![architectureOverView](media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>A kapcsolat nélküli bérlői analitikai mintázat

Több-bérlős SaaS-alkalmazásokat a felhőben tárolt bérlői adatok hatalmas mennyiségű általában rendelkeznek. Ezen adatok révén egy gazdag insights a művelet forrás- és az alkalmazás használatának és a bérlők viselkedését. Ezen elemzési funkcióinak fejlesztését, használhatóságuk javítását és más befektetéseket az alkalmazás és a platform képes útmutató.

Összes bérlőre vonatkozó adatok elérése akkor egyszerű, ha minden adat egyetlen több bérlős adatbázisban. A hozzáférés azonban összetettebb, ha méretezve potenciálisan ezer adatbázis között elosztva. Eláraszt az összetettséget és a tranzakciós adatok elemzési lekérdezések hatásainak minimalizálása érdekében egyike adatokat nyerhet ki az célra készült elemzési adatbázis vagy a data warehouse-bA.

Ez az oktatóanyag a Wingtip Tickets SaaS-alkalmazás egy teljes körű elemzési forgatókönyvet mutat be. Először *Elastic Jobs* adatok kinyerése az egyes bérlői adatbázisok, és töltse be az előkészítési táblák analytics tárolóban. Az analytics-tároló vagy egy SQL Database vagy az SQL Data Warehouse lehet. A nagy méretű adatok kinyerése [Azure Data Factory](../data-factory/introduction.md) ajánlott.

Ezután az összesített adatok egy készletét átalakításának [csillagséma-](https://www.wikipedia.org/wiki/Star_schema) táblákat. A táblák egy központi ténytáblát és a kapcsolódó dimenziótábla állnak.  A Wingtip Tickets:

- A központi ténytáblájából a csillagséma-jegy adatokat tartalmaz.
- A dimenziótáblák helyszínek, események, a vásárlók ismertetik, és a dátumok vásárolhat.

A központi tény- és dimenziótáblákból együtt hatékony elemzési feldolgozásának engedélyezése. A jelen oktatóanyagban használt csillagséma az alábbi képen látható:
 
![architectureOverView](media/saas-tenancy-tenant-analytics/StarSchema.png)

Végül az analytics tároló lekérdezett használatával **Power bi** bérlő viselkedése és használata során a Wingtip Tickets alkalmazás kiemeléséhez. Lekérdezéseket futtat, amely:
 
- Minden egyes helyszín relatív népszerűsége megjelenítése
- Jelölje ki a különböző események esetén jegyeladásokkal minták
- Különböző helyszínek relatív sikeres értékesítése meg az esemény megjelenítése

Mindegyik bérlő hogyan használja a szolgáltatás ismertetése szolgál ismertségre a szolgáltatást, és javítják a szolgáltatás segítségével sikeresebb bérlők lehetőségek közül válogathat. Ebben az oktatóanyagban alapvető példákat talál, amelyek a bérlői adatok alapján képes adatokat is.

## <a name="setup"></a>Beállítás

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás van telepítve. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezése és megismerése a Wingtip SaaS-alkalmazás](saas-dbpertenant-get-started-deploy.md)
- A Wingtip Tickets SaaS adatbázis Per bérlői parancsfájlok és az alkalmazás [forráskódját](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) letöltődnek a Githubról. Lásd: Töltse le az utasításokat. Ügyeljen arra, hogy *feloldása a zip-fájl* tartalmának beolvasása előtt. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása.
- A Power BI Desktop telepítve van. [A Power BI Desktop letöltése](https://powerbi.microsoft.com/downloads/)
- További bérlők kötegét kiépítését, tekintse meg a [ **kiépítése katalogizálása oktatóanyag**](saas-dbpertenant-provision-and-catalog.md).
- Egy feladat fiókot és a feladatfiók-adatbázis létrejött. Tekintse meg a megfelelő lépéseket a [ **séma oktatóanyaggal**](saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>A bemutató adatok létrehozása

Ebben az oktatóanyagban az elemzés a jegy értékesítési adatok történik. Az aktuális lépésben a bérlőknek hozhat létre jegyet adatokat.  Később az adatok kinyerésének elemzés céljából. *Győződjön meg arról, ellátta bérlők kötegét a korábban ismertetett módon, hogy rendelkezik egy jelentéssel bíró adatmennyiség*. Egy eléggé nagy mennyiségű adatot számos különböző jegyvásárlási tehetők közzé.

1. A PowerShell ISE-ben nyissa meg a *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*, és állítsa be a következő értéket:
    - **$DemoScenario** = **1** jegyek beszerzése minden helyszínen
2. Nyomja meg **F5** futtassa a szkriptet, és hozzon létre a jegyvásárlások naplóját a minden egyes helyszín minden eseményhez.  A parancsfájl futtatása tickets tízezer létrehozása néhány percig.

### <a name="deploy-the-analytics-store"></a>Az analytics-tároló üzembe helyezése
Vannak gyakran számos olyan tranzakciós adatbázisokhoz, amelyek együtt a bérlői adatok tárolásához. A sok tranzakciós adatbázisok egy elemzési tárolójába kell összesíteni a bérlő adatai. Az összesítés lehetővé teszi az adatok hatékony lekérdezések. Ebben az oktatóanyagban egy Azure SQL Database-adatbázis segítségével tárolja az összesített adatokat.

A következő lépésekben az analytics tárolóba, amelyet üzembe **tenantanalytics**. Is üzembe helyezheti az előre meghatározott-táblázatot, amely fel van töltve az oktatóanyag későbbi részében:
1. A PowerShell ISE-ben nyissa meg a *...\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. $DemoScenario változó állítsa be a parancsfájlt a választott analytics tároló megfelelően:
    - SQL database használata nélkül oszloptár, állítsa be **$DemoScenario** = **2**
    - Az oszlop store az SQL database használatához állítsa **$DemoScenario** = **3**  
3. Nyomja meg **F5** a bemutató-parancsfájl futtatása (amely meghívja a *üzembe helyezés – TenantAnalytics<XX>.ps1* parancsfájl) a bérlői analitikai tárolóban hoz létre, amely. 

Most, hogy az alkalmazás üzembe helyezése és érdekes bérlői adatok ki vannak töltve, használjon [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) való csatlakozáshoz **tenants1-dpt -&lt;felhasználói&gt;**  és **katalógus-dpt -&lt;felhasználói&gt;**  bejelentkezési kiszolgálók = *fejlesztői*, jelszó = *P@ssword1*. Tekintse meg a [bevezető oktatóanyag](saas-dbpertenant-wingtip-app-overview.md) további útmutatást.

![architectureOverView](media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

Az Object Explorerben hajtsa végre az alábbi lépéseket:

1. Bontsa ki a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló.
2. Bontsa ki az adatbázisok csomópontot, és a bérlői adatbázisok listájának megtekintéséhez.
3. Bontsa ki a *katalógus-dpt -&lt;felhasználói&gt;*  kiszolgáló.
4. Győződjön meg arról, hogy az analytics-tároló és a feladatfiók nevű adatbázist.

Tekintse meg a következő adatbázis-elemek az SSMS Object Explorer az analytics store csomópont kibontásával:

- Táblák **TicketsRawData** és **EventsRawData** a bérlői adatbázisok származó nyers kinyert adatok tárolásához.
- A csillagséma-táblák **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events**, és **dim_Dates** .
- A tárolt eljárás a csillagséma-táblák a nyers adatok táblákból feltöltésére szolgál.

![architectureOverView](media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Adatok kinyerése 

### <a name="create-target-groups"></a>Célcsoportok létrehozása 

A folytatás előtt győződjön meg arról, központilag telepített a feladat-fiók és a feladatfiók adatbázisban. A következő lépések készletben Elastic Jobs adatok kinyerése az egyes bérlői adatbázisok, és az analytics-tárolóban lévő adatok tárolásához használatos. Ezután a második feladat shreds az adatokat, és tárolja a csillagséma-táblákba. Ezek a feladatok két nevezetesen futtatásához két különböző célcsoportokhoz, **TenantGroup** és **AnalyticsGroup**. A kinyerési feladat fut a TenantGroup, amely tartalmazza az összes bérlői adatbázison. A aprítási feladat fut a AnalyticsGroup, amely csak az analytics-tároló tartalmazza. Az alábbi lépéseket követve hozzon létre a célcsoportok:

1. Csatlakozás az ssms-ben, a **jobaccount** katalógus-adatbázis – dpt -&lt;felhasználói&gt;.
2. Nyissa meg az ssms-ben, *...\Learning Modules\Operational Analytics\Tenant Analytics\ TargetGroups.sql* 
3. Módosítsa a @User változó felső részén a parancsfájlt, és cserélje le <User> a felhasználó a Wingtip SaaS-alkalmazás üzembe helyezésekor használt értékkel.
4. Nyomja meg **F5** , amely létrehozza a két célcsoportok szkript futtatásához.

### <a name="extract-raw-data-from-all-tenants"></a>Az összes bérlő nyers adatokat nyerhet ki

Mérőszámainkat módosítások gyakrabban fordulhatnak elő a *jegyet, és az ügyfél* adatokat, mint a *esemény és illetékességének* adatokat. Emiatt érdemes lehet jegyet, és a vásárlói adatok kinyerése, külön-külön és gyakrabban beolvashatja az esemény-és illetékességének. Ebben a szakaszban határozza meg, és két külön feladatok ütemezése:

- Bontsa ki a jegyet, és a vásárlói adatokat.
- Bontsa ki az esemény-és illetékességének.

Minden egyes feladat kinyeri az adatokat, és közzéteszi azt az analytics-tárolóba. Nincs külön feladatot a kinyert adatok shreds az analytics csillagséma-be.

1. Csatlakozás az ssms-ben, a **jobaccount** katalógus-adatbázis – dpt -&lt;felhasználói&gt; kiszolgáló.
2. Nyissa meg az ssms-ben, *...\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.sql*.
3. Módosítsa @User felső részén a parancsfájlt, és cserélje le <User> a Wingtip SaaS-alkalmazás üzembe helyezésekor használt felhasználónévvel 
4. Nyomja le az F5 billentyűt a szkript létrehoz és futtatja a feladatot, amely a jegyeket, és az ügyfelek adatokat gyűjt mindegyik bérlői adatbázis futtatásához. A feladat menti az adatokat az analytics-tárolóba.
5. A lekérdezés a TicketsRawData táblát az adatbázisban tenantanalytics, győződjön meg arról, hogy a táblázat az összes bérlő jegyek adatokkal van feltöltve.

![ticketExtracts](media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Ismételje meg a fenti lépéseket, kivételével ez idő cserélje le **\ExtractTickets.sql** a **\ExtractVenuesEvents.sql** 2. lépés.

A feladat sikeresen elindítva tölti fel az új események és a helyszínek adatokkal az összes bérlő analytics tároló EventsRawData táblájában. 

## <a name="data-reorganization"></a>Adatok átszervezési

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Zúzására szolgálnak kinyert adatok feltöltése a csillagséma-táblák

A következő lépés, hogy zúzására szolgálnak a kinyert nyers adatokat az elemzési lekérdezések optimalizált táblák egy készlete. A csillagséma szolgál. Egy központi ténytábla az egyéni jegy értékesítési rekordokat tartalmazza. Más táblák helyszínek, események és ügyfelek kapcsolódó adatokkal van feltöltve. És dimenziótáblák idő. 

Az oktatóanyag jelen szakaszában határozza meg, majd futtassa egy feladatot, amely egyesíti a csillagséma-táblázatokban lévő adatok a kinyert nyers adatokat. A merge feladat befejezése után a nyers adatok törlődnek, készen áll a következő bérlői adatok feltölthető a táblákat és csomagolja ki a feladat.

1. Csatlakozás az ssms-ben, a **jobaccount** katalógus-adatbázis – dpt -&lt;felhasználói&gt;.
2. Nyissa meg az ssms-ben, *...\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.sql*.
3. Nyomja meg **F5** futtassa a parancsfájlt egy feladatot, amely meghívja a sp_ShredRawExtractedData meghatározásához tárolt eljárást az analytics-tárolóban.
4. Hagyjon elegendő időt a feladat sikeresen lefutott.
    - Ellenőrizze a **életciklus** jobs.jobs_execution táblázat a feladat állapotát. Ellenőrizze, hogy a feladat **sikeres** a folytatás előtt. Sikeres futtatás a következő diagram hasonló adatokat jelenít meg:

![felaprítással](media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Az adatok feltárása

### <a name="visualize-tenant-data"></a>Bérlői adatok megjelenítése

A csillagséma-táblában lévő adatokat biztosít minden a jegy értékesítési adatokat az elemzéshez szükséges. Könnyebben tekintse meg a nagy méretű adatkészleteket a trendeket, meg kell jelenítheti meg grafikusan.  Ebben a szakaszban megismerheti, hogyan használható **Power BI** feldolgozására, és ki kell olvasni, és megszervezni bérlői adatok vizualizálásához.

Csatlakozás a Power bi-ba, illetve importálhatja a korábban létrehozott nézetek, kövesse az alábbi lépéseket:

1. Indítsa el a Power BI desktopban.
2. A kezdőlap menüszalagon válassza **adatok lekérése**, és válassza ki **több...** a menüből.
3. Az a **adatok lekérése** ablakban válassza ki az Azure SQL Database.
4. Az adatbázis bejelentkezési ablakban írja be a kiszolgáló nevét (catalog-dpt -&lt;felhasználói&gt;. database.windows.net). Válassza ki **importálás** a **adatkapcsolati mód**, majd kattintson az OK gombra. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Válassza ki **adatbázis** a bal oldali panelen, majd adjon meg felhasználónevet = *fejlesztői*, és adja meg a jelszó = *P@ssword1*. Kattintson a **Connect** (Csatlakozás) gombra.  

    ![databasesignin](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. Az a **kezelő** az analitikai adatbázis oldali ablaktábla, válassza ki a csillagséma-táblákat: fact_Tickets, dim_Events, dim_Venues, dim_Customers és dim_Dates. Válassza ki **terhelés**. 

Gratulálunk! Az adatok Power BI-bA sikeresen töltött. Most már megkezdheti segítségével betekintést nyerhet a bérlők számára érdekes vizualizációkat. A következő vezeti végig, hogyan analytics engedélyezheti, hogy a Wingtip Tickets üzleti csapatnak adatvezérelt javaslatok. A javaslatok segíthetnek az üzleti modell és a felhasználói élmény optimalizálása.

Első lépésként tekintse meg a szintaxis eltérése a helyszínek között, a jegy értékesítési adatok elemzése. Válassza ki az alábbi beállítások a Power bi-ban minden egyes helyszín által értékesített jegyek száma a sáv diagram megrajzolásához. A jegy generátor véletlenszerű változat, mert az eredmények eltérőek lehetnek.
 
![TotalTicketsByVenues](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

A fenti diagram megerősíti, hogy minden egyes helyszín által értékesített jegyek száma változhat. Helyszínek, amely további jegyek értékesítésére, amelyek kevesebb jegyek értékesítésére helyszínek részletesebben használja a szolgáltatást. Előfordulhat, hogy az erőforrás-elosztás másik bérlőben igényeinek megfelelően testre szabni itt lehetőséget.

Elemezheti az adatait, hogy lássa, hogyan jegyeladásokkal változnak idővel további. Válassza ki az alábbi beállítások a Power BI 60 nappal minden nap értékesített jegyek száma ábrázolásához.
 
![SaleVersusDate](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

A fenti diagram megjeleníti a jegy értékesítési megnövekedett, az egyes helyszínek. Kedvez a cél, hogy egyes helyszínek lehet, hogy lehet felhasználása rendszererőforrások aránytalanul megerősítése. Amennyiben nem áll sem nyilvánvaló minta esetén fordulhat elő, az adatforgalmi csúcsokhoz.

Ezután további vizsgálni kívánt ezek csúcsnapok értékesítés jelentőségét. Amikor hajtsa végre ezeket a csúcsok azt követően merülhet fel jegyek árusítása? Jeleníti meg napi értékesített jegyeket, válassza ki az alábbi beállítások a Power bi-ban.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

A fenti diagram bemutatja, hogy egyes helyszínek értékesítési első napján jegyek rengeteg értékesíteni. Amint a jegyek árusítása, ezek helyszínek, úgy tűnik, hogy mad sürgős. Ez a tevékenység által néhány helyszínek adatlöketek hatással lehet a szolgáltatás más bérlők számára.

Az adatok ismételt használatával ellenőrizheti a mad sürgős teljesül-e az összes esemény ezek helyszínek által üzemeltetett részletesen is. Az előző grafikon figyelhető meg, hogy a Contoso Concert Hall értékesít jegyek rengeteg, és hogy Contoso is ugrásszerű jegyeladásokkal az egyes napokon. A Contoso Concert Hall, az események mindegyike értékesítési trendek összpontosító összegző jegyeladásokkal nyomtatandó Power BI beállítások módosításával. Hajtsa végre az összes esemény hajtsa végre az értékesítési minta?

![ContosoSales](media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

A Contoso Concert Hall a fenti diagram bemutatja, hogy a mad sürgős sem történik az összes esemény. A szűrési lehetőségek értékesítési trendeket a többi helyszínek módosításával.

Minták jegyárusító betekintést az üzleti modell optimalizálása érdekében a Wingtip Tickets vezethet. Helyett az összes bérlőre vonatkozóan egyaránt díjszabási, például a Wingtip be kell vezetnie a szolgáltatási szintek a különböző számítási méretekre. Nagyobb helyszínek, amely naponta több jegyek értékesítésére kell egy magasabb szintű szolgáltatói szerződést (SLA) a magasabb szintű sikerült kínáljuk. Ezek a helyszínek lehet adatbázisaikat, adatbázis-erőforrás magasabb korlátokkal rendelkező készlet helyezi el. Minden szolgáltatási szint sikerült óránkénti értékesítési adatkeret, további díjakat számítanak fel díjat a meghaladja a lefoglalt rendelkezik. Nagyobb helyszínek, amelyek rendszeres adatlöketekkel értékesítési előnyös a magasabb szintű, és a Wingtip Tickets hatékonyabb szolgáltatást is értékesítheti.

Ugyanakkor az egyes Wingtip Tickets ügyfeleink panaszok, hogy azok kihívást jelent, hogy adja meg a szolgáltatás költsége elegendő jegyek értékesítésére. Például ilyen elemzést van lehetőség az alulteljesítő helyszínek jegyeladásokkal növelése érdekében. Magasabb értékesítési lenne növelje mintavételezéskor a szolgáltatás. Kattintson a jobb gombbal a fact_Tickets, és válassza ki **új mérték**. Adja meg az új mérték neve a következő kifejezést **AverageTicketsSold**:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

Válassza ki az alábbi Vizualizáció lehetőségek a relatív sikerük meghatározásához minden egyes helyszín által értékesített százalékos jegyek ábrázolásához.

![AvgTicketsByVenues](media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

A fenti diagram bemutatja, hogy annak ellenére, hogy a legtöbb helyszínek több mint 80 %-a jegyek értékesítésére, néhány elterjedtek már több mint fele a munkaállomások adja meg. Játsszon egy kicsit az értékeket is minden egyes helyszín értékesített jegyek maximális és minimális aránya kiválasztásához.

Korábban, mélyíteni felderíteni, hogy jegyeladásokkal általában a kiszámítható mintázatú hajtsa végre az elemzést. Ez a felderítés előfordulhat, hogy lehetővé teszik a Wingtip Tickets súgó alulteljesítő helyszínek boost jegyeladásokkal dinamikus díjszabás javaslatot. Ez a felderítési lehetőséget a alkalmazni a machine learning-módszerekkel előre jelezni az egyes eseményekhez jegyeladásokkal sikerült felfedéséhez. Előrejelzés is sikerült arról, hogy a bevétel jegyeladásokkal díjára ajánlat gyakorolt hatást. Power BI Embedded sikerült integrálni kell egy event management alkalmazás. Az integráció segíti a jelenítheti meg az előre jelzett értékesítési és a különböző kedvezmények hatását. Az alkalmazás segíti a megtervezi a közvetlenül az analytics megjelenítése a alkalmazni egy optimális kedvezményt.

A WingTip alkalmazás bérlő adatait a trendeket rendelkezik figyelhető meg. Azt is fontolóra egyéb módon az alkalmazás tájékoztatják a SaaS-tesztjéről alkalmazásszállítók számára üzleti döntéseket hozhasson. Szállítók jobban is biztosíthat a bérlők számára szükséges. Remélhetőleg ebben az oktatóanyagban van szerelve, a cégek számára az adatvezérelt döntések megjelenő új bérlő adatainak elemzés végrehajtásához szükséges eszközöket.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Üzembe helyezett előre definiált csillagsémával táblák bérlői analitikai adatbázis létrehozása
> - Adatok kinyerése a bérlői adatbázis használt rugalmas feladatok
> - A kibontott adatok egyesítése analytics tervezett csillagséma lévő táblák
> - Elemzési adatbázis lekérdezése 
> - Figyelje meg a bérlő adatainak trendeket adatvizualizáció a Power BI használatával 

Gratulálunk!

## <a name="additional-resources"></a>További források

- További [oktatóanyagok, amely a Wingtip SaaS-alkalmazás útmutatóra](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Rugalmas feladatok](sql-database-elastic-jobs-overview.md).
- [Több-bérlős analytics kinyert adatok – több-bérlős app használatával](saas-multitenantdb-tenant-analytics.md)