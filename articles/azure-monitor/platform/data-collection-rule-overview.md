---
title: Adatgyűjtés szabályai a Azure Monitorban (előzetes verzió)
description: Az adatgyűjtési szabályok (DCR) áttekintése Azure Monitor, beleértve azok tartalmát és szerkezetét, valamint hogyan hozhat létre és dolgozhat velük.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: af5324373359cea643a3e31b6bb94e614ddb7e36
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083059"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Adatgyűjtés szabályai a Azure Monitorban (előzetes verzió)
Az adatgyűjtési szabályok (DCR) a Azure Monitorba érkező, illetve az adatküldés és-tárolás helyét adja meg. Ez a cikk áttekintést nyújt az adatgyűjtési szabályokról, beleértve azok tartalmát és szerkezetét, valamint azt, hogy miként hozhat létre és dolgozhat velük.

## <a name="input-sources"></a>Bemeneti források
Az adatgyűjtés szabályai jelenleg a következő bemeneti forrásokat támogatják:

- Azure-beli virtuális gép Azure Monitor ügynökkel. Lásd: [az adatgyűjtés konfigurálása a Azure monitor-ügynökhöz (előzetes verzió)](data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Adatgyűjtési szabály összetevői
Az adatgyűjtési szabály a következő összetevőket tartalmazza.

| Összetevő | Leírás |
|:---|:---|
| Adatforrások | A monitorozási adatok egyedi forrása, amely a saját formátumával és metódusával teszi közzé az adatok mennyiségét. Adatforrások például a Windows Eseménynapló, a teljesítményszámlálók és a syslog. Az egyes adatforrások egy adott adatforrás-típusnak felelnek meg, az alább leírtak szerint. |
| Adatfolyamok | Egyedi leíró, amely egy adatforrások egy készletét írja le, amely egyetlen típusként lesz átalakítva és sematikus. Minden adatforráshoz egy vagy több stream szükséges, és egy adatfolyamot több adatforrás is használhat. Az adatfolyamban lévő összes adatforrás közös sémával rendelkezik. Több streamet is használhat, például ha egy adott adatforrást több, ugyanazon Log Analytics munkaterületen lévő táblába szeretne küldeni. |
| Célhelyek | Azon célhelyek összessége, amelyekben az adatküldés szükséges. Ilyenek például Log Analytics munkaterület, Azure Monitor mérőszámok és az Azure Event Hubs. | 
| Adatfolyamok | Annak meghatározása, hogy mely adatfolyamokat kell elküldeni a célhelyekre. | 

Az alábbi ábrán egy adatgyűjtési szabály és a kapcsolat összetevői láthatók.

[![DCR diagramja](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Adatforrástípusok
Minden adatforrás típusa adatforrással rendelkezik. Mindegyik típus egyedi tulajdonságokat határoz meg, amelyeket minden egyes adatforráshoz meg kell adni. A jelenleg elérhető adatforrás-típusok a következő táblázatban láthatók.

| Adatforrás típusa | Leírás | 
|:---|:---|
| kiterjesztés | Virtuálisgép-bővítmény-alapú adatforrás |
| performanceCounters | Teljesítményszámlálók Windows és Linux rendszerekhez |
| syslog | Syslog-események Linux rendszerű virtuális gépen |
| windowsEventLogs | Windows-Eseménynapló |


## <a name="limits"></a>Korlátok
A következő táblázat felsorolja az egyes adatgyűjtési szabályokra jelenleg érvényes korlátokat.

| Korlát | Érték |
|:---|:---|
| Adatforrások maximális száma | 10 |
| Maximális számlálók a teljesítményben | 100 |
| A rendelkezésre állási nevek maximális száma a SysLog-ben | 20 |
| XPath-lekérdezések maximális száma az eseménynaplóban | 100 |
| Az adatforgalom maximális száma | 10 |
| Adatfolyamok maximális száma | 10 |
| Bővítmények maximális száma | 10 |
| A bővítmények beállításainak maximális mérete | 32 KB |
| Log Analytics munkaterületek maximális száma | 10 |


## <a name="create-a-dcr"></a>DCR létrehozása
Jelenleg két elérhető módszer létezik a DCR létrehozásához:

- [Az Azure Portal használatával](data-collection-rule-azure-monitor-agent.md) hozzon létre egy adatgyűjtési szabályt, és társítsa azt egy vagy több virtuális géphez.
- Közvetlenül szerkessze az adatgyűjtési szabályt a JSON-ban, és küldje el a REST API használatával.

## <a name="sample-data-collection-rule"></a>Minta adatgyűjtési szabály
Az alábbi minta adatgyűjtési szabály az Azure felügyeleti ügynökkel rendelkező virtuális gépekre vonatkozik, és az alábbi adatokat tartalmaz:

- Teljesítményadatok
  - 15 másodpercenként gyűjti a processzort, a memóriát, a logikai lemezt és a fizikai lemez számlálóit, és percenként tölti fel a feltöltéseket.
  - 30 másodpercenként gyűjti az adott folyamat számlálóit, és 5 percenként feltölti a feltöltéseket.
- Windows-események
  - Gyűjti a Windows biztonsági eseményeit, és percenként feltölti a feltöltéseket.
  - A Windows alkalmazás-és rendszereseményeit gyűjti, és 5 percenként feltölti a fájlokat.
- Rendszernapló
  - Hibakeresési, kritikus és vészhelyzeti eseményeket gyűjt a cron létesítményből.
  - Riasztási, kritikus és vészhelyzeti eseményeket gyűjt a syslog létesítményből.
- Célhelyek
  - Az összes adatokat egy centralTeamWorkspace nevű Log Analytics-munkaterületre küldi.
  - Teljesítményadatokat küld Azure Monitor mérőszámoknak az aktuális előfizetésben.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "sylogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-WindowsEvent"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>További lépések

- [Hozzon létre egy adatgyűjtési szabályt](data-collection-rule-azure-monitor-agent.md) , és társítsa azt egy virtuális gépről a Azure monitor ügynök használatával.