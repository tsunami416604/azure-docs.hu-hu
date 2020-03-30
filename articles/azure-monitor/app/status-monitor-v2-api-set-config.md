---
title: Az Azure Application Insights Ügynök API-hivatkozása
description: Application Insights Ügynök API-referencia. Set-ApplicationInsightsMonitoringConfig. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 1226b3e10adf786ed3335844a5d3f4e530911705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671239"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights-ügynök API: Set-ApplicationInsightsMonitoringConfig

Ez a dokumentum az [Az.ApplicationMonitor PowerShell modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagjaként készült parancsmalapot ismerteti.

## <a name="description"></a>Leírás

Beállítja a konfigurációs fájlt anélkül, hogy teljes újratelepítést végezne.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.


## <a name="examples"></a>Példák

### <a name="example-with-a-single-instrumentation-key"></a>Példa egyetlen műszerezési kulccsal
Ebben a példában az aktuális számítógépen lévő összes alkalmazáshoz egyetlen instrumentation kulcs lesz rendelve.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Példa műszerezési kulcstérképpel
Ebben a példában:
- `MachineFilter`a helyettesítő karakter használatával `'.*'` megegyezik az aktuális számítógéppel.
- `AppFilter='WebAppExclude'``null` műszerezési kulcsot biztosít. A megadott alkalmazás nem lesz instrumented.
- `AppFilter='WebAppOne'`a megadott alkalmazáshoz egyedi instrumentation kulcsot rendel.
- `AppFilter='WebAppTwo'`a megadott alkalmazáshoz egyedi instrumentation kulcsot rendel.
- Végül `AppFilter` is használja `'.*'` a helyettesítő karaktert, hogy megfeleljen az összes olyan webalkalmazást, amely nem felel meg a korábbi szabályok, és rendeljen hozzá egy alapértelmezett instrumentation kulcs.
- Az olvashatóság érdekében a szóközök kerülnek hozzáadásra.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>Paraméterek

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Szükséges.** Ezzel a paraméterrel egyetlen instrumentation kulcsot biztosíthat a célszámítógépen lévő összes alkalmazás számára.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Szükséges.** Ezzel a paraméterrel több instrumentation kulcsot és az egyes alkalmazások által használt instrumentation kulcsok leképezését biztosítja.
A beállítással egyetlen telepítési parancsfájlt `MachineFilter`hozhat létre több számítógéphez.

> [!IMPORTANT]
> Az alkalmazások a szabályok sorrendjében egyeznek meg a szabályokkal. Ezért először a legkonkrétabb szabályokat kell megadnia, a legáltalánosabb szabályokat pedig utoljára.

#### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** a számítógép vagy a virtuális gép nevének kötelező C# regex.
    - '.*' mindennek megfelel
    - A "Számítógépnév" csak a megadott nevű számítógépeket egyezteti.
- **AppFilter** a számítógép vagy a virtuális gép nevének kötelező C# regex.
    - '.*' mindennek megfelel
    - Az "ApplicationName" csak a megadott nevű IIS-alkalmazásokat egyezteti.
- **InstrumentationKey** szükséges az előző két szűrőnek megfelelő alkalmazások figyelésének engedélyezéséhez.
    - Hagyja meg ezt az értéket null értékű, ha szabályokat szeretne definiálni a figyelés kizárásához.


### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.


## <a name="output"></a>Kimenet

Alapértelmezés szerint nincs kimenet.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Példa részletes kimenetre a konfigurációs fájl -InstrumentationKey-en keresztültörténő beállításából

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Példa részletes kimenet reked a konfigurációs fájl beállításából a -InstrumentationKeyMap

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
 - [Fedezze fel a teljesítmény](../../azure-monitor/app/metrics-explorer.md) és a használat figyeléséhez.
- [Keresés események és naplók](../../azure-monitor/app/diagnostic-search.md) diagnosztizálására problémákat.
- [Az Analytics használata](../../azure-monitor/app/analytics.md) fejlettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely elérhető maradjon.
- [Webes ügyfél telemetriai adatok](../../azure-monitor/app/javascript.md) hozzáadásával megtekintheti a weblapkódból származó kivételeket, és engedélyezheti a nyomkövetési hívásokat.
- [Adja hozzá az Application Insights SDK-t a kódhoz,](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhívásokat szúrhat be
 
 További információk az Application Insights-ügynökkel:
 - Útmutatónk segítségével [elháríthatja](status-monitor-v2-troubleshoot.md) az Application Insights-ügynök hibáit.
 - A beállítások helyes rögzítésének ellenőrzése a [konfigurációs eszközre.](status-monitor-v2-api-get-config.md)
 - [Kap az állapot,](status-monitor-v2-api-get-status.md) hogy vizsgálja meg a megfigyelést.
