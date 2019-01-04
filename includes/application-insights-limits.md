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
ms.openlocfilehash: 0dbc0834d5f5b87a39ae33d296af847cf4e368e3
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977463"
---
Bizonyos korlátozások érvényesek a mérőszámok számára és az alkalmazásonkénti eseményekre (kialakítási kulcsonként értendő). A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| Erőforrás | Alapértelmezett korlát | Megjegyzés
| --- | --- | --- |
| Napi teljes adatmennyiség | 100 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha több adat van szüksége, a portálon, a korlát növeléséhez legfeljebb 1000 GB-ig. Üzenet küldése a 1000 GB-nál nagyobb kapacitás, AIDataCap@microsoft.com.
| Throttling | 32 ezer esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzés | 90 nap | Ez az erőforrás a [Keresés](../articles/azure-monitor/app/diagnostic-search.md), [Elemzés](../articles/azure-monitor/app/analytics.md) és [Metrikaböngésző](../articles/application-insights/app-insights-metrics-explorer.md) esetén használható.
| [Többlépéses rendelkezésre állási teszt](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| A maximális mérete | 64 K | 
| Tulajdonság- és metrikanév hossza | 150 | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Tulajdonságérték-sztring hossza | 8,192 | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Nyomkövetési és kivételüzenet hossza | 10 EZER | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Rendelkezésre állási tesztek](../articles/azure-monitor/app/monitor-web-app-availability.md) száma alkalmazásonként | 100 |
| [Profiler](../articles/application-insights/app-insights-profiler.md) adatmegőrzés | 5 nap |
| [Profiler](../articles/application-insights/app-insights-profiler.md) naponta küldött adatok | 10 GB |

További információt [az Application Insights árait és kvótáit](../articles/application-insights/app-insights-pricing.md) ismertető cikkben talál.