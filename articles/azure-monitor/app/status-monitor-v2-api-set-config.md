---
title: Az Azure Application Insights Agent API-referenciája
description: Application Insights Agent API-referenciája. Set-ApplicationInsightsMonitoringConfig. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: d90739fbdc862d67dc2ce0f1dfdf5af5f4089a44
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899678"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights ügynök API: set-ApplicationInsightsMonitoringConfig

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
- a `MachineFilter` a `'.*'` helyettesítő karakterrel egyezik meg az aktuális számítógéppel.
- a `AppFilter='WebAppExclude'` `null` kialakítási kulcsot biztosít. A megadott alkalmazás nem lesz kialakítva.
- `AppFilter='WebAppOne'` – a megadott alkalmazást egyedi kialakítási kulcsként rendeli hozzá.
- `AppFilter='WebAppTwo'` – a megadott alkalmazást egyedi kialakítási kulcsként rendeli hozzá.
- Végezetül `AppFilter` a `'.*'` helyettesítő karaktert is használja a korábbi szabályoknak nem megfelelő webalkalmazások és az alapértelmezett kialakítási kulcs hozzárendeléséhez.
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
A `MachineFilter` beállítás megadásával egyetlen telepítési parancsfájlt is létrehozhat több számítógép számára.

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


## <a name="output"></a>Kimenet

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

## <a name="next-steps"></a>Következő lépések

  A telemetriai adatok megtekintése:
 - [Ismerje meg a mérőszámokat](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez.
- [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md) a problémák diagnosztizálásához.
- További speciális lekérdezésekhez [használja az elemzést](../../azure-monitor/app/analytics.md) .
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely élőben maradjon.
- [Vegyen fel webes ügyfél-telemetria](../../azure-monitor/app/javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
- [Adja hozzá a Application INSIGHTS SDK-t a kódjához](../../azure-monitor/app/asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el
 
 Több Application Insights-ügynökkel:
 - Az útmutató segítségével Application Insights-ügynököt lehet [elhárítani](status-monitor-v2-troubleshoot.md) .
 - [A konfiguráció beszerzésével](status-monitor-v2-api-get-config.md) ellenőrizze, hogy a beállítások megfelelően vannak-e rögzítve.
 - A figyelés ellenőrzésének [állapotának beolvasása](status-monitor-v2-api-get-status.md) .
