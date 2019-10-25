---
title: Intelligens észlelés az Azure Application Insightsban | Microsoft Docs
description: Application Insights végrehajtja az alkalmazás telemetria automatikus elemzését, és figyelmezteti a lehetséges problémákra.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 02/07/2019
ms.openlocfilehash: ae9af5721e53277f6c939840721ca50ea67ca51e
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818815"
---
# <a name="smart-detection-in-application-insights"></a>Intelligens észlelés a Application Insightsban
 Az intelligens észlelés automatikusan figyelmezteti a webalkalmazás lehetséges teljesítménybeli problémáit és meghibásodási rendellenességeit. Az alkalmazás által a [Application Insightsnak](../../azure-monitor/app/app-insights-overview.md)küldött telemetria proaktív elemzését végzi. Ha hirtelen megnőtt a meghibásodások aránya, vagy az ügyfél vagy a kiszolgáló teljesítménye rendellenes, akkor riasztást kap. Ehhez a szolgáltatáshoz nincs szükség konfigurációra. Akkor működik, ha az alkalmazása elég telemetria küld.

A Smart Detection által kiadott észleléseket elérheti a kapott e-mailekkel, és az intelligens észlelés panelen is.

## <a name="review-your-smart-detections"></a>Az intelligens észlelések áttekintése
Két módon derítheti fel az észleléseket:

* **E-mailt kap** Application Insights. Íme egy tipikus példa:
  
    ![E-mail riasztás](./media/proactive-diagnostics/03.png)
  
    Kattintson a Big (nagy) gombra, és nyissa meg részletesebben a portálon.
* **Az intelligens észlelés** panel Application Insights. A legutóbbi észlelések listájának megtekintéséhez válassza a **vizsgálat** menü **intelligens felismerés** elemét.

![Legutóbbi észlelések megtekintése](./media/proactive-diagnostics/04.png)

Válasszon ki egy észlelést a részletek megtekintéséhez.

## <a name="what-problems-are-detected"></a>Milyen problémák észlelhetők?
Az intelligens észlelés észleli és értesíti a különböző problémákról, például a következőkről:

* [Intelligens észlelés – hibák rendellenességei](../../azure-monitor/app/proactive-failure-diagnostics.md). A gépi tanulás segítségével megadhatja az alkalmazáshoz való sikertelen kérelmek várható arányát, a terhelést és más tényezőket. Ha a hiba aránya a várt borítékon kívül esik, riasztást küldünk.
* [Intelligens észlelés – teljesítménybeli rendellenességek](../../azure-monitor/app/proactive-performance-diagnostics.md). Értesítéseket kaphat, ha egy művelet vagy függőségi időtartam válaszideje lelassul a korábbi alapkonfigurációhoz képest, vagy ha rendellenes mintázatot azonosít a válaszidő vagy az oldal betöltési ideje alapján.   
* Az általános romlások és problémák, például a [nyomkövetési teljesítménycsökkenéséről](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), a [memória szivárgása](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), a kivételek és a [biztonság elleni](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack) [eltérések rendellenes növekedése](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) .

(A Súgó hivatkozásai az egyes értesítésekben a megfelelő cikkekre mutatnak.)

## <a name="smart-detection-email-notifications"></a>E-mail-értesítések intelligens észlelése

Az _előzetes_verzióként megjelölt szabályok kivételével az összes intelligens észlelési szabály alapértelmezés szerint e-mail-értesítések küldésére szolgál az észlelések észlelésekor.

Az e-mail-értesítések egy adott intelligens észlelési szabályhoz való konfigurálásához nyissa meg az intelligens észlelési **Beállítások** panelt, és válassza ki azt a szabályt, amely megnyitja a **szabály szerkesztése** panelt.

Azt is megteheti, hogy Azure Resource Manager-sablonok használatával módosítja a konfigurációt. További részletekért [lásd: Application Insights intelligens észlelési szabályok kezelése Azure Resource Manager-sablonok használatával](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) .

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Következő lépések
Ezek a diagnosztikai eszközök segítenek megvizsgálni a telemetria az alkalmazásból:

* [Metrika-kezelő](../../azure-monitor/app/metrics-explorer.md)
* [Keresési ablak](../../azure-monitor/app/diagnostic-search.md)
* [Analitika – hatékony lekérdezési nyelv](../../azure-monitor/log-query/get-started-portal.md)

Az intelligens észlelés teljesen automatikus. De lehet, hogy néhány riasztást szeretne beállítani?

* [Manuálisan konfigurált metrikai riasztások](../../azure-monitor/app/alerts.md)
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) 

