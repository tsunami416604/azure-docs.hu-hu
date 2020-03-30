---
title: Az Azure Application Insights Ügynök API-hivatkozása
description: Application Insights Ügynök API-referencia. Get-ApplicationInsightsMonitoringConfig. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 93ab8f613e5634b2eabe7c02189e223d3dfbb0a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671273"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Application Insights-ügynök API: Get-ApplicationInsightsMonitoringConfig

Ez a cikk az [Az.ApplicationMonitor PowerShell modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagjaként készült parancsmalapot ismerteti.

## <a name="description"></a>Leírás

Leveszi a konfigurációs fájlt, és kinyomtatja az értékeket a konzolra.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

## <a name="examples"></a>Példák

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Paraméterek

Nincs szükség paraméterekre.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-reading-the-config-file"></a>Példa kimenetre a konfigurációs fájl olvasásából

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>További lépések

  A telemetriai adatok megtekintése:
 - [Fedezze fel a teljesítmény](../../azure-monitor/app/metrics-explorer.md) és a használat figyeléséhez.
- [Keresés események és naplók](../../azure-monitor/app/diagnostic-search.md) diagnosztizálására problémákat.
- Speciális [lekérdezésekhez használjon analitikát.](../../azure-monitor/app/analytics.md)
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely elérhető maradjon.
- [Webes ügyfél telemetriai adatok](../../azure-monitor/app/javascript.md) hozzáadásával megtekintheti a weblapkódból származó kivételeket, és engedélyezheti a nyomkövetési hívásokat.
- [Adja hozzá az Application Insights SDK-t a kódhoz,](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhívásokat szúrhat be.
 
 További információk az Application Insights-ügynökkel:
 - Útmutatónk segítségével [elháríthatja](status-monitor-v2-troubleshoot.md) az Application Insights-ügynök hibáit.
 - Módosítsa a konfigurációt a [Set config](status-monitor-v2-api-set-config.md) parancsmag használatával.
