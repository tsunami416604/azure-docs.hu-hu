---
title: Az Azure Application Insights Ügynök hibaelhárítása és ismert problémák | Microsoft dokumentumok
description: Az Application Insights-ügynök ismert problémái és hibaelhárítási példák. A webhely teljesítményének figyelése a webhely újratelepítése nélkül. Együttműködik ASP.NET helyszíni, virtuális gépeken vagy az Azure-ban üzemeltetett webalkalmazásokkal.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9bb22b12a7b3e972ff144bd121db4288801e2488
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732939"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Alkalmazáselemzési ügynök hibaelhárítása (korábbi nevén Állapotfigyelő v2)

Ha engedélyezi a figyelést, olyan problémákléphetnek be, amelyek megakadályozzák az adatgyűjtést.
Ez a cikk felsorolja az összes ismert problémát, és hibaelhárítási példákat tartalmaz.
Ha olyan problémával találkozol, amely nem szerepel itt, felveheti velünk a kapcsolatot a [GitHubon.](https://github.com/Microsoft/ApplicationInsights-Home/issues)

## <a name="known-issues"></a>Ismert problémák

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Ütköző DLL-ek az alkalmazás tárolókönyvtárában

Ha ezen DL-ek bármelyike szerepel a bin könyvtárban, a figyelés sikertelen lehet:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemettryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Ezen DIL-k némelyikét a Visual Studio alapértelmezett alkalmazássablonjai tartalmazzák, még akkor is, ha az alkalmazás nem használja őket.
A hibaelhárító eszközök segítségével megtekintheti a tüneti viselkedést:

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

- IISReset és app load (telemetria nélkül). Vizsgálja meg a Sysinternals (Handle.exe és ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Ütközés az IIS megosztott konfigurációjával

Ha webkiszolgáló-fürttel rendelkezik, lehet, hogy [megosztott konfigurációt](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)használ.
A HttpModule nem adható be ebbe a megosztott konfigurációba.
Futtassa az Engedélyezés parancsot minden webkiszolgálón, és telepítse a DLL-t az egyes kiszolgálók GAC-jába.

Az Engedélyezés parancs futtatása után hajtsa végre az alábbi lépéseket:
1. Nyissa meg a megosztott konfigurációs könyvtárat, és keresse meg az applicationHost.config fájlt.
2. Adja hozzá ezt a sort a konfiguráció moduljainak szakaszához:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS beágyazott alkalmazások

Az IIS-ben az 1.0-s verzióban nem eszközként elágyazott alkalmazásokat eszközként.
Mi nyomon ezt a kérdést [itt](https://github.com/microsoft/ApplicationInsights-Home/issues/369).

### <a name="advanced-sdk-configuration-isnt-available"></a>Speciális SDK-konfiguráció nem érhető el.

Az SDK-konfiguráció nem elérhető a végfelhasználó számára az 1.0-s verzióban.
Mi nyomon ezt a kérdést [itt](https://github.com/microsoft/ApplicationInsights-Home/issues/375).

    
    
## <a name="troubleshooting"></a>Hibaelhárítás
    
### <a name="troubleshooting-powershell"></a>A PowerShell hibáinak elhárítása

#### <a name="determine-which-modules-are-available"></a>Határozza meg, hogy mely modulok érhetők el
A `Get-Module -ListAvailable` paranccsal meghatározhatja, hogy mely modulok vannak telepítve.

#### <a name="import-a-module-into-the-current-session"></a>Modul importálása az aktuális munkamenetbe
Ha egy modul nincs betöltve egy PowerShell-munkamenetbe, manuálisan betöltheti azt a `Import-Module <path to psd1>` parancs használatával.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Az Application Insights-ügynök modul hibaelhárítása

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Az Application Insights-ügynök modulban elérhető parancsok listája
Futtassa `Get-Command -Module Az.ApplicationMonitor` a parancsot a rendelkezésre álló parancsok beszerzéséhez:

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Az Application Insights-ügynök modul aktuális verziójának meghatározása
Futtassa a `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` parancsot a modullal kapcsolatos alábbi információk megjelenítéséhez:
   - A PowerShell modul verziója
   - Az Application Insights SDK-verziója
   - A PowerShell-modul fájlelérési útjai
    
Tekintse át az [API-referencia](status-monitor-v2-api-reference.md) részletes leírását, hogyan kell használni ezt a parancsmamot.


### <a name="troubleshooting-running-processes"></a>Futó folyamatok – hibaelhárítás

A műszeres számítógépen ellenőrizheti a folyamatokat, és megállapíthatja, hogy az összes DL be van-e töltve.
Ha a monitorozás működik, legalább 12 DL-t kell betölteni.

A `Get-ApplicationInsightsMonitoringStatus -InspectProcess` dl-ek ellenőrzéséhez használja a parancsot.

Tekintse át az [API-referencia](status-monitor-v2-api-reference.md) részletes leírását, hogyan kell használni ezt a parancsmamot.


### <a name="collect-etw-logs-by-using-perfview"></a>ETW-naplók gyűjtése a PerfView használatával

#### <a name="setup"></a>Telepítés

1. Töltse le a PerfView.exe és a PerfView64.exe játékot a [GitHubról.](https://github.com/Microsoft/perfview/releases)
2. Indítsa el a PerfView64.exe.start perfView64.exe.
3. Bontsa ki **a Speciális beállítások csomópontot.**
4. Törölje a jelet az alábbi jelölőnégyzetekből:
    - **Zip**
    - **Egyesítés**
    - **.NET szimbólumgyűjtemény**
5. Állítsa be ezeket **a további szolgáltatókat:**`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Naplók gyűjtése

1. Rendszergazdai jogosultságokkal rendelkező parancskonzolon `iisreset /stop` futtassa a paranccsal az IIS és az összes webalkalmazás kikapcsolására.
2. A PerfView nézetben válassza **a Gyűjtemény indítása**lehetőséget.
3. Rendszergazdai jogosultságokkal rendelkező parancskonzolon `iisreset /start` futtassa a parancsot az IIS elindításához.
4. Próbáljon meg böngészni az alkalmazással.
5. Az alkalmazás betöltése után térjen vissza a PerfView webhelyre, és válassza **a Gyűjtemény leállítása lehetőséget.**



## <a name="next-steps"></a>További lépések

- Tekintse át az [API-hivatkozást,](status-monitor-v2-overview.md#powershell-api-reference) és ismerje meg a kihagyott paramétereket.
- Ha olyan problémával találkozol, amely nem szerepel itt, felveheti velünk a kapcsolatot a [GitHubon.](https://github.com/Microsoft/ApplicationInsights-Home/issues)
