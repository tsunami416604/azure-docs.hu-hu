---
title: 'Az Azure Állapotmonitor v2 API-referenciája: Instrumentation-motor engedélyezése | Microsoft Docs'
description: Állapotmonitor v2 API-referenciát. Az Enable-InstrumentationEngine. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
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
ms.openlocfilehash: d4683a1cad5172f7104e745433bd141bcf36d56f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326376"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine"></a>Állapotmonitor v2 API: InstrumentationEngine engedélyezése

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
**Nem kötelező.** Ezzel a kapcsolóval fogadhatja el a fej nélküli telepítések licencét és adatvédelmi nyilatkozatát.

### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezt a kapcsolót használja a részletes naplók kinyomtatásához.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Példa a Instrumentation-motor sikeres engedélyezésének eredményére

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [A konfiguráció](status-monitor-v2-api-get-config.md) beszerzésével ellenőrizze, hogy a beállítások megfelelően vannak-e rögzítve.
 - [A](status-monitor-v2-api-get-status.md) figyelés ellenőrzésének állapotának beolvasása.
