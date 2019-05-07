---
title: 'Az Azure állapota figyelő v2 API-leírások: A rendszerállapot-motor |} A Microsoft Docs'
description: Állapot figyelő v2 API referencia engedélyezése – InstrumentationEngine. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: d886aa364ca928d32100c570689f13beb0c682c9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143420"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>A figyelő v2 API állapota: Enable-InstrumentationEngine (v0.2.1-alpha)

Ez a dokumentum ismerteti, amely tartalmazza a szükséges, amelynek a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Leírás

Ez a parancsmag a rendszerállapot-motor engedélyezi bizonyos beállításkulcsok beállításával.
Indítsa újra az IIS a módosítások érvénybe léptetéséhez.

A rendszerállapot-motor egészítheti ki a .NET SDK-k által gyűjtött adatokat.
Események és az üzenetek gyűjtendő, amelyek a felügyelt folyamat végrehajtását ismertetik. Beleértve többek között a függőségi eredménykódok, HTTP-műveletek és SQL-parancsszöveg. 

A rendszerállapot-motor engedélyezze, ha:
- Már engedélyezve van a figyelést az engedélyezése a parancsmag használatával, de nem engedélyezte a InstrumentationEngine.
- A .NET SDK-k segítségével manuálisan úgy alakította, és szeretné, hogy további telemetriai funkciók.

> [!IMPORTANT] 
> Ez a parancsmag egy PowerShell-munkamenetet rendszergazdai jogosultság szükséges.

> [!NOTE] 
> Ez a parancsmag kell, hogy tekintse át és fogadja el a licenc és az adatvédelmi nyilatkozatot.

> [!NOTE] 
> A rendszerállapot-motor hozzáadja a további erőforrásokra, és alapértelmezés szerint ki van kapcsolva.

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
 - [A metrikák áttekintése](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez
- [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához
- [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez
- [Irányítópultok létrehozása](../../azure-monitor/app/app-insights-dashboards.md)
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
- [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat szúrhasson be.
- [Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) , hogy Ön nyomkövetési és naplóhíváskat szúrhasson
 
 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítás](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
 - [A konfiguráció első](status-monitor-v2-api-get-config.md) annak ellenőrzéséhez, hogy a beállításainak megfelelően rögzítve.
 - [A kapcsolat állapotának lekérése](status-monitor-v2-api-get-status.md) vizsgálhatja meg a figyelés.
