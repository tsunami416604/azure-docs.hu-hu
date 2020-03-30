---
title: Intelligens észlelés az Azure Application Insightsban | Microsoft dokumentumok
description: Az Application Insights automatikus anam-es mélyelemzést végez az alkalmazástelemetriai adatokról, és figyelmezteta lehetséges problémákra.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: e232295f9da2a2ae8f3c6fafdd1dc33a42e92e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671800"
---
# <a name="smart-detection-in-application-insights"></a>Intelligens detektálás az Application Insightsban
 Az intelligens észlelés automatikusan figyelmezteti a webes alkalmazás lehetséges teljesítményproblémáira és hibaproblémáira. Proaktív elemzést végez az alkalmazás által az Application Insightsnak küldött telemetriai [adatokról.](../../azure-monitor/app/app-insights-overview.md) Ha a hibaarányok hirtelen emelkednek, vagy rendellenes minták jelennek meg az ügyfél vagy a kiszolgáló teljesítményében, riasztást kap. Ennek a szolgáltatásnak nincs szüksége konfigurációra. Akkor működik, ha az alkalmazás elegendő telemetriai adatokat küld.

Az Intelligens észlelés által kiadott észleléseket mind a kapott e-mailekből, mind az Intelligens észlelés panelről elérheti.

## <a name="review-your-smart-detections"></a>Az intelligens észlelések áttekintése
Az észleléseket kétféleképpen fedezheti fel:

* **E-mailt kap** az Application Insightstól. Íme egy tipikus példa:
  
    ![E-mail értesítés](./media/proactive-diagnostics/03.png)
  
    Kattintson a nagy gombra, hogy további részleteket nyisson meg a portálon.
* **Az intelligens észlelési panel** az Application Insightsban. Válassza az **Intelligens észlelés** lehetőséget a **Vizsgálat** menüben a legutóbbi észlelések listájának megtekintéséhez.

![Legutóbbi észlelések megtekintése](./media/proactive-diagnostics/04.png)

A részletek megtekintéséhez jelöljön ki egy észlelést.

## <a name="what-problems-are-detected"></a>Milyen problémákat észlel?
Az intelligens észlelés számos problémát észlel és ad a következőkről, például:

* [Intelligens észlelés - Hibaanomáliák](../../azure-monitor/app/proactive-failure-diagnostics.md). A gépi tanulás t, hogy állítsa be a várt sebessége sikertelen kérelmek az alkalmazás, korrelál a terhelés és egyéb tényezők. Ha a hibaarány kívül esik a várt borítékon, riasztást küldünk.
* [Intelligens észlelés - Teljesítményanomáliák](../../azure-monitor/app/proactive-performance-diagnostics.md). Értesítést kap, ha egy művelet vagy a függőség időtartama válaszidő lelassul a korábbi alapkonfigurációhoz képest, vagy ha rendellenes mintát azonosítunk a válaszidő vagy az oldal betöltési ideje szerint.   
* Általános degradációk és problémák, mint például [a Trace degredation](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [memóriavesztés](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [rendellenes növekedése kivétel mennyisége](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) és a biztonsági [anti-minták](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Az egyes értesítésekben található súgóhivatkozások a vonatkozó cikkekre mutatnak.)

## <a name="smart-detection-email-notifications"></a>Intelligens észlelési e-mail értesítések

Az _előzetes verzióként_megjelölt szabályok kivételével az összes intelligens észlelési szabály alapértelmezés szerint úgy van beállítva, hogy az észlelések észlelések esetén e-mail értesítéseket küldjön.

Az e-mail értesítések konfigurálása egy adott intelligens észlelési szabályhoz az Intelligens észlelési **beállítások** panel megnyitásával és a szabály kiválasztásával végezhető el, amely megnyitja a **Szerkesztési szabály** panelt.

Másik lehetőségként módosíthatja a konfigurációt az Azure Resource Manager-sablonok használatával. További részletekért [tekintse meg az Application Insights intelligens észlelési szabályainak kezelése az Azure Resource Manager-sablonok használatával.](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
Ezek a diagnosztikai eszközök segítenek az alkalmazástelemetria imitálatának vizsgálatában:

* [Metrikus felfedező](../../azure-monitor/app/metrics-explorer.md)
* [Kereséskezelő](../../azure-monitor/app/diagnostic-search.md)
* [Analytics – hatékony lekérdezési nyelv](../../azure-monitor/log-query/get-started-portal.md)

Az intelligens észlelés teljesen automatikus. De talán szeretne még néhány riasztást beállítani?

* [Manuálisan konfigurált metrikariasztások](../../azure-monitor/app/alerts.md)
* [Rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) 

