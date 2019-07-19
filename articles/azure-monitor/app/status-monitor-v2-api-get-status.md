---
title: 'Az Azure Állapotmonitor v2 API-referenciája: Állapot beolvasása | Microsoft Docs'
description: Állapotmonitor v2 API-referenciát. Get-ApplicationInsightsMonitoringStatus. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: b298d73620990dd8f6c6577818adaef9788122e9
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326330"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus"></a>Állapotmonitor v2 API: Get-ApplicationInsightsMonitoringStatus

Ez a cikk olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

## <a name="description"></a>Leírás

Ez a parancsmag hibaelhárítási információkat biztosít a Állapotmonitorról.
Ezzel a parancsmaggal vizsgálhatja meg a figyelési állapotot, a PowerShell-modul verzióját, valamint ellenőrizheti a futó folyamatot.
Ez a parancsmag a figyeléshez szükséges kulcsfontosságú fájlokkal kapcsolatos információkat és információkat fogja jelenteni.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

## <a name="examples"></a>Példák

### <a name="example-application-status"></a>Példa: Alkalmazás állapota

Futtassa a parancsot `Get-ApplicationInsightsMonitoringStatus` a webhelyek figyelési állapotának megjelenítéséhez.

```
Machine Identifier:
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

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
- A **számítógép-azonosító** egy névtelen azonosító, amely a kiszolgáló egyedi azonosítására szolgál. Ha támogatási kérelmet hoz létre, erre az AZONOSÍTÓra szüksége lesz a kiszolgáló naplófájljainak megkereséséhez.
- Az **alapértelmezett** webhely le van ÁLLÍTVA az IIS-ben
- A **DemoWebApp111** el lett indítva az IIS-ben, de nem kapott kérelmeket. Ez a jelentés azt jeleníti meg, hogy nincs futó folyamat (folyamatazonosító: nem található).
- A **DemoWebApp222** fut, és folyamatban van a figyelése (instrumentum: true). A felhasználói konfiguráció alapján a rendszerállapot-azonosító kulcs XXXXXXXX-XXXX-XXXX-XXXX-xxxxxxxxx123 a helyhez lett egyeztetve.
- A **DemoWebApp333** manuálisan lett kialakítva az Application Insights SDK használatával. Állapotmonitor észlelte az SDK-t, és nem figyeli ezt a helyet.


### <a name="example-powershell-module-information"></a>Példa: PowerShell-modul adatai

Futtassa a parancsot `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` az aktuális modul adatainak megjelenítéséhez:

```
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

### <a name="example-runtime-status"></a>Példa: Futtatókörnyezet állapota

Megvizsgálhatja a folyamatot a műszeres számítógépen, és ellenőrizheti, hogy az összes DLL-fájl be van-e töltve. Ha a figyelés működik, legalább 12 DLL-t be kell tölteni.

Futtassa a parancsot `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


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

## <a name="parameters"></a>Paraméterek

### <a name="no-parameters"></a>(Nincsenek paraméterek)

Alapértelmezés szerint ez a parancsmag a webalkalmazások figyelési állapotát fogja jelenteni.
Ezzel a beállítással ellenőrizheti, hogy az alkalmazás sikeresen létrejött-e.
Azt is megtekintheti, hogy melyik rendszerállapot-kulcs felelt meg a helynek.


### <a name="-powershellmodule"></a>-PowerShellModule
**Választható**. Ezzel a kapcsolóval jelentheti a figyeléshez szükséges DLL-ek verziószámait és elérési útját.
Akkor használja ezt a beállítást, ha meg kell határoznia bármely DLL verzióját, beleértve a Application Insights SDK-t is.

### <a name="-inspectprocess"></a>-InspectProcess

**Választható**. Ezzel a kapcsolóval jelentheti, hogy az IIS fut-e.
Emellett letölti a külső eszközöket is annak megállapításához, hogy a szükséges DLL-fájlok betöltődik-e az IIS-futtatókörnyezetbe.


Ha a folyamat bármilyen okból meghiúsul, manuálisan is futtathatja ezeket a parancsokat:
- iisreset.exe /status
- [handle64. exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p W3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights
- [listdlls64. exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) W3wp | findstr/I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Választható**. Csak a InspectProcess használatával használható. Ezzel a kapcsolóval átugorhatja a további eszközök letöltése előtt megjelenő felhasználói üzenetet.


## <a name="next-steps"></a>További lépések

 További Állapotmonitor v2:
 - A Állapotmonitor v2 [hibáinak megoldásához](status-monitor-v2-troubleshoot.md) használja az útmutatót.
