---
title: Az Azure Application Insights-ügynök áttekintése | Microsoft dokumentumok
description: Az Application Insights-ügynök áttekintése. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275710"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Az Azure Monitor Application Insights-ügynök központi telepítése helyszíni kiszolgálókhoz

> [!IMPORTANT]
> Ez az útmutató ajánlott a helyszíni és nem Azure felhőbeli telepítések Application Insights Ügynök. Az [azure-beli virtuálisgép- és virtuálisgép-méretezési csoport központi telepítéseinek](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)ajánlott megközelítése.

Az Application Insights Agent (korábbi nevén Status Monitor V2) egy PowerShell-modul, amelyet a [PowerShell-galériában](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)tettek közzé.
Ez helyettesíti [állapotfigyelő](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Telemetriai adatokat küld az Azure Portalon, ahol [figyelheti](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) az alkalmazást.

> [!NOTE]
> A modul jelenleg csak az IIS-ben tárolt .NET webalkalmazások kód nélküli instrumentációját támogatja. SDK használatával ASP.NET Core, Java és Node.js alkalmazásokat.

## <a name="powershell-gallery"></a>PowerShell-galéria

Az Application Insights Agent https://www.powershellgallery.com/packages/Az.ApplicationMonitoritt található: .

![PowerShell-galéria](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Utasítások
- Tekintse meg az [első lépések re vonatkozó utasításokat,](status-monitor-v2-get-started.md) hogy a kezdéshez tömör kódmintákat.
- Lásd a [részletes utasításokat](status-monitor-v2-detailed-instructions.md) egy mély merülés, hogyan kell elkezdeni.

## <a name="powershell-api-reference"></a>PowerShell API-referencia
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Hibaelhárítás
- [hibaelhárítással](status-monitor-v2-troubleshoot.md)
- [Ismert problémák](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>GYIK

- Az Application Insights-ügynök támogatja a proxytelepítéseket?

  *Igen,* ez az. Az Application Insights-ügynök letöltésének többféle módon is letölthető. Ha a számítógép rendelkezik internet-hozzáféréssel, paraméterek használatával `-Proxy` a PowerShell-galériába is beléphet.
Manuálisan is letöltheti a modult, és telepítheti a számítógépre, vagy közvetlenül használhatja.
A beállítások mindegyikét a [részletes utasítások ismertetik.](status-monitor-v2-detailed-instructions.md)

- Támogatja az Állapotfigyelő v2 ASP.NET az alapvető alkalmazásokat?

  *Nem, nem, nem, nem,* Az ASP.NET alapvető alkalmazások figyelésének engedélyezésére vonatkozó utasításokat az [Application Insights for ASP.NET Core applications (Alkalmazáselemzési ASP.NET.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) Nincs szükség a StatusMonitor telepítésére egy ASP.NET Core alkalmazáshoz. Ez akkor is igaz, ha ASP.NET Core alkalmazás az IIS-ben található.

- Hogyan ellenőrizhetem, hogy az engedélyezés sikeres volt-e?

  - A [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) parancsmag segítségével ellenőrizheti, hogy az engedélyezés sikeres volt-e.
  - Azt javasoljuk, hogy [az Élő metrikák](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) segítségével gyorsan megállapíthatja, hogy az alkalmazás telemetriai adatokat küld-e.

  - A Log [Analytics](../log-query/get-started-portal.md) segítségével is felsorolhatja az összes olyan felhőbeli szerepkört, amely jelenleg telemetriai adatokat küld:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>További lépések

A telemetriai adatok megtekintése:

* [Fedezze fel a teljesítmény](../../azure-monitor/app/metrics-explorer.md) és a használat figyeléséhez.
* [Keresés események és naplók](../../azure-monitor/app/diagnostic-search.md) diagnosztizálására problémákat.
* [Az Analytics használata](../../azure-monitor/app/analytics.md) fejlettebb lekérdezésekhez.
* [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).

További telemetriai funkciók hozzáadása:

* [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely elérhető maradjon.
* [Webes ügyfél telemetriai adatok](../../azure-monitor/app/javascript.md) hozzáadásával megtekintheti a weblapkódból származó kivételeket, és engedélyezheti a nyomkövetési hívásokat.
* [Adja hozzá az Application Insights SDK-t a kódhoz,](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhívásokat szúrhat be.

