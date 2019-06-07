---
title: 'Az Azure Állapotfigyelőt v2 API-leírások: Konfiguráció beolvasása |} A Microsoft Docs'
description: Állapot figyelő v2 API-hivatkozás. Get-ApplicationInsightsMonitoringConfig. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
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
ms.openlocfilehash: 1f7061c9823ddc8ff7f8f42976041f1c9ff68fc0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514386"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>A figyelő v2 API állapota: Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Ez a cikk ismerteti, amely tagja a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Leírás

A konfigurációs fájl beolvasása, és kinyomtatja az értékeket a konzol.

> [!IMPORTANT] 
> Ez a parancsmag megköveteli egy PowerShell-munkamenetet rendszergazdai jogosultságokkal rendelkezik.

## <a name="examples"></a>Példák

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Paraméterek

Nem szükséges paramétereket.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-reading-the-config-file"></a>A konfigurációs fájl olvasásakor. a példa a kimenetre

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>További lépések

  A telemetriai adatok megtekintése:
 - [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md) teljesítményének figyelése és használati.
- [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához.
- Használat [analytics](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
- [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat engedélyezése.
- [Az Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhíváskat szúrhasson.
 
 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítása](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
 - Módosítja a konfiguráció segítségével a [konfigurációs beállítása](status-monitor-v2-api-set-config.md) parancsmagot.
