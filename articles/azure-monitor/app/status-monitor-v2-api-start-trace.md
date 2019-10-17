---
title: 'Az Azure Application Insights Agent API-referenciája: Nyomkövetés indítása | Microsoft Docs'
description: Application Insights Agent API-referenciája. Start-trace. ETW-naplók gyűjtése Állapotmonitor és Application Insights SDK-ból.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: b1c5aa34c46a20631b328abfb061dc2477150c72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389848"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights Agent API: Start-ApplicationInsightsMonitoringTrace

Ez a cikk olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

## <a name="description"></a>Leírás

A [ETW-eseményeket](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) gyűjti a kód-visszacsatolási futtatókörnyezetből. Ez a parancsmag egy alternatív megoldás a [perfview eszköz](https://github.com/microsoft/perfview)futtatására.

Az összegyűjtött események valós időben lesznek kinyomtatva a konzolra, és egy ETL-fájlba lesznek mentve. A kimeneti ETL-fájl a [perfview eszköz](https://github.com/microsoft/perfview) segítségével nyitható meg további vizsgálat céljából.

Ez a parancsmag addig fut, amíg el nem éri az időtúllépési időtartamot (az alapértelmezett 5 perc), vagy manuálisan leállították (`Ctrl + C`).

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

## <a name="examples"></a>Példák

### <a name="how-to-collect-events"></a>Események gyűjtése

Általában azt javasoljuk, hogy gyűjtsön eseményeket, és vizsgálja meg, hogy az alkalmazás miért nincs kialakítva.

A kód-visszacsatolási futtatókörnyezet ETW eseményeket bocsát ki az IIS indításakor és az alkalmazás indításakor.

Az események összegyűjtése:
1. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon hajtsa végre az `iisreset /stop` parancsot az IIS és az összes webalkalmazás kikapcsolásához.
2. A parancsmag végrehajtása
3. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon hajtsa végre a `iisreset /start` parancsot az IIS elindításához.
4. Próbálja meg megkeresni az alkalmazást.
5. Miután az alkalmazás befejezte a betöltést, manuálisan leállíthatja (`Ctrl + C`), vagy megvárhatja az időtúllépést.

### <a name="what-events-to-collect"></a>A gyűjteni kívánt események

Az események gyűjtésére három lehetőség áll rendelkezésre:
1. A Application Insights SDK-ból származó események gyűjtéséhez használja a `-CollectSdkEvents` kapcsolót.
2. A Állapotmonitor és a Redfield futtatókörnyezet által kibocsátott események összegyűjtéséhez használja a `-CollectRedfieldEvents` kapcsolót. Ezek a naplók az IIS és az alkalmazások indításának diagnosztizálásakor hasznosak.
3. Mindkét kapcsolót mindkét eseménytípus összegyűjtésére használhatja.
4. Alapértelmezés szerint, ha nincs megadva kapcsoló, mindkét eseménytípus összegyűjtve lesz.


## <a name="parameters"></a>Paraméterek

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Választható.** Ezzel a paraméterrel állíthatja be, hogy a parancsfájl mennyi ideig gyűjtsön eseményeket. Az alapértelmezett érték 5 perc.

### <a name="-logdirectory"></a>– LogDirectory
**Választható.** Ezzel a kapcsolóval állíthatja be az ETL-fájl kimeneti könyvtárát. Alapértelmezés szerint ez a fájl a PowerShell-modulok könyvtárban lesz létrehozva. A parancsfájl végrehajtása során a teljes elérési út megjelenik.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Választható.** Ezzel a kapcsolóval Application Insights SDK-eseményeket gyűjthet.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Választható.** Ezzel a kapcsolóval gyűjthet eseményeket a Állapotmonitor és a Redfield futtatókörnyezetből.

### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezt a kapcsolót használja a részletes naplók kinyomtatásához.



## <a name="output"></a>Kimenet


### <a name="example-of-application-startup-logs"></a>Alkalmazás-indítási naplók – példa
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


## <a name="next-steps"></a>Következő lépések

További hibaelhárítás:

- Tekintse át a további hibaelhárítási lépéseket itt: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- A kihagyott paraméterek megismeréséhez tekintse át az [API-referenciát](status-monitor-v2-overview.md#powershell-api-reference) .
- Ha további segítségre van szüksége, felveheti velünk a kapcsolatot a [githubon](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Több Application Insights-ügynökkel:
 - Az útmutató segítségével Application Insights-ügynököt lehet [elhárítani](status-monitor-v2-troubleshoot.md) .
 - [A konfiguráció beszerzésével](status-monitor-v2-api-get-config.md) ellenőrizze, hogy a beállítások megfelelően vannak-e rögzítve.
 - A figyelés ellenőrzésének [állapotának beolvasása](status-monitor-v2-api-get-status.md) .
