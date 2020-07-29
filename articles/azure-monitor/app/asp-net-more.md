---
title: Ismerje meg az Azure-Application Insightst | Microsoft Docs
description: A Application Insights megkezdése után itt találja a felderíthető funkciók összegzését.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: c0be377636159dbb2bdb628af6a0bc9077942397
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321343"
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights további telemetria
Miután [hozzáadta Application Insightst a ASP.net-kódhoz](./asp-net.md), néhány dolog elvégezhető, hogy még több telemetria kapjon. 

| Művelet | Amihez jut|
|---|---|
|(IIS-kiszolgálók) [Telepítse a Állapotmonitort](https://go.microsoft.com/fwlink/?LinkId=506648) az egyes kiszolgálókon.<br/>(Azure Web Apps) A webalkalmazáshoz tartozó Azure-vezérlőpulton nyissa meg a Application Insights panelt.| [**Teljesítményszámlálók**](./performance-counters.md)<br/>[**Kivételek**](asp-net-exceptions.md) – részletes verem-nyomkövetés<br/>[**Függőségek**](./asp-net-dependencies.md)|
|[A JavaScript-kódrészlet hozzáadása a weblapokhoz](./javascript.md)|[Oldal teljesítmény](./usage-overview.md), böngészőbeli kivételek, Ajax-teljesítmény. Egyéni ügyféloldali telemetria.|
|[Rendelkezésre állási webes tesztek létrehozása](./monitor-web-app-availability.md)|Riasztások kérése, ha a webhely elérhetetlenné válik|
|[Győződjön meg róla](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) , hogy az MSBuild létrehozta buildinfo.config|[Jegyzetek létrehozása metrikus diagramokban](./annotations.md)
|[Egyéni események és metrikák írása](./api-custom-events-metrics.md)|Megszámolhatja az üzleti eseményeket és mérőszámokat, nyomon követheti a részletes használatot és egyebeket.|
|[Az élő webhely profilja](https://aka.ms/AIProfilerPreview)|Részletes függvények időzítése az élő webalkalmazásból|

