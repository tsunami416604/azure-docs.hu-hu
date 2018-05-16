---
title: Az alkalmazás állapotának és az Application insights szolgáltatással használatának figyelése
description: Ismerkedés az Application insights szolgáltatással. Használati, rendelkezésre állásának és a helyszíni vagy a Microsoft Azure-alkalmazások teljesítményének elemzése.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: sdash
ms.openlocfilehash: 02421492528e44ed6a913443a7793235170d4881
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="monitor-performance-in-web-applications"></a>Webalkalmazások teljesítményének monitorozása


Győződjön meg arról, hogy az alkalmazás teljesítménye, és tájékozódhat a gyorsan esetleges hibákat. [Az Application Insights] [ start] adja meg a teljesítménybeli problémák és kivételek, és és az alapvető okok diagnosztizálásában.

Az Application Insights figyelheti a Java és az ASP.NET webalkalmazások és szolgáltatások, WCF-szolgáltatások. Ezek lehetnek üzemeltethető a helyszínen, a virtuális gépek, illetve a Microsoft Azure-webhelyekre. 

Ügyféloldali Application Insights telemetria a weblapok és az eszközt, beleértve az iOS, Android és Windows Áruházbeli alkalmazások számos végre.

## <a name="setup"></a>Teljesítmény figyelése
Ha még nem még hozzáadta az Application Insights a projekthez (Ha nincs beállítva a ApplicationInsights.config), válasszon egyet az alábbi módszerrel lehet hozzálátni:

* [ASP.NET-webalkalmazások](app-insights-asp-net.md)
  * [Kivételfigyelés hozzáadása](app-insights-asp-net-exceptions.md)
  * [A függőségi figyelés felvétele](app-insights-monitor-performance-live-website-now.md)
* [J2EE webalkalmazások](app-insights-java-get-started.md)
  * [A függőségi figyelés felvétele](app-insights-java-agent.md)

