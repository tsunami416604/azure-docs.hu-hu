---
title: 'Az Azure Állapotfigyelőt v2 API-leírások: A rendszerállapot-motor |} A Microsoft Docs'
description: Állapot figyelő v2 API-hivatkozás. Enable-InstrumentationEngine. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
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
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514370"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>A figyelő v2 API állapota: Enable-InstrumentationEngine (v0.2.1-alpha)

Ez a cikk ismerteti, amely tagja a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Leírás

Lehetővé teszi, hogy a rendszerállapot-motor bizonyos beállításkulcsok beállításával.
Indítsa újra az IIS a módosítások érvénybe léptetéséhez.

A rendszerállapot-motor egészítheti ki a .NET SDK-k által gyűjtött adatokat.
Azt gyűjti az eseményeket és az állapotüzeneteket, amelyeket a felügyelt folyamat végrehajtását ismertetik. Ezen események és az üzenetek között függőségi eredménykódok, HTTP-műveletek és SQL-parancsszöveg.

A rendszerállapot-motor engedélyezze, ha:
- Már engedélyezve van az Enable-parancsmaggal figyelését, de nem engedélyezte a rendszerállapot-motor.
- Az alkalmazás a .NET SDK-k manuálisan úgy alakította, és szeretné, hogy további telemetriai funkciók.

> [!IMPORTANT] 
> Ez a parancsmag megköveteli egy PowerShell-munkamenetet rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE] 
> - Ez a parancsmag megköveteli, hogy tekintse át és fogadja el a licenc és az adatvédelmi nyilatkozatot.
> - A rendszerállapot-motor hozzáadja a további erőforrásokra, és alapértelmezés szerint ki van kapcsolva.

## <a name="examples"></a>Példák

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Paraméterek

### <a name="-acceptlicense"></a>-AcceptLicense
**Nem kötelező.** Használja ezt a kapcsolót a licenc és az adatvédelmi nyilatkozatot távfelügyelt telepítés fogadására.

### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Használja ezt a kapcsolót a kimenetben a részletes naplókat.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Példa a kimenetre való sikeres engedélyezése a rendszerállapot-motor

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [A konfiguráció első](status-monitor-v2-api-get-config.md) annak ellenőrzéséhez, hogy a beállításainak megfelelően rögzítve.
 - [A kapcsolat állapotának lekérése](status-monitor-v2-api-get-status.md) vizsgálhatja meg a figyelés.
