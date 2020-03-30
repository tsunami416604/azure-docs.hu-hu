---
title: Az Azure diagnosztikai adatainak küldése az Application Insightsnak
description: Frissítse az Azure Diagnostics nyilvános konfigurációját, hogy adatokat küldjön az Application Insightsnak.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/19/2016
ms.openlocfilehash: 80d971abd248ca8253a374b488c693ea9aa2ea3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672327"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Felhőszolgáltatás, virtuális gép vagy a Service Fabric diagnosztikai adatainak küldése az Application Insightsba
A felhőszolgáltatások, a virtuális gépek, a virtuálisgép-méretezési készletek és a Service Fabric mind az Azure Diagnostics bővítményt használják az adatok gyűjtésére.  Az Azure-diagnosztika adatokat küld az Azure Storage-táblák.  Azonban az Azure Diagnostics 1.5-ös vagy újabb bővítményével az adatok egészét vagy egy részhalmazát is átviheti más helyekre.

Ez a cikk ismerteti, hogyan küldhet adatokat az Azure Diagnostics bővítményből az Application Insights.

## <a name="diagnostics-configuration-explained"></a>A diagnosztika konfigurációja magyarázata
Az Azure 1.5-ös diagnosztikai bővítménye bevezette a fogadókat, amelyek további helyek, ahol diagnosztikai adatokat küldhet.

Példa egy fogadó az Application Insights:

```XML
<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "ApplicationInsights",
            "ApplicationInsights": "{Insert InstrumentationKey}",
            "Channels": {
                "Channel": [
                    {
                        "logLevel": "Error",
                        "name": "MyTopDiagData"
                    },
                    {
                        "logLevel": "Error",
                        "name": "MyLogData"
                    }
                ]
            }
        }
    ]
}
```
- A **Fogadó** *név* attribútum egy karakterlánc-érték, amely egyedileg azonosítja a fogadó.

- Az **ApplicationInsights** elem meghatározza az Application Insights erőforrás instrumentációs kulcsát, ahol az Azure diagnosztikai adatokat küldi.
    - Ha nem rendelkezik egy meglévő Application Insights-erőforrással, olvassa [el az Új Application Insights-erőforrás létrehozása](../../azure-monitor/app/create-new-resource.md ) című témakört, amely további információt tartalmaz az erőforrás létrehozásáról és a műszerezési kulcs beszerzése című témakörről.
    - Ha az Azure SDK 2.8-as és újabb verzióival felhőszolgáltatást fejleszt, ez a műszerezési kulcs automatikusan kitöltődik. Az érték a **APPINSIGHTS_INSTRUMENTATIONKEY** szolgáltatás konfigurációs beállításán alapul a Cloud Service projekt csomagolásakor. Lásd: [Az Alkalmazáselemzési adatok használata a felhőszolgáltatásokkal.](../../azure-monitor/app/cloudservices.md)

- A **Csatornák** elem egy vagy több **csatornaelemet** tartalmaz.
    - A *name* attribútum egyedileg az adott csatornára hivatkozik.
    - A *loglevel* attribútum lehetővé teszi a csatorna által lehetővé tesz naplószint megadását. A rendelkezésre álló naplószintek a legtöbb és a legkisebb információ sorrendjében a következők:
        - Részletes
        - Információ
        - Figyelmeztetés
        - Hiba
        - Kritikus

A csatorna szűrőként működik, és lehetővé teszi a célfogadónak küldő adott naplószintek kiválasztását. Például részletes naplókat gyűjthet, és elküldheti őket a tárolóba, de csak hibákat küldhet a fogadónak.

A következő ábra ezt a kapcsolatot mutatja be.

![Diagnosztika nyilvános konfigurációja](media/diagnostics-extension-to-application-insights/AzDiag_Channels_App_Insights.png)

A következő ábra összefoglalja a konfigurációs értékeket és azok működését. A hierarchia különböző szintjein több fogadót is felvehet a konfigurációba. A legfelső szintű fogadó globális beállításként működik, és az egyes elemekben megadott beállítás felülírja az adott globális beállítást.

