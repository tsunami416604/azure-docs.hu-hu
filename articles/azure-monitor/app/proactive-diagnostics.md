---
title: Intelligens detektálás az Azure Application Insights |} A Microsoft Docs
description: Application Insights az alkalmazás telemetriai adatainak automatikus mélyreható elemzésével, és figyelmezteti a potenciális problémákról.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: 8ee2dea364253d871d5624242d15d8a81ab6f08f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465901"
---
# <a name="smart-detection-in-application-insights"></a>Az Application Insights intelligens detektálás
 Intelligens észlelés automatikusan figyelmezteti, potenciális teljesítménybeli problémák és a rendellenes hibák webalkalmazásban. Proaktív elemzése a telemetriát, amely elküldi az alkalmazás végrehajt [Application Insights](../../azure-monitor/app/app-insights-overview.md). Ha hirtelen megnövekedhet a hibaarányok, vagy a rendellenes mintákat ügyfél vagy kiszolgáló teljesítményét, riasztást kap. Ez a funkció nincs konfigurációs van szüksége. Ha az alkalmazása elég telemetriát küld működik.

Az intelligens detektálási által kiadott, mind az e-mailt kap, és az intelligens detektálási panelen észlelések érheti el.

## <a name="review-your-smart-detections"></a>Tekintse át az intelligens észlelés
Észlelések kétféleképpen deríthet fel:

* **E-mailt kap** az Application Insights szolgáltatásból. Íme egy tipikus példát:
  
    ![Értesítő e-mail](./media/proactive-diagnostics/03.png)
  
    A big Data típusú gombra kattintva nyissa meg a további részleteket a portálon.
* **Az intelligens detektálási panel** az Application Insightsban. Válassza ki **intelligens detektálás** alatt a **vizsgálat** menüjében. Itt megtekintheti a legutóbbi észlelési listáját.

![Legutóbbi észlelés megtekintése](./media/proactive-diagnostics/04.png)

Válassza ki az észlelést a részletek megtekintéséhez.

## <a name="what-problems-are-detected"></a>Milyen problémákat észlelt?
Intelligens detektálás észleli, és értesíti kapcsolatos számos különböző probléma, például:

* [Intelligens detektálás – rendellenes hibák](../../azure-monitor/app/proactive-failure-diagnostics.md). A machine learning sikertelen kérelmek az alkalmazás a várt arány beállításához használjuk, a terhelés és egyéb tényezők használatával történik. Ha a Hibaarány megfelelően a várt boríték kívül, küldünk egy riasztást.
* [Intelligens detektálás – teljesítménnyel kapcsolatos anomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md). Ha egy művelet és a függőségi időtartam válaszideje rendszer lelassul kiinduló alapkonfigurációt képest, vagy ha azt azonosítható a rendellenes minta válaszidejének vagy a lapbetöltési idő értesítéseket kap.   
* Általános romlását és a problémák, például [számolt nyomkövetési](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [memóriavesztés](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [Abnormal kivételek mennyiségi növekménye](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) és [biztonsági kizárási mintákat](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(A Súgó az egyes értesítések hivatkozások az ide tartozó cikkekre.)

## <a name="smart-detection-email-notifications"></a>Az intelligens észlelési e-mail-értesítések

Minden intelligens detektálási szabályok, kivéve megjelölve szabályok _előzetes_, e-mail-értesítések küldése az észlelések esetén alapértelmezés szerint úgy vannak konfigurálva.

Az intelligens detektálási szabályra adott e-mail-értesítések konfigurálása teheti meg az intelligens detektálási megnyitásával **beállítások** panelre, és válassza a szabályt, amely megnyitja a **szabály szerkesztése** panelen.

Másik lehetőségként a konfigurációját az Azure Resource Manager-sablonok segítségével módosíthatja. [Tekintse meg az Azure Resource Manager-sablonok használata kezelheti az Application Insights intelligens detektálási szabályok](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) további részletekért.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
Ezek a diagnosztikai eszközök segítséget nyújt az alkalmazásából származó telemetriai adatok vizsgálata:

* [Metrika explorer](../../azure-monitor/app/metrics-explorer.md)
* [A keresési ablak](../../azure-monitor/app/diagnostic-search.md)
* [Analytics – erőteljes lekérdezési nyelv](../../azure-monitor/log-query/get-started-portal.md)

Az intelligens észlelés a teljesen automatikus. De esetleg szeretné néhány további riasztásokat állíthat be?

* [Manuálisan konfigurált metrikákhoz kapcsolódó riasztások](../../azure-monitor/app/alerts.md)
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) 

