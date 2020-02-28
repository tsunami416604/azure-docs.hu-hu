---
title: Az alkalmazás állapotának és használatának monitorozása Application Insights
description: A Application Insights első lépései. A helyszíni vagy Microsoft Azure alkalmazások használatának, rendelkezésre állásának és teljesítményének elemzése.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: e398f23559729580ae4ad8b6507c2f09328052b5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670899"
---
# <a name="monitor-performance-in-web-applications"></a>Webalkalmazások teljesítményének monitorozása


Győződjön meg arról, hogy az alkalmazás jól teljesít, és gyorsan tájékozódhat a hibákról. [Application Insights][start] közli a teljesítménnyel kapcsolatos problémákat és kivételeket, és segít megtalálni és diagnosztizálni a kiváltó okokat.

A Application Insights Java-és ASP.NET-webalkalmazások és-szolgáltatások, WCF-szolgáltatások figyelésére is képes. Helyszíni, virtuális gépeken vagy Microsoft Azure webhelyeken is üzemeltethető. 

Az ügyféloldali oldalon Application Insights a weblapokról és az iOS-, Android-és Windows áruházbeli alkalmazásokból származó telemetria is igénybe vehet.

## <a name="setup"></a>Teljesítményfigyelés beállítása
Ha még nem adott hozzá Application Insightst a projekthez (azaz ha nem rendelkezik a ApplicationInsights. config fájlval), akkor az első lépésekhez válasszon a következő lehetőségek közül:

