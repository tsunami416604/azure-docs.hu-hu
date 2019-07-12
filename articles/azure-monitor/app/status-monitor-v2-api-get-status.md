---
title: 'Az Azure Állapotfigyelőt v2 API-leírások: Állapot beolvasása |} A Microsoft Docs'
description: Állapot figyelő v2 API-hivatkozás. Get-ApplicationInsightsMonitoringStatus. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: e579db587d5f56aecd60f584ea4805dd4ac1bf98
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718358"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v040-alpha"></a>A figyelő v2 API állapota: Get-ApplicationInsightsMonitoringStatus (v0.4.0-alpha)

Ez a cikk ismerteti, amely tagja a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Leírás

Ez a parancsmag az Állapotfigyelőt hibaelhárítási információkat biztosít.
Ezt a parancsmagot használhatja a monitorozási állapot, a PowerShell-modul verziószámát vizsgálatához és a futó folyamat vizsgálata.
Ez a parancsmag jelentést fájlverzió-információkat és figyeléséhez szükséges kulcs fájlokkal kapcsolatos információk.

> [!IMPORTANT] 
> Ez a parancsmag megköveteli egy PowerShell-munkamenetet rendszergazdai jogosultságokkal rendelkezik.

## <a name="examples"></a>Példák

### <a name="example-application-status"></a>Példa: Az alkalmazás állapota

Futtassa a parancsot `Get-ApplicationInsightsMonitoringStatus` webhelyek figyelési állapot megjelenítéséhez.

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
- **A gép azonosítója** a célkiszolgáló egyedi azonosításához használt névtelen azonosító. Ha támogatási kérelmet hoz létre, a naplók keresése a kiszolgáló ezt az Azonosítót kell.
- **Alapértelmezett webhely** le van állítva, az IIS-ben
- **DemoWebApp111** az IIS-ben elindult, de nem kapott, bármilyen kérelmeket. Ez a jelentés azt mutatja, hogy egyetlen futó folyamat (folyamatazonosító: nem található).
- **DemoWebApp222** fut, és a megfigyelés alatt áll (Instrumented: igaz). A felhasználói konfiguráció alapján a kialakítási kulcsot xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 megfelelést ehhez a helyhez.
- **DemoWebApp333** rendelkezik lett manuálisan kialakítva az Application Insights SDK használatával. Az állapotfigyelő észlelt az SDK-t, és nem figyeli a webhelyhez.


### <a name="example-powershell-module-information"></a>Példa: PowerShell modul információ

Futtassa a parancsot `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` az aktuális modult információit jeleníti meg:

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

### <a name="example-runtime-status"></a>Példa: Futtatókörnyezetbeli állapot

A folyamatot a finomhangolt számítógépet, hogy ha az összes DLL-fájl a rendszer betölti az vizsgálhatja meg. Figyelés működik, ha legalább 12 DLL-ek betöltési.

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

### <a name="no-parameters"></a>(Nincsenek paraméterei)

Alapértelmezés szerint ez a parancsmag jelentést küld a webes alkalmazások figyelési állapotát.
Ez a beállítás használatával tekintse át, ha az alkalmazás tagolása sikeresen.
Emellett áttekintheti az eszközkulcsot való megfelelést, amely a hely.


### <a name="-powershellmodule"></a>-PowerShellModule
**Választható**. Használja ezt a kapcsolót jelenti a verziószámok és -figyeléshez szükséges DLL-elérési utak.
Használja ezt a beállítást, ha szeretné azonosítani minden olyan DLL, beleértve az Application Insights SDK-verzióját.

### <a name="-inspectprocess"></a>-InspectProcess

**Választható**. Használja ezt a kapcsolót jelenti, hogy fut-e az IIS.
Annak megállapítása, hogy a szükséges DLL-ek betöltődik az IIS-modul a külső eszközöket is le fogja tölteni.


Ha ez a folyamat bármely okból meghiúsul, manuálisan is futtatható ezekkel a parancsokkal:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) - p w3wp |} findstr /I "InstrumentationEngine mesterséges Intelligencia. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp |} findstr /I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Választható**. Csak a InspectProcess használható. Ez a kapcsoló használatával hagyja ki a felhasználói kérés, amely előtt a rendszer letölti a további eszközök jelenik meg.


## <a name="next-steps"></a>További lépések

 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítása](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