## <a name="view"></a>Teljesítménymértékeket felfedezése
A [az Azure-portálon](https://portal.azure.com), keresse meg az Application Insights-erőforrást az alkalmazáshoz beállított. Az Áttekintés panel alapvető teljesítményadatokat jeleníti meg:

Kattintson a részletek megtekintéséhez, és hosszabb ideig eredmények megtekintése érdekében bármelyik olyan diagram. Kattintson például a kérések csempe, és válassza ki egy időtartományt:

![Kattintson a további adatok, és válasszon egy időtartományt](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Kattintson a diagram mely metrikák azt jeleníti meg, vagy új diagram hozzáadása, és válassza ki a metrikák kiválasztásához:

![Kattintson egy grafikonon metrikák kiválasztásához](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Törölje a jelet a metrikák** a teljes kijelölt elérhető. A metrikák sorolhatók csoportok; egy csoport minden tagja van kijelölve, csak a többi a csoportnak a tagjaira jelennek meg.

## <a name="metrics"></a>Mire azt minden középérték? Teljesítmény csempék és jelentések
Nincsenek is elérhetővé teljesítménymutatók. Kezdjük azokkal, amely az alkalmazás panel alapértelmezés szerint megjelenik.

### <a name="requests"></a>Kérelmek
Egy megadott időszakban fogadott HTTP-kérelmek száma. Hasonlítsa ezt össze az eredményeket egyéb jelentések megtekintéséhez a terhelés, hogyan viselkedik az alkalmazás függ.

HTTP-kérések összes GET vagy POST kérelem lapok, adatok és lemezképek tartalmazzák.

Kattintson a csempére lekérésére adott URL-címek száma.

### <a name="average-response-time"></a>Átlagos válaszidő
Írja be az alkalmazás és a válaszban visszaadott webes kérelem közötti időt méri.

A pontok megjelenítése mozgó átlagos. Ha sok kérelmet, előfordulhat, egyes, amelyek eltérnek az átlagos egy nyilvánvaló csúcs nélkül, vagy a grafikonon merítsük.

Szokatlan csúcsait keresi. Általában várt válaszideje nőtt a kérelmeket az értéke. Ha a megnövekedhet le aránytalanul nagy, előfordulhat, hogy az alkalmazás szerezze például a Processzor- vagy egy szolgáltatást, használja a kapacitás erőforrás korlátozni.

A megadott URL-címek alkalommal csempén kattintson.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Leglassabb kérelmek
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Jeleníti meg, mely kérelmek teljesítményhangolás kell.

### <a name="failed-requests"></a>Sikertelen kérések
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Kérelmek kivételt váltott ki a nem kezelt kivételek számát.

Kattintson a csempére kattintva megtekintheti az adott hibák részleteit, és válassza ki az egyes kérést a részletek megtekintéséhez. 

Egyéni ellenőrzési hibák csak egy reprezentatív minta megmarad.

### <a name="other-metrics"></a>Más metrikákkal
Hogy más metrikákkal jeleníti meg, kattintson egy grafikonon, és törölje a jelölést a rendelkezésre álló teljes megjelenítéséhez a metrikák beállítása. (I) kattintva megtekintheti az egyes metrika definíciója.

![Törölje a teljes című szakaszban láthat minden metrikák](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Bármely metrika kijelölése letiltja a többi, amelyek nem jelennek meg ugyanabban a diagramban.

## <a name="set-alerts"></a>Riasztások beállítása
Rendkívüli értékek a metrika az e-mailben értesítést kapjon, adjon hozzá egy riasztást. Ön választhatja az e-mailt küldhet a rendszergazdák, vagy az adott e-mail címekre.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Állítsa be az erőforrást, mielőtt a többi tulajdonság. Ne válassza ki a webtesztben erőforrásokat, ha a teljesítmény vagy a használati metrikák állíthatók be riasztások.

Ügyeljen arra, ügyeljen az egységeket, amelyekben azonosítóját, írja be a küszöbértéket.

*A riasztási hozzáadása gomb nem látható.* -Az Ez a csoport olyan fiókhoz, amelyhez csak olvasási hozzáféréssel? Kérje meg a fiók rendszergazdájához.

## <a name="diagnosis"></a>Problémák diagnosztizálása
Az alábbiakban néhány tippek megkereséséhez és teljesítménnyel kapcsolatos problémák diagnosztizálásához használható:

* Állítson be [webalkalmazás-tesztek] [ availability] értesítést, ha a webhely nem működik, vagy helytelenül vagy lassan válaszol. 
* Hasonlítsa össze a más metrikákkal, ha a kapcsolódó betöltése sikertelen vagy lassú válasz a kérelmek számát.
* [INSERT, és nyomkövetési utasítások keresési] [ diagnostic] érdekében a rögzítési ponthoz problémák a kódban.
* A webalkalmazás műveletet a figyelheti [metrikák adatfolyamot][livestream].
* A .net alkalmazás állapotának rögzítésére [pillanatkép hibakereső][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Keresse meg és javítsa ki a szűk keresztmetszetek a teljesítmény vizsgálat élmény

Tekintse át a lassú művelet a Web app alkalmazásban használhatja a vizsgálat teljesítményt nyújtanak. Gyorsan kiválaszthatja egy adott lassú műveletet, és használja [Profilkészítő](app-insights-profiler.md) legfelső szintű le kódot lassú műveletek miatt. A kiválasztott művelet látható gyorsan egy pillantással felmérheti milyen hibás a szolgáltatás az ügyfelek számára új időtartam-eloszlás használatával. Láthatja, hogy hány felhasználó ügyfélkapcsolati volt érintett minden lassú művelethez. A következő példában azt döntött, hogy szorosabb tekintse meg az beszerzése ügyfelek-részletek művelet élményét. A duration terjesztési láthatja, hogy vannak-e három teljesítményt. Bal szélső csúcs körülbelül 400 MS, és rendkívül rugalmas élmény jelöli. Középső csúcs körül 1.2-es s, pedig a rendszer a közepes felhasználói élményt nyújtja. Végül, az 3.6 s van egy másik kis csúcs okozhatja, hogy elégedetlen ügyfeleink 99th PERCENTILIS élmény jelölő. A szolgáltatás tízszeresének lassabb, mint a kiváló felhasználói élmény ugyanazt a műveletet. 

![GET ügyfelek/részletek három időtartama igényeiben jelentkező](./media/app-insights-web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Ahhoz, hogy a felhasználói élmények jobb logika ehhez a művelethez, azt egy nagyobb időtartomány választhat. Azt is majd is szűkítéséhez időben egy adott időpont ablakban, ha lassú volt-e a műveletet. A következő példában azt átállítását az alapértelmezett 24 órában a 7 napra időtartomány időtartománynak, és majd nagyítva közötti k a 12., Sze a 13. és a 9:47-12:47 idő ablakba. A duration terjesztési és a minta és a profiler nyomkövetések száma a jobb oldali frissítése megtörtént.

![Egy olyan időkeretet a tartomány GET ügyfelek/részletek három időtartama igényeiben jelentkező 7 napban](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

A lassú használatakor szűkítéséhez azt mellett nagyíthatja 95th és a 99th PERCENTILIS közé eső időtartamok. Ezek képviselik a felhasználó olyan műveleteket, amelyek lassú volt 4 %.

![Egy olyan időkeretet a tartomány GET ügyfelek/részletek három időtartama igényeiben jelentkező 7 napban](./media/app-insights-web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Azt is most a reprezentatív mintát, vagy nézze meg a Profilkészítő nyomkövetések gombra kattintva a minták gombra, vagy a reprezentatív szolgáltatásprofil-elemzői adat kattintva. Ebben a példában nincsenek négy-nyomkövetési adatok összegyűjtése az beszerzése ügyfelek/részleteit a az ablak és a tartomány időtartamig iránt.

Egyes esetekben a probléma nem lesz a kódban, de ahelyett, hogy a függőség a code calls. Vizsgálja meg az ilyen lassú függőségek teljesítmény osztályozás nézetben a függőségek lapon lehet váltani. Alapértelmezés szerint a teljesítmény nézet trendekkel átlagok, de mi valóban szeretné megtekinteni a 95. percentilis (vagy a 99th, abban az esetben, ha egy összetett szolgáltatás figyeli). Az alábbi példa azt összpontosít a lassú Azure BLOB-függőség, ahol PUT fabrikamaccount nevezzük. A jó fürt körülbelül 40 ms észlel, míg az azonos függőségi a lassú hívások háromszor olyan lassú, körülbelül 120 ms fürtszolgáltatás. Ezeket a hívásokat, hogy a megfelelő művelet feltétlenül lelassíthatják egyezzen számos nem veszi. A reprezentatív mintát és szolgáltatásprofil-elemzői adat, ugyanúgy, mint a Műveletek lap is tovább részletezhető.

![Egy olyan időkeretet a tartomány GET ügyfelek/részletek három időtartama igényeiben jelentkező 7 napban](./media/app-insights-web-monitor-performance/SlowDependencies95thTrend.png)

A teljesítmény vizsgálat tapasztalat mentén ügyféloldali vonatkozó elemzések a minta-készlet úgy döntött, összpontosíthat. A legjobb nézze meg az összes rendelkezésre álló feltárása módja váltson egy 30 napos időtartományt, és válassza ki a insights feltűnik az elmúlt hónapban az összes művelet átfogó.

![Egy olyan időkeretet a tartomány GET ügyfelek/részletek három időtartama igényeiben jelentkező 7 napban](./media/app-insights-web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Következő lépések
[Webalkalmazás-tesztek] [ availability] -rendelkezik a webes kérelmek világszerte a rendszeres időközönként az alkalmazás számára.

[Rögzítése, és diagnosztikai nyomkövetési keresési] [ diagnostic] - nyomkövetési hívások beszúrása és az eredményeket a rögzítési ponthoz problémák keletkezik.

[Használat nyomon követése] [ usage] -megtudhatja, hogyan személyek használhassa az alkalmazást.

[Hibaelhárítási] [ qna] - és a kérdések és válaszok



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md
[livestream]: app-insights-live-stream.md
[snapshot]: app-insights-snapshot-debugger.md



