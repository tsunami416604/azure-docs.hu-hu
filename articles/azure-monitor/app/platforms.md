---
title: 'Application Insights: nyelvek, platformok és integrációk| Microsoft Docs'
description: Az Application Insightshoz elérhető nyelvek, platformok és integrációk
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 0aee18115f34c86e84054f1aeddcc99d563f5f64
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116517"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Fejlesztői elemzések: nyelvek, platformok és integrációk
Ezen elemek az [Application Insights](../../azure-monitor/app/app-insights-overview.md) azon megvalósításai, amelyekről hallottunk, beleértve néhány harmadik fél által létrehozottat.

## <a name="languages---officially-supported-by-application-insights-team"></a>Az Application Insights csapata által hivatalosan támogatott nyelvek
* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript-weblapok](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="languages---community-supported"></a>Közösség által támogatott nyelvek
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Bármi más](#projects)

## <a name="platforms-and-frameworks"></a>Platformok és keretrendszerek
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET – már élő alkalmazásokhoz](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* 
  [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)&#151;beleértve a webes és a feldolgozói szerepköröket
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](../../azure-monitor/app/docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [J2EE](../../azure-monitor/app/java-get-started.md)
* [J2EE – már élő alkalmazásokhoz](../../azure-monitor/app/java-live.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [SAFE Stack](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Univerzális Windows-alkalmazás](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Asztali Windows-alkalmazások, szolgáltatások és feldolgozói szerepkörök](../../azure-monitor/app/windows-desktop.md)
* [Bármi más](#projects)

## <a name="logging-frameworks"></a>Naplózási keretrendszerek
* [Log4Net, NLog, vagy System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J, vagy Logback](../../azure-monitor/app/java-trace-logs.md)
* [Szemantikus naplózás (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) – integrálható a [szemantikus naplózási alkalmazásblokkal](https://msdn.microsoft.com/library/dn440729.aspx)
* [Felhőalapú terheléses tesztelés](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [LogStash beépülő modul](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)

## <a name="content-management-systems"></a>Tartalomkezelő rendszerek
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](../../azure-monitor/app/sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Exportálás és adatelemzés
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md )

## <a name="projects"></a> Saját SDK kialakítása
Ha még nem készült SDK az Ön nyelvéhez vagy platformjához, akár Ön is létrehozhat egyet. Tekintse meg a meglévő SDK-k kódjainak listáját a [GitHubon elérhető Application Insights SDK-projektben](https://github.com/Microsoft/AppInsights-Home).
