---
title: Ismert problémák és az Azure Állapotfigyelőt v2 elhárítása |} A Microsoft Docs
description: Az ismert hibákat az állapotfigyelő v2 és a hibaelhárítás példákat. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
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
ms.openlocfilehash: df59766ce38ac81568570cd6544ee28808ff8249
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807017"
---
# <a name="troubleshooting-status-monitor-v2"></a>Hibaelhárítási állapot v2 figyelése

Ha engedélyezi a figyelés, előfordulhat, hogy problémákba, amelyek megakadályozzák az adatgyűjtést.
Ez a cikk felsorolja az összes ismert problémák és hibaelhárítás példákat talál.
Ha között a hibát, amely nem szerepel itt, forduljon hozzánk a [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="known-issues"></a>Ismert problémák

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Az alkalmazás bin könyvtárához ütköző dll-fájlja

Ha ezek a DLL fájlok bármelyike szerepel a bin könyvtárat, a figyelési meghiúsulhat:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Ezek a DLL fájlok némelyikét szerepelnek a Visual Studio alapértelmezett alkalmazás sablonokat, akkor is, ha az alkalmazás nem használja őket.
Hibaelhárítási eszközök segítségével tekintse meg a tüneteket mutató viselkedés:

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

- IISReset és az alkalmazás betöltése (nélkül telemetria). Vizsgálja meg a Sysinternals (Handle.exe és ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Az IIS megosztott konfigurációs ütközés

Ha webkiszolgálók fürttel rendelkezik, akkor előfordulhat, hogy használja a [a megosztott konfiguráció](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
A beállítás nem lehet kártevő program férkőzik be ezt a megosztott konfigurációt.
Futtassa az Enable parancsot valamennyi webkiszolgálón telepíteni a dll-fájlt az egyes kiszolgálók GAC-bA.

Az Enable-parancs futtatása után végezze el az alábbi lépéseket:
1. Nyissa meg a megosztott konfigurációs könyvtárba, és az applicationHost.config fájl található.
2. Adja hozzá ezt a sort a konfigurációs modulok szakaszához:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>Hibaelhárítás
    
### <a name="troubleshooting-powershell"></a>PowerShell hibaelhárítása

#### <a name="determine-which-modules-are-available"></a>Határozza meg, melyik-modulok elérhetők
Használhatja a `Get-Module -ListAvailable` parancsot annak meghatározásához, hogy melyik modulokat telepített.

#### <a name="import-a-module-into-the-current-session"></a>Importálja a modult az aktuális munkamenet
Ha egy modul nem lett betöltve, egy PowerShell-munkamenetbe, manuálisan betöltheti azokat a használatával a `Import-Module <path to psd1>` parancsot.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>Az Állapotmonitor v2 modul hibaelhárítása

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>Az Állapotmonitor v2 modulban elérhető parancsok listázása
Futtassa a parancsot `Get-Command -Module Az.ApplicationMonitor` beolvasni az elérhető parancsok:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>Az Állapotmonitor v2 modul az aktuális verziójának
Futtassa a `Get-ApplicationInsightsMonitoringStatus` parancs megjeleníti a modul a következő információkat:
   - PowerShell-modul verzió
   - Application Insights SDK-verzió
   - PowerShell-moduljának a Fájlelérési utak
    
Tekintse át a [API-referencia](status-monitor-v2-api-get-status.md) Ez a parancsmag részletes leírását.


### <a name="troubleshooting-running-processes"></a>Futó folyamatok hibaelhárítása

A folyamatok annak meghatározásához, hogy az összes DLL-fájl a rendszer betölti a finomhangolt számítógépen vizsgálhatja meg.
Figyelés működik, ha legalább 12 DLL-ek betöltési.

Használja a `Get-ApplicationInsightsMonitoringStatus -InspectProcess` paranccsal ellenőrizheti a DLL-ek.

Tekintse át a [API-referencia](status-monitor-v2-api-get-status.md) Ez a parancsmag részletes leírását.


### <a name="collect-etw-logs-by-using-perfview"></a>A PerfView használatával ETW-naplók összegyűjtése

#### <a name="setup"></a>Beállítás

1. Töltse le a PerfView.exe és a PerfView64.exe [GitHub](https://github.com/Microsoft/perfview/releases).
2. Indítsa el a PerfView64.exe.
3. Bontsa ki a **speciális beállítások**.
4. Törölje az alábbi jelölőnégyzetet:
    - **Zip**
    - **Merge**
    - **.NET szimbólum gyűjtemény**
5. Állítsa be ezeket **további szolgáltatókat**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Naplók gyűjtése

1. A parancskonzolban rendszergazdai jogosultságokkal futtassa a `iisreset /stop` paranccsal kapcsolja ki az IIS és az összes webes alkalmazásokat.
2. A PerfView, válassza ki **gyűjtés megkezdése**.
3. A parancskonzolban rendszergazdai jogosultságokkal futtassa a `iisreset /start` parancsot az IIS elindításához.
4. Az alkalmazás megkeresése tallózással próbálja meg.
5. Az alkalmazás betöltése után térjen vissza a PerfView, és válassza ki **leállítása gyűjtemény**.



## <a name="next-steps"></a>További lépések

- Tekintse át a [API-referencia](status-monitor-v2-overview.md#powershell-api-reference) , előfordulhat, hogy kihagyott paraméterekkel kapcsolatos további.
- Ha között a hibát, amely nem szerepel itt, forduljon hozzánk a [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
