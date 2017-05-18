---
title: "Teljesítményfigyelés a WTP alkalmazásban (az Azure SQL Database-t használó minta SaaS-alkalmazás) | Microsoft Docs"
description: "Egy Azure SQL Database-t használó minta SaaS-alkalmazás teljesítményének figyelése"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 1698a06dc326b1d215bb635c40724f914ba82442
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="monitor-performance-of-the-wtp-sample-saas-application"></a>Mintául szolgáló WTP SaaS-alkalmazás teljesítményének figyelése

Ez az oktatóanyag az SQL Database és a rugalmas készletek beépített figyelési és riasztási funkcióit mutatja be, majd ismertet néhány főbb, az SaaS-alkalmazásokban használatos teljesítménykezelési forgatókönyvet.

A Wingtip Tickets alkalmazás egy egybérlős adatmodellt használ, amelyben minden egyes helyszín (bérlő) saját adatbázissal rendelkezik. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. Ezen tipikus adatbázis-felhasználási mintha kihasználása érdekében a bérlői adatbázisokat rugalmas adatbáziskészletekben helyezik üzembe. A rugalmas készletek optimalizálják a megoldások költségeit azáltal, hogy számos adatbázis között osztják meg az erőforrásokat. Ennél a típusú mintánál fontos az adatbázis és a készleterőforrások felhasználásának figyelése annak biztosítása érdekében, hogy a terhelések egyenletesen oszoljanak meg a készletek közt. Emellett azt is biztosítani kell, hogy az egyes adatbázisok elengedő mennyiségű erőforrással rendelkezzenek, és hogy a készletek ne érjék el a maximális [eDTU](sql-database-what-is-a-dtu.md)-korlátot. Ez az oktatóanyag különböző módszereket ismertet az adatbázisok és készletek figyelésére és kezelésére, valamint a számítási feladatok változásaira adott korrekciós műveletek elvégzésére.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]

> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére


Az oktatóanyag teljesítéséhez a következő előfeltételeknek kell teljesülnie:

* A WTP alkalmazás üzembe van helyezve. A kevesebb mint öt perc alatt történő üzembe helyezéshez lásd [a WTP SaaS-alkalmazás üzembe helyezésével és megismerésével](sql-database-saas-tutorial.md) foglalkozó cikket.
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Bevezetés az SaaS teljesítménykezelési mintáiba

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll a paraméterek módosításával, hogy az alkalmazás válaszideje elfogadható szinten maradjon. Több bérlő üzemeltetésekor a rugalmas adatbáziskészletek költséghatékony megoldást jelentenek az erőforrások biztosításához és kezeléséhez a kiszámíthatatlan számítási feladatokkal szembesülő adatbáziscsoportok számára. Bizonyos számításifeladat-mintáknál már akár két S3-adatbázist is előnyös lehet készletben kezelni. A készlet nem pusztán az erőforrások költségeit osztja meg, de a különálló adatbázisok folyamatos figyelésének és nyomon követését is szükségtelenné teszi.

![média](./media/sql-database-saas-tutorial-performance-monitoring/app-diagram.png)

A készleteket és a készletekben található adatbázisokat ettől függetlenül továbbra is megfigyelés alatt kell tartani a teljesítményük elfogadható tartományon belül tartása érdekében. Finomhangolja a készlet konfigurációját az összesített számítási feladat igényei szerint. Ezzel biztosíthatja, hogy a készlet eDTU-i megfeleljenek a teljes számítási feladatnak. Állítsa be az adatbázisonkénti minimális és maximális eDTU-értékeket az alkalmazás specifikus igényei szerint.

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* A manuális teljesítményfigyelés szükségessége úgy előzhető meg a leghatékonyabban, ha **riasztásokat állít be olyan esetekre, ha az adatbázisok vagy készletek kilépnek az engedélyezett tartományból**.
* A készletek összesített teljesítményszintjének rövidtávú ingadozásaira válaszul **a készletek eDTU-szintje vertikálisan fel- vagy leskálázható**. Ha az ingadozás rendszeres vagy kiszámítható, akkor **a készlet beállítható úgy, hogy a skálázás automatikusan ütemezve legyen**. Beállítható például a vertikálisan leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* A hosszabb távú ingadozásokra vagy az adatbázisok számának változására válaszul **az egyes adatbázisok áthelyezhetők másik készletekbe**.
* Az *egyes* adatbázisok terhelésének rövid távú növekedéseire válaszul **az egyes adatbázisok kivehetők a készletből, és bizonyos ideig egy egyéni teljesítményszint rendelhető hozzájuk**. A terhelés csökkenésével az adatbázis visszahelyezhető a készletbe. Ha az ilyen esetek előre láthatók, akkor az adatbázisok megelőző jelleggel is áthelyezhetők annak érdekében, hogy mindig rendelkezzenek a szükséges erőforrásokkal, és hogy a terhelésük növekedése ne legyen hatással a készletben található további adatbázisokra. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. Az SQL Database esetén figyelési és riasztási lehetőségeket biztosít az adatbázisokhoz és készletekhez. Ez a beépített figyelés és riasztás erőforrás-specifikus, így kis számú erőforrásnál kényelmes a használata, sok erőforrás esetén azonban nem a legmegfelelőbb megoldás.

