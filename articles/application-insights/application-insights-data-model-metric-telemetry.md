---
title: Azure Application Insights Telemetria-adatmodell - metrika Telemetriai |} Microsoft Docs
description: Application Insights adatmodell metrika telemetriai adat
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: bd09e2a21c25097fa4b378cb2dbe2787edbb1967
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrika telemetriai: Application Insights adatmodell

Metrika telemetriai által támogatott két típusa van [Application Insights](app-insights-overview.md): egyszeri mérési és előre összesített mértéket. Egyetlen érték csak a nevét és értékét. Előre összesített metrika legkisebb és legnagyobb értéke a metrika az aggregációs időköznek és szórása azt határozza meg.

Előre összesített metrika telemetriai azt feltételezi, hogy az összesítő időszak egy perc volt.

Nincsenek Application Insights által támogatott több jól ismert metrika neve. A metrikák elhelyezett performanceCounters táblába.

A mérőszám a rendszer és a folyamat számlálók képviselő:

| **.NET neve**             | **Platform független neve** | **REST API-név** | **Leírás**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Megoldás folyamatban... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | gép Processzorainak száma
| `\Memory\Available Bytes`                 | Megoldás folyamatban... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | a lemezen rendelkezésre álló memória
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Megoldás folyamatban... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | A folyamat az alkalmazást futtató Processzor
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Megoldás folyamatban... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | az alkalmazást a folyamat által használt memória
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Megoldás folyamatban... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | i/o-műveletek futtatja az alkalmazást futtató folyamat
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Megoldás folyamatban... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | alkalmazás által feldolgozott kérelmek száma 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Megoldás folyamatban... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | alkalmazás által kiváltott kivételekre is ki aránya
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Megoldás folyamatban... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | a kérelmek átlagos végrehajtási idő
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Megoldás folyamatban... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | a sorhoz feldolgozásra váró kérelmek száma

## <a name="name"></a>Név

Az Application Insights portál és a felhasználói felületen szeretné metrika neve. 

## <a name="value"></a>Érték

Mérési egyetlen értéket. Az összesítés egyéni mértékek összege.

## <a name="count"></a>Darabszám

Metrika súlyának a összesített metrika. Nem lehet beállítani a mérés.

## <a name="min"></a>Perc

Az összesített metrika minimális értéke. Nem lehet beállítani a mérés.

## <a name="max"></a>Maximum

Az összesített metrika maximális értékét. Nem lehet beállítani a mérés.

## <a name="standard-deviation"></a>Szórás

Az összesített metrika szórása. Nem lehet beállítani a mérés.

## <a name="custom-properties"></a>Egyéni tulajdonságok

Egyéni tulajdonság metrika `CustomPerfCounter` beállítása `true` jelzi, hogy a mérték jelenti-e a windows-teljesítményszámláló. A metrikák performanceCounters tábla kerülnek. Nem található customMetrics. Ez a mérőszám a neve is kategória, a számláló és példánynevek kibontásához elemzi.

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan használható [Application Insights API egyéni események és metrikák](app-insights-api-custom-events-metrics.md#trackmetric).
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
