---
title: "Észlelési - teljesítményanomáliákat intelligens |} Microsoft Docs"
description: "Az Application Insights hajtja végre az alkalmazás telemetriai intelligens elemzése és figyelmezteti, potenciális problémákat. Ez a funkció a telepítés nem kell."
services: application-insights
documentationcenter: windows
author: antonfrMSFT
manager: carmonm
ms.assetid: 6acd41b9-fbf0-45b8-b83b-117e19062dd2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mbullwin
ms.openlocfilehash: 6886d1239fa074a6316b243e574833f5554ac298
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="smart-detection---performance-anomalies"></a>Intelligens észlelési - Teljesítményanomáliákat

[Az Application Insights](app-insights-overview.md) automatikusan elemzi a webes alkalmazás teljesítményének és figyelmeztetést küld, azokról a potenciális problémákról. Akkor lehet, hogy lehet olvassa, mert az intelligens észlelési értesítést kapott.

Ehhez a szolgáltatáshoz nem speciális beállítási kivételével az alkalmazás konfigurálását az Application Insights (a [ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), vagy [Node.js](app-insights-nodejs.md), majd a [weblap kód](app-insights-javascript.md)). Azt akkor aktív, ha az alkalmazás elég telemetriai adatokat állít elő.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Ha visszajelzést kap egy intelligens észlelési értesítést?

Az Application Insights azt észlelte, hogy az alkalmazás teljesítményének segít az alábbi módszerek valamelyikével:

* **Válasz idő teljesítménycsökkenést** – az alkalmazás elindult, a használt lassabban válaszol a kérelmekre. A módosítás gyors, például megváltoztak, mert hiba történt egy regressziós a legújabb környezetben. Vagy volna fokozatos, talán memóriavesztés okozza. 
* **Függőség időtartama teljesítménycsökkenést** – az alkalmazás lehetővé teszi a REST API-n, adatbázis vagy más függőségi hívásokat. A függőség a használt lassabban válaszol.
* **Lassú teljesítmény mintát** – az alkalmazás úgy tűnik, hogy rendelkezik a teljesítménycsökkenés oka, hogy csak bizonyos kérelmek érinti. Például lapok betöltött lassabban egy típus a böngésző, mint a többire; vagy a kérelmek lassabban által kiszolgált egy adott kiszolgálóhoz. Jelenleg az algoritmusok tekintse meg lapbetöltési idők kérelem válaszidejét és függőségi válaszidejét.  

Intelligens észlelési legalább 8 nap, alkalmazható kötet telemetriai adatot igényel a normál teljesítmény meghatározásához. Igen után az alkalmazás adott ideig futott, bármely jelentős probléma okozza értesítést.


## <a name="does-my-app-definitely-have-a-problem"></a>Saját alkalmazás mindenképpen rendelkezik probléma?

Nem, egy értesítés nem jelenti azt, hogy az alkalmazás véglegesen rendelkezik-e probléma. Egyszerűen javaslatot kapcsolatos valami érdemes szorosan meg több.

## <a name="how-do-i-fix-it"></a>Hogyan tegye megjavítani?

Az értesítések tárgya lehet a diagnosztikai adatokat. Íme egy példa:


![Íme egy példa a kiszolgáló válasza idő teljesítménycsökkenést észlelés](./media/app-insights-proactive-diagnostics/server_response_time_degradation.png)

1. **Osztályozás**. Az értesítés elsajátíthatja, hogy hány felhasználó vagy az érintett hány művelet. Ez segíthet a probléma megoldásának prioritást.
2. **Hatókör**. A hiba érinti az összes forgalom, vagy csak bizonyos lapok? Az korlátozott adott böngészők vagy helyek? Ez az információ az értesítés lehet lekérni.
3. **Diagnosztizálja**. A diagnosztikai adatokat az értesítés gyakran, a probléma természetétől javaslatot tesz. Például ha válaszidő lelassul, ha nagy a kérések aránya, javasolja a kiszolgáló vagy a függőségek túlterhelt. 

    Ellenkező esetben nyissa meg az Application Insightsban a teljesítmény-panelre. Itt megtalálja [Profilkészítő](app-insights-profiler.md) adatokat. Ha a kivételek, is megpróbálhatja a [pillanatkép hibakereső](app-insights-snapshot-debugger.md).



## <a name="configure-email-notifications"></a>E-mail értesítések beállítása

