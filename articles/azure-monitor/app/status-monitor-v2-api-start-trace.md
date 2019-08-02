---
title: 'Az Azure Állapotmonitor v2 API-referenciája: Nyomkövetés indítása | Microsoft Docs'
description: Állapotmonitor v2 API-referenciát. Start-trace. ETW-naplók gyűjtése Állapotmonitor és Application Insights SDK-ból.
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
ms.openlocfilehash: 3060a48e297082f5b70c8bd96030fe519f6e8b6f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326313"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace"></a>Állapotmonitor v2 API: Start – ApplicationInsightsMonitoringTrace

Ez a cikk olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

## <a name="description"></a>Leírás

A [ETW-eseményeket](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) gyűjti a kód-visszacsatolási futtatókörnyezetből. Ez a parancsmag egy alternatív megoldás a [perfview eszköz](https://github.com/microsoft/perfview)futtatására.

Az összegyűjtött események valós időben lesznek kinyomtatva a konzolra, és egy ETL-fájlba lesznek mentve. A kimeneti ETL-fájl a [perfview eszköz](https://github.com/microsoft/perfview) segítségével nyitható meg további vizsgálat céljából.

Ez a parancsmag addig fut, amíg el nem éri az időtúllépés időtartamát (alapértelmezés szerint 5 perc)`Ctrl + C`, vagy manuálisan leállt ().

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenet szükséges.

## <a name="examples"></a>Példák

### <a name="how-to-collect-events"></a>Események gyűjtése

Általában azt javasoljuk, hogy gyűjtsön eseményeket, és vizsgálja meg, hogy az alkalmazás miért nincs kialakítva.

A kód-visszacsatolási futtatókörnyezet ETW eseményeket bocsát ki az IIS indításakor és az alkalmazás indításakor.

Az események összegyűjtése:
1. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon hajtsa végre `iisreset /stop` az IIS és az összes webalkalmazás kikapcsolását.
2. A parancsmag végrehajtása
3. Rendszergazdai jogosultságokkal rendelkező cmd-konzolon futtassa `iisreset /start` a parancsot az IIS elindításához.
4. Próbálja meg megkeresni az alkalmazást.
5. Miután az alkalmazás befejezte a betöltést, manuálisan leállíthatja (`Ctrl + C`), vagy megvárhatja az időtúllépést.

### <a name="what-events-to-collect"></a>A gyűjteni kívánt események

Az események gyűjtésére három lehetőség áll rendelkezésre:
1. Használja a kapcsolót `-CollectSdkEvents` a Application Insights SDK-ból kibocsátott események gyűjtéséhez.
2. A kapcsolóval `-CollectRedfieldEvents` összegyűjtheti Állapotmonitor és a Redfield futtatókörnyezet által kibocsátott eseményeket. Ezek a naplók az IIS és az alkalmazások indításának diagnosztizálásakor hasznosak.
3. Mindkét kapcsolót mindkét eseménytípus összegyűjtésére használhatja.
4. Alapértelmezés szerint, ha nincs megadva kapcsoló, mindkét eseménytípus összegyűjtve lesz.


## <a name="parameters"></a>Paraméterek

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Nem kötelező.** Ezzel a paraméterrel állíthatja be, hogy a parancsfájl mennyi ideig gyűjtsön eseményeket. Az alapértelmezett érték 5 perc.

### <a name="-logdirectory"></a>– LogDirectory
**Nem kötelező.** Ezzel a kapcsolóval állíthatja be az ETL-fájl kimeneti könyvtárát. Alapértelmezés szerint ez a fájl a PowerShell-modulok könyvtárban lesz létrehozva. A parancsfájl végrehajtása során a teljes elérési út megjelenik.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Nem kötelező.** Ezzel a kapcsolóval Application Insights SDK-eseményeket gyűjthet.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Nem kötelező.** Ezzel a kapcsolóval gyűjthet eseményeket a Állapotmonitor és a Redfield futtatókörnyezetből.

### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezt a kapcsolót használja a részletes naplók kinyomtatásához.



## <a name="output"></a>Output


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


## <a name="next-steps"></a>További lépések

További hibaelhárítás:

- Tekintse át a további hibaelhárítási lépéseket itt: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- A kihagyott paraméterek megismeréséhez tekintse át az [API](status-monitor-v2-overview.md#powershell-api-reference) -referenciát.
- Ha további segítségre van szüksége, felveheti velünk a [](https://github.com/Microsoft/ApplicationInsights-Home/issues)kapcsolatot a githubon.



 További Állapotmonitor v2:
 - A Állapotmonitor v2 [hibáinak megoldásához](status-monitor-v2-troubleshoot.md) használja az útmutatót.
 - [A konfiguráció](status-monitor-v2-api-get-config.md) beszerzésével ellenőrizze, hogy a beállítások megfelelően vannak-e rögzítve.
 - [A](status-monitor-v2-api-get-status.md) figyelés ellenőrzésének állapotának beolvasása.
