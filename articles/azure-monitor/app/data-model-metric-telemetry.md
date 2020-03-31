---
title: Adatmodell metrikatelemetriai adatokhoz – Azure Application Insights
description: Application Insights-adatmodell metrikatelemetriai adatokhoz
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 3e4a1fc3de58b8e65ab9c7a288bdf3eb37e7bae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671970"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrikatelemetria: Application Insights-adatmodell

Az [Application Insights](../../azure-monitor/app/app-insights-overview.md)által támogatott metrikatelemetriai két típus létezik: egyetlen mérés és előre összesített metrika. Egyetlen mérés csak egy név és érték. Az előre összesített metrika a metrika minimális és maximális értékét határozza meg az összesítési intervallumban és annak szórásában.

Az összesített metrikatelemetriai adatok feltételezik, hogy az összesítési időszak egy perc volt.

Az Application Insights számos jól ismert metrikanévet támogat. Ezek a mérőszámok a performanceCounters táblába kerülnek.

Rendszer- és folyamatszámlálókat jelölő metrikus mutatók:

| **.NET név**             | **Platform agnosztikus név** | **REST API-név** | **Leírás**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Folyamatban lévő munka... | [processorCpuSzázalék](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | teljes gép CPU
| `\Memory\Available Bytes`                 | Folyamatban lévő munka... | [memóriaElérhető bájt](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | A számítógépen futó folyamatok számára rendelkezésre álló fizikai memória bájtban való mennyiségét jeleníti meg. Kiszámítása a nullázott, szabad és készenléti memórialistákon lévő terület összegének összegzésével történik. A szabad memória készen áll a használatra; a nullázott memória nullákkal töltött memóriaoldalakból áll, hogy a későbbi folyamatok ne láthassák az előző folyamat által használt adatokat; a készenléti memória olyan memória, amelyet eltávolítottak a folyamat munkakészletéből (fizikai memóriájából) a lemezre vezető úton, de még visszahívható. Lásd [memóriaobjektum](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Folyamatban lévő munka... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Cpu a folyamat otthont az alkalmazás
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Folyamatban lévő munka... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | az alkalmazást üzemeltető folyamat által használt memória
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Folyamatban lévő munka... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | az I/O-műveletek sebessége az alkalmazást tároló folyamat szerint fut
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Folyamatban lévő munka... | [kérésekPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | alkalmazásonként feldolgozott kérelmek aránya 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Folyamatban lévő munka... | [kivételekPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | az alkalmazás által okozott kivételek aránya
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Folyamatban lévő munka... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | átlagos kérelmek végrehajtási ideje
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Folyamatban lévő munka... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | a várólistában történő feldolgozásra váró kérelmek száma

## <a name="name"></a>Név

Az Application Insights-portálon és a felhasználói felületen látni kívánt metrika neve. 

## <a name="value"></a>Érték

Egyetlen érték a méréshez. Az összesítéshez egyedi mérések összege.

## <a name="count"></a>Darabszám

Az összesített metrika mérőszám súlya. Nem szabad beállítani a méréshez.

## <a name="min"></a>Min

Az összesített metrika minimális értéke. Nem szabad beállítani a méréshez.

## <a name="max"></a>Max

Az összesített metrika maximális értéke. Nem szabad beállítani a méréshez.

## <a name="standard-deviation"></a>Szórás

Az összesített metrika szórása. Nem szabad beállítani a méréshez.

## <a name="custom-properties"></a>Egyéni tulajdonságok

Metrika `CustomPerfCounter` az `true` egyéni tulajdonság beállítása azt jelzi, hogy a metrika képviseli a Windows teljesítményszámláló. Ezek a mérőszámok a performanceCounters táblázatban. Nem a customMetrics.Not in customMetrics. A metrika neve is elemezve van a kategória-, számláló- és példánynevek kinyeréséhez.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan használhatja az [Application Insights API-t egyéni eseményekhez és mérőszámokhoz.](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)
- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](data-model.md)
- Tekintse meg az Application Insights által támogatott [platformokat.](../../azure-monitor/app/platforms.md)
