---
title: 'Az Azure Állapotmonitor v2 API-referenciája: Konfiguráció beállítása | Microsoft Docs'
description: Állapotmonitor v2 API-referenciát. Set-ApplicationInsightsMonitoringConfig. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
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
ms.openlocfilehash: f3a55caba13b3b96884d446e0750d9fb67a343df
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326289"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig"></a>Állapotmonitor v2 API: Set-ApplicationInsightsMonitoringConfig

Ez a dokumentum egy olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

## <a name="description"></a>Leírás

A konfigurációs fájl teljes újratelepítés nélkül állítható be.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.


## <a name="examples"></a>Példák

### <a name="example-with-a-single-instrumentation-key"></a>Példa egyetlen kialakítási kulccsal
Ebben a példában az aktuális számítógépen lévő összes alkalmazás hozzá lesz rendelve egyetlen kialakítási kulcshoz.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Példa a kialakítási kulcs megfeleltetésére
Ebben a példában:
- `MachineFilter`az aktuális számítógépnek felel meg a `'.*'` helyettesítő karakter használatával.
- `AppFilter='WebAppExclude'``null` rendszerállapot-kulcsot biztosít. A megadott alkalmazás nem lesz kialakítva.
- `AppFilter='WebAppOne'`egy egyedi kialakítási kulcsot rendel hozzá a megadott alkalmazáshoz.
- `AppFilter='WebAppTwo'`egy egyedi kialakítási kulcsot rendel hozzá a megadott alkalmazáshoz.
- Végezetül a`'.*'` helyettesítő karaktert ishasználjaakorábbiszabályoknaknemmegfelelőwebalkalmazásokésazalapértelmezettkialakításikulcshozzárendeléséhez.`AppFilter`
- A szóközök hozzáadódnak az olvashatósághoz.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Paraméterek

### <a name="-instrumentationkey"></a>– InstrumentationKey
**Szükséges.** Ezzel a paraméterrel egyetlen rendszerállapot-kulcsot adhat meg a célszámítógépen lévő összes alkalmazás számára.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Szükséges.** Ezzel a paraméterrel több rendszerállapot-kulcs és az egyes alkalmazások által használt rendszerállapot-kulcsok leképezése is megadható.
A beállítással `MachineFilter`egyetlen telepítési parancsfájlt is létrehozhat több számítógép számára.

> [!IMPORTANT]
> Az alkalmazások a szabályok megadásának sorrendjében egyeznek meg a szabályokkal. Ezért a legpontosabb szabályokat kell megadnia az első és a legáltalánosabb szabályok közül.

#### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- A **MachineFilter** a számítógép C# vagy a virtuális gép nevének kötelező regexje.
    - a ". *" mindennek megfelel
    - A "számítógépnév" csak a megadott névvel rendelkező számítógépekhez fog megfelelni.
- A **AppFilter** a számítógép C# vagy a virtuális gép nevének kötelező regexje.
    - a ". *" mindennek megfelel
    - A "ApplicationName" csak a megadott nevű IIS-alkalmazásoknak felel meg.
- A **InstrumentationKey** az előző két szűrőnek megfelelő alkalmazások figyelésének engedélyezéséhez szükséges.
    - Ha a figyelés kizárásához szabályokat szeretne meghatározni, hagyja üresen ezt az értéket.


### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.


## <a name="output"></a>Output

Alapértelmezés szerint nincs kimenet.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Példa részletes kimenetre a konfigurációs fájlnak a-InstrumentationKey használatával történő beállításához

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Példa részletes kimenetre a konfigurációs fájlnak a-InstrumentationKeyMap használatával történő beállításához

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>További lépések

  A telemetriai adatok megtekintése:
 - [Ismerje](../../azure-monitor/app/metrics-explorer.md) meg a mérőszámokat a teljesítmény és a használat figyeléséhez.
- [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md) a problémák diagnosztizálásához.
- További speciális lekérdezésekhez [használja](../../azure-monitor/app/analytics.md) az elemzést.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely élőben maradjon.
- [Vegyen fel webes ügyfél-telemetria](../../azure-monitor/app/javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
- [Adja hozzá a Application INSIGHTS SDK-t a kódjához](../../azure-monitor/app/asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el
 
 További Állapotmonitor v2:
 - A Állapotmonitor v2 [hibáinak megoldásához](status-monitor-v2-troubleshoot.md) használja az útmutatót.
 - [A konfiguráció](status-monitor-v2-api-get-config.md) beszerzésével ellenőrizze, hogy a beállítások megfelelően vannak-e rögzítve.
 - [A](status-monitor-v2-api-get-status.md) figyelés ellenőrzésének állapotának beolvasása.
