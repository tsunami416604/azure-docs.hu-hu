---
title: Ismerje meg az Azure-Application Insightst | Microsoft Docs
description: A Application Insights megkezdése után itt találja a felderíthető funkciók összegzését.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/03/2017
ms.openlocfilehash: 8c51745c43ced8ad3031a6a01096261ef72b33fc
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678353"
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights további telemetria
Miután [hozzáadta Application Insightst a ASP.net-kódhoz](../../azure-monitor/app/asp-net.md), néhány dolog elvégezhető, hogy még több telemetria kapjon. 

| Műveletek | Az ajándékok|
|---|---|
|(IIS-kiszolgálók) [Telepítse a Állapotmonitort](https://go.microsoft.com/fwlink/?LinkId=506648) az egyes kiszolgálókon.<br/>(Azure Web Apps) A webalkalmazáshoz tartozó Azure-vezérlőpulton nyissa meg a Application Insights panelt.| [**Teljesítményszámlálók**](../../azure-monitor/app/performance-counters.md)<br/>[**Kivételek**](asp-net-exceptions.md) – részletes verem-nyomkövetés<br/>[**Függőségek**](../../azure-monitor/app/asp-net-dependencies.md)|
|[A JavaScript-kódrészlet hozzáadása a weblapokhoz](../../azure-monitor/app/javascript.md)|[Oldal teljesítmény](../../azure-monitor/app/usage-overview.md), böngészőbeli kivételek, Ajax-teljesítmény. Egyéni ügyféloldali telemetria.|
|[Rendelkezésre állási webes tesztek létrehozása](../../azure-monitor/app/monitor-web-app-availability.md)|Riasztások kérése, ha a webhely elérhetetlenné válik|
|Győződjön meg arról, hogy az MSBuild létrehozta a [buildinfo. config fájlt.](https://msdn.microsoft.com/library/dn449058.aspx)|[Jegyzetek létrehozása metrikus diagramokban](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Egyéni események és metrikák írása](../../azure-monitor/app/api-custom-events-metrics.md)|Megszámolhatja az üzleti eseményeket és mérőszámokat, nyomon követheti a részletes használatot és egyebeket.|
|[Az élő webhely profilja](https://aka.ms/AIProfilerPreview)|Részletes függvények időzítése az élő webalkalmazásból|






