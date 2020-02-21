---
title: Azure Application Insights – Azure Functions támogatott szolgáltatások
description: A Azure Functions által támogatott szolgáltatások Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 807dde08d6ae88054524cb9e397d36db41324482
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484570"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions támogatott funkciók Application Insights

A Azure Functions [beépített integrációt](../../azure-functions/functions-monitoring.md) biztosít Application Insightsekkel, amely a ILogger felületen keresztül érhető el. Alább látható a jelenleg támogatott funkciók listája. Tekintse át a Azure Functions útmutatót az [első lépésekhez](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

További információ a függvények futtatókörnyezeti verzióiról: [itt](../../azure-functions/functions-versions.md).

A Application Insights kompatibilis verzióival kapcsolatos további információkért lásd: [függőségek](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Támogatott funkciók

| Azure Functions                       | 1\. verzió                | V2 & v3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Automatikus gyűjtemény**        |                 |                   |               
| &bull; kérelmek                     | Igen             | Igen               | 
| Kivételek &bull;                   | Igen             | Igen               | 
| Teljesítményszámlálók &bull;         | Igen             | Igen               |
| &bull; függőségek                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Igen               | 
| | | | 
| **Támogatott funkciók**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Igen             | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; biztonságos vezérlési csatornát|                 | Igen               | 
| &bull; mintavételezés                     | Igen             | Igen               | 
| &bull; szívverések                   |                 | Igen               | 
| | | | 
| **Korrelációs**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Igen               | 
| &bull; EventHub                       |                   | Igen               | 
| | | | 
| **Konfigurálható**                      |                   |                   |           
| &bull;teljes mértékben konfigurálható.<br/>Útmutatásért lásd [Azure functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) .<br/>Az összes beállításhoz lásd: [ASP.net Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) .               |                   | Igen                   | 


## <a name="performance-counters"></a>Teljesítményszámlálók

A teljesítményszámlálók automatikus gyűjtése csak a Windows rendszerű gépeken működik.


## <a name="live-metrics--secure-control-channel"></a>Élő metrikák & biztonságos vezérlési csatornán

Az egyéni szűrők megadott feltételeit a rendszer visszaküldi a Application Insights SDK élő metrikák összetevőjére. A szűrők potenciálisan bizalmas adatokat is tartalmazhatnak, például customerIDs. A csatornát titkos API-kulccsal is biztonságossá teheti. További útmutatásért lásd [a vezérlési csatorna biztonságossá](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) tételét ismertető témakört.

## <a name="sampling"></a>Mintavételezés

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
