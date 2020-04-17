---
title: Az Azure Application Insights Ügynök API-hivatkozása
description: Application Insights Ügynök API-referencia. Enable-ApplicationInsightsMonitoring. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: f5d66c5c21f7491e3bdc6bb70f693c3b98bf62dd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536743"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Application Insights-ügynök API: Enable-ApplicationInsightsMonitoring

Ez a cikk az [Az.ApplicationMonitor PowerShell modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagjaként készült parancsmalapot ismerteti.

## <a name="description"></a>Leírás

Lehetővé teszi az IIS-alkalmazások kód nélküli csatolását a célszámítógépen.

Ez a parancsmag módosítja az IIS applicationHost.config fájlját, és beállít néhány beállításkulcsot.
Létrehoz egy applicationinsights.ikey.config fájlt is, amely meghatározza az egyes alkalmazások által használt instrumentation kulcsot.
Az IIS indításkor betölti a RedfieldModule modult, amely az alkalmazások indításakor befecskendezi az Application Insights SDK-t az alkalmazásokba.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

Miután engedélyezte a figyelést, azt javasoljuk, hogy [az Élő metrikák](live-stream.md) használatával gyorsan ellenőrizze, hogy az alkalmazás telemetriai adatokat küld-e nekünk.


> [!NOTE] 
> - A kezdéshez szüksége van egy műszerkulcsra. További információt az [Erőforrás létrehozása](create-new-resource.md#copy-the-instrumentation-key)című témakörben talál.
> - Ez a parancsmag megköveteli, hogy tekintse át és fogadja el licencünket és adatvédelmi nyilatkozatunkat.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel és emelt szintű végrehajtási szabályzattal rendelkező PowerShell-munkamenet szükséges. További információ: [PowerShell futtatása rendszergazdaként emelt szintű végrehajtási házirenddel.](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)

## <a name="examples"></a>Példák

### <a name="example-with-a-single-instrumentation-key"></a>Példa egyetlen műszerezési kulccsal
Ebben a példában az aktuális számítógépen lévő összes alkalmazáshoz egyetlen instrumentation kulcs van rendelve.

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
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Paraméterek

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Kötelező.** Ezzel a paraméterrel egyetlen instrumentation kulcsot biztosíthat a célszámítógépen lévő összes alkalmazás számára.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Kötelező.** Ezzel a paraméterrel több instrumentation kulcsot és az egyes alkalmazások által használt instrumentation kulcsok leképezését biztosítja.
A beállítással egyetlen telepítési parancsfájlt `MachineFilter`hozhat létre több számítógéphez.

> [!IMPORTANT]
> Az alkalmazások a szabályok sorrendjében egyeznek meg a szabályokkal. Ezért először a legkonkrétabb szabályokat kell megadnia, a legáltalánosabb szabályokat pedig utoljára.

#### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** a számítógép vagy a virtuális gép nevének kötelező C# regex.
    - '.*' mindennek megfelel
    - A "Számítógépnév" csak a megadott pontos névvel rendelkező számítógépeket egyezteti.
- **Az AppFilter** az IIS helynevének kötelező C# regex-e. Tudod kap egy listát a helyszínek a szerveren futtatásával a parancsot [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' mindennek megfelel
    - A "SiteName" csak a megadott pontos névvel rendelkező IIS-webhelynek felel meg.
- **InstrumentationKey** szükséges az előző két szűrőnek megfelelő alkalmazások figyelésének engedélyezéséhez.
    - Hagyja meg ezt az értéket null értékű, ha szabályokat szeretne definiálni a figyelés kizárásához.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Választható.** Ezzel a kapcsolóval lehetővé teszi, hogy a műszerezési motor eseményeket és üzeneteket gyűjtsön afelügyelt folyamat végrehajtása során zajló eseményekről és üzenetekről. Ezek az események és üzenetek függőségi eredménykódokat, HTTP-műveleteket és SQL parancsszöveget tartalmaznak.

A műszerezési motor növeli a rezsi, és alapértelmezés szerint ki van kapcsolva.

### <a name="-acceptlicense"></a>-AcceptLicense
**Választható.** Ezzel a kapcsolóval elfogadhatja a licencet és az adatvédelmi nyilatkozatot a fej nélküli telepítésekben.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Ha webkiszolgáló-fürttel rendelkezik, lehet, hogy [megosztott konfigurációt](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)használ.
A HttpModule nem adható be ebbe a megosztott konfigurációba.
Ez a parancsfájl sikertelen lesz azzal az üzenettel, hogy további telepítési lépésekre van szükség.
Ezzel a kapcsolóval figyelmen kívül hagyhatja ezt az ellenőrzést, és folytathatja az előfeltételek telepítését. További információ: [known conflict-with-iis-shared-configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.

### <a name="-whatif"></a>-Milenne 
**Közös paraméter.** Ezzel a kapcsolóval tesztelheti és ellenőrizheti a bemeneti paramétereket anélkül, hogy ténylegesen engedélyezné a figyelést.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-a-successful-enablement"></a>Példa kimenetre egy sikeres engedélyezésből

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>További lépések

  A telemetriai adatok megtekintése:
 - [Fedezze fel a teljesítmény](../../azure-monitor/platform/metrics-charts.md) és a használat figyeléséhez.
- [Keresés események és naplók](../../azure-monitor/app/diagnostic-search.md) diagnosztizálására problémákat.
- [Az Analytics használata](../../azure-monitor/app/analytics.md) fejlettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely elérhető maradjon.
- [Webes ügyfél telemetriai adatok](../../azure-monitor/app/javascript.md) hozzáadásával megtekintheti a weblapkódból származó kivételeket, és engedélyezheti a nyomkövetési hívásokat.
- [Adja hozzá az Application Insights SDK-t a kódhoz,](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhívásokat szúrhat be.
 
 További információk az Application Insights-ügynökkel:
 - Útmutatónk segítségével [elháríthatja](status-monitor-v2-troubleshoot.md) az Application Insights-ügynök hibáit.
 - A beállítások helyes rögzítésének ellenőrzése a [konfigurációs eszközre.](status-monitor-v2-api-get-config.md)
 - [Kap az állapot,](status-monitor-v2-api-get-status.md) hogy vizsgálja meg a megfigyelést.
