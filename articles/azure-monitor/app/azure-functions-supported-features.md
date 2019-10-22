---
title: Azure Application Insights – Azure Functions támogatott funkciók | Microsoft Docs
description: A Azure Functions által támogatott szolgáltatások Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 31f962ca96ca5c47d18f9250e567abb8f4024e6f
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677555"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions támogatott funkciók Application Insights

A Azure Functions [beépített integrációt](../../azure-functions/functions-monitoring.md) biztosít Application Insightsekkel, amely a ILogger felületen keresztül érhető el. Alább látható a jelenleg támogatott funkciók listája. Tekintse át a Azure Functions útmutatót az [első lépésekhez](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

## <a name="supported-features"></a>Támogatott funkciók

| Azure Functions                       | 1\. verzió                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatikus gyűjtemény**        |                 |                   |               
| &bull; kérelmek                     | Igen             | Igen               | 
| Kivételek &bull;                   | Igen             | Igen               | 
| Teljesítményszámlálók &bull;         | Igen             | Igen               |
| &bull; függőségek                   |                   |                   |               
| &nbsp; &nbsp; &nbsp; &mdash; HTTP      |                 | Igen               | 
| &nbsp; &nbsp; &nbsp; &mdash; ServiceBus|                 | Igen               | 
| &nbsp; &nbsp; &nbsp; &mdash; EventHub  |                 | Igen               | 
| &nbsp; &nbsp; &nbsp; &mdash; SQL       |                 | Igen               | 
| | | | 
| **Támogatott funkciók**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Igen             | Igen               | 
| &nbsp; &nbsp; &nbsp; &mdash; biztonságos vezérlési csatornát|                 | Igen               | 
| &bull; mintavételezés                     | Igen             | Igen               | 
| &bull; szívverések                   |                 | Igen               | 
| | | | 
| **Korrelációs**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Igen               | 
| &bull; EventHub                       |                   | Igen               | 
| | | | 
| **Konfigurálható**                      |                   |                   |           
| &bull;Fully konfigurálható.<br/>Útmutatásért lásd [Azure functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) .<br/>Az összes beállításhoz lásd: [ASP.net Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) .               |                   | Igen                   | 


## <a name="performance-counters"></a>Teljesítményszámlálók

A teljesítményszámlálók automatikus gyűjtése csak a Windows rendszerű gépeken működik.


## <a name="live-metrics--secure-control-channel"></a>Élő metrikák & biztonságos vezérlési csatornán

Az egyéni szűrők megadott feltételeit a rendszer visszaküldi a Application Insights SDK élő metrikák összetevőjére. A szűrők potenciálisan bizalmas adatokat is tartalmazhatnak, például customerIDs. A csatornát titkos API-kulccsal is biztonságossá teheti. További útmutatásért lásd [a vezérlési csatorna biztonságossá](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) tételét ismertető témakört.

## <a name="sampling"></a>Mintavétel

A Azure Functions alapértelmezés szerint engedélyezi a mintavételezést a konfigurációban. További információ: a [mintavételezés konfigurálása](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Ha a projektben a manuális telemetria követése függ a Application Insights SDK-tól, akkor furcsa viselkedést tapasztalhat, ha a mintavételezési konfiguráció eltér a függvények mintavételezési konfigurációjával. 

Azt javasoljuk, hogy ugyanazt a konfigurációt használja, mint a függvények. A **functions v2**használatával ugyanazt a konfigurációt veheti igénybe, ha függőségi befecskendezést használ a konstruktorban:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
