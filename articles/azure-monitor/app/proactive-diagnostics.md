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
ms.openlocfilehash: edbd7000001ae6927078e2f1bb9e348cc78f9efa
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962111"
---
# <a name="smart-detection-in-application-insights"></a>Az Application Insights intelligens detektálás
 Intelligens észlelés automatikusan figyelmezteti, az esetleges teljesítményproblémákat okozhat a webalkalmazásban. Proaktív elemzése a telemetriát, amely elküldi az alkalmazás végrehajt [Application Insights](../../azure-monitor/app/app-insights-overview.md). Ha hirtelen megnövekedhet a hibaarányok, vagy a rendellenes mintákat ügyfél vagy kiszolgáló teljesítményét, riasztást kap. Ez a funkció nincs konfigurációs van szüksége. Ha az alkalmazása elég telemetriát küld működik.

Az intelligens észlelési riasztásokkal is elérheti, mind az e-mailt kap, és az intelligens detektálási panelen.

## <a name="review-your-smart-detections"></a>Tekintse át az intelligens észlelés
Észlelések kétféleképpen deríthet fel:

* **E-mailt kap** az Application Insights szolgáltatásból. Íme egy tipikus példát:
  
    ![Értesítő e-mail](./media/proactive-diagnostics/03.png)
  
    A big Data típusú gombra kattintva nyissa meg a további részleteket a portálon.
* **Az intelligens detektálási csempe** az alkalmazás áttekintése panel megjeleníti a legutóbbi riasztások száma. Kattintson a csempére, és a egy új figyelmeztetések listájának megtekintéséhez.

![Legutóbbi észlelés megtekintése](./media/proactive-diagnostics/04.png)

Válasszon ki egy riasztást, a részletek megtekintéséhez.

## <a name="what-problems-are-detected"></a>Milyen problémákat észlelt?
Észlelés három fő típusba sorolhatók:

* [Intelligens detektálás – rendellenes hibák](../../azure-monitor/app/proactive-failure-diagnostics.md). A machine learning sikertelen kérelmek az alkalmazás a várt arány beállításához használjuk, a terhelés és egyéb tényezők használatával történik. Ha a Hibaarány megfelelően a várt boríték kívül, küldünk egy riasztást.
* [Intelligens detektálás – teljesítménnyel kapcsolatos anomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md). Ha egy művelet és a függőségi időtartam válaszideje rendszer lelassul kiinduló alapkonfigurációt képest, vagy ha azt azonosítható a rendellenes minta válaszidejének vagy a lapbetöltési idő értesítéseket kap.   
* [Intelligens detektálás – Azure-Felhőszolgáltatás problémák](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Riasztásokat kaphat, ha az alkalmazás üzemel az Azure Cloud Services és a egy szerepkörpéldány rendelkezik a hibák, gyakori újrahasznosítás vagy futásidejű összeomlások.

(A Súgó az egyes értesítések hivatkozások az ide tartozó cikkekre.)

## <a name="smart-detection-email-notifications"></a>Az intelligens észlelési e-mail-értesítések

Minden intelligens detektálási szabályok, kivéve a szabályokat, az előzetes verzióként megjelölt e-mail értesítések küldéséhez észlelések esetén alapértelmezés szerint vannak konfigurálva.

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

