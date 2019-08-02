---
title: 'Application Insights: nyelvek, platformok és integrációk | Microsoft Docs'
description: A Application Insights számára elérhető nyelvek, platformok és integrációk
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: b2670ec844df192bb73e8b1e76b1ebf611b2539e
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619371"
---
# <a name="supported-languages"></a>Támogatott nyelvek

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>Támogatott platformok és keretrendszerek

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Már üzembe helyezett alkalmazások rendszerállapotba állítása (kód nélküli, ügynök-alapú)
* [Azure-beli virtuális gépek és Azure-beli virtuálisgép-méretezési csoportok](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – már élő alkalmazásokhoz](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure-Cloud Services](../../azure-monitor/app/cloudservices.md), beleértve a webes és a feldolgozói szerepköröket is
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Rendszerállapot-kódok (SDK-k)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Univerzális Windows-alkalmazás](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Asztali Windows-alkalmazások, szolgáltatások és feldolgozói szerepkörök](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Naplózási keretrendszerek
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog, vagy System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J, vagy Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash beépülő modul](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Exportálás és adatelemzés
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Nem támogatott SDK-k
Tisztában vagyunk azzal, hogy számos más Közösség által támogatott SDK létezik, és mi is hozzájárultak néhány közülük. A Azure Monitor azonban csak az ezen a lapon felsorolt támogatott SDK-k használata esetén nyújt támogatást. Folyamatosan kivizsgáljuk, hogyan bővítjük támogatását más nyelvekre, ezért kövesse a [GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) -közlemények oldalon a legújabb SDK-híreket.
