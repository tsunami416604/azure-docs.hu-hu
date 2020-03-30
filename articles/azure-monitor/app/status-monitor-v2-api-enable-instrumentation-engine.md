---
title: Az Azure Application Insights Ügynök API-hivatkozása
description: Application Insights Ügynök API-referencia. Enable-InstrumentationEngine. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671356"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Application Insights-ügynök API: Enable-InstrumentationEngine

Ez a cikk az [Az.ApplicationMonitor PowerShell modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagjaként készült parancsmalapot ismerteti.

## <a name="description"></a>Leírás

Néhány beállításkulcs beállításával engedélyezi a műszerezési motort.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

A műszerezési motor kiegészítheti a .
Olyan eseményeket és üzeneteket gyűjt, amelyek egy felügyelt folyamat végrehajtását írják le. Ezek az események és üzenetek közé tartoznak a függőségi eredménykódok, a HTTP-műveletek és [az SQL parancsszöveg.](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)

Engedélyezze a műszermotort, ha:
- Már engedélyezte a figyelést a Parancsmag engedélyezése, de nem engedélyezte a műszermotort.
- Manuálisan instrumented az alkalmazást a .NET SDK-k, és szeretné összegyűjteni további telemetriai adatokat.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

> [!NOTE] 
> - Ez a parancsmag megköveteli, hogy tekintse át és fogadja el licencünket és adatvédelmi nyilatkozatunkat.
> - A műszerezési motor további többletterhelést jelent, és alapértelmezés szerint ki van kapcsolva.

## <a name="examples"></a>Példák

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Paraméterek

### <a name="-acceptlicense"></a>-AcceptLicense
**Választható.** Ezzel a kapcsolóval elfogadhatja a licencet és az adatvédelmi nyilatkozatot a fej nélküli telepítésekben.

### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat kell kiadnia.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Példa kimenet a műszerezési motor sikeres engedélyezéséből

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - A beállítások helyes rögzítésének ellenőrzése a [konfigurációs eszközre.](status-monitor-v2-api-get-config.md)
 - [Kap az állapot,](status-monitor-v2-api-get-status.md) hogy vizsgálja meg a megfigyelést.
