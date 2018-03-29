---
title: 'Application Insights: nyelvek, platformok és integrációk| Microsoft Docs'
description: Az Application Insightshoz elérhető nyelvek, platformok és integrációk
services: application-insights
documentationcenter: ''
author: OlegAnaniev-MSFT
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mbullwin
ms.openlocfilehash: e172b04144afec04579de92b2393587635d3fecf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Fejlesztői elemzések: nyelvek, platformok és integrációk
Ezen elemek az [Application Insights](app-insights-overview.md) azon megvalósításai, amelyekről hallottunk, beleértve néhány harmadik fél által létrehozottat.

## <a name="languages---officially-supported-by-application-insights-team"></a>Az Application Insights csapata által hivatalosan támogatott nyelvek
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [JavaScript-weblapok](app-insights-javascript.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>Közösség által támogatott nyelvek
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Bármi más](#projects)

## <a name="platforms-and-frameworks"></a>Platformok és keretrendszerek
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET – már élő alkalmazásokhoz](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center, HockeyApp)
* [Azure Web Apps](app-insights-azure-web-apps.md)
* [Azure Cloud Services](app-insights-cloudservices.md)&#151;beleértve a webes és a feldolgozói szerepköröket
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center, HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE – már élő alkalmazásokhoz](app-insights-java-live.md)
* [macOS-alkalmazás](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Univerzális Windows-alkalmazás](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (App Center, HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Windows Phone 8- és 8.1-alkalmazás](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Windows megjelenítési alaprendszer alkalmazás](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Asztali Windows-alkalmazások, szolgáltatások és feldolgozói szerepkörök](app-insights-windows-desktop.md)
* [Bármi más](#projects)

## <a name="logging-frameworks"></a>Naplózási keretrendszerek
* [Log4Net, NLog, vagy System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J, vagy Logback](app-insights-java-trace-logs.md)
* [Szemantikus naplózás (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) – integrálható a [szemantikus naplózási alkalmazásblokkal](https://msdn.microsoft.com/library/dn440729.aspx)
* [Felhőalapú terheléses tesztelés](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash beépülő modul](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)

## <a name="content-management-systems"></a>Tartalomkezelő rendszerek
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Exportálás és adatelemzés
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Saját SDK kialakítása
Ha még nem készült SDK az Ön nyelvéhez vagy platformjához, akár Ön is létrehozhat egyet. Tekintse meg a meglévő SDK-k kódjainak listáját a [GitHubon elérhető Application Insights SDK-projektben](https://github.com/Microsoft/AppInsights-Home).
