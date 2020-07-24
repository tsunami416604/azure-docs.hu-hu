---
title: 'Application Insights: nyelvek, platformok és integrációk | Microsoft Docs'
description: A Application Insights számára elérhető nyelvek, platformok és integrációk
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: a5403162a2511862dd1c649dc273a35a550abaaf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024282"
---
# <a name="supported-languages"></a>Támogatott nyelvek

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Támogatott platformok és keretrendszerek

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Már üzembe helyezett alkalmazások rendszerállapotba állítása (kód nélküli, ügynök-alapú)
* [Azure-beli virtuális gépek és Azure-beli virtuálisgép-méretezési csoportok](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – már élő alkalmazásokhoz](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure-Cloud Services](../../azure-monitor/app/cloudservices.md), beleértve a webes és a feldolgozói szerepköröket is
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Rendszerállapot-kódok (SDK-k)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Univerzális Windows-alkalmazás](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Asztali Windows-alkalmazások, szolgáltatások és feldolgozói szerepkörök](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Naplózási keretrendszerek
* [ILogger](./ilogger.md)
* [Log4Net, NLog, vagy System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J, vagy Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash beépülő modul](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Exportálás és adatelemzés
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Nem támogatott SDK-k
Tisztában vagyunk azzal, hogy számos más Közösség által támogatott SDK létezik. A Azure Monitor azonban csak az ezen a lapon felsorolt támogatott SDK-k használata esetén nyújt támogatást. Folyamatosan kivizsgáljuk, hogyan bővítjük támogatását más nyelvekre, ezért kövesse a [GitHub-közlemények](https://github.com/microsoft/ApplicationInsights-Announcements/issues) oldalon a legújabb SDK-híreket. 
