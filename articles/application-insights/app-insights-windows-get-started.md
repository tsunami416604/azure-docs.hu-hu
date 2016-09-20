<properties
    pageTitle="Elemzés Windows Phone és Windows Áruházból származó alkalmazásokhoz | Microsoft Azure"
    description="Elemezheti a Windows eszköze alkalmazásának használati adatait és összeomlásait."
    services="application-insights"
    documentationCenter="windows"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/26/2016"
    ms.author="awills"/>

# Elemzés Windows Phone és Windows Áruházból származó alkalmazásokhoz

A Microsoft két megoldást kínál eszközfejlesztőknek és -üzemeltetőknek: [HockeyApp](http://hockeyapp.net/) ügyféloldali elemezésekhez és [Application Insights](app-insights-overview.md) a kiszolgálóoldalhoz.

A [HockeyApp](http://hockeyapp.net/) a Mobile DevOps megoldásunk iOS, OS X, Android vagy Windows rendszert futtató eszközök alkalmazásaihoz, de működik a Xamarin-, Cordova- és Unity-alapú platformfüggetlen alkalmazásokkal is. A segítségével kioszthatja a buildeket a bétatesztelők között, begyűjtheti az összeomlási adatokat, és lekérheti a felhasználói mérőszámokat és visszajelzéseket. Integrálva van a Visual Studio Team Services szolgáltatással, így egyszerű buildtelepítést és munkaelem-integrációt tesz lehetővé. 

## Ismerkedés a HockeyApp alkalmazással

Ugrás:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp Windows rendszeren](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [HockeyApp blog](http://hockeyapp.net/blog/)
* A korai kiadásokhoz való hozzáférésért csatlakozzon a [HockeyApp Preseason](http://hockeyapp.net/preseason/) programhoz.

Ha az alkalmazása kiszolgálóoldali, az [Application Insights](app-insights-overview.md) segítségével megfigyelheti az alkalmazás webkiszolgálói oldalát az [ASP.NET](app-insights-asp-net.md) vagy [J2EE](app-insights-java-get-started.md) platformon. 


A [Windows asztali alkalmazásokhoz készült Application Insights](app-insights-windows-desktop.md) szolgáltatást is használhatja.

## HockeyApp-adatok elemzése, exportálása és API-k általi elérése 

[Állítson be egy HockeyApp-hidat](app-insights-hockeyapp-bridge-app.md) az Application Insightsban. Ezzel a következőket teheti:

* Használhatja a hatékony [Analytics](app-insights-analytics.md) lekérdezési nyelvet a telemetriára vonatkozóan. 
* [Exportálhatja a telemetriát](app-insights-export-telemetry.md) az Azure Blob Storage-ba.

## Következő lépések

* [Ismerkedés a Windows rendszerre készült HockeyApp alkalmazással](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)



<!--HONumber=sep16_HO1-->


