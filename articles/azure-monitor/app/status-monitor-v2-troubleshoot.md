---
title: Ismert problémák és az Azure Állapotfigyelőt v2 elhárítása |} A Microsoft Docs
description: Az ismert hibákat az állapotfigyelő v2 és a hibaelhárítás példákat. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: 26d677df81df6e10fae8d63362d49deee2cd0aca
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870747"
---
# <a name="troubleshooting-status-monitor-v2"></a>Hibaelhárítási állapot v2 figyelése

Figyelés engedélyezésekor az adatgyűjtés megakadályozó problémákat tapasztalhat. Ez a dokumentum felsorolja az összes ismert problémák és hibaelhárítás példákat.
Ha itt nem felsorolt probléma között, akkor előfordulhat, hogy lépjen kapcsolatba velünk [Itt](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="known-issues"></a>Ismert problémák

### <a name="conflicting-dlls-in-an-applications-bin-directory"></a>Egy alkalmazás bin könyvtárához ütköző dll-fájlja

Ha ezek a DLL fájlok bármelyike szerepel a bin könyvtárat, figyelés meghiúsulhat.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Ezek a DLL fájlok némelyikét szerepelnek a Visual Studio alapértelmezett alkalmazássablonok, még akkor is, ha az alkalmazás nem használja őket.
Tüneteket mutató viselkedés elemének hibaelhárítási eszközei segítségével:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- iisreset + alkalmazás betöltése (nem TELEMETRIA). A Sysinternals (Handle.exe és ListDLLs.exe) vizsgálata
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Az IIS megosztott konfigurációs ütközés

Ha webkiszolgálók fürttel rendelkezik, akkor előfordulhat, hogy használja a [megosztott konfiguráció](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211). A Microsoft nem tudja automatikusan behelyezése a HttpModule a megosztott konfiguráció. Valamennyi webkiszolgálón előbb futtatnia kell a engedélyezése parancsot, a dll-fájl telepítése a GAC-bA.

Az Enable-parancs futtatása után 
1. Tallózással keresse meg a megosztott konfigurációs könyvtárba, és keresse meg a `applicationHost.config` fájlt.
2. A konfiguráció a modulok szakaszban adja hozzá ezt a sort:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
    


## <a name="troubleshooting"></a>Hibaelhárítás
    
### <a name="troubleshooting-powershell"></a>PowerShell hibaelhárítása

#### <a name="how-to-inspect-what-modules-are-available"></a>Hogyan vizsgálhatja meg, milyen-modulok elérhetők?
Telepített modulok, a parancs használatával naplózható: `Get-Module -ListAvailable`

#### <a name="how-to-import-a-module-into-the-current-session"></a>Importálja a modult az aktuális munkamenet hogyan?
Ha a modul nem lett betöltve, egy PowerShell-munkamenetbe, manuálisan betöltheti a parancsot: `Import-Module <path to psd1>`


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Az Állapotmonitor v2 modul hibaelhárítása

#### <a name="how-to-review-what-commands-are-available-in-the-status-monitor-v2-module"></a>Tekintse át, milyen parancsokat érhetők el az Állapotfigyelőt v2 modul hogyan?
- Futtassa a parancsot: `Get-Command -Module Az.ApplicationMonitor` beolvasni az elérhető parancsok:

    ```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
    Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
    Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    ```

#### <a name="what-is-the-current-version-of-the-status-monitor-v2-module"></a>Mi az az Állapotfigyelőt v2 modul jelenlegi verziója?
- Futtassa a parancsot: `Get-ApplicationInsightsMonitoringStatus` a kimenetnek ezzel a modullal kapcsolatos információkat:

    ```
    PowerShell Module version:
    0.2.1-alpha

    Application Insights SDK version:
    2.9.0.3872

    Executing PowerShell Module Assembly:
    Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.9129, Culture=neutral, PublicKeyToken=31bf3856ad364e35

    PowerShell Module Directory:
    C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\PowerShell

    Runtime Paths:
    ParentDirectory: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content Exists: True
    ConfigurationPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\applicationInsights.ikey.config Exists: False
    ManagedHttpModuleHelperPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll Exists: True
    RedfieldIISModulePath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll Exists: True
    InstrumentationEngine86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll Exists: True
    InstrumentationEngine64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll Exists: True
    InstrumentationEngineExtensionHost86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll Exists: True
    InstrumentationEngineExtensionHost64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll Exists: True
    InstrumentationEngineExtensionConfig86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config Exists: True
    InstrumentationEngineExtensionConfig64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config Exists: True
    ApplicationInsightsSdkPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.ApplicationInsights.dll Exists: True
    ```




### <a name="troubleshooting-running-processes"></a>Futó folyamatok hibaelhárítása

A folyamat a finomhangolt gépen annak ellenőrzéséhez, hogy a rendszer betölti az összes DLL-ek vizsgálhatja meg.
Figyelés működik, ha legalább 12 DLL-ek betöltési.

- Cmd: `Get-ApplicationInsightsMonitoringStatus -InspectProcess`

    ```
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

### <a name="collect-etw-logs-with-perfview"></a>A PerfView ETW-naplók összegyűjtése

#### <a name="setup"></a>Beállítás

1. PerfView.exe és a PerfView64.exe letöltése https://github.com/Microsoft/perfview/releases
2. Indítsa el a PerfView64.exe
3. Bontsa ki a "Speciális beállítások"
4. Törölje a jelet:
    - Irányítószám
    - Egyesítés
    - .NET szimbólum gyűjtemény
5. Állítsa be a további szolgáltatók: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Naplók összegyűjtése

1. A rendszergazdai jogosultságokat cmd-konzolhoz, hajtsa végre `iisreset /stop` tiltsa le az IIS és az összes webes alkalmazást.
2. A PerfView kattintson a "Start gyűjtemény"
3. A rendszergazdai jogosultságokat cmd-konzolhoz, hajtsa végre `iisreset /start` IIS elindításához.
4. Az alkalmazás megkeresése tallózással próbálja meg.
5. Az alkalmazás a betöltés befejezése után térjen vissza a PerfView, és kattintson a "Stop gyűjtemény"

