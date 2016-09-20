<properties
    pageTitle="Ismerkedés a Visual Studio Application Insights eszközzel | Microsoft Azure"
    description="A Visual Studio Application Insights segítségével elemezheti a használati adatokat, a rendelkezésre állást és a teljesítményt a helyszíni vagy Microsoft Azure webalkalmazásán."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="06/06/2016"
    ms.author="awills"/>

# Ismerkedés a Visual Studio Application Insights eszközzel

*Az Application Insights jelenleg még előzetes verziójú kiadásban érhető el.*

Hibákat észlelhet, problémákat oldhat meg és folyamatosan fejlesztheti az alkalmazásait. Gyorsan diagnosztizálhat bármilyen problémát az élő alkalmazással. Megismerheti, hogyan használják a felhasználók az alkalmazást.

A konfiguráció nagyon egyszerű, és perceken belül láthatja az eredményeket.

Jelenleg az iOS-, Android- és Windows-alkalmazásokat, a J2EE- és az ASP.NET.webalkalmazásokat és a WCF szolgáltatásokat támogatjuk. A webalkalmazások az Azure-ban vagy a saját helyszíni kiszolgálóin futtathatók. A JavaScript SDK bármilyen weblapon fut.

[Tekintse meg a bevezető animációt](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## Első lépések

A következő diagramon megjelenő belépési pontok bármilyen kombinációjával kezdhet, bármilyen sorrendben. Kövesse az Önnek legmegfelelőbb útvonalat.

Az Application Insights SDK-t ad az alkalmazáshoz, amely telemetriát küld az [Azure Portalnak](https://portal.azure.com). Különböző SDK-k léteznek a támogatott platformok, nyelvek és IDE-k különböző kombinációihoz.

Ehhez szüksége lesz egy [Microsoft Azure](http://azure.com)-fiókra. Lehet, hogy már hozzáfér egy csoportfiókhoz a szervezetén keresztül, vagy esetleg használatalapú fizetéssel rendelkező fiókot szeretne beszerezni. Az Application Insights rendelkezik egy ingyenes szinttel, így nem kell fizetnie, amíg az alkalmazása nem válik népszerűvé. Tekintse meg a [díjszabási lapot](https://azure.microsoft.com/pricing/details/application-insights/).

Amire szüksége van | Teendő | Amihez jut
---|---|---
 <a href="app-insights-asp-net.md">![ASP.NET](./media/app-insights-get-started/appinsights-gs-i-01-perf.png)</a> | <a href="app-insights-asp-net.md">Adja az Application Insights SDK-t a webes projekthez</a> <br/> ![ehhez jut:](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-asp-net.md">![Teljesítmény- és használatfigyelés](./media/app-insights-get-started/appinsights-gs-r-01-perf.png)</a>
<a href="app-insights-monitor-performance-live-website-now.md">![Már élő ASP.NET-hely](./media/app-insights-get-started/appinsights-gs-i-04-red2.png)</a><br/><a href="app-insights-monitor-performance-live-website-now.md">![Függőség- és teljesítményfigyelés](./media/app-insights-get-started/appinsights-gs-i-03-red.png)</a>|<a href="app-insights-monitor-performance-live-website-now.md">Telepítse az állapotfigyelőt az IIS-kiszolgálóra</a> <br/> ![ehhez jut:](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-performance-live-website-now.md">![ASP.NET-függőségfigyelés](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="insights-perf-analytics.md">![Azure-webalkalmazás vagy virtuális gép](./media/app-insights-get-started/appinsights-gs-i-10-azure.png)</a>|<a href="insights-perf-analytics.md">Engedélyezze az Insights eszközt az Azure-webalkalmazásban vagy virtuális gépen</a> <br/> ![ehhez jut:](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="insights-perf-analytics.md">![Függőség- és teljesítményfigyelés](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="app-insights-java-get-started.md">![Java](./media/app-insights-get-started/appinsights-gs-i-11-java.png)</a>|<a href="app-insights-java-get-started.md">Adja az SDK-t a Java-projekthez</a><br/>![ehhez jut:](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-java-get-started.md">![Teljesítmény- és használatfigyelés](./media/app-insights-get-started/appinsights-gs-r-10-java.png)</a>
<a href="app-insights-web-track-usage.md">![JavaScript](./media/app-insights-get-started/appinsights-gs-i-02-usage.png)</a>|<a href="app-insights-web-track-usage.md">Szúrja be az Application Insights eszközt a weblapokra</a><br/>![ehhez jut:](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-web-track-usage.md">![lapmegtekintések és böngészőteljesítmény](./media/app-insights-get-started/appinsights-gs-r-02-usage.png)</a>
<a href="app-insights-monitor-web-app-availability.md">![Rendelkezésre állás](./media/app-insights-get-started/appinsights-gs-i-05-avail.png)</a>|<a href="app-insights-monitor-web-app-availability.md">Hozzon létre webes teszteket</a><br/>![ehhez jut:](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-web-app-availability.md">![Rendelkezésre állás](./media/app-insights-get-started/appinsights-gs-r-05-avail.png)</a>
<a href="app-insights-platforms.md">![iOS, Android és Windows rendszerű eszközök](./media/app-insights-get-started/appinsights-gs-i-07-device.png)</a>|<a href="http://hockeyapp.net">Használja a HockeyApp-ot</a><br/>![ehhez jut:](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="http://hockeyapp.net">![Összeomlási és használati adatok](./media/app-insights-get-started/appinsights-gs-r-06-device.png)</a>

## Támogatás és visszajelzés


* Kérdések és problémák:
 * [Hibaelhárítás][qna]
 * [MSDN fórum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Javaslatok:
 * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Kódminták
 * [Kódminták](app-insights-code-samples.md)



## <a name="video"></a>Videó

[![Aanimált bevezetés](./media/app-insights-get-started/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]



<!--Link references-->

[qna]: app-insights-troubleshoot-faq.md



<!--HONumber=sep16_HO1-->


