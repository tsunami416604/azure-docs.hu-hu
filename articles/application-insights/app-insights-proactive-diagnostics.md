---
title: "Az Azure Application Insightsban észlelési intelligens |} Microsoft Docs"
description: "Az Application Insights az alkalmazás telemetriai adatot automatikus mélyreható elemzésével és figyelmezteti, potenciális problémákat."
services: application-insights
documentationcenter: windows
author: rakefetj
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: bwren
ms.openlocfilehash: f203b2a532ea721d9797c67a4750896e3ab2b9f7
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="smart-detection-in-application-insights"></a>Az Application Insightsban intelligens észlelése
 Intelligens észlelési automatikusan figyelmezteti, mert ez teljesítményproblémákat okozhat a webalkalmazásban. Proaktív, amelyet az alkalmazás küld a telemetriai adatok elemzését végez [Application Insights](app-insights-overview.md). Ha nincs, hirtelen megnövekedhet a meghibásodás arányának vagy rendellenes minták ügyfél vagy kiszolgáló teljesítményét, akkor figyelmeztetést kap. Ez a funkció nincs konfiguráció szükséges. Ha az alkalmazás küld elég mérési adat működik.

Intelligens észlelési riasztásokat érheti el az e-maileket kapni a, mind az intelligens észlelési paneljén.

## <a name="review-your-smart-detections"></a>Tekintse át az intelligens észlelések
Két módon észlelések fel tud deríteni:

* **E-mailt kapni** az Application Insights. Például a következő:
  
    ![E-mail értesítés](./media/app-insights-proactive-diagnostics/03.png)
  
    A nagy gombra kattintva nyissa meg a további információkhoz juthat a portálon.
* **Az intelligens észlelési csempe** a az alkalmazás áttekintése panel a legújabb riasztások számát jeleníti meg. Kattintson a csempére kattintva megtekintheti a legújabb riasztások listáját.

![Legutóbbi nézetet észlelések](./media/app-insights-proactive-diagnostics/04.png)

Válasszon ki egy riasztást, a részletek megtekintéséhez.

## <a name="what-problems-are-detected"></a>Milyen problémákat észlelt?
Észlelési három fő típusba sorolhatók:

* [Észlelési - hiba rendellenességeket intelligens](app-insights-proactive-failure-diagnostics.md). Gépi tanulási a sikertelen kérelmek az alkalmazás várható sebesség beállításához használjuk, betöltés és más tényezők használatával történik. Ha a hibaaránya kerül, a várt boríték kívül, riasztást kapni.
* [Észlelési - Teljesítményanomáliákat intelligens](app-insights-proactive-performance-diagnostics.md). Ha egy művelet vagy függőségi időtartamot válaszideje az lassítja kiinduló képest, vagy ha azt azonosítani a rendellenes mintát válaszidő vagy a lapbetöltési idő értesítéseket kap.   
* [Észlelési - Azure Cloud Service problémák intelligens](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Riasztásokat kaphat, ha az alkalmazás üzemel Azure Cloud Services és a szerepkör példánya van indítási hibák, gyakori újrahasznosítása vagy futásidejű összeomlik.

(A súgó minden értesítés hivatkozásokra kattintva a kapcsolódó cikkekben.)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Következő lépések
A diagnosztikai eszközök segítségével vizsgálja meg az alkalmazás a telemetriai adatok:

* [Metrika explorer](app-insights-metrics-explorer.md)
* [Keresési ablak](app-insights-diagnostic-search.md)
* [Elemzés - hatékony lekérdezési nyelv](app-insights-analytics-tour.md)

Intelligens észlelési teljesen automatikus. De lehet, hogy milyen néhány további riasztások beállítása?

* [Manuálisan konfigurált metrika riasztások](app-insights-alerts.md)
* [A webteszt rendelkezésre állása](app-insights-monitor-web-app-availability.md) 

