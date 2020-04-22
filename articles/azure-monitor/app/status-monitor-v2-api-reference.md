---
title: Az Azure Application Insights .Net Agent API-hivatkozás
description: Application Insights Ügynök API-referencia. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733673"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Monitor Application Insights Ügynök API-referencia

Ez a cikk az [Az.ApplicationMonitor PowerShell modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagjaként készült parancsmalapot ismerteti.

> [!NOTE] 
> - A kezdéshez szüksége van egy műszerkulcsra. További információt az [Erőforrás létrehozása](create-new-resource.md#copy-the-instrumentation-key)című témakörben talál.
> - Ez a parancsmag megköveteli, hogy tekintse át és fogadja el licencünket és adatvédelmi nyilatkozatunkat.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel és emelt szintű végrehajtási szabályzattal rendelkező PowerShell-munkamenet szükséges. További információ: [PowerShell futtatása rendszergazdaként emelt szintű végrehajtási házirenddel.](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)
> - Ez a parancsmag megköveteli, hogy tekintse át és fogadja el licencünket és adatvédelmi nyilatkozatunkat.
> - A műszerezési motor további többletterhelést jelent, és alapértelmezés szerint ki van kapcsolva.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Néhány beállításkulcs beállításával engedélyezi a műszerezési motort.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

A műszerezési motor kiegészítheti a .
Olyan eseményeket és üzeneteket gyűjt, amelyek egy felügyelt folyamat végrehajtását írják le. Ezek az események és üzenetek közé tartoznak a függőségi eredménykódok, a HTTP-műveletek és [az SQL parancsszöveg.](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)

Engedélyezze a műszermotort, ha:
- Már engedélyezte a figyelést a Parancsmag engedélyezése, de nem engedélyezte a műszermotort.
- Manuálisan instrumented az alkalmazást a .NET SDK-k, és szeretné összegyűjteni további telemetriai adatokat.

### <a name="examples"></a>Példák

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Paraméterek

#### <a name="-acceptlicense"></a>-AcceptLicense
**Választható.** Ezzel a kapcsolóval elfogadhatja a licencet és az adatvédelmi nyilatkozatot a fej nélküli telepítésekben.

#### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat kell kiadnia.

### <a name="output"></a>Kimenet


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Példa kimenet a műszerezési motor sikeres engedélyezéséből

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Lehetővé teszi az IIS-alkalmazások kód nélküli csatolását a célszámítógépen.

Ez a parancsmag módosítja az IIS applicationHost.config fájlját, és beállít néhány beállításkulcsot.
Létrehoz egy applicationinsights.ikey.config fájlt is, amely meghatározza az egyes alkalmazások által használt instrumentation kulcsot.
Az IIS indításkor betölti a RedfieldModule modult, amely az alkalmazások indításakor befecskendezi az Application Insights SDK-t az alkalmazásokba.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

Miután engedélyezte a figyelést, azt javasoljuk, hogy [az Élő metrikák](live-stream.md) használatával gyorsan ellenőrizze, hogy az alkalmazás telemetriai adatokat küld-e nekünk.

### <a name="examples"></a>Példák

#### <a name="example-with-a-single-instrumentation-key"></a>Példa egyetlen műszerezési kulccsal
Ebben a példában az aktuális számítógépen lévő összes alkalmazáshoz egyetlen instrumentation kulcs van rendelve.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Példa műszerezési kulcstérképpel
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


### <a name="parameters"></a>Paraméterek

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Kötelező.** Ezzel a paraméterrel egyetlen instrumentation kulcsot biztosíthat a célszámítógépen lévő összes alkalmazás számára.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Kötelező.** Ezzel a paraméterrel több instrumentation kulcsot és az egyes alkalmazások által használt instrumentation kulcsok leképezését biztosítja.
A beállítással egyetlen telepítési parancsfájlt `MachineFilter`hozhat létre több számítógéphez.

> [!IMPORTANT]
> Az alkalmazások a szabályok sorrendjében egyeznek meg a szabályokkal. Ezért először a legkonkrétabb szabályokat kell megadnia, a legáltalánosabb szabályokat pedig utoljára.

##### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** a számítógép vagy a virtuális gép nevének kötelező C# regex.
    - '.*' mindennek megfelel
    - A "Számítógépnév" csak a megadott pontos névvel rendelkező számítógépeket egyezteti.
- **Az AppFilter** az IIS helynevének kötelező C# regex-e. Tudod kap egy listát a helyszínek a szerveren futtatásával a parancsot [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' mindennek megfelel
    - A "SiteName" csak a megadott pontos névvel rendelkező IIS-webhelynek felel meg.
- **InstrumentationKey** szükséges az előző két szűrőnek megfelelő alkalmazások figyelésének engedélyezéséhez.
    - Hagyja meg ezt az értéket null értékű, ha szabályokat szeretne definiálni a figyelés kizárásához.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Választható.** Ezzel a kapcsolóval lehetővé teszi, hogy a műszerezési motor eseményeket és üzeneteket gyűjtsön afelügyelt folyamat végrehajtása során zajló eseményekről és üzenetekről. Ezek az események és üzenetek függőségi eredménykódokat, HTTP-műveleteket és SQL parancsszöveget tartalmaznak.

A műszerezési motor növeli a rezsi, és alapértelmezés szerint ki van kapcsolva.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Választható.** Ezzel a kapcsolóval elfogadhatja a licencet és az adatvédelmi nyilatkozatot a fej nélküli telepítésekben.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Ha webkiszolgáló-fürttel rendelkezik, lehet, hogy [megosztott konfigurációt](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)használ.
A HttpModule nem adható be ebbe a megosztott konfigurációba.
Ez a parancsfájl sikertelen lesz azzal az üzenettel, hogy további telepítési lépésekre van szükség.
Ezzel a kapcsolóval figyelmen kívül hagyhatja ezt az ellenőrzést, és folytathatja az előfeltételek telepítését. További információ: [known conflict-with-iis-shared-configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.

#### <a name="-whatif"></a>-Milenne 
**Közös paraméter.** Ezzel a kapcsolóval tesztelheti és ellenőrizheti a bemeneti paramétereket anélkül, hogy ténylegesen engedélyezné a figyelést.

### <a name="output"></a>Kimenet

#### <a name="example-output-from-a-successful-enablement"></a>Példa kimenetre egy sikeres engedélyezésből

```powershell
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

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Letiltja a műszerezési motort néhány beállításkulcs eltávolításával.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

### <a name="examples"></a>Példák

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Paraméterek 

#### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat kell kiadnia.

### <a name="output"></a>Kimenet


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Példa a műszermotor sikeres letiltásából származó kimenetre

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Letiltja a figyelést a célszámítógépen.
Ez a parancsmag eltávolítja az IIS applicationHost.config fájl szerkesztéseit, és eltávolítja a beállításkulcsokat.

### <a name="examples"></a>Példák

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Paraméterek 

#### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.

### <a name="output"></a>Kimenet


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Példa a figyelés sikeres letiltásának kimenetére

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
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
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Leveszi a konfigurációs fájlt, és kinyomtatja az értékeket a konzolra.

### <a name="examples"></a>Példák

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Paraméterek

Nincs szükség paraméterekre.

### <a name="output"></a>Kimenet


##### <a name="example-output-from-reading-the-config-file"></a>Példa kimenetre a konfigurációs fájl olvasásából

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Ez a parancsmag hibaelhárítási információkat nyújt az Állapotfigyelővel kapcsolatban.
Ezzel a parancsmagmal vizsgálja meg a figyelési állapot, a PowerShell-modul verziója, és vizsgálja meg a futó folyamat.
Ez a parancsmag a figyeléshez szükséges kulcsfájlok verzióadatait és információit jelenti.

### <a name="examples"></a>Példák

#### <a name="example-application-status"></a>Példa: Alkalmazás állapota

Futtassa `Get-ApplicationInsightsMonitoringStatus` a parancsot a webhelyek figyelési állapotának megjelenítéséhez.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

Ebben a példában;
- **A számítógépazonosító** egy névtelen azonosító, amely a kiszolgáló egyedi azonosítására szolgál. Ha létrehoz egy támogatási kérelmet, szükségünk lesz erre az azonosítóra a kiszolgáló naplóinak megkereséséhez.
- **Az alapértelmezett webhely** le van állítva az IIS-ben
- **A DemoWebApp111** elindult az IIS-ben, de nem kapott kéréseket. Ez a jelentés azt mutatja, hogy nincs futó folyamat (ProcessId: nem található).
- **DemoWebApp222** fut, és figyelemmel kíséri (Instrumented: true). A felhasználói konfiguráció alapján a Instrumentation Key xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 egyeztetve lett ehhez az oldalhoz.
- **A DemoWebApp333** manuálisan lett instrumentálva az Application Insights SDK használatával. Az Állapotfigyelő észlelte az SDK-t, és nem figyeli ezt a helyet.


#### <a name="example-powershell-module-information"></a>Példa: A PowerShell moduladatai

Futtassa `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` a parancsot az aktuális modullal kapcsolatos információk megjelenítéséhez:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Példa: Futásidejű állapot

Megvizsgálhatja a folyamatot a műszeres számítógépen, és ellenőrizheti, hogy az összes DL be van-e töltve. Ha a monitorozás működik, legalább 12 DL-t kell betölteni.

Futtassa `Get-ApplicationInsightsMonitoringStatus -InspectProcess`a parancsot:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Paraméterek

#### <a name="no-parameters"></a>(Nincs paraméter)

Alapértelmezés szerint ez a parancsmag jelenti a webes alkalmazások figyelési állapotát.
Ezzel a beállítással ellenőrizheti, hogy az alkalmazás sikeresen lett-e instrumented.
Azt is megtekintheti, hogy melyik Instrumentation key-t párosította a webhelyéhez.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Nem kötelező**. Ezzel a kapcsolóval jelentheti a figyeléshez szükséges DEL-ek verziószámait és elérési útjait.
Akkor használja ezt a beállítást, ha azonosítania kell bármely DLL verzióját, beleértve az Application Insights SDK-t is.

#### <a name="-inspectprocess"></a>-InspectProcess

**Nem kötelező**. Ezzel a kapcsolóval jelentheti, hogy fut-e az IIS.
Emellett külső eszközöket is letölt annak megállapítására, hogy a szükséges DEL-ek betöltődnek-e az IIS futásidejébe.


Ha ez a folyamat bármilyen okból sikertelen, manuálisan is futtathatja a parancsokat:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Nem kötelező**. Csak az InspectProcess segítségével használható. Ezzel a kapcsolóval átugorhat a további eszközök letöltése előtt megjelenő felhasználói kérdést.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Beállítja a konfigurációs fájlt anélkül, hogy teljes újratelepítést végezne.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.


### <a name="examples"></a>Példák

#### <a name="example-with-a-single-instrumentation-key"></a>Példa egyetlen műszerezési kulccsal
Ebben a példában az aktuális számítógépen lévő összes alkalmazáshoz egyetlen instrumentation kulcs lesz rendelve.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Példa műszerezési kulcstérképpel
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

### <a name="parameters"></a>Paraméterek

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Kötelező.** Ezzel a paraméterrel egyetlen instrumentation kulcsot biztosíthat a célszámítógépen lévő összes alkalmazás számára.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Kötelező.** Ezzel a paraméterrel több instrumentation kulcsot és az egyes alkalmazások által használt instrumentation kulcsok leképezését biztosítja.
A beállítással egyetlen telepítési parancsfájlt `MachineFilter`hozhat létre több számítógéphez.

> [!IMPORTANT]
> Az alkalmazások a szabályok sorrendjében egyeznek meg a szabályokkal. Ezért először a legkonkrétabb szabályokat kell megadnia, a legáltalánosabb szabályokat pedig utoljára.

##### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** a számítógép vagy a virtuális gép nevének kötelező C# regex.
    - '.*' mindennek megfelel
    - A "Számítógépnév" csak a megadott nevű számítógépeket egyezteti.
- **AppFilter** a számítógép vagy a virtuális gép nevének kötelező C# regex.
    - '.*' mindennek megfelel
    - Az "ApplicationName" csak a megadott nevű IIS-alkalmazásokat egyezteti.
- **InstrumentationKey** szükséges az előző két szűrőnek megfelelő alkalmazások figyelésének engedélyezéséhez.
    - Hagyja meg ezt az értéket null értékű, ha szabályokat szeretne definiálni a figyelés kizárásához.


#### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.


### <a name="output"></a>Kimenet

Alapértelmezés szerint nincs kimenet.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Példa részletes kimenetre a konfigurációs fájl -InstrumentationKey-en keresztültörténő beállításából

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Példa részletes kimenet reked a konfigurációs fájl beállításából a -InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

[EtW-események](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) gyűjtése a kód nélküli csatolási futásidejű. Ez a parancsmag a [PerfView](https://github.com/microsoft/perfview)futtatásának alternatívája.

Az összegyűjtött eseményeket valós időben nyomtatjuk ki a konzolra, és etl fájlba mentjük. A kimeneti ETL fájlt a [PerfView](https://github.com/microsoft/perfview) további vizsgálatra megnyithatja.

Ez a parancsmag addig fog futni, amíg el nem éri az`Ctrl + C`időtúltöltés időtartamát (alapértelmezett 5 perc), vagy manuálisan le nem állítja ( ).

### <a name="examples"></a>Példák

#### <a name="how-to-collect-events"></a>Hogyan gyűjthetünk eseményeket?

Általában arra kérjük, hogy gyűjtsön eseményeket, hogy kivizsgálja, miért nem a jelentkezését instrumented.

A kód nélküli csatolási futásidejű etw-eseményeket fog kivetni az IIS indításakor és az alkalmazás indításakor.

Az események összegyűjtése:
1. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon hajtsa végre `iisreset /stop` az IIS és az összes webalkalmazás kikapcsolására.
2. A parancsmag végrehajtása
3. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon hajtsa végre `iisreset /start` az IIS elindításához.
4. Próbáljon meg böngészni az alkalmazással.
5. Miután az alkalmazás betöltése befejeződik, manuálisan leállíthatja (`Ctrl + C`) vagy megvárhatja az időtúltöltést.

#### <a name="what-events-to-collect"></a>Milyen eseményeket kell összegyűjteni

Az események gyűjtésesorán három lehetőség közül választhat:
1. A kapcsoló `-CollectSdkEvents` segítségével összegyűjtheti az Application Insights SDK által kibocsátott eseményeket.
2. A kapcsoló `-CollectRedfieldEvents` segítségével összegyűjtheti az Állapotfigyelő és a Redfield Runtime által kibocsátott eseményeket. Ezek a naplók az IIS és az alkalmazások indításának diagnosztizálásakor hasznosak.
3. Mindkét kapcsoló t használja mindkét eseménytípus összegyűjtéséhez.
4. Alapértelmezés szerint, ha nincs megadva kapcsoló, a rendszer mindkét eseménytípust összegyűjti.


### <a name="parameters"></a>Paraméterek

#### <a name="-maxdurationinminutes"></a>-MaxDurationinMinutes
**Választható.** Ezzel a paraméterrel beállíthatja, hogy a parancsfájl mennyi ideig gyűjtsön eseményeket. Az alapértelmezett érték 5 perc.

#### <a name="-logdirectory"></a>-LogDirectory
**Választható.** Ezzel a kapcsolóval állíthatja be az ETL-fájl kimeneti könyvtárát. Alapértelmezés szerint ez a fájl a PowerShell-modulok könyvtárban jön létre. A teljes elérési út megjelenik a parancsfájl végrehajtása során.


#### <a name="-collectsdkevents"></a>-CollectSdkEsemények
**Választható.** Ezzel a kapcsolóval összegyűjtheti az Application Insights SDK-eseményeket.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEsemények
**Választható.** Ezzel a kapcsolóval eseményeket gyűjthet az Állapotfigyelőből és a Redfield-futásórából.

#### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat kell kiadnia.



### <a name="output"></a>Kimenet


#### <a name="example-of-application-startup-logs"></a>Példa az alkalmazások indítási naplóira
```powershell
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
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






