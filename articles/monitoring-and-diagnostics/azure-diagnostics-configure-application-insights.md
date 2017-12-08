---
title: "Konfigurálja az Azure diagnosztikai adatokat küldeni a Application Insights |} Microsoft Docs"
description: "Frissítse a Azure diagnosztikai adatokat küldeni a Application Insights nyilvános konfigurációt."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: f9e12c3e-c307-435e-a149-ef0fef20513a
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2016
ms.author: robb
ms.openlocfilehash: 67dc2d5bbfa2012e4e098616edda593d023c4c1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="send-cloud-service-virtual-machine-or-service-fabric-diagnostic-data-to-application-insights"></a>A felhőalapú szolgáltatás, a virtuális gép vagy a Service Fabric diagnosztikai adatok küldése az Application insights szolgáltatással
Felhőszolgáltatások, virtuális gép, virtuálisgép-méretezési csoportok és a Service Fabric összes adatok összegyűjtéséhez használja az Azure Diagnostics-bővítmény.  Az Azure diagnosztikai adatokat küld az Azure Storage-táblákat.  Azonban is minden cső vagy más helyekre Azure Diagnostics kiterjesztéssel 1.5-ös vagy újabb adatok egy részét.

Ez a cikk ismerteti, hogyan szeretnék adatokat küldeni az Azure Diagnostics bővítmény a Application insights szolgáltatással.

## <a name="diagnostics-configuration-explained"></a>Diagnosztikai konfiguráció ismertetése
Az Azure diagnostics-bővítmény 1.5-ös bevezetett fogadók esetében, amelyek további helyre, ahol a diagnosztikai adatokat küldhet.

Példa az konfigurálását, a fogadó Application Insights:

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
- A **fogadó** *neve* attribútum, amely egyedileg azonosítja a fogadó karakterlánc-érték.

- A **ApplicationInsights** elem azt adja meg a Application insights-erőforrást, ahol az Azure diagnosztikai adatok küldése instrumentation kulcsa.
    - Ha még nem rendelkezik egy meglévő Application Insights-erőforrást, lásd: [hozzon létre egy új Application Insights-erőforrást](../application-insights/app-insights-create-new-resource.md) további információ a erőforrás létrehozása, és a rendszerállapot-kulcs beolvasása.
    - Ha egy felhőalapú szolgáltatást biztosít az Azure SDK 2.8 fejleszt, a instrumentation kulcs automatikusan feltöltődik értékkel. Az érték alapján a **APPINSIGHTS_INSTRUMENTATIONKEY** szolgáltatás konfigurációs beállítás, amikor a Felhőszolgáltatás-projekt-csomagban. Lásd: [használja az Application Insights az Azure Diagnostics Felhőszolgáltatás kapcsolatos problémák elhárítása](../cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md).

- A **csatornák** egy vagy több elemet tartalmaz **csatorna** elemek.
    - A *neve* egyedi attribútum hivatkozik a csatorna.
    - A *loglevel* attribútum lehetővé teszi, hogy adja meg a naplózási szint, amely lehetővé teszi, hogy a csatorna. A rendelkezésre álló naplózási szintek legalább információkat a legtöbb sorrendben a következők:
        - Részletes
        - Információ
        - Figyelmeztetés
        - Hiba
        - Kritikus

Egy csatorna úgy viselkedik, mint egy szűrőt, és választhatja ki a cél fogadó küldendő adott naplózási szintet. Például akkor sikerült be a részletes naplókat, és küldje el a tárolási, de csak a hibák elküldése a fogadó.

A következő ábra ezt a kapcsolatot mutatja be.

![Diagnosztikai nyilvános konfigurációja](./media/azure-diagnostics-configure-applicationinsights/AzDiag_Channels_App_Insights.png)

A következő ábra a konfigurációs értékeket és a leírás foglalja össze. A konfigurációban az a hierarchia különböző szintjein több mosdók is megadhat. A legfelső szinten fogadó úgy működik, mint a globális beállítás, és az egyes elemben megadott úgy viselkedik, mint egy felülbírálást, hogy a globális beállítás.

![Diagnosztika fogadók esetében az Application Insights-konfiguráció](./media/azure-diagnostics-configure-applicationinsights/Azure_Diagnostics_Sinks.png)

## <a name="complete-sink-configuration-example"></a>Teljes fogadó konfigurációja – példa
Íme egy átfogó példát a nyilvános konfigurációs fájlba
1. az összes hiba küld az Application Insights (a megadott a **DiagnosticMonitorConfiguration** csomópont)
2. is küld az alkalmazás naplóiban a részletes szint naplók (a megadott a **naplók** csomópont).

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

### <a name="send-all-the-data-that-is-being-collected-by-azure-diagnostics"></a>Az Azure diagnostics által gyűjtött összes adat küldése

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights",
}
```

### <a name="send-only-error-logs-to-the-application-insights-sink"></a>Csak a hibanaplók küldése az Application Insights fogadó

```XML
<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```
```JSON
"DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "sinks": "ApplicationInsights.MyTopDiagData",
}
```

### <a name="send-verbose-application-logs-to-application-insights"></a>Az Application Insights részletes alkalmazást tartalmazó naplófájlok elküldése

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

- **Csatornák csak típusa és a teljesítmény nem számlálók naplózásához.** Ha egy csatornát a teljesítmény számláló elemet adja meg, figyelmen kívül.
- **A naplózási szint a csatorna nem haladhatja meg a naplózási szint esetében mi az Azure diagnostics gyűjtött.** Például nem gyűjteni a naplókat elem alkalmazásnaplóban hibák, és próbálja meg elküldeni a részletes naplókat az Application Insights fogadó. A *scheduledTransferLogLevelFilter* attribútum mindig gyűjtése egyenlő vagy több naplót, mint a naplók próbál küldeni a fogadó.
- **Az Application Insights Azure diagnostics bővítmény által gyűjtött blob adatokat nem lehet elküldeni.** Például semmit alatt megadott a *könyvtárak* csomópont. Összeomlási memóriaképek a tényleges összeomlási memóriakép továbbítja a rendszer a blob-tároló, és csak olyan, hogy létrejött-e a összeomlási biztonsági másolatot a rendszer értesítést küld az Application Insights.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan [tekintse meg az Azure diagnostics](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-cloudservices#view-azure-diagnostic-events) az Application insights szolgáltatással.
* Használjon [PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md) engedélyezi az Azure diagnostics bővítményt az alkalmazáshoz.
* Használjon [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) engedélyezi az Azure diagnostics bővítményt az alkalmazáshoz
