---
title: Az Azure Application Insights Telemetria adatmodell - metrika Telemetriai |} A Microsoft Docs
description: Application Insights adatmodell metrika telemetria
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: f624e65508c33fa08bf1381b434847cedcf0df00
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721102"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrika telemetriai: Application Insights adatmodell

Metrika telemetriai által támogatott két típusa van [Application Insights](app-insights-overview.md): egyszeri mérési és előre összesített metrikája. Egyetlen érték csak egy nevet és egy értéket. Előre összesített metrikája minimális és maximális értékét a metrika az aggregációs időköz és szórását, adja meg.

Előzetes összesítésére metrika telemetriai feltételezi, hogy összesítési időszak egy perc volt.

Nincsenek az Application Insights által támogatott több jól ismert metrikák nevei. Ezek a metrikák helyezni performanceCounters táblába.

A metrika jelölő rendszer és a folyamat számlálókat:

| **.NET-név**             | **Független platform neve** | **REST API-név** | **Leírás**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Feldolgozás folyamatban... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | összes gép CPU
| `\Memory\Available Bytes`                 | Feldolgozás folyamatban... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Bájt, a számítógépen futó folyamatok számára elérhető fizikai memória összegét mutatja. Összesítjük terület mennyisége a nullázott, ingyenes és a készenléti memória listák számítható. Szabad memória készen áll a használatra; megakadályozza, hogy a későbbi folyamatok jelennek meg adatok egy korábbi folyamat; nullák kitöltött memórialapok áll nullázott memória készenléti memória mérete, el lett távolítva a folyamat munkakészletének (a fizikai memória) útközben lemezre, de továbbra is elérhető visszaírni a memória. Lásd: [memóriabeli objektum](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Feldolgozás folyamatban... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Az alkalmazást üzemeltető folyamat CPU
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Feldolgozás folyamatban... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | a folyamat az alkalmazást üzemeltető által használt memória
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Feldolgozás folyamatban... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | i/o-műveletek sebessége futtatja az alkalmazást üzemeltető folyamat
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Feldolgozás folyamatban... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | alkalmazás által feldolgozott kérelmek száma 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Feldolgozás folyamatban... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | alkalmazás által okozott kivételek sebessége
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Feldolgozás folyamatban... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | a kérelmek átlagos végrehajtási idő
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Feldolgozás folyamatban... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | egy üzenetsorban lévő feldolgozásra váró kérelmek száma

## <a name="name"></a>Name (Név)

Tekintse meg az Application Insights portálon és a felhasználói felületen szeretné a metrika neve. 

## <a name="value"></a>Érték

Mérési egyetlen értéket. Egyéni mérések összesítési összegét.

## <a name="count"></a>Darabszám

Metrika súlyának a összesített mérőszám. Nem állítható egy mérték.

## <a name="min"></a>Min

Az összesített metrikája minimális értékét. Nem állítható egy mérték.

## <a name="max"></a>Max

Az összesített metrikája maximális értéke. Nem állítható egy mérték.

## <a name="standard-deviation"></a>Szórás

Az összesített metrikája szórását. Nem állítható egy mérték.

## <a name="custom-properties"></a>Egyéni tulajdonságok

Egyéni tulajdonság metrika `CustomPerfCounter` beállítása `true` azt jelzik, hogy a metrika a windows-teljesítményszámláló jelöli. Ezek a metrikák performanceCounters tábla elhelyezni. Nincs a customMetrics. Bontsa ki a kategóriát, számláló és példány nevét is ez a metrika neve a rendszer elemzi.

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használható [Application Insights API egyéni eseményekhez és a metrikák](app-insights-api-custom-events-metrics.md#trackmetric).
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights és modellhez.
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
