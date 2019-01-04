---
title: Az alkalmazás állapotának és az Application Insights használatának monitorozása
description: Ismerkedés az Application Insights. Használat, a rendelkezésre állás és a teljesítményt a helyszíni vagy a Microsoft Azure-alkalmazások elemzése.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 6731b19984c0b2e0a663e93b2a6ef00c530a4d48
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014641"
---
# <a name="monitor-performance-in-web-applications"></a>Webalkalmazások teljesítményének monitorozása


Győződjön meg arról, hogy az alkalmazás jól teljesít-e, és ismerje meg gyorsan esetleges hibák. [Az Application Insights] [ start] mutassa be, semmilyen teljesítménybeli problémák és kivételek, és segítséget nyújtanak a észlelése és diagnosztizálása az alapvető okait.

Az Application Insights Java és az ASP.NET webes alkalmazások és szolgáltatások, WCF-szolgáltatások követheti nyomon. Ezek lehetnek üzemeltethető a helyszínen, a virtual machines, vagy a Microsoft Azure-webhelyeken. 

Az ügyféloldalon Application Insights is igénybe vehet a weblapok és számos különböző eszközt, beleértve az iOS, Android és Windows Store apps telemetriai adatokat.

## <a name="setup"></a>Alkalmazásteljesítmény-figyelés beállítása
Ha még nem még hozzáadta az Application Insights a projekthez (azaz, ha azt nem kell az applicationinsights.config fájlban), válasszon egyet az alábbi módszerrel lehet hozzálátni:

* [ASP.NET-webalkalmazásokat](../azure-monitor/app/asp-net.md)
  * [Kivételfigyelés hozzáadása](../azure-monitor/app/asp-net-exceptions.md)
  * [Adja hozzá a függőség figyelése](../azure-monitor/app/monitor-performance-live-website-now.md)
* [J2EE-webalkalmazások](../azure-monitor/app/java-get-started.md)
  * [Adja hozzá a függőség figyelése](../azure-monitor/app/java-agent.md)

