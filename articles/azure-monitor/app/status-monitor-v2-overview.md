---
title: Az Azure Állapotfigyelőt v2 – áttekintés |} A Microsoft Docs
description: Az állapotfigyelő v2 áttekintése. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
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
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734162"
---
# <a name="status-monitor-v2"></a>Állapotmonitor v2

Állapot figyelő v2 egy PowerShell-modul közzétett a [PowerShell-galériából](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
A szolgáltatás lecseréli [Állapotfigyelőt](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
A modul adja meg az IIS-kiszolgálón üzemeltetett .NET webes alkalmazások, kód nélküli kialakítási.
Telemetriája el lesz küldve az Azure Portalt, ahol [figyelő](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) az alkalmazást.

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="powershell-gallery"></a>PowerShell-galéria

A PowerShell-galériából található itt: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.


## <a name="instructions"></a>Utasítások
- Tekintse meg a [első lépések útmutató](status-monitor-v2-get-started.md) tömör Kódminták a kezdés beolvasásához.
- Tekintse meg a [részletes utasítások](status-monitor-v2-detailed-instructions.md) az első lépésekről részletes bemutatása.

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

  *Igen*. Töltse le az Állapotfigyelőt v2 többféle módon lehet. Ha a számítógép rendelkezik internet-hozzáféréssel, segítségével készítheti elő a PowerShell-galériában használatával `-Proxy` paramétereket.
Manuálisan is letöltheti a modult, és telepítse azt a számítógépet, vagy közvetlenül használni.
A felsorolt lehetőségek ismertetett a [részletes utasítások](status-monitor-v2-detailed-instructions.md).
  
- Hogyan ellenőrizhetem, hogy sikerült-e a engedélyezését?

   Nincs nem parancsmag lehetővé tétele sikerességéről.
Javasoljuk, használjon [élő mérőszámok](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) gyors meghatározását, ha az alkalmazás telemetriai adatokat küldenek.

   Is [Log Analytics](../log-query/get-started-portal.md) listázhatja az összes jelenleg a telemetriai adatokat küldenek a felhőalapú szerepkörök:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>További lépések

A telemetriai adatok megtekintése:

* [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md) teljesítményének figyelése és használati.
* [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához.
* [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez.
* [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

További telemetriai funkciók hozzáadása:

* [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
* [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat engedélyezése.
* [Az Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhíváskat szúrhasson.

