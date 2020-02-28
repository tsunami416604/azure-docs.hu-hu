---
title: Ismerje meg az Azure-Application Insightst | Microsoft Docs
description: A Application Insights megkezdése után itt találja a felderíthető funkciók összegzését.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666024"
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights további telemetria
Miután [hozzáadta Application Insightst a ASP.net-kódhoz](../../azure-monitor/app/asp-net.md), néhány dolog elvégezhető, hogy még több telemetria kapjon. 

| Műveletek | Amihez jut|
|---|---|
|(IIS-kiszolgálók) [Telepítse a Állapotmonitort](https://go.microsoft.com/fwlink/?LinkId=506648) az egyes kiszolgálókon.<br/>(Azure Web Apps) A webalkalmazáshoz tartozó Azure-vezérlőpulton nyissa meg a Application Insights panelt.| [**Teljesítményszámlálók**](../../azure-monitor/app/performance-counters.md)<br/>[**Kivételek**](asp-net-exceptions.md) – részletes verem-nyomkövetés<br/>[**Függőségek**](../../azure-monitor/app/asp-net-dependencies.md)|
|[A JavaScript-kódrészlet hozzáadása a weblapokhoz](../../azure-monitor/app/javascript.md)|[Oldal teljesítmény](../../azure-monitor/app/usage-overview.md), böngészőbeli kivételek, Ajax-teljesítmény. Egyéni ügyféloldali telemetria.|
|[Rendelkezésre állási webes tesztek létrehozása](../../azure-monitor/app/monitor-web-app-availability.md)|Riasztások kérése, ha a webhely elérhetetlenné válik|
|Győződjön meg arról, hogy az MSBuild létrehozta a [buildinfo. config fájlt.](https://msdn.microsoft.com/library/dn449058.aspx)|[Jegyzetek létrehozása metrikus diagramokban](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Egyéni események és metrikák írása](../../azure-monitor/app/api-custom-events-metrics.md)|Megszámolhatja az üzleti eseményeket és mérőszámokat, nyomon követheti a részletes használatot és egyebeket.|
|[Az élő webhely profilja](https://aka.ms/AIProfilerPreview)|Részletes függvények időzítése az élő webalkalmazásból|