Utóbbi esetben érdemes megfontolni a Log Analytics (más néven OMS) használatát. Ez egy különálló Azure-szolgáltatás, amely elemzési lehetőségeket nyújt a Log Analytics-munkaterületen összegyűjtött kibocsátott diagnosztikai naplókhoz és telemetriákhoz. A szolgáltatás több különböző szolgáltatástól képes telemetriákat összegyűjteni, emellett pedig lekérdezésekhez és riasztások beállításához is használható.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazás szkriptjeinek lekérése

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaas](https://github.com/Microsoft/WingtipSaaS) GitHub-adattárban érhető el. A szkriptfájlok a [Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) (Tanulási modulok) mappában találhatók. Töltse le a **Learning Modules** (Tanulási modulok) mappát a helyi számítógépére a mappaszerkezet megtartásával.

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

Noha a készletek használata már két S3-adatbázis esetén is költséghatékony lehet, minél több adatbázis található egy készletben, a költséghatékonyság mértéke annál jobban nő. Annak érdekében, hogy a teljesítményfigyelés és -kezelés nagy léptékben való használatát kellőképpen szemléltetni lehessen, jelen oktatóanyaghoz legalább 20 üzembe helyezett adatbázis szükséges.

Ha egy korábbi oktatóanyagban már kiépített egy bérlőköteget, akkor továbbléphet [Az összes bérlői adatbázis használatának szimulálása](#simulate-usage-on-all-tenant-databases) szakaszhoz.

1. Nyissa meg a \\Learning Modules\\Provision and Catalog\\*Demo-PerformanceMonitoringAndManagement.ps1* fájlt a **PowerShell ISE**-ben. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Válassza a **$DemoScenario** = **1**, **Bérlők kötegelt kiépítése** lehetőséget.
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A szkript kevesebb mint öt perc alatt 17 bérlőt helyez üzembe.

A *New-TenantBatch* szkript egy beágyazott vagy hivatkozott [Resource Manager](../azure-resource-manager/index.md)-sablonkészletet használ a bérlőköteg létrehozásához, és alapértelmezés szerint a katalóguskiszolgálón található **baseTenantDb** adatbázis másolásával hozza létre az új bérlői adatbázisokat, majd regisztrálja ezeket a katalógusban, végül pedig inicializálja őket a bérlő nevével és a helyszíntípussal. Ez egybevág azzal a módszerrel, amellyel a WTP alkalmazás végzi az új bérlők kiépítését. A *baseTenantDB* adatbázis bármely módosítása a későbbiekben kiépített összes bérlőre érvényes lesz. A [Sémakezelés oktatóanyag](sql-database-saas-tutorial-schema-management.md) ismerteti, hogy a *létező* adatbázisokon (köztük az *alapul szolgáló* adatbázison) hogyan eszközölhetők sémamódosítások.

## <a name="simulate-different-usage-patterns-by-generating-different-load-types"></a>Különböző használati minták szimulálása különböző terheléstípusok létrehozásával

A *Demo-PerformanceMonitoringAndManagement.ps1* szkript elindítja a terhelésgenerátort az elérhető *terheléstípusok* valamelyikével:

| Bemutató | Forgatókönyv |
|:--|:--|
| 2 | Normál intenzitású terhelés létrehozása (kb. 40 DTU) |
| 3 | Terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel|
| 4 | Terhelés létrehozása adatbázisonkénti magasabb DTU-löketekkel (kb. 80 DTU)|
| 5 | Normál terhelés létrehozása, valamint magasabb terhelés létrehozása egyetlen bérlő számára (kb. 95 DTU)|
| 6 | Kiegyensúlyozatlan terhelés létrehozása több készlet számára|

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A terhelések szintje (eDTU-ban mérve), időtartama és időköze minden adatbázis esetén más és más, ezzel szimulálva a kiszámíthatatlan bérlői aktivitást.

1. Válassza a **$DemoScenario** = **2**, *Normál intenzitású terhelés létrehozása* lehetőséget.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

A terhelések szórványos jellege miatt hagyja futni a generátort 10–20 percig, hogy a tevékenység elérhessen egy stabil állapotot, és elrendeződjön egy bizonyos mintába.

> [!IMPORTANT]
> A terhelésgenerátor feladatok sorozataként fut a helyi PowerShell-munkamenetben. Hagyja nyitva a *Demo-PerformanceMonitoringAndManagement.ps1* lapot! Ha bezárja a lapot vagy felfüggeszti a gép működését, a terhelésgenerátor leáll.

## <a name="monitor-resource-usage-using-the-portal"></a>Erőforrás-használat figyelése a Portal használatával

A terhelések alkalmazásából eredő erőforrás-használat figyeléséhez nyissa meg a bérlői adatbázisokat tartalmazó készletet a Portalon.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és tallózással keresse meg a tenants1-&lt;FELHASZNÁLÓ&gt; kiszolgálót.
1. Itt meg kell jelennie a bérlői adatbázisok listájának, beleértve az új adatbázisköteget is.
1. Görgessen lefelé, keresse meg a rugalmas készleteket, és kattintson a **Pool1** készletre. Ez a készlet tartalmazza az összes eddig létrehozott bérlői adatbázist.
1. Bontsa ki a készlet megnyíló paneljét, majd tanulmányozza át a készlet és a legfelső adatbázis használati diagramját.

A készlet kihasználtsága lényegében a készletben található összes adatbázis kihasználtságának az összesítése. Az adatbázis-kihasználtsági diagram az 5 legkihasználtabb adatbázist jeleníti meg:

![](./media/sql-database-saas-tutorial-performance-monitoring/pool1.png)

Mivel a készletben az első 5 mellett további adatbázisok is találhatók, a készlet kihasználtsági diagramja az első öt adatbázis diagramjában nem látható tevékenységeket is tartalmaz. További részletekért kattintson az **Adabáziserőforrás-felhasználás** elemre.

![](./media/sql-database-saas-tutorial-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Teljesítményriasztások beállítása a készletben

Állítson be a készletben egy riasztást, amely \>5 percig fennálló 75%-os kihasználtság esetén aktiválódik:

1. Nyissa meg az [Azure Portalon](https://portal.azure.com) a *Pool1* készletet (a *tenants1-\<felhasználó\>* kiszolgálón).
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](media/sql-database-saas-tutorial-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**. 
1. Állítsa be a következő értékeket:
   * **Metrika = eDTU százalékos értéke**
   * **Feltétel = nagyobb, mint**.
   * **Küszöbérték = 75**.
   * **Időtartam = Az utolsó 30 percben**.

   ![riasztás beállítása](media/sql-database-saas-tutorial-performance-monitoring/alert-rule.png)

Az értesítéseket elküldheti az Azure-fiókja e-mail-címére, és igény szerint további címekre is (ezt nem ajánlott beállítani, hacsak nem Ön a használt előfizetés tulajdonosa).

> [!NOTE]
> Mivel a riasztás csak akkor jelez, ha a küszöbérték az utolsó 30 perc során lett túllépve, a terhelésgenerátornak a riasztás teszteléséhez legalább 30 percig futnia kell.


## <a name="scale-up-a-busy-pool"></a>Foglalt készlet vertikális felskálázása

Ha egy készlet összesített terhelési szintje addig növekszik, hogy teljesen lefoglalja a készletet és 100%-os eDTU-használatot ér el, az hatással van az adatbázisok egyéni teljesítményére, és lelassíthatja a lekérdezések válaszidejét a készletben található összes adatbázisban.

Rövid távon fontolja meg további erőforrások biztosítását a készlet vertikális felskálázásával, vagy adatbázisok eltávolítását a készletből (az adatbázisokat áthelyezheti más készletekbe, vagy kiveheti őket a készletekből, és különálló szolgáltatásszintre helyezheti).

Hosszabb távon fontolja meg a lekérdezések optimalizálását vagy egy index használatát az adatbázis-teljesítmény növelése érdekében. Az alkalmazás teljesítményingadozásokra való érzékenységétől függően az ajánlott eljárás a készlet vertikális felskálázása még a 100%-os eDTU-használat elérése előtt. Használjon olyan riasztást, amely előre figyelmezteti Önt.

Foglalt készletet a generátor által létrehozott terhelés növelésével szimulálhat. Ha az adatbázisok terhelésében gyakrabban és hosszabb ideig jelentkeznek adatlöketek, az növeli a készlet összesített terhelését az egyes adatbázisok követelményeinek megváltoztatása nélkül. A készlet vertikális felskálázása könnyedén elvégezhető a Portalon vagy a PowerShellben. A gyakorlat során a Portalt használjuk.

1. Állítsa be a *$DemoScenario* = **3**, _Terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel_ értéket, hogy megnövelje a készlet összesített terhelési intenzitását az egyes adatbázisok csúcsterhelési követelményeinek megváltoztatása nélkül.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.


1. **Nyissa meg a** **tenants1/Pool1 készlet paneljét**.


1. Figyelje meg a készlet megnövekedett DTU-használatát a felső diagramon. Beletelik néhány percbe, amíg jelentkezik az új, magasabb terhelés, de hamarosan látni fogja, ahogy a készlet elkezdi elérni a 100%-os kihasználtságot, majd ahogy a terhelés stabilan beáll az új mintára, hamar túl is terheli a készletet.


1. A készlet vertikális felskálázásához kattintson a **Készlet beállítása** elemre.
1. Állítsa a **Készlet eDTU** csúszkáját 100-as értékre (ennél magasabbra nem ajánlott, nehogy túl sok költséggel kelljen számolnia). Figyelje meg, hogy a készlet összes adatbázisa számára elérhető összesített tárterület, amit a **Készlet – GB** jelez, összefügg az eDTU beállításával, és szintén megnövekszik. A készlet eDTU-értékének módosítása nem módosítja az adatbázisonkénti beállításokat (ami továbbra is adatbázisonként legfeljebb 50 eDTU). Az adatbázisonkénti beállítás a **Készlet beállítása** panel jobb oldalán látható.
1. Kattintson a **Mentés** gombra a kérés elküldéséhez. A módosítás egy Standard készlet esetében általában 3–5 percet vesz igénybe.

Lépjen vissza a **Pool1** > **Áttekintés** elemhez a megfigyelési diagramok megtekintéséhez. Figyelje meg, hogy a készlet több erőforrással való ellátása milyen hatást von maga után (noha kevés adatbázis és véletlenszerű terhelés esetén ezt nem mindig lehet egyszerűen és biztosan megállapítani). A diagramok megtekintése közben vegye figyelembe, hogy a felső diagramon látható 100% most 100 eDTU-t jelent, míg az alsó diagramon látható 100% továbbra is 50 eDTU-t, mivel az adatbázisonkénti maximum változatlanul 50 eDTU.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Abban a pillanatban, hogy minden adatbázis készen áll az engedélyezésre a készlet új eDTU-értékével, minden aktív kapcsolat megszakad. Az alkalmazás kódját mindig úgy kell megírni, hogy újrapróbálja a megszakított kapcsolatokat, így újra csatlakozni fog az adatbázishoz a felskálázott készletben.

## <a name="create-a-second-pool-and-load-balance-databases-to-handle-increased-aggregate-load"></a>Második készlet létrehozása és az adatbázisok terhelésének kiegyenlítése a megnövekedett összesített terhelés kezelése érdekében

A készlet felskálázása mellett másik lehetőségként létrehozhat egy második készletet és áthelyezhet abba adatbázisokat, hogy kiegyenlítse a két készlet terhelését. Ehhez az új készletet ugyanazon a kiszolgálón kell létrehozni, amelyen az első is megtalálható.

1. Nyissa meg a **customers1-&lt;FELHASZNÁLÓ&gt; kiszolgáló paneljét**. Ha egy adatbázis vagy készlet paneljén tartózkodik, lenyithatja az alapvető erőforrások vezérlőjét, és kiválaszthatja a kiszolgáló nevét, mint parancsikont.
1. Kattintson az **+ Új készlet** lehetőségre egy új készlet létrehozásához a jelenlegi kiszolgálón.
1. Az Új rugalmas adatbáziskészlet sablonban:

    1. állítsa be a **Név = Pool2** értéket.
    1. A tarifacsomagnál hagyja meg a **Standard készlet** beállítást.
    1. Kattintson a **Készlet beállítása** elemre.
    1. A megjelenő Készlet beállítása panelen állítsa be a **Készlet eDTU = 50 DTU** értéket.
    1. Kattintson az **Adatbázisok hozzáadása** parancsra, és megjelenik egy lista a kiszolgálón található adatbázisokról, amelyek nem a jelenlegi készletben találhatók.
    1. Ebben a listában **jelölje ki** az adatbázisok felét (20-ból 10-et) az új készletbe való áthelyezéshez, majd kattintson a **Kiválasztás** gombra.
    1. Kattintson újra a **Kiválasztás** gombra a konfigurációs módosítások elfogadásához. Vegye figyelembe a költségbecslést, ami egy hónapnyi, a kiválasztott beállításokkal való használatra vonatkozik.
    1. Kattintson az **OK** gombra az új készlet létrehozásához az új konfigurációval és az adatbázisok áthelyezéséhez.

A készlet létrehozása és az adatbázisok áthelyezése a készletbe néhány percet vesz igénybe. Az áthelyezett adatbázisok mindegyike online állapotú és teljes mértékben elérhető marad egészen az utolsó pillanatig, amikor minden megnyitott kapcsolatuk bezáródik. Ha az ügyfél megpróbál újracsatlakozni, az alkalmazás az új készletben csatlakozik az adatbázishoz.

A készlet a létrehozása után megjelenik a customers1 kiszolgáló paneljén. Kattintson a készlet nevére a készlet paneljének megnyitásához és a teljesítmény megfigyeléséhez.

Elvileg azt fogja látni, hogy a Pool1 készlet erőforrás-használata csökkent, és hogy a Pool2-t hasonló mértékű terhelés éri.

## <a name="manage-an-increased-load-on-a-single-database"></a>Megnövekedett terhelés kezelése egy önálló adatbázisban

Ha a készletben egy önálló adatbázist tartósan magas terhelés ér, akkor a készlet beállításaitól függően előfordulhat, hogy uralni fogja a készlet erőforrásainak használatát, ami hatással lehet a többi adatbázisra. Ha a tevékenység várhatóan huzamosabb ideig folytatódni fog, az adatbázis ideiglenesen kivehető a készletből. Ez egyszerre teszi lehetővé, hogy az érintett adatbázis a készlet többi adatbázisánál több erőforrásban részesüljön, illetve elszigetelődjön tőlük. Ez a gyakorlat a Contoso Concert Hall magas terhelésének a hatását szimulálja, amikor megkezdődik a jegyek árusítása egy népszerű koncertre.

1. A …\\**Demo-PerformanceManagementAndMonitoring**.ps1 szkriptben:
1. Állítsa be a **$DemoScenario = 5, Normál terhelés létrehozása, valamint magasabb terhelés létrehozása egyetlen bérlő számára (kb. 95 DTU).** értéket.
1. Állítsa be a **$SingleTenantDatabaseName = contosoconcerthall**értéket.
1. Futtassa a szkriptet az **F5** billentyűvel.
1. **Nyissa meg a** **Customers1/Pool1 készlet paneljét**.
1. Tekintse meg a **Rugalmas készlet figyelése** részt a panel tetején, és tanulmányozza a készlet megnövekedett DTU-használatát. Egy-két perc után jelentkezik a magas terhelés, aminek következtében a készlet eléri a 100%-os kihasználtságot.
1. Tartsa figyelemmel a **Rugalmas adatbázis figyelése** részt is, ami az elmúlt órában legtöbbet használt adatbázisokat mutatja meg. A contosoconcerthall adatbázis hamarosan megjelenik az első 5 adatbázis között.
1. **Kattintson a Rugalmas adatbázis figyelése** **diagramra** az **Adabáziserőforrás-felhasználás** panel megnyitásához, ahol bármelyik adatbázist kiválaszthatja külön megfigyelésre. Ez lehetővé teszi, hogy csak a contosoconcerthall adatbázis adatait jelenítse meg.
1. Az adatbázisok listájában **kattintson a contosoconcerthall adatbázisra** az adatbázis paneljének megnyitásához.
1. Kattintson a helyi menü **Tarifacsomag (DTU-k méretének beállítása)** elemre a **Teljesítmény konfigurálása** panel megnyitásához, ahol az adatbázishoz beállíthat egy elkülönített teljesítményszintet.
1. Kattintson a **Standard** lapra a Standard csomag skálázási beállításainak megnyitásához.
1. Húzza a **DTU csúszkát** jobbra a 100-as DTU-érték kiválasztásához. Ez megfelel az **S3** szolgáltatási célnak, ami zárójelben jelenik meg a DTU és a tárterület mérői között.
1. Kattintson az **Alkalmaz** gombra az adatbázis kivételéhez a készletből és Standard S3-adatbázissá alakításához.
1. Ha az üzembe helyezés kész, figyelje meg rugalmas készlet és az adatbázis paneljén a contosoconcerthall adatbázisra és arra a készletre gyakorolt hatást, ahonnan az adatbázist eltávolította.

Ha a contosoconcerthall adatbázis megszokottnál nagyobb terhelése megszűnik, helyezze vissza a készletbe a költségek csökkentése érdekében. Ha nem látja előre, hogy ez mikor fog bekövetkezni, beállíthat egy riasztást az adatbázison, amely értesíti, ha a DTU-kihasználtság a készlet adatbázisonkénti maximuma alá esik. Egy adatbázis készletbe történő áthelyezésének menetét az 5. gyakorlat írja le.

## <a name="other-performance-management-patterns"></a>Egyéb teljesítménykezelési minták

**Preemptív méretezés** A 6. gyakorlatban, amely egy elkülönített adatbázis méretezését ismertette, előre adott volt, melyik adatbázist kell keresni. Ha a Contoso Concert Hall vezetősége előre értesítette volna a WTP-t a várható jegyértékesítésről, az adatbázis előzetesen kivehető lett volna a készletből. Máskülönben valószínűleg egy riasztást kellett volna beállítani a készleten vagy az adatbázison ahhoz, hogy észre lehessen venni, mi történik. Nem jó, ha az ilyen eseményekről úgy szerez tudomást, hogy a készletben található többi bérlő csökkenő teljesítményről panaszkodik. Ha a bérlő meg tudja jósolni, hogy milyen hosszan lesz szüksége további erőforrásokra, beállítható egy Azure Automation-runbook, amely pontosan ütemezi az adatbázis kivételét, majd visszahelyezését a készletbe.

**Bérlők általi önkiszolgáló skálázás** Mivel a skálázási feladat könnyen meghívható a felügyeleti API-n keresztül, így a bérlői adatbázisok méretezésének lehetősége könnyen beépíthető a bérlőoldali alkalmazásba, és felkínálható az SaaS-szolgáltatás egy funkciójaként. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

### <a name="scaling-a-pool-up-and-down-on-a-schedule-to-match-usage-patterns"></a>Készletek használati mintákhoz igazított, ütemezett vertikális fel- és leskálázása

Ha az összesített bérlői használat kiszámítható mintákat követ, az Azure Automationnel ütemezni tudja a készletek vertikális fel- és leskálázását. Például egy készletet hétköznapokon este 6 után leskálázhat, reggel 6 előtt pedig felskálázhat, ha tudja, hogy a két időpont között csökken az erőforrásigény.



## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére

[Egyetlen bérlő visszaállítása – oktatóanyag](sql-database-saas-tutorial-restore-single-tenant.md)


## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek az eredetileg üzembe helyezett Wingtip Tickets Platform (WTP) alkalmazásra épülnek](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Rugalmas SQL-készletek](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](sql-database-saas-tutorial-log-analytics.md) – A Log Analytics beállítását és használatát ismertető oktatóanyag
