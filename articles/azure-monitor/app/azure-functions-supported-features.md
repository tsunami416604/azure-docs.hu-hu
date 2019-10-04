---
title: Azure Application Insights – Azure Functions támogatott funkciók | Microsoft Docs
description: A Azure Functions által támogatott szolgáltatások Application Insights
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: cf39c8b5e204493380c095519e0ff25c3ce19f68
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959921"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions támogatott funkciók Application Insights

A Azure Functions [beépített integrációt](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) biztosít Application Insightsekkel, amely a ILogger felületen keresztül érhető el. Alább látható a jelenleg támogatott funkciók listája. Tekintse át a Azure Functions útmutatót az [első lépésekhez](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Támogatott funkciók

| Azure Functions                       | 1-es verzió                | V2 (Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatikus gyűjtemény**        |                 |                   |               
| @no__t – 0 kérelem                     | Igen             | Igen               | 
| @no__t – 0 kivétel                   | Igen             | Igen               | 
| @no__t – 0 teljesítményszámlálók         | Igen             | Igen               |
| @no__t – 0 függőség                   |                   |                   |               
| &nbsp; @ NO__T-1 @ NO__T-2 @ NO__T-3 HTTP      |                 | Igen               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 ServiceBus|                 | Igen               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 EventHub  |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Igen               | 
| | | | 
| **Támogatott funkciók**                |                   |                   |               
| @no__t – 0 QuickPulse/LiveMetrics       | Igen             | Igen               | 
| &nbsp; @ no__t-1 @ no__t-2 @ no__t-3 biztonságos vezérlési csatorna|                 | Igen               | 
| @no__t – 0 mintavételezés                     | Igen             | Igen               | 
| @no__t – 0 szívverés                   |                 | Igen               | 
| | | | 
| **Korrelációs**                       |                   |                   |               
| @no__t – 0 ServiceBus                     |                   | Igen               | 
| @no__t – 0 EventHub                       |                   | Igen               | 
| | | | 
| **Konfigurálható**                      |                   |                   |           
| @no__t – a 0Fully konfigurálható.<br/>Útmutatásért lásd [Azure functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) .<br/>Az összes beállításhoz lásd: [ASP.net Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) .               |                   | Igen                   | 


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