## <a name="view"></a>Teljesítmény-mérőszámokat derít fel
A [az Azure Portalon](https://portal.azure.com), tallózással keresse meg az Application Insights-erőforrást, amely az alkalmazás beállítása. Az Áttekintés panelen az alapvető teljesítménymutatók adatokat jelenít meg:

Kattintson valamely diagramra kattintva további részleteket tekinthet, és hosszabb ideig eredmények megtekintéséhez. Kattintson például a kérések csempére, és válassza ki egy időtartományt:

![Kattintson végig rajtuk a további adatokat, és jelöljön ki egy időtartományt](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Kattintson egy diagramra, mely metrikákat azt jeleníti meg, vagy adjon hozzá egy új diagramot és a metrikák kiválasztása:

![Kattintson egy diagramra metrikák kiválasztása](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Törölje a jelet a mérőszámok** a teljes kijelölt, amely érhető el. A mérőszám értéke csoportokba; Ha egy csoport bármely tagja van kijelölve, csak a többi a csoportnak a tagjaira jelennek meg.

## <a name="metrics"></a>Mi nem, minden mean? Teljesítmény-csempék és jelentések
Számos különböző teljesítmény-mérőszámokat kaphat. Kezdjük azokkal, amelyek alapértelmezés szerint a az alkalmazás paneljén jelennek meg.

### <a name="requests"></a>Kérelmek
Egy megadott időszakban kapott HTTP-kérések száma. Hasonlítsa ezt össze az eredményeket megtekintheti, ahogy a terhelés hogyan viselkedik az alkalmazás más jelentések változik.

HTTP-kérések minden GET vagy POST kéréseket az oldalak, az adatok és lemezképek tartalmazzák.

Kattintson a csempére kattintva számát adott URL-címek lekérése.

### <a name="average-response-time"></a>Átlagos válaszidő
Írja be az alkalmazás és a visszaadott válasz webes kérelem között eltelt idő méri.

A pontok megjelenítése egy mozgó átlag. Ha sok kérelem, van néhány eltérnek az átlagos egy nyilvánvaló csúcs nélkül, vagy a gráf mártsuk.

Keresse meg a szokatlan csúcsok. Általánosságban véve a várt válaszideje nőtt a kérelmeket a növekszik. Ha aránytalan az okot, előfordulhat, hogy az alkalmazás lenyomásával egy erőforráskorlátot, például a Processzor- vagy egy szolgáltatást, használja a kapacitását.

Kattintson a csempére, és többször adott URL-címek lekérése.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Leglassabb kérelmek
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Látható, hogy mely kérelmek szükség lehet a teljesítmény-finomhangolási.

### <a name="failed-requests"></a>Sikertelen kérelmek
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Nem kezelt kivételek által kiváltott kérelmek száma.

Kattintson a csempére az adott hibákat a részletek megtekintéséhez, és a egy adott kérésre, hogy a részletek megtekintéséhez válassza ki. 

Egyéni ellenőrzési hibák csak egy reprezentatív mintát megmarad.

### <a name="other-metrics"></a>Egyéb metrika
Mit megjeleníteni, kattintson egy diagramra, és törölje a jelölést a mérőszámok megtekintéséhez az összes rendelkezésre álló egyéb metrika értékre. (I) kattintva megtekintheti az egyes metrika definíciója.

![Kapcsolja ki az összes metrikát, tekintse meg a teljes készletet](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Bármelyik metrika kijelölése letiltja a többi, amely nem szerepelhet ugyanezen a diagramon.

## <a name="set-alerts"></a>Riasztások beállítása
Valamelyik metrika rendkívüli értékek az e-mailben értesítést kapjon, riasztás hozzáadásához. Választhat az e-mailt küldjön a fiók a rendszergazdák, vagy adott e-mail-címek.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Állítsa be az erőforrást, mielőtt a többi tulajdonságot. Nem válassza ki a webteszt erőforrásokat, ha azt szeretné, a teljesítmény vagy a használati metrikákhoz riasztásokat állíthat be.

Ügyeljen arra, hogy vegye figyelembe az egységeket, amelyben kéri, hogy adja meg a küszöbértéket.

*A riasztás hozzáadása gomb nem látható.* -Van ez a csoport a fiók, amely csak olvasási hozzáféréssel rendelkezik? Ellenőrizze a fiók rendszergazdája.

## <a name="diagnosis"></a>Problémák diagnosztizálása
Íme néhány tipp megtalálásához, és a teljesítménnyel kapcsolatos problémák diagnosztizálásához:

* Állítsa be a [webes teszteket] [ availability] riasztani kell, ha a webhely leáll vagy nem megfelelően vagy lassan válaszol. 
* Hasonlítsa össze más metrikákkal annak ellenőrzéséhez, hogy a kapcsolódó betöltése sikertelen vagy lassú válasz a kérések száma.
* [Szúrjon be, és keresse meg a nyomkövetési utasításokat] [ diagnostic] segítségével azonosíthatja a problémákat a kódban.
* A műveletet a webes alkalmazás figyelése [élő metrikák Stream][livestream].
* A .net-alkalmazás az állapotát [Snapshot Debugger][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Megkeresheti és kijavíthatja a teljesítmény szűk teljesítményvizsgálati felület

A teljesítményvizsgálati felület segítségével tekintse át a lassú művelet a webalkalmazásban. Gyorsan egy adott lassú művelet kiválasztása és használata [Profiler](../azure-monitor/app/profiler.md) legfelső szintű kód le a lassú műveletek miatt. Használja az új időtartamok eloszlása látható a kijelölt művelet gyorsan egyetlen pillantással felmérheti milyen rossz tapasztalat van az ügyfelek számára. Láthatja, hogy hány felhasználó ügyfélkapcsolati érintett minden lassú művelet. A következő példában azt korábban úgy döntött, hogy közelebb tekintse meg a GET Customers/Details művelet nyújtotta felhasználói élmény. Az az időtartam terjesztés láthatjuk, hogy nincsenek-e három adatforgalmi csúcsokhoz. Bal szélső kiugrás körülbelül 400 MS, és rugalmas kiváló jelöli. Középső kiugrás van 1,2 s és jelöli egy közepes élményt. Végül, a 3.6-os s van egy másik kis kiugrás, amely nagy eséllyel eredményez elégedetlen vagyok velük, hogy ügyfeleink az esetek 99 % PERCENTILIS élmény jelöli. Ez a tapasztalat tízszer lassabb, mint a nagyszerű felhasználói élményt ugyanehhez a művelethez. 

![GET Customers/Details három időtartama adatforgalmi csúcsokhoz](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Nagyobb időtartomány kiválasztjuk a művelet a felhasználói élmény jobban érti lekéréséhez. Hogy majd is szűkíthető időben egy megadott idő ablak, ahol a művelet nem volt. A következő példában azt átállítását az alapértelmezett 24 óra és a 7 nap időtartomány időtartomány, és ezután nagyított Tue a 12. és szerda a 13. közötti 9:47, 12:47 idő ablakba. A időtartamok eloszlása és a minta és a profiler nyomkövetések számát is frissítve lett-e a jobb oldalon.

![GET Customers/Details egy olyan időkeretet a tartomány három kiugrások időtartam 7 nap](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

A lassú élményt szűkítéséhez azt ezután nagyíthatja a 95. és 99 közé eső időtartamának összegénél. Ezek képviselik a felhasználói tevékenységeket, amelyek lassú 4 %.

![GET Customers/Details egy olyan időkeretet a tartomány három kiugrások időtartam 7 nap](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Azt is most már a reprezentatív mintát, vagy tekintse meg a Profiler nyomkövetések gombra kattintva a minták gombra, vagy a reprezentatív profiler hívásláncai gombra kattintva. Ebben a példában négy összegyűjtése a GET Customers/Details az ablakot, és a tartomány időtartamot a lényeges a nyomkövetési adatok vannak.

Néha a probléma nem lesz a kódban, de függőségekben inkább a kód meghívja. Az ilyen lassú függőségek vizsgálata az osztályozási teljesítménye nézeten függőségek lapot válthat. Alapértelmezés szerint a teljesítmény nézet felkapott átlagok, de mi valóban szeretné megtekinteni a 95. percentilis (vagy a 99th abban az esetben, ha a figyelt érett szolgáltatás). Az alábbi példa azt a lassú Azure BLOB-függőség, ahol nevezzük PUT fabrikamaccount szóltak. A jó teljesen körülbelül 40 ms, fürt, míg a lassú hívások az ugyanazon függőségi háromszor lassabb, körülbelül 120 ms fürtszolgáltatás. Adja ki a megfelelő művelet észrevehetően lassítani hívásokat a számos nem használ. A reprezentatív mintát és a profiler hívásláncai, hasonlóan a Műveletek lap részletesen is.

![GET Customers/Details egy olyan időkeretet a tartomány három kiugrások időtartam 7 nap](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

A teljesítményvizsgálati felület jelentésvászon oldalán vonatkozó elemzéseket a minta-készlet, úgy döntött, hogy arra koncentrálhasson, jeleníti meg. A legjobb módszer esetén az összes rendelkezésre álló elemzések, hogy váltson át egy 30 napig időtartományt, és válassza a teljes insights megtekintéséhez a múlt hónapban az összes művelet között.

![GET Customers/Details egy olyan időkeretet a tartomány három kiugrások időtartam 7 nap](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Következő lépések
[Webes teszteket] [ availability] -alkalmazását a világ különböző pontjain, rendszeres időközönként webes kéréseket rendelkezik.

[Diagnosztikai nyomkövetési rögzítése és keresése] [ diagnostic] – szúrja be a nyomkövetési és tagolása az eredményeket a felmerülő problémákat.

[Használat nyomon követése] [ usage] – ismerje meg, hogy a felhasználók miként használják alkalmazását.

[Hibaelhárítási] [ qna] - és a Q & A



<!--Link references-->

[availability]: ../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../azure-monitor/app/asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: ../azure-monitor/app/monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: ../azure-monitor/app/live-stream.md
[snapshot]: ../azure-monitor/app/snapshot-debugger.md



