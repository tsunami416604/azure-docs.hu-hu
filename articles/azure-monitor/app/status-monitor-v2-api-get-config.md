---
title: 'Az Azure Állapotmonitor v2 API-referenciája: Konfiguráció beolvasása | Microsoft Docs'
description: Állapotmonitor v2 API-referenciát. Get-ApplicationInsightsMonitoringConfig. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
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
ms.openlocfilehash: 1da24a93a671b388c44d96711bc2fe007f33d197
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033155"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig"></a>Állapotmonitor v2 API: Get-ApplicationInsightsMonitoringConfig

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

## <a name="output"></a>Output


#### <a name="example-output-from-reading-the-config-file"></a>Példa kimenetre a konfigurációs fájl olvasásakor

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>További lépések

  A telemetriai adatok megtekintése:
 - [Ismerje](../../azure-monitor/app/metrics-explorer.md) meg a mérőszámokat a teljesítmény és a használat figyeléséhez.
- [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md) a problémák diagnosztizálásához.
- További [](../../azure-monitor/app/analytics.md) speciális lekérdezésekhez használja az elemzést.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely élőben maradjon.
- [Vegyen fel webes ügyfél-telemetria](../../azure-monitor/app/javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
- [Adja hozzá a Application INSIGHTS SDK-t a kódhoz](../../azure-monitor/app/asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el.
 
 További Állapotmonitor v2:
 - A Állapotmonitor v2 [hibáinak megoldásához](status-monitor-v2-troubleshoot.md) használja az útmutatót.
 - Módosítsa a konfigurációt a [set config](status-monitor-v2-api-set-config.md) parancsmag használatával.
