---
title: 'Az Azure Állapotfigyelőt v2 API-leírások: Nyomkövetés indítása |} A Microsoft Docs'
description: Állapot figyelő v2 API-hivatkozás. Start-nyomkövetést. ETW-naplók gyűjtése Állapotfigyelőt és Application Insights SDK-t.
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
ms.openlocfilehash: b6787134707273a76290adb723a9bc9012252ebd
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807056"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v040-alpha"></a>A figyelő v2 API állapota: Start-ApplicationInsightsMonitoringTrace (v0.4.0-alpha)

Ez a cikk ismerteti, amely tagja a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Leírás

Gyűjti a [ETW-események](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) a központtal való csatolása modul. Ez a parancsmag futtatása helyett [PerfView](https://github.com/microsoft/perfview).

Összegyűjtött események kinyomtatja a konzolba a valós idejű és a egy ETL-fájlba menthető. A kimeneti ETL-fájlok is megnyithatók [PerfView](https://github.com/microsoft/perfview) további vizsgálat.

Ez a parancsmag fog futni, amíg azt eléri az időtúllépési időtartam (alapértelmezés szerint 5 perc), vagy manuálisan le van állítva (`Ctrl + C`).

> [!IMPORTANT] 
> Ez a parancsmag megköveteli egy PowerShell-munkamenetet rendszergazdai jogosultságokkal rendelkezik.

## <a name="examples"></a>Példák

### <a name="how-to-collect-events"></a>Hogyan eseményeinek gyűjtése

Azt kérjük szokásos módon, hogy miért nincs folyamatban van kialakítva az alkalmazás vizsgálata eseménygyűjtési.

A kód nélküli csatolása futásidejű IIS indulásakor, és az alkalmazás indulásakor ETW-események eredményez.

Ezek az események gyűjtésére:
1. A rendszergazdai jogosultságokat cmd-konzolhoz, hajtsa végre `iisreset /stop` tiltsa le az IIS és az összes webes alkalmazást.
2. Ez a parancsmag végrehajtása
3. A rendszergazdai jogosultságokat cmd-konzolhoz, hajtsa végre `iisreset /start` IIS elindításához.
4. Az alkalmazás megkeresése tallózással próbálja meg.
5. Az alkalmazás a betöltés befejezése után manuálisan állítsa le azt (`Ctrl + C`), vagy várja meg, az időkorlátot.

### <a name="what-events-to-collect"></a>Milyen események gyűjtése

Ha az események gyűjtése a három lehetősége van:
1. A kapcsoló `-CollectSdkEvents` az Application Insights SDK által kibocsátott események gyűjtéséhez.
2. A kapcsoló `-CollectRedfieldEvents` állapotfigyelő és a Redfield modul által kibocsátott események gyűjtéséhez. Ezek a naplók hasznosak a diagnosztizálása az IIS és az alkalmazás indításakor.
3. Mindkét eseménytípusok összegyűjtéséhez használja mindkét kapcsolók.
4. Alapértelmezés szerint, ha nem ad meg kapcsolót mindkét eseménytípusok legyenek összegyűjtve.


## <a name="parameters"></a>Paraméterek

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Nem kötelező.** Ez a paraméter használatával mennyi ideig Ez a szkript eseményeinek gyűjtése kell beállítani. Az alapértelmezett érték 5 perc.

### <a name="-logdirectory"></a>-LogDirectory
**Nem kötelező.** Ez a kapcsoló használatával állítsa be az ETL-fájl kimeneti könyvtárat. Alapértelmezés szerint ezt a fájlt a PowerShell-modulok címtárban létrejön. A szkript futása során jelenik meg a teljes elérési útja.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Nem kötelező.** Ez a kapcsoló használatával az Application Insights SDK-események gyűjtése.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Nem kötelező.** Ez a kapcsoló használatával állapotfigyelő és a Redfield modul eseményeinek gyűjtése.

### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Használja ezt a kapcsolót a kimenetben a részletes naplókat.



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>Protokoly aplikací indítási – példa
```
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

További hibaelhárítás:

- Áttekintheti Itt a további hibaelhárítási lépéseket: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Tekintse át a [API-referencia](status-monitor-v2-overview.md#powershell-api-reference) , előfordulhat, hogy kihagyott paraméterekkel kapcsolatos további.
- Ha további segítségre van szüksége, forduljon hozzánk az [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítása](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
 - [A konfiguráció első](status-monitor-v2-api-get-config.md) annak ellenőrzéséhez, hogy a beállításainak megfelelően rögzítve.
 - [A kapcsolat állapotának lekérése](status-monitor-v2-api-get-status.md) vizsgálhatja meg a figyelés.
