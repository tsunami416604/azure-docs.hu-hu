---
title: Monitoring Azure Time Series Insights adathivatkozás | Microsoft Docs
description: Dokumentáció a Azure Time Series Insights figyeléséhez.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 0b564ddfdea2cf24b7f9b1bc608d47fa4cfe541b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632670"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Azure Time Series Insights adathivatkozás figyelése

Ismerje meg a Azure Time Series Insights-környezetből Azure Monitor által gyűjtött adatokat és erőforrásokat. A figyelési adatok gyűjtésével és elemzésével kapcsolatos részletekért tekintse meg a [figyelési Time Series Insights]( ./how-to-monitor-tsi.md) .

## <a name="metrics"></a>Mérőszámok

Ez a szakasz felsorolja a Azure Time Series Insights összegyűjtött, automatikusan összegyűjtött platform-metrikákat. Az összes Azure Monitor támogatási metrikájának listáját (beleértve a Azure Time Series Insightst is) lásd: [Azure monitor támogatott metrikák](../azure-monitor/platform/metrics-supported.md). A metrikák erőforrás-szolgáltatója a [Microsoft. TimeSeriesInsights/Environments/eventsources](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) és a [Microsoft. TimeSeriesInsights/Environments](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Bejövő forgalom
 
|Metrika|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|
|---|---|---|---|---|
|IngressReceivedBytes|Bejövő fogadott bájtok száma|Bájt|Összesen|Az eseményforrás által beolvasott bájtok száma|
|IngressReceivedInvalidMessages|A bejövő forgalom érvénytelen üzeneteket kapott|Darabszám|Összesen|Az eseményforrás által beolvasott érvénytelen üzenetek száma|
|IngressReceivedMessages|Bejövő fogadott üzenetek|Darabszám|Összesen|Az eseményforrás által olvasott üzenetek száma|
|IngressReceivedMessagesCountLag|Bejövő fogadott üzenetek számának késése|Darabszám|Átlag|Az eseményforrás-partíció utolsó várólistán lévő-üzenetének sorszáma és a bejövő forgalomban feldolgozott üzenetek sorozatszáma közötti különbség|
|IngressReceivedMessagesTimeLag|Bejövő fogadott üzenetek időbeli késése|Másodperc|Maximum|Az üzenet várólistán lévő és a bejövő forgalomban való feldolgozásának időpontja közötti különbség|
|IngressStoredBytes|Bejövő forgalomban tárolt bájtok|Bájt|Összesen|Az események teljes mérete sikeresen feldolgozva és elérhető a lekérdezéshez|
|IngressStoredEvents|Bejövő tárolt események|Darabszám|Összesen|A sikeresen feldolgozott és a lekérdezéshez elérhető összeolvasztott események száma|

### <a name="storage"></a>Tárolás

|Metrika|Metrika megjelenítendő neve|Egység|Aggregáció típusa|Description|
|---|---|---|---|---|
|WarmStorageMaxProperties|A meleg tárolás Max tulajdonságai|Darabszám|Maximum|A környezet által az S1/S2 SKU számára engedélyezett tulajdonságok maximális száma, valamint a TB SKU-hoz tartozó meleg tár által engedélyezett tulajdonságok maximális száma|
|WarmStorageUsedProperties|Meleg tároláshoz használt tulajdonságok |Darabszám|Maximum|A környezet által az S1/S2 SKU-hoz használt tulajdonságok száma, valamint a TB SKU meleg tárolója által használt tulajdonságok száma|

## <a name="resource-logs"></a>Erőforrásnaplók

Ez a szakasz felsorolja a Azure Time Series Insights-környezethez összegyűjthető erőforrás-naplók típusait.

| Kategória | Megjelenítendő név | Leírás |
|----- |----- |----- |
| Bejövő forgalom | TSIIngress | A bejövő forgalom kategóriában nyomon követheti a beáramló folyamat során felmerülő hibákat. Ez a kategória olyan hibákat tartalmaz, amelyek akkor jelentkeznek, amikor eseményeket fogadnak (például egy esemény forrásához való kapcsolódási hibák) és az események feldolgozásának folyamatát (például az események hasznos adatainak elemzésekor felmerülő hibákat). |

## <a name="schemas"></a>Sémák
A következő sémákat használják Azure Time Series Insights

### <a name="tsiingress-table"></a>TSIIngress táblázat

| Tulajdonság | Leírás |
|----- |----- |
| TimeGenerated | Az esemény generálásának időpontja (UTC). |
| Hely | Az erőforrás helye. |
| Kategória | A naplózási Esemény kategóriája. |
| OperationName | Az esemény műveleti neve. |
| CorrelationId | A kérelem korrelációs azonosítója. |
| Level | Az esemény súlyossági szintje. |
| ResultDescription | A művelet eredményének leírása, például "beérkezett tiltott hiba". |
| Üzenet | A hibához tartozó üzenet. A hibával és a hiba elhárításával kapcsolatos részleteket tartalmaz. |
| ErrorCode | A hibához társított kód |
| EventSourceType | Az eseményforrás típusa Lehet az Event hub vagy az IoT hub is. |
| EventSourceProperties | Az adott eseményforrás tulajdonságaira jellemző tulajdonságok gyűjteménye. Olyan részleteket tartalmaz, mint például a fogyasztói csoport és a hozzáférési kulcs neve. |
