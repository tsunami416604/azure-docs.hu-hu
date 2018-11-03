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
ms.devlang: na
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 5d46b446a71d75a20cc4771b651fbf107db31358
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958441"
---
# <a name="more-telemetry-from-application-insights"></a>További telemetria az Application Insightsból
Miután [hozzá az Application Insights az ASP.NET-kódban](app-insights-asp-net.md), akkor megteheti, hogy további telemetriai adatokat néhány dolgot. 

| Műveletek | Amihez jut|
|---|---|
|(Az IIS-kiszolgálókkal) [Állapotfigyelő telepítése](http://go.microsoft.com/fwlink/?LinkId=506648) az összes kiszolgálón.<br/>(Az azure web apps) A webalkalmazás Azure Vezérlőpulton nyissa meg az Application Insights paneljén.| [**Teljesítményszámlálók**](app-insights-performance-counters.md)<br/>[**Kivételek** ](app-insights-asp-net-exceptions.md) – részletes veremkiíratásokat<br/>[**Függőségek**](app-insights-asp-net-dependencies.md)|
|[A JavaScript-kódrészletet a weboldalakhoz hozzáadása](app-insights-javascript.md)|[Teljesítmény lapon](app-insights-usage-overview.md), böngészőbeli kivételekkel, az AJAX teljesítménye. Egyéni ügyféloldali telemetria.|
|[Rendelkezésre állási webes tesztek létrehozása](app-insights-monitor-web-app-availability.md)|Riasztásokat kaphat, ha a webhely elérhetetlenné válik.|
|[Győződjön meg, hogy buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) MSBuild által generált|[A mérőszám-diagramok jegyzetek létrehozása](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Egyéni események és metrikák írása](app-insights-api-custom-events-metrics.md)|Üzleti eseményeket és mérőszámokat száma, nyomon követheti a részletes használati és egyebek.|
|[Az élő webhelyet profil](https://aka.ms/AIProfilerPreview)|Az élő webalkalmazását részletes függvény időzítés|






