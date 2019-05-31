---
title: Az Azure Állapotfigyelőt v2 – áttekintés |} A Microsoft Docs
description: Az állapotfigyelő v2 áttekintése. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 2adc706c5da4fa53ace2a8a471789e276878c491
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255861"
---
# <a name="status-monitor-v2"></a>Állapotmonitor v2

Állapot figyelő v2 egy közzétett PowerShell-modul a [PowerShell-Galériabeli](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) és a helyettesíti a [Állapotfigyelőt](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Ez a modul adja meg az IIS-kiszolgálón üzemeltetett webalkalmazások .NET kód nélküli kialakítási.
Telemetriát küld az Azure Portalon ahol [figyelő](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) alkalmazását.

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>PowerShell-galéria

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Utasítások
- Tekintse át a [első lépések útmutató](status-monitor-v2-get-started.md) tömör Kódminták a kezdéshez.
- Tekintse át a [részletes utasítások](status-monitor-v2-detailed-instructions.md) az első lépésekről részletes bemutatása.

## <a name="powershell-api-reference"></a>PowerShell API-referencia
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>Hibaelhárítás
- [Hibaelhárítás](status-monitor-v2-troubleshoot.md)
- [Ismert problémák](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>GYIK

- Támogatja az Állapotfigyelőt v2 proxy telepítések?

  **Igen**. Töltse le az Állapotfigyelőt v2 több lehetősége van. Ha a számítógép rendelkezik internet-hozzáféréssel, segítségével készítheti elő a PowerShell-galériából történő `-Proxy` paramétereket. Manuálisan is letöltheti a modult, és telepítse azt a gépen, vagy a modul közvetlenül használni. A felsorolt lehetőségek ismertetett a [részletes utasításokat](status-monitor-v2-detailed-instructions.md).
  
- Az engedélyezés ellenőrzése sikeres volt?

   A parancsmag nem rendelkezünk, ellenőrizze, hogy engedélyezése sikeres volt. Azt javasoljuk, [élő mérőszámok](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) gyorsan megfigyelni, ha az alkalmazás USA telemetriai adatokat küldenek.

   Is [Analytics](../log-query/get-started-portal.md) minden jelenleg a telemetriai adatokat küldenek a felhőalapú szerepkörök listáját.
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>További lépések

A telemetriai adatok megtekintése:

* [A metrikák áttekintése](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez
* [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához
* [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez
* [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md)

További telemetriai funkciók hozzáadása:

* [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
* [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat szúrhasson be.
* [Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) , hogy Ön nyomkövetési és naplóhíváskat szúrhasson

