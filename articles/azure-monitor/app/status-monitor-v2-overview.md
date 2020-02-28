---
title: Az Azure Application Insights Agent áttekintése | Microsoft Docs
description: A Application Insights-ügynök áttekintése. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671171"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>A helyszíni kiszolgálók Azure Monitor Application Insights ügynökének üzembe helyezése

> [!IMPORTANT]
> Ez az útmutató a Application Insights-ügynök helyszíni és nem Azure-beli Felhőbeli üzembe helyezéséhez ajánlott. Az [Azure-beli virtuális gépek és a virtuálisgép-méretezési csoport üzembe helyezésének](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)ajánlott módszere.

Application Insights ügynök (korábbi nevén Állapotmonitor v2) a [PowerShell-Galéria](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)közzétett PowerShell-modul.
Lecseréli [Állapotmonitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
A rendszer elküldje a telemetria a Azure Portalba, ahol [nyomon](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) követheti az alkalmazást.

> [!NOTE]
> A modul jelenleg csak az IIS-ben üzemeltetett .NET-webalkalmazások Code uninstrumentation-alapú rendszerállapotát támogatja. SDK használata ASP.NET Core, Java és Node. js-alkalmazásokhoz.

## <a name="powershell-gallery"></a>PowerShell-galéria

Application Insights ügynök itt található: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell-galéria](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Utasítások
- Tekintse meg az [első lépéseket ismertető útmutatót](status-monitor-v2-get-started.md) , amelyből megtudhatja, hogyan kezdheti el a rövid kód mintáit.
- Tekintse meg az első lépések [részletes leírását](status-monitor-v2-detailed-instructions.md) .

## <a name="powershell-api-reference"></a>PowerShell API-referenciák
- [ApplicationInsightsMonitoring letiltása](status-monitor-v2-api-disable-monitoring.md)
- [InstrumentationEngine letiltása](status-monitor-v2-api-disable-instrumentation-engine.md)
- [ApplicationInsightsMonitoring engedélyezése](status-monitor-v2-api-enable-monitoring.md)
- [InstrumentationEngine engedélyezése](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start – ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Hibakeresés
- [Hibaelhárítás](status-monitor-v2-troubleshoot.md)
- [Ismert problémák](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>GYIK

- Támogatja a Application Insights ügynök a proxy telepítéseit?

  *Igen*. Application Insights-ügynök letöltése több módon is lehetséges. Ha a számítógép rendelkezik internet-hozzáféréssel, `-Proxy` paraméterek használatával bejelentkezhet a PowerShell-galériaba.
Manuálisan is letöltheti a modult, és telepítheti a számítógépre, vagy közvetlenül is használhatja azt.
Ezeket a beállításokat a [részletes utasítások](status-monitor-v2-detailed-instructions.md)ismertetik.

- Támogatja az Állapotmonitor v2 ASP.NET Core alkalmazásokat?

  *Nem*. ASP.NET Core alkalmazások figyelésének engedélyezéséhez lásd: [Application Insights ASP.net Core alkalmazásokhoz](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Nincs szükség a StatusMonitor telepítésére egy ASP.NET Core alkalmazáshoz. Ez akkor is igaz, ha ASP.NET Core alkalmazást az IIS üzemelteti.

- Hogyan ellenőrizze, hogy az engedélyezés sikeres volt-e?

  - A [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) parancsmag használatával ellenőrizheti, hogy az engedélyezés sikeres volt-e.
  - Javasoljuk, hogy [élő metrikák](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) használatával gyorsan megállapítsa, hogy az alkalmazás telemetria küld-e.

  - A [log Analytics](../log-query/get-started-portal.md) használatával is listázhatja az összes jelenleg telemetria küldő felhőalapú szerepkört:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Következő lépések

A telemetriai adatok megtekintése:

* [Ismerje meg a mérőszámokat](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez.
* [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md) a problémák diagnosztizálásához.
* További speciális lekérdezésekhez [használja az elemzést](../../azure-monitor/app/analytics.md) .
* [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

További telemetriai funkciók hozzáadása:

* [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely élőben maradjon.
* [Vegyen fel webes ügyfél-telemetria](../../azure-monitor/app/javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
* [Adja hozzá a Application INSIGHTS SDK-t a kódhoz](../../azure-monitor/app/asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el.

