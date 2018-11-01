---
title: Azure diagnosztikai adatokat küldeni az Application Insights konfigurálása
description: Adatok küldése az Application insights-bA az Azure Diagnostics nyilvános konfigurációjának frissítése.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2016
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: f2a48756252f538adc7d813aafe6dbfbf3a270b5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416683"
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>Cloud Service, a virtuális gép vagy a Service Fabric diagnosztikai adatok küldése az Application Insights
Cloud services, Virtual Machines, Virtual Machine Scale Sets és a Service Fabric összes adatok összegyűjtéséhez használja az Azure Diagnostics bővítmény.  Az Azure diagnosztikai adatokat küld az Azure Storage-táblák.  Azonban is minden cső vagy más helyekre, 1.5-ös vagy újabb Azure Diagnostics bővítmény használata az adatok egy részét.

Ez a cikk ismerteti az Azure Diagnostics bővítmény adatokat küldeni az Application Insights.

## <a name="diagnostics-configuration-explained"></a>Diagnosztikai konfiguráció ismertetése
Az Azure diagnosztikai bővítmény 1.5-ös bevezetett fogadók, melyek a további helyeket, ahol elküldheti a diagnosztikai adatok.

A példában egy fogadó történő konfigurálása az Application Insights:

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
- A **fogadó** *neve* attribútumnak egy karakterláncérték, amely egyedileg azonosítja a fogadó.

- A **ApplicationInsights** elem azt határozza meg az Application insights-erőforrás, ahol az Azure-beli diagnosztikai adatok küldése a kialakítási kulcsot.
    - Ha nem rendelkezik egy meglévő Application Insights-erőforrást, [hozzon létre egy új Application Insights-erőforrást](../application-insights/app-insights-create-new-resource.md) egy erőforrás létrehozását és a kialakítási kulcsot a további tájékoztatást.
    - Egy Felhőszolgáltatás, Azure SDK 2.8-as és újabb verziók fejleszt, a rendszer automatikusan kitölti a kialakítási kulcsot. Az érték alapján az **állítani az APPINSIGHTS_INSTRUMENTATIONKEY** konfigurációs beállítás iránti csomagolása a Felhőszolgáltatás-projekt. Lásd: [az Application Insights használata a Cloud Services](../application-insights/app-insights-cloudservices.md).

- A **csatornák** egy vagy több elemet tartalmaz **csatorna** elemeket.
    - A *neve* egyedi attribútum hivatkozik azt a csatornát.
    - A *loglevel* attribútum megadását teszi lehetővé a naplózási szint, amely lehetővé teszi, hogy a csatorna. A rendelkezésre álló naplózási szintek legalább információkat a legtöbb sorrendben a következők:
        - Részletes
        - Információ
        - Figyelmeztetés
        - Hiba
        - Kritikus

A csatorna úgy viselkedik, mint egy szűrőt, és lehetővé teszi, hogy válassza ki a cél fogadó küldése adott naplózási szintet. Például, sikerült részletes naplók gyűjtése és elküldheti azokat tároló, de csak hibák elküldése a fogadó.

A következő ábra ezt a kapcsolatot mutatja be.

![Diagnosztikai nyilvános konfigurációja](media/azure-diagnostics-configure-application-insights/AzDiag_Channels_App_Insights.png)

A következő ábra összefoglalja a konfigurációs értékeket, és azok működéséről. A konfiguráció a hierarchiában lévő különböző szinteken több fogadóként is felvehet. A legfelső szinten a fogadó úgy működik, mint egy globális beállítás, és az egyes elemén, megadott ugyanúgy viselkedik, mint egy felülbírálást a globális beállítás.

![Diagnosztika az Application Insights konfigurációs fogadók](media/azure-diagnostics-configure-application-insights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Teljes fogadó példakonfigurációt
Íme egy teljes példát a nyilvános konfigurációjának-fájlba
1. hibákat küld az Application Insights (megadott, a **DiagnosticMonitorConfiguration** csomópont)
2. is elküldi az Alkalmazásnaplókat a részletes szintű naplók (megadott, a **naplók** csomópont).

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
Az előző beállítás szerint a következő sorokat jelentése a következő:

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Az Azure diagnostics által gyűjtött minden adat küldése

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Csak a hibanaplók elküldése az Application Insights fogadó

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Alkalmazás részletes naplók küldése az Application Insights

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

- **Csatornák csak bejelentkezéshez írja be és nem a teljesítményszámlálókhoz.** Ha megad egy csatornát a teljesítmény számláló elemmel, akkor figyelmen kívül hagyja.
- **A naplózási szint egy csatorna nem haladhatja meg a naplózási szint esetében mi az Azure diagnostics által összegyűjtött.** Például nem gyűjtése a naplók elem a napló hibák, és próbálja meg elküldeni a részletes naplók az Application Insights fogadó. A *scheduledTransferLogLevelFilter* attribútum mindig gyűjtése egyenlő vagy, mint a naplókat további naplók próbált küldeni a fogadóba másolt.
- **Az Application insights-bA az Azure diagnostics bővítmény által gyűjtött Blobadatok nem küldhető el.** Például semmit alatt megadott a *könyvtárak* csomópont. Az összeomlási memóriaképek a tényleges összeomlási memóriakép blob storage-bA küldött, és csak egy értesítést, hogy az összeomlási memóriaképben jött létre az Application insights szolgáltatásba küldi el.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [megtekintése az Azure diagnostics adatait](https://docs.microsoft.com/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) az Application Insightsban.
* Használat [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) az alkalmazás az Azure diagnosztikai bővítmény engedélyezése.
* Használat [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) az alkalmazás az Azure diagnosztikai bővítmény engedélyezése
