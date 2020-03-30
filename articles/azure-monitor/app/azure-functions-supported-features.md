---
title: Azure Application Insights – Az Azure Functions által támogatott funkciók
description: Az Application Insights által támogatott funkciók az Azure-függvényekhez
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655650"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Az Application Insights for Azure Functions által támogatott funkciók

Az Azure Functions [beépített integrációt](../../azure-functions/functions-monitoring.md) kínál az Application Insights szolgáltatással, amely az ILogger-felületen keresztül érhető el. Az alábbiakban felsorolunk a jelenleg támogatott funkciókat. Tekintse át az Azure Functions útmutatóját az [első lépésekhez.](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)

A Functions futásidejű verzióiról itt [olvashat](../../azure-functions/functions-versions.md)bővebben.

Az Application Insights kompatibilis verzióiról a Függőségek című [témakörben talál](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)további információt.

## <a name="supported-features"></a>Támogatott funkciók

| Azure Functions                       | 1. verzió                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Automatikus gyűjteménye**        |                 |                   |               
| &bull;Kérelmek                     | Igen             | Igen               | 
| &bull;Kivételek                   | Igen             | Igen               | 
| &bull;Teljesítményszámlálók         | Igen             | Igen               |
| &bull;Függőségek                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash;HTTP      |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash;ServiceBus (Szervizbusz)|                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash;EventHub  |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash;Sql       |                 | Igen               | 
| | | | 
| **Támogatott funkciók**                |                   |                   |               
| &bull;QuickPulse/LiveMetrics       | Igen             | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash;Biztonságos vezérlőcsatorna|                 | Igen               | 
| &bull;Mintavételi                     | Igen             | Igen               | 
| &bull;Szívdobbanás                   |                 | Igen               | 
| | | | 
| **Korrelációs**                       |                   |                   |               
| &bull;ServiceBus (Szervizbusz)                     |                   | Igen               | 
| &bull;EventHub                       |                   | Igen               | 
| | | | 
| **Konfigurálható**                      |                   |                   |           
| &bull;Teljesen konfigurálható.<br/>Az utasításokat az [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) ben találja.<br/>Az összes lehetőséget [Asp.NET Core-ban](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) láthatja.               |                   | Igen                   | 


## <a name="performance-counters"></a>Teljesítményszámlálók

A teljesítményszámlálók automatikus gyűjtése csak Windows-gépeken működik.


## <a name="live-metrics--secure-control-channel"></a>Élő mérőszámok & biztonságos vezérlőcsatornával

A megadott egyéni szűrők feltételek et visszaküldi az Application Insights SDK Élő metrikák összetevőjének. A szűrők potenciálisan bizalmas információkat, például ügyfélazonosítókat tartalmazhatnak. Titkos API-kulccsal biztonságossá teheti a csatornát. Az utasításokat [a Vezérlőcsatorna biztonságossá](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) tétele című témakörben találja.

## <a name="sampling"></a>Mintavételezés

Az Azure Functions alapértelmezés szerint engedélyezi a mintavételezést a konfigurációjukban. További információ: [Mintavétel konfigurálása](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Ha a projekt az Application Insights SDK-tól függ a manuális telemetriai adatok nyomon követéséhez, furcsa viselkedést tapasztalhat, ha a mintavételi konfiguráció eltér a Functions mintavételi konfigurációjától. 

Javasoljuk, hogy ugyanazt a konfigurációt használja, mint a Functions. A **Functions v2**funkcióval ugyanazt a konfigurációt kaphatja meg a konstruktor függőségi injektálásával:

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
