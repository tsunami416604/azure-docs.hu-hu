---
title: Még több előny az Azure Application Insights |} A Microsoft Docs
description: Ismerkedés az Application insights szolgáltatással, miután itt található egy összefoglaló megismerheti a szolgáltatást.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 8b66949c759d8a45d6142d9e29f4a7baa0964c79
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53968927"
---
# <a name="more-telemetry-from-application-insights"></a>További telemetria az Application Insightsból
Miután [hozzá az Application Insights az ASP.NET-kódban](../../azure-monitor/app/asp-net.md), akkor megteheti, hogy további telemetriai adatokat néhány dolgot. 

| Műveletek | Amihez jut|
|---|---|
|(Az IIS-kiszolgálókkal) [Állapotfigyelő telepítése](https://go.microsoft.com/fwlink/?LinkId=506648) az összes kiszolgálón.<br/>(Az azure web apps) A webalkalmazás Azure Vezérlőpulton nyissa meg az Application Insights paneljén.| [**Teljesítményszámlálók**](../../application-insights/app-insights-performance-counters.md)<br/>[**Kivételek** ](asp-net-exceptions.md) – részletes veremkiíratásokat<br/>[**Függőségek**](../../azure-monitor/app/asp-net-dependencies.md)|
|[A JavaScript-kódrészletet a weboldalakhoz hozzáadása](../../azure-monitor/app/javascript.md)|[Teljesítmény lapon](../../application-insights/app-insights-usage-overview.md), böngészőbeli kivételekkel, az AJAX teljesítménye. Egyéni ügyféloldali telemetria.|
|[Rendelkezésre állási webes tesztek létrehozása](../../azure-monitor/app/monitor-web-app-availability.md)|Riasztásokat kaphat, ha a webhely elérhetetlenné válik.|
|[Győződjön meg, hogy buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) MSBuild által generált|[A mérőszám-diagramok jegyzetek létrehozása](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Egyéni események és metrikák írása](../../azure-monitor/app/api-custom-events-metrics.md)|Üzleti eseményeket és mérőszámokat száma, nyomon követheti a részletes használati és egyebek.|
|[Az élő webhelyet profil](https://aka.ms/AIProfilerPreview)|Az élő webalkalmazását részletes függvény időzítés|