Intelligens észlelési értesítések alapértelmezés szerint engedélyezve van, és azok, akik rendelkeznek küldött [tulajdonosai, közreműködő szerepkörrel rendelkező személyek és olvasókat a hozzáférést az Application Insights-erőforrás](app-insights-resources-roles-access-control.md). Ennek módosításához kattintson a **konfigurálása** a e-mailben értesítést, vagy az Application Insightsban intelligens észlelési beállítások megnyitása. 
  
  ![Intelligens észlelési beállítások](./media/app-insights-proactive-diagnostics/smart_detection_configuration.png)
  
  * Használhatja a **leiratkozhat** kapni az e-mail értesítések intelligens észlelési e-mailben szereplő hivatkozásra.

E-mailek kapcsolatos intelligens észlelések teljesítményanomáliákat / nap / Application Insights-erőforrást egy e-mail korlátozódnak. Az e-mailt kapnak, csak akkor, ha legalább egy új, az adott napon észlelt probléma van. Ismétlődik minden üzenet nem jelenik meg. 

## <a name="faq"></a>GYIK

* *Igen a Microsoft személyzete nézze meg az adatok?*
  * Nem. A szolgáltatás nem teljesen automatikus. Csak az értesítéseket kap. Az adatok [titkos](app-insights-data-retention-privacy.md).
* *Az Application Insights által gyűjtött összes adat elemzése?*
  * Jelenleg nem. Jelenleg a Microsoft elemzése kérelmek válaszideje, a függőségi válaszidő és a lap betöltési ideje. További metrikák elemzését a várakozó fájlok – Reméljük be van kapcsolva.

