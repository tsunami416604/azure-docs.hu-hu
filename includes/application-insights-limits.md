---
title: fájl belefoglalása
description: fájl belefoglalása
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 31ff6ae66b13eb5e5abcde94e8d3df7f6e32f63c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407983"
---
Bizonyos korlátozások érvényesek a metrikák és események alkalmazásonként, azaz kialakítási kulcsonként. A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| Erőforrás | Alapértelmezett korlát | Megjegyzés
| --- | --- | --- |
| Napi teljes adatmennyiség | 100 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha több adat van szüksége, a portálon, a korlát növeléséhez legfeljebb 1000 GB-ig. 1000 GB-nál nagyobb kapacitás, küldjön e-mailek AIDataCap@microsoft.com.
| Throttling | 32000 esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzés | 90 nap | Ez az erőforrás a [Keresés](../articles/azure-monitor/app/diagnostic-search.md), [Elemzés](../articles/azure-monitor/app/analytics.md) és [Metrikaböngésző](../articles/azure-monitor/app/metrics-explorer.md) esetén használható.
| [Többlépéses rendelkezésre állási teszt](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| A maximális mérete | 64,000 |
| Tulajdonság- és metrikanév hossza | 150 | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Tulajdonságérték-sztring hossza | 8,192 | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Nyomkövetési és kivételüzenet hossza | 32,768  | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Rendelkezésre állási tesztek](../articles/azure-monitor/app/monitor-web-app-availability.md) száma alkalmazásonként | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) adatmegőrzés | 5 nap |
| [Profiler](../articles/azure-monitor/app/profiler.md) naponta küldött adatok | 10 GB |

További információt [az Application Insights árait és kvótáit](../articles/azure-monitor/app/pricing.md) ismertető cikkben talál.