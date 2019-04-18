---
title: Az Azure Application Insights – az Azure Functions támogatott funkciók |} A Microsoft Docs
description: Az Application Insights az Azure Functions támogatott szolgáltatások
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 101c985178b8269b4ff542b94b057330d0c2652a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496061"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights az Azure Functions támogatott szolgáltatások

Azure Functions-ajánlatok [beépített integráció](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) az Application insights szolgáltatással, amely a ILogger felületen keresztül érhető el. Az alábbi, a jelenleg támogatott funkciók listáját. Tekintse át az Azure Functions útmutatója [bevezetés](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Támogatott funkciók

| Azure Functions                       | 1. verzió                | V2 (az Ignite 2018.)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatikus gyűjteménye**        |                 |                   |               
| &bull; Kérelmek                     | Igen             | Igen               | 
| &bull; Kivételek                   | Igen             | Igen               | 
| &bull; Teljesítményszámlálók         | Igen             | Igen               |
| &bull; Függőségek                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; Szolgáltatásbusz|                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; az eventHub  |                 | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Igen               | 
| | | | 
| **Támogatott szolgáltatások**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Igen             | Igen               | 
| &nbsp;&nbsp;&nbsp;&mdash; Biztonságos vezérlőcsatorna|                 | Igen               | 
| &bull; Mintavétel                     | Igen             | Igen               | 
| &bull; A szívverések                   |                 | Igen               | 
| | | | 
| **Korreláció**                       |                   |                   |               
| &bull; Szolgáltatásbusz                     |                   | Igen               | 
| &bull; az eventHub                       |                   | Igen               | 
| | | | 
| **Konfigurálható**                      |                   |                   |           
| &bull;Teljes mértékben konfigurálhatók.<br/>Lásd: [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) útmutatást.<br/>Lásd: [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) összes beállításának.               |                   | Igen                   | 


## <a name="performance-counters"></a>Teljesítményszámlálók

A teljesítményszámlálók automatikus gyűjtését csak a Windows-gépek működik.


## <a name="live-metrics--secure-control-channel"></a>Élő metrikák és biztonságos vezérlőcsatorna

A megadott egyéni szűrők feltételek lesznek visszaküldve az élő mérőszámok az Application Insights SDK összetevőt. A szűrők potenciálisan bizalmas adatokat például customerIDs tartalmazhatnak. Hogy a csatorna biztonságos titkos API-kulccsal. Lásd: [a vezérlőcsatorna biztonságos](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) útmutatást.

## <a name="sampling"></a>Mintavételezés

Az Azure Functions lehetővé teszi a mintavétel a konfigurálásukban alapértelmezés szerint. További információkért lásd: [mintavétel konfigurálása](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

A projekt függőséget vesz fel az Application Insights SDK manuális telemetriai követési tennie, ha rendellenes viselkedés tapasztalhat, ha a mintavételi konfiguráció eltér attól az a funkciók mintavételi konfiguráció. 

Funkciókként ugyanaz a konfiguráció használatát javasoljuk. A **funkciók v2**, kérheti, hogy ugyanazt a konfigurációt a konstruktorban függőségi beszúrást használatával:

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
