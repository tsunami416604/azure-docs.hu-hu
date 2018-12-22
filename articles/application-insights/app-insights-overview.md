---
title: Mi az Azure Application Insights? | Microsoft Docs
description: Alkalmazásteljesítmény-felügyelet és élő webalkalmazások használatának nyomon követése.  Észlelheti, osztályozhatja és diagnosztizálhatja a problémákat, valamint megismerheti, hogy a felhasználók miként használják alkalmazását.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: overview
ms.custom: mvc
ms.date: 09/06/2018
ms.author: mbullwin
ms.openlocfilehash: d5279391c4f85411e592ce1d071fb1743cd135d5
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744122"
---
# <a name="what-is-application-insights"></a>Mi az Application Insights?
Az Application Insights egy bővíthető és több platformon működő alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek. Az élő webalkalmazásának figyelésére használhatja. Automatikusan felismeri a teljesítményanomáliákat. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást.  Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Sokféle platform alkalmazásaival együttműködik, ilyen többek között a .NET, a Node.js és a J2EE platform helyszíni vagy felhőbeli tárolás esetén is. Emellett a DevOps folyamattal is integrálható, és eszközök széles köréhez rendelkezik kapcsolódási ponttal. Képes monitorozni és elemezni a mobilalkalmazások telemetriáját a Visual Studio App Center integrálásával.

