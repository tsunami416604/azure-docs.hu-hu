<properties
    pageTitle="Elemzés Android-alkalmazásokhoz | Microsoft Azure"
    description="Az Android-alkalmazás használatának és teljesítményének elemzése."
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/26/2016"
    ms.author="awills"/>

# Elemzés Android-alkalmazásokhoz

A Microsoft két megoldást kínál eszközfejlesztőknek és -üzemeltetőknek: [HockeyApp](http://hockeyapp.net/) ügyféloldali elemezésekhez és [Application Insights](app-insights-overview.md) a kiszolgálóoldalhoz.

A [HockeyApp](http://hockeyapp.net/) a Mobile DevOps megoldásunk iOS, OS X, Android vagy Windows rendszert futtató eszközök alkalmazásaihoz, de működik a Xamarin-, Cordova- és Unity-alapú platformfüggetlen alkalmazásokkal is. A segítségével eloszthatja a buildeket a bétatesztelők között, begyűjtheti az összeomlási adatokat, és lekérheti a felhasználói visszajelzéseket. Integrálva van a Visual Studio Team Services szolgáltatással, így egyszerű buildtelepítést és munkaelem-integrációt tesz lehetővé.

Ugrás:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [Ismerkedés az Android rendszerhez készült HockeyApp alkalmazással](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk).
* [HockeyApp blog](http://hockeyapp.net/blog/)
* A korai kiadásokhoz való hozzáférésért csatlakozzon a [HockeyApp Preseason](http://hockeyapp.net/preseason/) programhoz.

Ha az alkalmazása kiszolgálóoldali, az [Application Insights](app-insights-overview.md) segítségével megfigyelheti az alkalmazás webkiszolgálói oldalát az [ASP.NET](app-insights-asp-net.md) vagy [J2EE](app-insights-java-get-started.md) platformon. 


## HockeyApp-adatok elemzése, exportálása és API-k általi elérése 

[Állítson be egy HockeyApp-hidat](app-insights-hockeyapp-bridge-app.md) az Application Insightsban. Ezzel a következőket teheti:

* Használhatja a hatékony [Analytics](app-insights-analytics.md) lekérdezési nyelvet a telemetriára vonatkozóan. 
* [Exportálhatja a telemetriát](app-insights-export-telemetry.md) az Azure Blob Storage-ba.

## Következő lépések

* [Ismerkedés az Android rendszerhez készült HockeyApp alkalmazással](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)




<!--HONumber=sep16_HO1-->


