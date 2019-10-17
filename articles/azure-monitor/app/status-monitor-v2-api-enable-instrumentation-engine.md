---
title: 'Azure Application Insights Agent API-referenciák: Instrumentation-motor engedélyezése | Microsoft Docs'
description: Application Insights Agent API-referenciája. Az Enable-InstrumentationEngine. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
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
ms.openlocfilehash: 9958121c38b96dc9cfa4dda2812fa9ce2b18d785
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388289"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Application Insights ügynök API: Enable-InstrumentationEngine

Ez a cikk olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

## <a name="description"></a>Leírás

A rendszerállapot-kezelő motor egyes kulcsainak beállításával engedélyezhető.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

A kialakítási motor a .NET SDK-k által gyűjtött adatokat is kiegészítheti.
Olyan eseményeket és üzeneteket gyűjt, amelyek leírják a felügyelt folyamat végrehajtását. Ezek az események és üzenetek függőségi eredmény kódokat, HTTP-műveleteket és [SQL-parancsok szövegét](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)tartalmazzák.

A rendszerállapot-kezelő motor engedélyezése, ha:
- Már engedélyezte a figyelést az Enable parancsmaggal, de nem engedélyezte a kialakítási motort.
- Manuálisan alakította ki az alkalmazást a .NET SDK-val, és további telemetria szeretne gyűjteni.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

> [!NOTE] 
> - Ehhez a parancsmaghoz meg kell vizsgálni és el kell fogadnia a licenc-és adatvédelmi nyilatkozatot.
> - A Instrumentation-motor további terhelést hoz létre, és alapértelmezés szerint ki van kapcsolva.

## <a name="examples"></a>Példák

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Paraméterek

### <a name="-acceptlicense"></a>-AcceptLicense
**Választható.** Ezzel a kapcsolóval fogadhatja el a fej nélküli telepítések licencét és adatvédelmi nyilatkozatát.

### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezt a kapcsolót használja a részletes naplók kinyomtatásához.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Példa a Instrumentation-motor sikeres engedélyezésének eredményére

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [A konfiguráció beszerzésével](status-monitor-v2-api-get-config.md) ellenőrizze, hogy a beállítások megfelelően vannak-e rögzítve.
 - A figyelés ellenőrzésének [állapotának beolvasása](status-monitor-v2-api-get-status.md) .
