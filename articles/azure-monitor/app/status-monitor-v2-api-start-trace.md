---
title: Az Azure Application Insights Ügynök API-hivatkozása
description: Application Insights Ügynök API-referencia. Start-Trace. EtW-naplók gyűjtése az Állapotfigyelőből és az Application Insights SDK-ból.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671222"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights-ügynök API: Start-ApplicationInsightsMonitoringTrace

Ez a cikk az [Az.ApplicationMonitor PowerShell modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagjaként készült parancsmalapot ismerteti.

## <a name="description"></a>Leírás

[EtW-események](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) gyűjtése a kód nélküli csatolási futásidejű. Ez a parancsmag a [PerfView](https://github.com/microsoft/perfview)futtatásának alternatívája.

Az összegyűjtött eseményeket valós időben nyomtatjuk ki a konzolra, és etl fájlba mentjük. A kimeneti ETL fájlt a [PerfView](https://github.com/microsoft/perfview) további vizsgálatra megnyithatja.

Ez a parancsmag addig fog futni, amíg el nem éri az`Ctrl + C`időtúltöltés időtartamát (alapértelmezett 5 perc), vagy manuálisan le nem állítja ( ).

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

## <a name="examples"></a>Példák

### <a name="how-to-collect-events"></a>Hogyan gyűjthetünk eseményeket?

Általában arra kérjük, hogy gyűjtsön eseményeket, hogy kivizsgálja, miért nem a jelentkezését instrumented.

A kód nélküli csatolási futásidejű etw-eseményeket fog kivetni az IIS indításakor és az alkalmazás indításakor.

Az események összegyűjtése:
1. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon hajtsa végre `iisreset /stop` az IIS és az összes webalkalmazás kikapcsolására.
2. A parancsmag végrehajtása
3. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon hajtsa végre `iisreset /start` az IIS elindításához.
4. Próbáljon meg böngészni az alkalmazással.
5. Miután az alkalmazás betöltése befejeződik, manuálisan leállíthatja (`Ctrl + C`) vagy megvárhatja az időtúltöltést.

### <a name="what-events-to-collect"></a>Milyen eseményeket kell összegyűjteni

Az események gyűjtésesorán három lehetőség közül választhat:
1. A kapcsoló `-CollectSdkEvents` segítségével összegyűjtheti az Application Insights SDK által kibocsátott eseményeket.
2. A kapcsoló `-CollectRedfieldEvents` segítségével összegyűjtheti az Állapotfigyelő és a Redfield Runtime által kibocsátott eseményeket. Ezek a naplók az IIS és az alkalmazások indításának diagnosztizálásakor hasznosak.
3. Mindkét kapcsoló t használja mindkét eseménytípus összegyűjtéséhez.
4. Alapértelmezés szerint, ha nincs megadva kapcsoló, a rendszer mindkét eseménytípust összegyűjti.


## <a name="parameters"></a>Paraméterek

### <a name="-maxdurationinminutes"></a>-MaxDurationinMinutes
**Választható.** Ezzel a paraméterrel beállíthatja, hogy a parancsfájl mennyi ideig gyűjtsön eseményeket. Az alapértelmezett érték 5 perc.

### <a name="-logdirectory"></a>-LogDirectory
**Választható.** Ezzel a kapcsolóval állíthatja be az ETL-fájl kimeneti könyvtárát. Alapértelmezés szerint ez a fájl a PowerShell-modulok könyvtárban jön létre. A teljes elérési út megjelenik a parancsfájl végrehajtása során.


### <a name="-collectsdkevents"></a>-CollectSdkEsemények
**Választható.** Ezzel a kapcsolóval összegyűjtheti az Application Insights SDK-eseményeket.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEsemények
**Választható.** Ezzel a kapcsolóval eseményeket gyűjthet az Állapotfigyelőből és a Redfield-futásórából.

### <a name="-verbose"></a>-Részletes
**Közös paraméter.** Ezzel a kapcsolóval részletes naplókat kell kiadnia.



## <a name="output"></a>Kimenet


### <a name="example-of-application-startup-logs"></a>Példa az alkalmazások indítási naplóira
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

- Tekintse át a további hibaelhárítási lépéseket az alábbi témakörben:https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Tekintse át az [API-hivatkozást,](status-monitor-v2-overview.md#powershell-api-reference) és ismerje meg a kihagyott paramétereket.
- Ha további segítségre van szüksége, lépjen kapcsolatba velünk a [GitHubon.](https://github.com/Microsoft/ApplicationInsights-Home/issues)



 További információk az Application Insights-ügynökkel:
 - Útmutatónk segítségével [elháríthatja](status-monitor-v2-troubleshoot.md) az Application Insights-ügynök hibáit.
 - A beállítások helyes rögzítésének ellenőrzése a [konfigurációs eszközre.](status-monitor-v2-api-get-config.md)
 - [Kap az állapot,](status-monitor-v2-api-get-status.md) hogy vizsgálja meg a megfigyelést.