[Tekintse meg a bevezető animációt](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## <a name="how-does-application-insights-work"></a>Hogyan működik az Application Insights?
Ön telepít egy kis méretű kialakítási csomagot az alkalmazásban, és beállít egy Application Insights-erőforrást a Microsoft Azure Portalon. A kialakítási csomag figyeli az alkalmazást, és telemetriai adatokat küld a portálnak. (Az alkalmazás bárhol futhat – nem kötelező az Azure-ban üzemeltetni.)

Ráadásul nem csak webszolgáltatási alkalmazásokat vizsgálhat, hanem háttérösszetevőket és weboldalak JavaScript-kódjait is. 

![Az alkalmazásban telepített Application Insights-csomag telemetriai adatokat küld az Application Insights-erőforrásnak.](./media/app-insights-overview/01-scheme.png)

Emellett telemetriai adatok olvashatók be a gazdakörnyezetekből, például teljesítményszámlálók, Azure-beli diagnosztikai adatok vagy Docker-naplók. Olyan webes teszteket is beállíthat, amelyek rendszeres időközönként szintetikus kéréseket küldenek a webszolgáltatásnak.

A telemetriai adatok integrálása az Azure Portalon történik, ahol hatékony elemzési és keresési eszközöket alkalmazhat a nyers adatokra.


### <a name="whats-the-overhead"></a>Milyen mértékű a többletterhelés?
Az alkalmazásteljesítményre gyakorolt hatás rendkívül kicsi. A nem blokkoló nyomkövetési hívásokat a rendszer kötegeli, és a küldés külön szálakon történik.

## <a name="what-does-application-insights-monitor"></a>Mit figyel az Application Insights?

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

## <a name="where-do-i-see-my-telemetry"></a>Hol láthatók a telemetriai adatok?

Az adatokat többféle módon lehet böngészni. Olvassa el az alábbi cikkeket:

|  |  |
| --- | --- |
| [**Intelligens észlelés és manuális riasztások**](app-insights-proactive-diagnostics.md)<br/>Az automatikus riasztások alkalmazkodnak az alkalmazás normál telemetriai mintáihoz, és a riasztásokat a szokásos mintán kívüli események váltják ki. [Riasztásokat is beállíthat](../azure-monitor/app/alerts.md) egyéni vagy Standard mérőszámok adott szintjeire. |![Példa a riasztásokra](./media/app-insights-overview/alerts-tn.png) |
| [**Alkalmazástérkép**](../azure-monitor/app/app-map.md)<br/>Az alkalmazás összetevői, alapvető metrikákkal és riasztásokkal. |![Alkalmazástérkép](./media/app-insights-overview/appmap-tn.png)  |
| [**Profilkészítő**](app-insights-profiler.md)<br/>A mintavételezett kérelmek végrehajtási profiljainak vizsgálata. |![Profilkészítő](./media/app-insights-overview/profiler.png) |
| [**Használatelemzés**](app-insights-usage-overview.md)<br/>Felhasználószegmentálás és -megtartás elemzése.|![Megtartási eszköz](./media/app-insights-overview/retention.png) |
| [**Példányadatok diagnosztikai keresése**](app-insights-diagnostic-search.md)<br/>Események keresése és szűrése, például kérések, kivételek, függőségi hívások, naplókivonatok és lapmegtekintések.  |![Telemetriai adatok keresése](./media/app-insights-overview/search-tn.png) |
| [**Összesített adatok metrikaböngészője**](app-insights-metrics-explorer.md)<br/>Összesített adatok – például kérés- és hibaarányok, valamint kivételek, válaszidők és lapbetöltési idők – böngészése, szűrése és szegmentálása. |![Mérőszámok](./media/app-insights-overview/metrics-tn.png) |
| [**Irányítópultok**](../azure-monitor/app/app-insights-dashboards.md#dashboards)<br/>Különböző erőforrásokból származó adatokat fűzhet össze és oszthat meg másokkal. Kiváló megoldás több összetevőből álló alkalmazások, valamint csoportszobában való folyamatos megjelenítés esetén. |![Példa az irányítópultokra](./media/app-insights-overview/dashboard-tn.png) |
| [**Élő metrikastream**](app-insights-live-stream.md)<br/>Új buildverzió telepítésekor közel valós idejű teljesítménymutatókat figyelhet, és meggyőződhet arról, hogy minden megfelelő módon működik-e. |![Példa a valós idejű metrikákra](./media/app-insights-overview/live-metrics-tn.png) |
| [**Elemzés**](../azure-monitor/app/analytics.md)<br/>A hatékony lekérdezési nyelvnek köszönhetően válaszokat kaphat az alkalmazás teljesítményére és használatára vonatkozó legégetőbb kérdésekre. |![Példa az elemzésre](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](app-insights-visual-studio.md)<br/>A kódban tekintheti meg a teljesítményadatokat. A hívásláncokból a kódba ugorhat.|![Visual Studio](./media/app-insights-overview/visual-studio-tn.png) |
| [**Pillanatkép-hibakereső**](app-insights-snapshot-debugger.md)<br/>A működés közbeni műveletekről készült pillanatképek hibakeresése paraméterértékekkel.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](app-insights-export-power-bi.md)<br/>Integrálhatja a használati metrikákat más üzleti intelligenciával.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Kód írásával lekérdezéseket futtathat a metrikákhoz és a nyers adatokhoz kapcsolódóan.| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**Folyamatos exportálás**](app-insights-export-telemetry.md)<br/>Az adatok beérkezésével párhuzamosan végezheti a nyers adatok tömeges exportálását a tárba. |![Exportálás](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Hogyan használható az Application Insights?

### <a name="monitor"></a>Figyelés
Telepítse az Application Insightsot az alkalmazásba, állítsa be a [rendelkezésre állási webes teszteket](app-insights-monitor-web-app-availability.md), és az alábbiakra nyílik lehetőség:

* Beállíthat egy [irányítópultot](../azure-monitor/app/app-insights-dashboards.md) a csoportszobában, és nyomon követheti a terhelést, a válaszidőt, a függőségek teljesítményét, a lapbetöltéseket és az AJAX-hívásokat.
* Megismerheti a leglassabb és a legsikertelenebb kéréseket.
* Figyelheti az [élő streamet](app-insights-live-stream.md) új kiadások telepítésekor, és azonnal értesülhet bármilyen teljesítménycsökkenésről.

### <a name="detect-diagnose"></a>Észlelés, diagnosztizálás
Riasztások fogadásakor vagy problémák észlelésekor:

* Felmérheti, hogy hány felhasználó érintett.
* Elvégezheti a kivételek, a függőségi hívások és a nyomkövetési adatok korrelációját.
* A profilkészítő, a pillanatképek, a veremkiíratások és a nyomkövetési naplók vizsgálata.

### <a name="build-measure-learn"></a>Fejlesztés, mérés, tapasztalatszerzés
Az újonnan üzembe helyezett szolgáltatások [hatékonyságának mérése](app-insights-usage-overview.md).

* Megtervezheti az új felhasználói felület vagy üzleti szolgáltatások ügyfelek általi használatának mérését.
* Egyéni telemetriai adatokat vehet fel a kódba.
* A következési fejlesztési ciklust a telemetriai adatokból származó konkrét tényekre alapozhatja.

## <a name="get-started"></a>Bevezetés
Az Application Insights a Microsoft Azure-ban üzemeltetett számos szolgáltatás egyike, és a telemetriai adatok ide érkeznek be elemzésre és megjelenítésre. A használat megkezdéséhez egy [Microsoft Azure-előfizetésre](https://azure.com) van szükség. A regisztráció ingyenes, és ha az Application Insights alapszintű [díjcsomagját](https://azure.microsoft.com/pricing/details/application-insights/) választja, a használat mindaddig díjmentes, amíg az alkalmazás el nem ér egy jelentős használati értéket. Ha a munkahelye már rendelkezik előfizetéssel, lehetőség van felvenni az Ön Microsoft-fiókját az adott előfizetésbe.

Az első lépések megtételének számos módja van. Kezdje azzal, amelyik Önnek a legmegfelelőbb. További szolgáltatásokat később is felvehet.

* **Futtatáskor: webalkalmazás vizsgálata a kiszolgálón.** Elkerülheti a kódfrissítéseket. Adminisztrátori hozzáféréssel kell rendelkeznie a kiszolgálón.
  * [**IIS a helyszínen vagy egy virtuális gépen**](app-insights-monitor-performance-live-website-now.md)
  * [**Azure-webalkalmazás vagy virtuális gép**](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **Fejlesztéskor: az Application Insights hozzáadása a kódhoz.** Egyéni telemetriai adatok használatát, valamint háttérbeli szolgáltatások és asztali alkalmazások vizsgálatát teszi lehetővé.
  * [Visual Studio](../azure-monitor/app/asp-net.md) 2013 2. frissítés vagy újabb.
  * [Java](app-insights-java-get-started.md)
  * [Node.js](app-insights-nodejs.md)
  * [Más platformok](app-insights-platforms.md)
* **[Vizsgálhatja a weblapokat](app-insights-javascript.md)** lapmegtekintés, AJAX-használat és egyéb ügyféloldali telemetria tekintetében.
* **[Elemezheti a mobilalkalmazások használatát](app-insights-mobile-center-quickstart.md)** a Visual Studio App Center integrálásával.
* **[Rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md)** – rendszeresen pingelheti webhelyét kiszolgálóinkról.


## <a name="next-steps"></a>További lépések
Első lépések futtatáskor:

* [IIS-kiszolgáló](app-insights-monitor-performance-live-website-now.md)
* [J2EE-kiszolgáló](app-insights-java-live.md)

Első lépések fejlesztéskor:

* [ASP.NET](../azure-monitor/app/asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>Támogatás és visszajelzés
* Kérdések és problémák:
  * [Hibaelhárítás][qna]
  * [MSDN-fórum](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Javaslatok:
  * [UserVoice-on](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Application Insights blog](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Videók

[![Animált bevezetés](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

<!--Link references-->

[android]: app-insights-mobile-center-quickstart.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: ../azure-monitor/app/asp-net.md
[ios]: app-insights-mobile-center-quickstart.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: app-insights-platforms.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
