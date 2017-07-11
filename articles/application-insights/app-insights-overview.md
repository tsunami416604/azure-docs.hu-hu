---
title: Mi az Azure Application Insights? | Microsoft Docs
description: "Alkalmazásteljesítmény-felügyelet és élő webalkalmazások használatának nyomon követése.  Észlelheti, osztályozhatja és diagnosztizálhatja a problémákat, valamint megismerheti, hogy a felhasználók miként használják alkalmazását."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: e1915f9333033a98d4d3a75cf404d285045c75ce
ms.contentlocale: hu-hu
ms.lasthandoff: 05/19/2017

---
<a id="what-is-application-insights" class="xliff"></a>

# Mi az Application Insights?
Az Application Insights egy bővíthető és több platformon működő alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek. Az élő webalkalmazásának figyelésére használhatja. Automatikusan felismeri a teljesítményanomáliákat. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást.  Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Sokféle platform alkalmazásaival együttműködik, ilyen többek között a .NET, a Node.js és a J2EE platform helyszíni vagy felhőbeli tárolás esetén is. Emellett a devOps folyamattal is integrálható, és eszközök széles köréhez rendelkezik kapcsolódási ponttal.

![Felhasználói tevékenységek statisztikáit ábrázolhatja diagramon, vagy konkrét eseményeket elemezhet.](./media/app-insights-overview/00-sample.png)