* Milyen típusú alkalmazás ez működik?
  * Ezek degradations bármely alkalmazás hozza létre a megfelelő telemetriai adatokat észlelt. Ha telepítette, akkor az Application Insights a webes alkalmazást, majd kérelmek és függőségei automatikusan követi. De a háttér-szolgáltatások vagy alkalmazások, ha Ön hívások szúrja be [TrackRequest()](app-insights-api-custom-events-metrics.md#trackrequest) vagy [TrackDependency](app-insights-api-custom-events-metrics.md#trackdependency), majd intelligens észlelési a megszokott módon fognak működni.

* *Saját anomáliadetektálási észlelési szabályok létrehozása vagy meglévő szabályok testreszabása?*

  * Még nem, de a következőket teheti:
    * [Riasztások beállítása](app-insights-alerts.md) , amely közli, ha egy metrika keverve használ a küszöbértéket.
    * [Telemetriai adatok exportálása](app-insights-export-telemetry.md) való egy [adatbázis](app-insights-code-sample-export-sql-stream-analytics.md) vagy [a Power bi szolgáltatásba](app-insights-export-power-bi.md), ahol elemezhetjük magát.
* *Milyen gyakran történik az elemzés?*

  * Futtatás az elemzés nap lefuttatjuk az előző napi (UTC időzónában teljes nap) telemetriai adatok.
* *Ezáltal ez a név felülírandó [metrika riasztások](app-insights-alerts.md)?*
  * Nem.  Azt nem véglegesíthető a minden, érdemes lehet rendellenes viselkedés észlelése.


* *Ha nem tesz semmit, a válasz egy adott értesítésre, I kap egy emlékeztető?*
  * Nem, egy üzenetet kap minden problémával kapcsolatos csak egyszer. Ha a probléma továbbra is fennáll, az adatcsatorna-panel Intelligens észlelését frissíti.
* *Az e-mailt elvész. Hol található az értesítéseket a portálon?*
  * Az Application Insights az alkalmazás áttekintésében, kattintson a **intelligens észlelési** csempére. Nem lesz az értesítések találja mentése 90 nap biztonsági.

## <a name="how-can-i-improve-performance"></a>Hogyan javíthatja a teljesítményt?
Lassú és sikertelen válaszok amelyeket közül a legnagyobb frustrations webhely számára a saját tapasztalatunkat is tudja. Ezért fontos, a problémák.

### <a name="triage"></a>Osztályozás
Első lépésként számít? Ha egy lap mindig lassú betöltése, de csak 1 %-át a hely felhasználóinak kell meg, lehet, hogy, hogy gondolniuk fontosabb részek a háttérbe. Másrészről Ha csak a felhasználók 1 % Nyissa meg, de minden egyes jelez kivételek, amely érdekében érdemes lehet vizsgál.

A hatás utasítás (érintett felhasználók vagy forgalom %) használható általános útmutatóként, de vegye figyelembe, hogy nincs-e a lényeg. Győződjön meg arról, hogy igazolják összegyűjtése.

Fontolja meg a hiba paramétereit. Ha a földrajzi-függő, állítsa be [rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md) többek között az adott régióban: egyszerűen hálózati problémák adódhatnak az adott területre.

### <a name="diagnose-slow-page-loads"></a>Lassúlap terhelések diagnosztizálása
Hol található a problémát? Lassan válaszol-e a kiszolgáló, a lap nagyon hosszú, vagy nem a böngészőt kell elvégezni megjelenítéséhez munka nagy?

A böngészők metrika panel megnyitásához. A böngésző lap betöltési ideje mutat be ahol állapotra vált, az idő szegmentált megjelenítését. 

* Ha **küldési kérelem ideje** értéke magas, a kiszolgáló válaszol lassan, vagy a kérelem a nagy mennyiségű adat post. Tekintse meg a [teljesítménymutatók](app-insights-web-monitor-performance.md#metrics) válaszidők vizsgálatára.
* Állítson be [követési függőségi](app-insights-asp-net-dependencies.md) , hogy a lassúsága külső szolgáltatások vagy az adatbázis miatt.
* Ha **válasz fogadása** elsődleges, a lap és a függő részek - JavaScript, CSS, és így tovább (de nem aszinkron módon betöltött adatokról) lemezképek hosszúak. Állítson be egy [elérhetőségi teszt](app-insights-monitor-web-app-availability.md), és ügyeljen arra, hogy állítsa be, hogy a függő részek betölteni. Amikor bizonyos adatokat, nyissa meg az eredményt részleteit, és bontsa ki a betöltési idők különböző fájlok megtekintéséhez.
* Magas **ügyfél feldolgozási idejének** lassan futnak a parancsfájlok javasol. Ha az OK nem egyértelmű, fontolja meg néhány időzítési kódot, és időpontok trackMetric hívások küldése.

### <a name="improve-slow-pages"></a>Lassú lapok javítása
A webes tanácsadás javítása a kiszolgáló válaszainak és lapbetöltési idők, ezért többször nem fognak összes itt megismételni a teljes van. Az alábbiakban néhány tippeket, amelyet már valószínűleg tudni kapcsolatban, csak az első végezni:

* Lassú betöltése nagy fájlok miatt: a parancsfájlok és egyéb részeinek aszinkron módon betöltése. Ezen parancsfájl kötegelése. A fő lapján felosztása widgeteket, amely az adatok külön-külön betöltése. Ne küldjön egyszerű régi HTML hosszú táblázatok: egy parancsfájl segítségével az adatok JSON vagy más kompakt formátumú kérelem, majd töltse ki a tábla helyen. Nincsenek kiváló keretrendszerek, ez segít. (Ezek maguk után nagy parancsfájlok természetesen.)
* Lassú server függőségek: fontolja meg az összetevők földrajzi elhelyezkedését. Például Azure használata, győződjön meg arról, hogy a webkiszolgáló és az adatbázis ugyanabban a régióban. Hajtsa végre a lekérdezések több információt kell olvassák be? Gyorsítótárazás vagy súgó kötegelés volna?
* A kapacitás problémák: nézze meg a kiszolgálói metrikák válaszidejének és a kérelmek számát. Ha válaszidők aránytalanul csúcsait ügyfélkérelmek számát az a maximális, valószínű, hogy a kiszolgálók úgy módosítja a program.


## <a name="server-response-time-degradation"></a>Kiszolgáló válasza idő teljesítménycsökkenése

A válasz idő teljesítménycsökkenést értesítés, miszerint:

* A szokásos válaszidő ehhez a művelethez képest válaszidőt.
* Érintett felhasználók számát.
* Átlagos válaszidő és 90 PERCENTILIS válaszideje ezt a műveletet a nap az észlelési és előtt 7 nap. 
* Ez az észlelési napját és 7 nappal az előtt művelet kérelmek száma.
* Ebben a műveletben teljesítménycsökkenést és a kapcsolódó függőségek degradations közötti korreláció. 
* Hivatkozásokat tartalmaz, akkor a probléma diagnosztizálása érdekében.
  * Szolgáltatásprofil-elemzői adat megjelenítéséhez, ahol művelet töltött idő, van (a hivatkozás akkor használható, ha ehhez a művelethez az észlelési időszak során összegyűjtött Profilkészítő nyomkövetési példák). 
  * Teljesítmény-jelentések a metrika Intézőben, ahol Ön is részletekbe menően idő tartományszűrő ehhez a művelethez.
  * Keresse meg a hívások adott hívások tulajdonságainak megtekintése.
  * Hiba – Ha jelent ez azt jelenti, hogy hiba történt a művelet, amely előfordulhat, hogy hozzájárultak teljesítménycsökkenés > 1 száma.

## <a name="dependency-duration-degradation"></a>A függőségi időtartama teljesítménycsökkenése

A modern alkalmazásnak nagyobb micro szolgáltatások tervezett módszert alkalmaz, ami sok esetben nagy megbízhatóságot külső szolgáltatások fogad el. Például ha az alkalmazás egyes adatplatform vagy is készít a saját botot lesz valószínűleg továbbítják a néhány kognitív szolgáltató ahhoz, hogy a botok több emberi módon interaktív és bizonyos adatokat tároló szolgáltatás botot való lekérésére a válaszokat a.  

Példa függőségi teljesítménycsökkenést értesítés:

![Függőség időtartama teljesítménycsökkenést észlelés példa](./media/app-insights-proactive-diagnostics/dependency_duration_degradation.png)

Figyelje meg, amely jelzi, hogy:

* A szokásos válaszidő ehhez a művelethez képest időtartama
* Hány felhasználó érintett
* Átlagos és 90 PERCENTILIS időtartama a függőség észlelését napját és előtt 7 nap
* Függőség száma meghívja a az észlelés és előtt 7 nap
* Hivatkozások segítséget nyújtanak a probléma diagnosztizálása
  * Teljesítmény jelentéseinek a függőség metrika Explorerben
  * Keresse meg a függőségi hívás hívások tulajdonságainak megtekintése
  * Hiba – Ha jelent > 1 Ez jelenti, hogy történtek az észlelési időszakban, előfordulhat, hogy hozzájárultak időtartama teljesítménycsökkenést nem sikerült függőségi hívások száma. 
  * Nyissa meg a függőségi időtartamát és a count számító lekérdezéseket elemzés  

## <a name="smart-detection-of-slow-performing-patterns"></a>A lassú teljesítő minták intelligens észlelése 

Az Application Insights megkeresése, amelyek csak hatással vannak a felhasználók egy részét, vagy csak az egyes esetekben felhasználókat érintő teljesítményproblémákat. Például a lapok betöltési értesítést más típusú böngészők, mint a böngésző egyik típusú slowler, vagy ha a kérések lassabban rendelkezésre az adott kiszolgálóhoz. Feltérképezésére is alkalmas kombinációk, kapcsolódó problémák, mint például adott operációs rendszert használó ügyfelek számára egy földrajzi terület lassúlap tölti be.  

Hasonló rendellenességeket nagyon nehéz észlelése csak az adatok vizsgálatával, de több mint érdemes. Gyakran azok csak felület, amikor a felhasználók panaszkodnak mert. Addigra túl későn: az érintett felhasználók már vált a versenytársak!

Jelenleg az algoritmusok tekintse meg lapbetöltési idők, a kérelem a válaszhoz szükséges idő a kiszolgáló és a függőségi válaszidejét.  

Nincs a küszöbértékek és szabályok konfigurálása. Gépi tanulás és adatbányászati algoritmusokat használunk rendellenes minták észlelésére.

![Az e-mail riasztást kattintson a hivatkozásra kattintva nyissa meg a diagnosztikai jelentés az Azure-ban](./media/app-insights-proactive-performance-diagnostics/03.png)

* **Amikor** megjeleníti az időt a rendszer észlelte a problémát.
* **Mi** ismerteti:

  * A problémát észlelt;
  * Az eseményeket, amelyek észleltünk készletét jellemzői jelenik meg a probléma viselkedését.
* A táblázat összehasonlítja a gyengén teljesítő készlet az összes esemény átlagos viselkedését.

Kattintson az egyes hivatkozásokra kattintva nyissa meg a metrika Explorer, és keresse a megfelelő jelentésekben, az idő-és a lassú teljesítő csoport tulajdonságainak szűrve.

Időtartomány és telemetriai adatok felfedezése szűrők módosíthatók.

## <a name="next-steps"></a>Következő lépések
A diagnosztikai eszközök segítségével vizsgálja meg az alkalmazás a telemetriai adatok:

* [Profilkészítő](app-insights-profiler.md) 
* [Pillanatkép hibakereső](app-insights-snapshot-debugger.md)
* [Elemzés](app-insights-analytics-tour.md)
* [Elemzés intelligens diagnosztika](app-insights-analytics-diagnostics.md)

Intelligens észlelések rendszer teljesen automatikus. De lehet, hogy milyen néhány további riasztások beállítása?

* [Manuálisan konfigurált metrika riasztások](app-insights-alerts.md)
* [A webteszt rendelkezésre állása](app-insights-monitor-web-app-availability.md)