* [ASP.NET Web Apps](../../azure-monitor/app/asp-net.md)
  * [Kivétel-figyelés hozzáadása](../../azure-monitor/app/asp-net-exceptions.md)
  * [Függőség figyelésének hozzáadása](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Java EE-alapú webalkalmazások](../../azure-monitor/app/java-get-started.md)
  * [Függőség figyelésének hozzáadása](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Teljesítmény-mérőszámok feltárása
A [Azure Portal](https://portal.azure.com)tallózással keresse meg az alkalmazáshoz beállított Application Insights-erőforrást. Az Áttekintés panelen az alapvető teljesítményadatok láthatók:

Kattintson bármelyik diagramra a további részletek megtekintéséhez, valamint az eredmények hosszabb időszakra való megtekintéséhez. Kattintson például a kérések csempére, majd válassza ki az időtartományt:

![Kattintson a Tovább gombra, és válassza ki az időtartományt](./media/web-monitor-performance/appinsights-48metrics.png)

A diagramra kattintva kiválaszthatja a megjelenített mérőszámokat, vagy hozzáadhat egy új diagramot, és kiválaszthatja annak metrikáit:

![Kattintson egy gráfra a metrikák kiválasztásához](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Törölje az összes mérőszámot** az elérhető teljes választék megjelenítéséhez. A metrikák csoportokba esnek; Ha a csoport bármelyik tagja ki van választva, csak a csoport többi tagja jelenik meg.

## <a name="metrics"></a>Mit jelent mindez? Teljesítmény csempék és jelentések
Számos teljesítmény-mérőszámot érhet el. Kezdjük azokkal, amelyek alapértelmezés szerint megjelennek az alkalmazás paneljén.

### <a name="requests"></a>Kérelmek
A megadott időszakban fogadott HTTP-kérelmek száma. Hasonlítsa össze a többi jelentés eredményét, hogy megtekintse, hogyan viselkedik az alkalmazás a terheléstől függően.

A HTTP-kérelmek tartalmazzák a lapok, az adatfájlok és a képek összes GET vagy POST kérését.

A csempére kattintva megtekintheti az egyes URL-címek számát.

### <a name="average-response-time"></a>Átlagos válaszidő
Az alkalmazást és a visszaadott választ meghaladó webes kérelem közötti időt méri.

A pontok mozgó átlagot mutatnak. Ha sok kérés van, előfordulhat, hogy néhány érték eltér az átlagtól, amely nem nyilvánvaló csúcs vagy DIP a gráfban.

Szokatlan csúcsokat keres. Általánosságban elmondható, hogy a válaszadási idő egyre nő a kérelmekben. Ha a növekedés aránytalan, előfordulhat, hogy az alkalmazás egy erőforrás-korlátot (például CPU-t) vagy az általa használt szolgáltatás kapacitását sújtja.

Kattintson a csempére adott URL-címek lekéréséhez.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Leglassabb kérelmek
![](./media/web-monitor-performance/appinsights-44slowest.png)

Megjeleníti, hogy mely kérelmekre lehet szükség a teljesítmény finomhangolásához.

### <a name="failed-requests"></a>Sikertelen kérelmek
![](./media/web-monitor-performance/appinsights-46failed.png)

A nem kezelt kivételeket kidobott kérelmek száma.

A csempére kattintva megtekintheti az adott hibák részleteit, és kiválaszthat egy egyéni kérést a részletek megtekintéséhez. 

A rendszer csak a hibák reprezentatív mintáját őrzi meg az egyes ellenőrzésekhez.

### <a name="other-metrics"></a>Egyéb mérőszámok
Ha szeretné megtekinteni, hogy milyen egyéb mérőszámokat jeleníthet meg, kattintson egy gráfra, majd törölje az összes mérőszám kijelölését a teljes rendelkezésre álló készlet megtekintéséhez. Kattintson (i) az egyes mérőszámok definíciójának megjelenítéséhez.

![Az összes metrika kijelölésének kikapcsolása a teljes készlet megjelenítéséhez](./media/web-monitor-performance/appinsights-62allchoices.png)

Bármely metrika kiválasztásával letilthatja azokat a többieket, amelyek nem szerepelhetnek ugyanazon a diagramon.

## <a name="set-alerts"></a>Riasztások beállítása
Ha bármilyen metrika szokatlan értékeit e-mailben szeretné értesíteni, adjon hozzá egy riasztást. Választhatja azt is, hogy az e-mailt a fiók rendszergazdái vagy adott e-mail-címei számára küldje el.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Adja meg az erőforrást a többi tulajdonság előtt. Ne válassza a webteszt-erőforrásokat, ha riasztásokat szeretne beállítani a teljesítményre vagy a használati metrikára vonatkozóan.

Ügyeljen arra, hogy az egység, amelyben meg kell adnia a küszöbértéket.

*Nem jelenik meg a riasztás hozzáadása gomb.* – Ez egy csoportfiók, amelyhez csak olvasási hozzáférése van? Forduljon a fiók rendszergazdájához.

## <a name="diagnosis"></a>Problémák diagnosztizálása
Íme néhány tipp a teljesítménnyel kapcsolatos problémák megkereséséhez és diagnosztizálásához:

* Beállíthatja, hogy a rendszer figyelmeztesse a [webteszteket][availability] , ha a webhely leáll, vagy helytelenül vagy lassan válaszol. 
* Hasonlítsa össze a kérések darabszámát más metrikákkal, hogy ellenőrizze, hogy a hibák vagy a lassú válasz kapcsolódik-e a betöltéshez.
* A problémák azonosításához [szúrjon be és keressen nyomkövetési utasításokat][diagnostic] a kódban.
* A webalkalmazás figyelése a [élő metrikastreamsal][livestream]való működés közben.
* Rögzítse a .NET-alkalmazás állapotát [Snapshot Debugger][snapshot]használatával.

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Teljesítménybeli szűk keresztmetszetek keresése és javítása a teljesítménnyel kapcsolatos vizsgálatokkal

A teljesítmény-vizsgálati folyamattal ellenőrizheti a lassú teljesítményű műveleteket a webalkalmazásban. Gyorsan kiválaszthat egy adott lassú műveletet, és a [Profilert](../../azure-monitor/app/profiler.md) használhatja a root értékre, mert a lassú műveletek le lettek hajtva a kódban. A kiválasztott művelethez megjelenített új időtartam-eloszlás használatával gyorsan megtekintheti, hogy milyen rossz a felhasználói élmény. Láthatja, hogy a felhasználói interakciók közül hány lett hatással az egyes lassú működésekre. A következő példában úgy döntöttünk, hogy alaposabban szemügyre vesszük az ügyfelek/részletek beszerzésének élményét. Az időtartam eloszlása esetében láthatjuk, hogy három tüske van. A bal szélső tüske körülbelül 400 MS, és nagyszerűen reagáló élményt jelent. A középső tüske körülbelül 1,2 s, és közepes élményt jelent. Végül a 3,6 s-ben egy kis tüske is szerepel, amely a esetek 99% percentilis-élményt képviseli, ami valószínűleg azt eredményezi, hogy az ügyfelek nem maradnak meg. Ez a élmény tízszer lassabb, mint az azonos művelettel kapcsolatos nagy élmény. 

![Ügyfelek/részletek beolvasása három időtartamra vonatkozó tüskék](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

A művelettel kapcsolatos felhasználói élmény jobb megismeréséhez kijelölhetünk egy nagyobb időtartományt. Ezután leszűkítheti az időt egy adott időablakban, ahol a művelet lassú volt. A következő példában az alapértelmezett 24 órás időtartományról a 7 napos időtartományra váltottunk át, majd a 9:47 – 12:47 időintervallumra nagyítottuk a 12. és a 13. közötti időszakot. A jobb oldalon az időtartam eloszlása és a minta-és Profiler-Nyomkövetések száma is frissült.

![Ügyfelek/részletek beolvasása három időtartammal 7 nap alatt, időintervallummal](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

A lassú élmények csökkentése érdekében legközelebb a 95. és a esetek 99% percentilis közötti időtartamokra nagyítjuk. Ezek a felhasználói interakciók 4%-át jelentik, ami lassú volt.

![Ügyfelek/részletek beolvasása három időtartammal 7 nap alatt, időintervallummal](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Most már megnézheti a reprezentatív mintákat, ha a minták gombra kattint, vagy a képviselő Profiler-nyomkövetésben a Profiler Nyomkövetések gombra kattint. Ebben a példában négy nyomkövetést gyűjtöttünk be az ügyfelek/részletek beszerzésére az időablakban és a tartomány érdeklődési ideje alatt.

Előfordulhat, hogy a probléma nem szerepel a kódban, hanem a kód által megadotttól függ. A lassú függőségek kivizsgálásához váltson a teljesítmény-osztályozás nézet függőségek lapjára. Alapértelmezés szerint a teljesítmény nézet a trendek átlaga, de a 95. percentilis (vagy a esetek 99%, ha egy érett szolgáltatást figyel). A következő példában a lassú Azure BLOB-függőségre koncentráltunk, ahol a PUT fabrikamaccount-t hívjuk. A jó élmények a 40 MS körüli fürtök, míg a lassú hívások ugyanahhoz a függőséghez háromszor lassabbak, a 120 MS körüli fürtözést. Nem veszi igénybe a hívások nagy részét, hogy felvegye a megfelelő műveletet, ami észrevehetően lelassul. Az Operations (műveletek) lapon megtekintheti a reprezentatív mintákat és a Profiler-nyomkövetéseket.

![Ügyfelek/részletek beolvasása három időtartammal 7 nap alatt, időintervallummal](./media/web-monitor-performance/SlowDependencies95thTrend.png)

A teljesítmény vizsgálatával kapcsolatos tapasztalatok az arra a példára összpontosító, a kiválasztott minta felé irányuló releváns elemzéseket mutatják be. Az összes rendelkezésre álló információ megtekinthető úgy, hogy 30 napos időtartományra váltson, majd az általános lehetőségre kattintva megtekintheti az elmúlt hónap összes műveletét.

![Ügyfelek/részletek beolvasása három időtartammal 7 nap alatt, időintervallummal](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Következő lépések
[Webes tesztek][availability] – a webes kérelmeket rendszeres időközönként, a világ minden tájáról küldik el az alkalmazásnak.

[Diagnosztikai Nyomkövetések rögzítése és keresése][diagnostic] – nyomkövetési hívásokat szúrhat be, és átirányíthatja az eredményeket a problémák azonosításához.

[Használat nyomon követése][usage] – Ismerje meg, hogy a felhasználók hogyan használják az alkalmazást.

[Hibaelhárítás][qna] – és Q & A



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