[Tekintse meg a bevezető animációt](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

<a id="how-does-application-insights-work" class="xliff"></a>

## Hogyan működik az Application Insights?
Ön telepít egy kis méretű kialakítási csomagot az alkalmazásban, és beállít egy Application Insights-erőforrást a Microsoft Azure Portalon. A kialakítási csomag figyeli az alkalmazást, és telemetriai adatokat küld a portálnak. (Az alkalmazás bárhol futhat – nem kötelező az Azure-ban üzemeltetni.)

Ráadásul nem csak webszolgáltatási alkalmazásokat vizsgálhat, hanem háttérösszetevőket és weboldalak JavaScript-kódjait is. 

![Az alkalmazásban telepített Application Insights-csomag telemetriai adatokat küld az Application Insights-erőforrásnak.](./media/app-insights-overview/01-scheme.png)


Emellett telemetriai adatok olvashatók be a gazdakörnyezetekből, például teljesítményszámlálók, Azure-beli diagnosztikai adatok vagy Docker-naplók. Olyan webes teszteket is beállíthat, amelyek rendszeres időközönként szintetikus kéréseket küldenek a webszolgáltatásnak.

A telemetriai adatok integrálása az Azure Portalon történik, ahol hatékony elemzési és keresési eszközöket alkalmazhat a nyers adatokra.


<a id="whats-the-overhead" class="xliff"></a>

### Milyen mértékű a többletterhelés?
Az alkalmazásteljesítményre gyakorolt hatás rendkívül kicsi. A nem blokkoló nyomkövetési hívásokat a rendszer kötegeli, és a küldés külön szálakon történik.

<a id="what-does-application-insights-monitor" class="xliff"></a>

## Mit figyel az Application Insights?

A fejlesztőcsapatoknak készült Application Insights segít az adott alkalmazás teljesítményének megismerésében, valamint abban, hogy a felhasználók miként használják az alkalmazást. A szolgáltatás az alábbiakat figyeli:

* **Kérések sebessége, válaszidők és hibaarányok** – megtudhatja, hogy mely lapok, mely napszakokban a legnépszerűbbek, és hol találhatók a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha több kérés esetén a válaszidők és a hibaarányok értéke megnő, valószínűleg erőforrás-gazdálkodási hibáról van szó. 
* **Függőségi értékek, válaszidők és hibaarányok** – megtudhatja, hogy mely külső szolgáltatások okoznak lassulást.
* **Kivételek** – elemezheti az összesített statisztikákat, vagy konkrét példányok kijelölése után részletesen tekintheti meg a hívásláncot és a kapcsolódó kéréseket. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.
* **Lapmegtekintések és betöltési teljesítmény** – a felhasználói böngészők jelentése alapján készül.
* Weblapokról származó **AJAX-hívások** – értékek, válaszidők és hibaarányok.
* **Felhasználók és munkamenetek száma**.
* Windows vagy Linux rendszerű kiszolgálói gépekről származó **teljesítményszámlálók**, például processzor-, memória- és hálózathasználat. 
* Dockerből vagy Azure-ból származó **gazdadiagnosztika**. 
* Alkalmazásból származó **nyomkövetési naplók diagnosztikája** – megállapíthatja a nyomkövetési események és a kérések korrelációját.
* **Egyéni események és metrikák**, amelyeket saját kezűleg helyezhet el az ügyfél vagy a kiszolgáló kódjában, és ezáltal üzleti eseményeket követhet (például eladott tételek vagy megnyert játékok).

<a id="where-do-i-see-my-telemetry" class="xliff"></a>

## Hol láthatók a telemetriai adatok?

Az adatokat többféle módon lehet böngészni. Olvassa el az alábbi cikkeket:

|  |  |
| --- | --- |
| [**Intelligens észlelés és manuális riasztások**](app-insights-proactive-diagnostics.md)<br/>Az automatikus riasztások alkalmazkodnak az alkalmazás normál telemetriai mintáihoz, és a riasztásokat a szokásos mintán kívüli események váltják ki. [Riasztásokat is beállíthat](app-insights-alerts.md) egyéni vagy Standard mérőszámok adott szintjeire. |![Példa a riasztásokra](./media/app-insights-overview/alerts-tn.png) |
| [**Alkalmazástérkép**](app-insights-app-map.md)<br/>Az alkalmazás összetevői, alapvető metrikákkal és riasztásokkal. |![Alkalmazástérkép](./media/app-insights-overview/appmap-tn.png)  |
| [**Profilkészítő**](app-insights-profiler.md)<br/>A mintavételezett kérelmek végrehajtási profiljainak vizsgálata. |![Profilkészítő](./media/app-insights-overview/profiler.png) |
| [**Használatelemzés**](app-insights-usage-overview.md)<br/>Felhasználószegmentálás és -megtartás elemzése.|![Megtartási eszköz](./media/app-insights-overview/retention.png) |
| [**Példányadatok diagnosztikai keresése**](app-insights-diagnostic-search.md)<br/>Események keresése és szűrése, például kérések, kivételek, függőségi hívások, naplókivonatok és lapmegtekintések.  |![Telemetriai adatok keresése](./media/app-insights-overview/search-tn.png) |
| [**Összesített adatok metrikaböngészője**](app-insights-metrics-explorer.md)<br/>Összesített adatok – például kérés- és hibaarányok, valamint kivételek, válaszidők és lapbetöltési idők – böngészése, szűrése és szegmentálása. |![Mérőszámok](./media/app-insights-overview/metrics-tn.png) |
| [**Irányítópultok**](app-insights-dashboards.md#dashboards)<br/>Különböző erőforrásokból származó adatokat fűzhet össze és oszthat meg másokkal. Kiváló megoldás több összetevőből álló alkalmazások, valamint csoportszobában való folyamatos megjelenítés esetén. |![Példa az irányítópultokra](./media/app-insights-overview/dashboard-tn.png) |
| [**Élő metrikastream**](app-insights-live-stream.md)<br/>Új buildverzió telepítésekor közel valós idejű teljesítménymutatókat figyelhet, és meggyőződhet arról, hogy minden megfelelő módon működik-e. |![Példa a valós idejű metrikákra](./media/app-insights-overview/live-metrics-tn.png) |
| [**Elemzés**](app-insights-analytics.md)<br/>A hatékony lekérdezési nyelvnek köszönhetően válaszokat kaphat az alkalmazás teljesítményére és használatára vonatkozó legégetőbb kérdésekre. |![Példa az elemzésre](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](app-insights-visual-studio.md)<br/>A kódban tekintheti meg a teljesítményadatokat. A hívásláncokból a kódba ugorhat.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Pillanatkép-hibakereső**](app-insights-snapshot-debugger.md)<br/>A működés közbeni műveletekről készült pillanatképek hibakeresése paraméterértékekkel.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](app-insights-export-power-bi.md)<br/>Integrálhatja a használati metrikákat más üzleti intelligenciával.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Kód írásával lekérdezéseket futtathat a metrikákhoz és a nyers adatokhoz kapcsolódóan.| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**Folyamatos exportálás**](app-insights-export-telemetry.md)<br/>Az adatok beérkezésével párhuzamosan végezheti a nyers adatok tömeges exportálását a tárba. |![Exportálás](./media/app-insights-overview/export-tn.png) |

<a id="how-do-i-use-application-insights" class="xliff"></a>

## Hogyan használható az Application Insights?

<a id="monitor" class="xliff"></a>

### Figyelés
Telepítse az Application Insightsot az alkalmazásba, állítsa be a [rendelkezésre állási webes teszteket](app-insights-monitor-web-app-availability.md), és az alábbiakra nyílik lehetőség:

* Beállíthat egy [irányítópultot](app-insights-dashboards.md) a csoportszobában, és nyomon követheti a terhelést, a válaszidőt, a függőségek teljesítményét, a lapbetöltéseket és az AJAX-hívásokat.
* Megismerheti a leglassabb és a legsikertelenebb kéréseket.
* Figyelheti az [élő streamet](app-insights-live-stream.md) új kiadások telepítésekor, és azonnal értesülhet bármilyen teljesítménycsökkenésről.

<a id="detect-diagnose" class="xliff"></a>

### Észlelés, diagnosztizálás
Riasztások fogadásakor vagy problémák észlelésekor:

* Felmérheti, hogy hány felhasználó érintett.
* Elvégezheti a kivételek, a függőségi hívások és a nyomkövetési adatok korrelációját.
* A profilkészítő, a pillanatképek, a veremkiíratások és a nyomkövetési naplók vizsgálata.

<a id="build-measure-learn" class="xliff"></a>

### Fejlesztés, mérés, tapasztalatszerzés
Az újonnan üzembe helyezett szolgáltatások [hatékonyságának mérése](app-insights-usage-overview.md).

* Megtervezheti az új felhasználói felület vagy üzleti szolgáltatások ügyfelek általi használatának mérését.
* Egyéni telemetriai adatokat vehet fel a kódba.
* A következési fejlesztési ciklust a telemetriai adatokból származó konkrét tényekre alapozhatja.

<a id="get-started" class="xliff"></a>

## Bevezetés
Az Application Insights a Microsoft Azure-ban üzemeltetett számos szolgáltatás egyike, és a telemetriai adatok ide érkeznek be elemzésre és megjelenítésre. A használat megkezdéséhez egy [Microsoft Azure-előfizetésre](http://azure.com) van szükség. A regisztráció ingyenes, és ha az Application Insights alapszintű [díjcsomagját](https://azure.microsoft.com/pricing/details/application-insights/) választja, a használat mindaddig díjmentes, amíg az alkalmazás el nem ér egy jelentős használati értéket. Ha a munkahelye már rendelkezik előfizetéssel, lehetőség van felvenni az Ön Microsoft-fiókját az adott előfizetésbe.

Az első lépések megtételének számos módja van. Kezdje azzal, amelyik Önnek a legmegfelelőbb. További szolgáltatásokat később is felvehet.

* **Futtatáskor: webalkalmazás vizsgálata a kiszolgálón.** Elkerülheti a kódfrissítéseket. Adminisztrátori hozzáféréssel kell rendelkeznie a kiszolgálón.
  * [**IIS a helyszínen vagy egy virtuális gépen**](app-insights-monitor-performance-live-website-now.md)
  * [**Azure-webalkalmazás vagy virtuális gép**](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **Fejlesztéskor: az Application Insights hozzáadása a kódhoz.** Egyéni telemetriai adatok használatát, valamint háttérbeli szolgáltatások és asztali alkalmazások vizsgálatát teszi lehetővé.
  * [Visual Studio](app-insights-asp-net.md) 2013 2. frissítés vagy újabb.
  * Java és [Eclipse](app-insights-java-eclipse.md) vagy [más eszközök](app-insights-java-get-started.md)
  * [Node.js](app-insights-nodejs.md)
  * [Más platformok](app-insights-platforms.md)
* **[Vizsgálhatja a weblapokat](app-insights-javascript.md)** lapmegtekintés, AJAX-használat és egyéb ügyféloldali telemetria tekintetében.
* **[Rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md)** – rendszeresen pingelheti webhelyét kiszolgálóinkról.


<a id="next-steps" class="xliff"></a>

## Következő lépések
Első lépések futtatáskor:

* [IIS-kiszolgáló](app-insights-monitor-performance-live-website-now.md)
* [J2EE-kiszolgáló](app-insights-java-live.md)

Első lépések fejlesztéskor:

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

<a id="support-and-feedback" class="xliff"></a>

## Támogatás és visszajelzés
* Kérdések és problémák:
  * [Hibaelhárítás][qna]
  * [MSDN-fórum](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Javaslatok:
  * [UserVoice-on](https://visualstudio.uservoice.com/forums/357324)
* Blog:
  * [Application Insights blog](https://azure.microsoft.com/blog/tag/application-insights)

<a id="videos" class="xliff"></a>

## Videók

[![Animált bevezetés](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md

