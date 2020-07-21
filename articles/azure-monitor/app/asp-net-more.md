---
title: Ismerje meg az Azure-Application Insightst | Microsoft Docs
description: A Application Insights megkezdése után itt találja a felderíthető funkciók összegzését.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540026"
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights további telemetria
Miután [hozzáadta Application Insightst a ASP.net-kódhoz](../../azure-monitor/app/asp-net.md), néhány dolog elvégezhető, hogy még több telemetria kapjon. 

| Művelet | Amihez jut|
|---|---|
|(IIS-kiszolgálók) [Telepítse a Állapotmonitort](https://go.microsoft.com/fwlink/?LinkId=506648) az egyes kiszolgálókon.<br/>(Azure Web Apps) A webalkalmazáshoz tartozó Azure-vezérlőpulton nyissa meg a Application Insights panelt.| [**Teljesítményszámlálók**](../../azure-monitor/app/performance-counters.md)<br/>[**Kivételek**](asp-net-exceptions.md) – részletes verem-nyomkövetés<br/>[**Függőségek**](../../azure-monitor/app/asp-net-dependencies.md)|
|[A JavaScript-kódrészlet hozzáadása a weblapokhoz](../../azure-monitor/app/javascript.md)|[Oldal teljesítmény](../../azure-monitor/app/usage-overview.md), böngészőbeli kivételek, Ajax-teljesítmény. Egyéni ügyféloldali telemetria.|
|[Rendelkezésre állási webes tesztek létrehozása](../../azure-monitor/app/monitor-web-app-availability.md)|Riasztások kérése, ha a webhely elérhetetlenné válik|
|[Győződjön meg róla](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) , hogy az MSBuild létrehozta buildinfo.config|[Jegyzetek létrehozása metrikus diagramokban](./annotations.md)
|[Egyéni események és metrikák írása](../../azure-monitor/app/api-custom-events-metrics.md)|Megszámolhatja az üzleti eseményeket és mérőszámokat, nyomon követheti a részletes használatot és egyebeket.|
|[Az élő webhely profilja](https://aka.ms/AIProfilerPreview)|Részletes függvények időzítése az élő webalkalmazásból|
