---
title: Az alkalmazás állapotának és használatának figyelése az Application Insights segítségével
description: Ismerkedés az Application Insights alkalmazással. Elemezze a helyszíni vagy a Microsoft Azure-alkalmazások használatát, rendelkezésre állását és teljesítményét.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: 873fc41585c387246d83008a8f97d6c4d9a32c3b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985065"
---
# <a name="monitor-performance-in-web-applications"></a>Webalkalmazások teljesítményének monitorozása


Győződjön meg arról, hogy az alkalmazás jól teljesít, és gyorsan megtudhatja a hibákat. [Az Application Insights][start] megmondja a teljesítményekkel kapcsolatos problémákat és kivételeket, és segít megtalálni és diagnosztizálni a kiváltó okokat.

Az Application Insights figyelheti a Java és ASP.NET webalkalmazások és szolgáltatások, WCF-szolgáltatások figyelése. Ezek üzemeltethetők a helyszínen, a virtuális gépeken, vagy a Microsoft Azure-webhelyeken. 

Az ügyféloldalon az Application Insights telemetriai adatokat vehet fel a weblapokról és számos eszközről, például az iOS, az Android és a Windows Áruházbeli alkalmazásokból.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Teljesítményfigyelés beállítása
Ha még nem adott hozzá Application Insights-ot a projekthez (azaz ha nem rendelkezik ApplicationInsights.config fájlval), válasszon az alábbi módszerek közül:

* [ASP.NET-webalkalmazások](../../azure-monitor/app/asp-net.md)
  * [Kivételfigyelés hozzáadása](../../azure-monitor/app/asp-net-exceptions.md)
  * [Függőségfigyelés hozzáadása](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Java EE webalkalmazások](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Teljesítménymutatók feltárása
Az [Azure Portalon](https://portal.azure.com)keresse meg az alkalmazáshoz beállított Application Insights-erőforrást. Az áttekintő panel az alapvető teljesítményadatokat jeleníti meg:

Kattintson bármelyik diagramra a részletek megtekintéséhez és a hosszabb időszakra vonatkozó eredmények megtekintéséhez. Kattintson például a Kérések csempére, majd jelöljön ki egy időtartományt:

![Kattintson át a további adatokhoz, és válasszon ki egy időtartományt](./media/web-monitor-performance/appinsights-48metrics.png)

Kattintson egy diagramra a megjelenített mutatók kiválasztásához, vagy adjon hozzá egy új diagramot, és válassza ki annak mutatóit:

![Kattintson egy grafikonra a mérőszámok kiválasztásához](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Törölje az összes mérőszám jelölését** a teljes rendelkezésre álló kijelölés megtekintéséhez. A mérőszámok csoportokba tartoznak; ha egy csoport bármely tagja ki van jelölve, csak a csoport többi tagja jelenik meg.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Mit jelent ez az egész? Teljesítménycsempék és jelentések
Vannak különböző teljesítmény mérőszámok lehet kapni. Kezdjük azokkal, amelyek alapértelmezés szerint megjelennek az alkalmazáspanelen.

### <a name="requests"></a>Kérelmek
A megadott időszakban fogadott HTTP-kérelmek száma. Hasonlítsa össze ezt a többi jelentés eredményeivel, és tekintse meg, hogyan viselkedik az alkalmazás a terhelés változó korának.

A HTTP-kérelmek tartalmazzák az oldalakra, adatokra és képekre vonatkozó összes GET vagy POST kérelmet.

Kattintson a csempére, hogy számít az egyes URL-ek.

### <a name="average-response-time"></a>Átlagos válaszidő
Az alkalmazásba belépő webes kérelem és a válasz visszaadása közötti időt méri.

A pontok mozgó átlagot mutatnak. Ha sok kérés van, lehet, hogy vannak olyanok, amelyek eltérnek az átlagtól anélkül, hogy nyilvánvaló csúcs vagy dip lenne a grafikonon.

Keress szokatlan csúcsokat. Általánosságban elmondható, hogy a kérelmek számának növekedésével várhatóan emelkedni fog a válaszadási idő. Ha az emelkedés aránytalan, előfordulhat, hogy az alkalmazás eléri az erőforrás-korlátot, például a CPU-t vagy az általa használt szolgáltatás kapacitását.

Az adott URL-címek időpontjainak lekért időpontjainak lenyomásához kattintson a csempére.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Leglassabb kérések
![](./media/web-monitor-performance/appinsights-44slowest.png)

Megmutatja, hogy mely kérelmeknek lehet szükségük teljesítményhangolásra.

### <a name="failed-requests"></a>Sikertelen kérelmek
![](./media/web-monitor-performance/appinsights-46failed.png)

A nem feledt kivételekhez nem kapott kérelmek száma.

A csempére kattintva megtekintheti az egyes hibák részleteit, és kiválaszthat egy egyedi kérelmet a részletek megtekintéséhez. 

Az egyedi ellenőrzéshez csak a hibák reprezentatív mintáját őrzik meg.

### <a name="other-metrics"></a>Egyéb mutatók
Ha meg szeretné tekinteni, hogy milyen egyéb metrikákat jeleníthet meg, kattintson egy grafikonra, majd törölje az összes mutató kijelölését a teljes elérhető készlet megtekintéséhez. Kattintson (i) az egyes mutatók definíciójának megtekintéséhez.

![Törölje az összes mutató kijelölését a teljes készlet megtekintéséhez](./media/web-monitor-performance/appinsights-62allchoices.png)

Ha kiválasztja a mutatót, letiltja azokat a többi mutatót, amelyek nem jelenhetnek meg ugyanazon a diagramon.

## <a name="set-alerts"></a>Riasztások beállítása
Ha e-mailben szeretne értesítést kapni bármely mutató szokatlan értékéről, adjon hozzá egy riasztást. Választhat, hogy az e-mailt elküldi a fiók rendszergazdáinak, vagy adott e-mail címekre.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Állítsa be az erőforrást a többi tulajdonság elé. Ne válassza ki a webteszt erőforrásokat, ha a teljesítmény- vagy használati mutatókra vonatkozó riasztásokat szeretne beállítani.

Ügyeljen arra, hogy vegye figyelembe azokat az egységeket, amelyekben a rendszer kéri a küszöbérték megadását.

*Nem látható a Riasztás hozzáadása gomb* - Ez egy olyan csoportfiók, amelyhez írásvédett hozzáférése van? Kérdezze meg a fiók rendszergazdáját.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Diagnosztikai problémák
Íme néhány tipp a teljesítményproblémák megtalálásához és diagnosztizálásáért:

* Webes [tesztek][availability] beállítása, hogy a webhely leáll, illetve helytelenül vagy lassan reagáljon. 
* Hasonlítsa össze a kérelmek száma más metrikák, hogy ha a hibák vagy lassú válasz a terheléshez kapcsolódó.
* A problémák azonosításához [szúrjon be és keressen nyomkövetési kimutatásokat][diagnostic] a kódba.
* Az [Élő metrikák][livestream]streamelésével folyamatosan működő webalkalmazás figyelése.
* Rögzítse a .NET alkalmazás állapotát a [Snapshot Debugger][snapshot]segítségével.

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Teljesítménybeli szűk keresztmetszetek keresése és javítása teljesítményvizsgálati tapasztalattal

A teljesítményvizsgálati élmény segítségével áttekintheti a webalkalmazásban a lassan teljesítő műveleteket. Gyorsan kiválaszthat egy adott lassú műveletet, és [a Profiler](../../azure-monitor/app/profiler.md) segítségével a lassú műveleteket kódig csökkentheti. A kiválasztott művelethez megjelenített új időtartam-eloszlás használatával gyorsan, egy pillantással felmérheti, hogy milyen rossz a felhasználói élmény az ügyfelek számára. Láthatja, hogy hány felhasználói interakciót érintett az egyes lassú műveletek. A következő példában úgy döntöttünk, hogy közelebbről megvizsgáljuk az Ügyfelek/Részletek beszerzése művelet élményét. Az időtartam eloszlásában láthatjuk, hogy három tüske van. Leftmost tüske mintegy 400 ms, és képviseli a nagy érzékeny élményt. Középső tüske körül 1,2 s, és képviseli a középszerű élmény. Végül a 3,6 s van egy kis tüske, amely képviseli a 99 percentilis tapasztalat, ami valószínűleg okoz ügyfeleink elhagyni elégedetlen. Ez a tapasztalat tízszer lassabb, mint a nagy tapasztalat az azonos művelet. 

![Get ügyfelek / Részletek három időtartam tüskék](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Annak érdekében, hogy jobban érzékelhessük a művelet felhasználói élményét, nagyobb időtartományt választhatunk. Ezután leszűkíthetjük az időt egy adott időablakra, ahol a művelet lassú volt. A következő példában az alapértelmezett 24 órás időtartományról a 7 napos időtartományra váltottunk, majd a 12-es és a 13-as kulc a 12.-es nap között 9:47 és 12:47 közötti időablakra nagyítottunk. A jobb oldalon mind az időtartam eloszlása, mind a minta- és profilozó-nyomkövetések száma frissült.

![Get Ügyfelek/Részletek három időtartam-csúcs 7 nap alatt egy időablakkal](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

A lassú élmények szűkítése érdekében a következő lépés a 95. Ezek a lassú felhasználói interakciók 4%-át képviselik.

![Get Ügyfelek/Részletek három időtartam-csúcs 7 nap alatt egy időablakkal](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Most már vagy nézd meg a reprezentatív mintákat, kattintson a Minták gombra, vagy a reprezentatív profiler nyomait, kattintson a Profiler traces gombra. Ebben a példában négy nyomkövetés van, amelyek et gyűjtöttek a GET vevők/részletek az időablakban és a tartomány időtartama a kamat.

Néha a probléma nem lesz a kódot, hanem a függőség a kód hívásokat. Az ilyen lassú függőségek vizsgálatához a teljesítményosztályozási nézet Függőségek lapjára válthat. Alapértelmezés szerint a teljesítmény nézet trendátlagok, de amit igazán meg szeretne nézni, az a 95. A következő példában a lassú Azure BLOB-függőségre összpontosítottunk, ahol PUT fabrikamaccount-nak hívjuk. A jó tapasztalatok körülbelül 40 ms fürt, míg a lassú hívások ugyanazon függőség háromszor lassabb, fürtözés mintegy 120 ms. Nem kell sok ilyen hívás, hogy adja ki, hogy az adott művelet észrevehetően lelassul. A reprezentatív mintákat és a profilozó nyomkövetéseit ugyanúgy részletezheti, mint az Műveletek lapon.

![Get Ügyfelek/Részletek három időtartam-csúcs 7 nap alatt egy időablakkal](./media/web-monitor-performance/SlowDependencies95thTrend.png)

A teljesítményvizsgálati tapasztalat releváns elemzéseket mutat a mintakészlet mellett, amelyre úgy döntött, hogy összpontosít. A legjobb módja annak, hogy nézd meg az összes rendelkezésre álló insights, hogy váltson egy 30 napos időtartományban, és válassza az Általános az összes művelet az elmúlt hónapban.

![Get Ügyfelek/Részletek három időtartam-csúcs 7 nap alatt egy időablakkal](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>További lépések
[Webes tesztek][availability] - Webes kéréseket küld az alkalmazás rendszeres időközönként a világ minden tájáról.

[Diagnosztikai nyomkövetések rögzítése és keresése][diagnostic] – Nyomkövetési hívások beszúrása és az eredmények átnézése a problémák azonosításához.

[Használatkövetés][usage] – Megtudhatja, hogy az emberek hogyan használják az alkalmazást.

[Hibaelhárítás][qna] - és Q & A



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



