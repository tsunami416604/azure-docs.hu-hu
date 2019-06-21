---
title: fájl belefoglalása
description: fájl belefoglalása
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/20/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 708421feca879ade26a1488541fa7fbf8a601358
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303835"
---
Bizonyos korlátozások érvényesek a metrikák és események alkalmazásonként, azaz kialakítási kulcsonként. A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| Resource | Alapértelmezett korlát | Megjegyzés
| --- | --- | --- |
| Napi teljes adatmennyiség | 100 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha több adat van szüksége, a portálon, a korlát növeléséhez legfeljebb 1000 GB-ig. 1000 GB-nál nagyobb kapacitás, küldjön e-mailek AIDataCap@microsoft.com.
| Throttling | 32000 esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzés | 90 nap | Ez az erőforrás a [Keresés](../articles/azure-monitor/app/diagnostic-search.md), [Elemzés](../articles/azure-monitor/app/analytics.md) és [Metrikaböngésző](../articles/azure-monitor/app/metrics-explorer.md) esetén használható.
| [Többlépéses rendelkezésre állási teszt](../articles/azure-monitor/app/availability-multistep.md) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| A maximális mérete | 64,000 |
| Tulajdonság- és metrikanév hossza | 150 | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Tulajdonságérték-sztring hossza | 8,192 | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Nyomkövetési és kivételüzenet hossza | 32,768  | Lásd: [írja be a sémák](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Rendelkezésre állási tesztek](../articles/azure-monitor/app/monitor-web-app-availability.md) száma alkalmazásonként | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) adatmegőrzés | 5 nap |
| [Profiler](../articles/azure-monitor/app/profiler.md) naponta küldött adatok | 10 GB |

További információt [az Application Insights árait és kvótáit](../articles/azure-monitor/app/pricing.md) ismertető cikkben talál.