![A diagnosztika elsüllyeszti a konfigurációt az Application Insights segítségével](media/diagnostics-extension-to-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Példa a teljes fogadókonfigurációra
Íme egy teljes példa a nyilvános konfigurációs fájlra, amely
1. az összes hibát elküldi az Application Insights szolgáltatásnak (a **DiagnosticMonitorConfiguration** csomóponton van megadva)
2. részletes szintű naplókat is küld az alkalmazásnaplókhoz (a **Naplók** csomóponton megadva).

```XML
<WadCfg>
  <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
    <DiagnosticInfrastructureLogs />
    <PerformanceCounters>
      <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
      <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
    </PerformanceCounters>
    <WindowsEventLog scheduledTransferPeriod="PT1M">
      <DataSource name="Application!*" />
    </WindowsEventLog>
    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
  </DiagnosticMonitorConfiguration>

<SinksConfig>
    <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
    </Sink>
  </SinksConfig>
</WadCfg>
```
```JSON
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
        "overallQuotaInMB": 4096,
        "sinks": "ApplicationInsights.MyTopDiagData", "_comment": "All info below sent to this channel",
        "DiagnosticInfrastructureLogs": {
        },
        "PerformanceCounters": {
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                },
                {
                    "counterSpecifier": "\\Memory\\Available MBytes",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "DataSource": [
                {
                    "name": "Application!*"
                }
            ]
        },
        "Logs": {
            "scheduledTransferPeriod": "PT1M",
            "scheduledTransferLogLevelFilter": "Verbose",
            "sinks": "ApplicationInsights.MyLogData", "_comment": "This specific info sent to this channel"
        }
    },
    "SinksConfig": {
        "Sink": [
            {
                "name": "ApplicationInsights",
                "ApplicationInsights": "{Insert InstrumentationKey}",
                "Channels": {
                    "Channel": [
                        {
                            "logLevel": "Error",
                            "name": "MyTopDiagData"
                        },
                        {
                            "logLevel": "Verbose",
                            "name": "MyLogData"
                        }
                    ]
                }
            }
        ]
    }
}
```
Az előző konfigurációban a következő sorok jelentése a következő:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Az Azure diagnosztika által gyűjtött összes adat elküldése

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Csak hibanaplók küldése az Application Insights-fogadóba

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Részletes alkalmazásnaplók küldése az Application Insightsba

```XML
<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyLogData",
}
```

## <a name="limitations"></a>Korlátozások

- **A csatornák csak naplótípust naplóznak, a teljesítményszámlálókat nem.** Ha teljesítményszámláló-elemmel rendelkező csatornát ad meg, a rendszer figyelmen kívül hagyja.
- **A csatorna naplószintje nem haladhatja meg az Azure-diagnosztika által gyűjtött naplószintet.** Például nem gyűjthet alkalmazásnapló hibákat a Naplók elemben, és megpróbálhat részletes naplókat küldeni az Application Insight fogadójába. Az *scheduledTransferLogLevelFilter* attribútumnak mindig egyenlő vagy több naplót kell gyűjtenie, mint a fogadónak küldeni kívánt naplóknak.
- **Az Azure diagnosztikai bővítmény által gyűjtött blobadatok nem küldhető el az Application Insightsnak.** Például bármi, amit a *Könyvtárak* csomópont ban határoz meg. Összeomlási memóriaképek esetén a tényleges összeomlási memóriakép a blob storage-ba kerül, és csak egy értesítést, hogy az összeomlási memóriakép jött létre az Application Insights.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [tekintheti](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices) meg az Azure diagnosztikai adatait az Application Insightsban.
* A [PowerShell](../../cloud-services/cloud-services-diagnostics-powershell.md) használatával engedélyezze az Azure diagnosztikai bővítményt az alkalmazáshoz.
* A [Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) segítségével engedélyezze az alkalmazás Azure diagnosztikai bővítményét

