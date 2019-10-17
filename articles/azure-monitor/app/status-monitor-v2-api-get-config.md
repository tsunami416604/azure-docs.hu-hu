---
title: 'Azure Application Insights Agent API-referenciák: a konfiguráció beolvasása | Microsoft Docs'
description: Application Insights Agent API-referenciája. Get-ApplicationInsightsMonitoringConfig. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: e0b389dfb27443b9b26b8ed650399c641c55eb86
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388263"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Application Insights ügynök API: Get-ApplicationInsightsMonitoringConfig

Ez a cikk olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

## <a name="description"></a>Leírás

Lekéri a konfigurációs fájlt, és kiírja az értékeket a-konzolra.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

## <a name="examples"></a>Példák

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Paraméterek

Nincs szükség paraméterre.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-reading-the-config-file"></a>Példa kimenetre a konfigurációs fájl olvasásakor

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Következő lépések

  A telemetriai adatok megtekintése:
 - [Ismerje meg a mérőszámokat](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez.
- [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md) a problémák diagnosztizálásához.
- További speciális lekérdezésekhez használja az [elemzést](../../azure-monitor/app/analytics.md) .
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely élőben maradjon.
- [Vegyen fel webes ügyfél-telemetria](../../azure-monitor/app/javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
- [Adja hozzá a Application INSIGHTS SDK-t a kódhoz](../../azure-monitor/app/asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el.
 
 Több Application Insights-ügynökkel:
 - Az útmutató segítségével Application Insights-ügynököt lehet [elhárítani](status-monitor-v2-troubleshoot.md) .
 - Módosítsa a konfigurációt a [set config](status-monitor-v2-api-set-config.md) parancsmag használatával.
