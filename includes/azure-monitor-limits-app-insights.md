---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: c68e58bb18a5dba07855234af07b0a8be767bed0
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75928081"
---
A mérőszámok és események másodpercenkénti száma korlátozott a rendszerállapot-kulcs alapján. A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| Erőforrás | Alapértelmezett korlát | Megjegyzés
| --- | --- | --- |
| Napi teljes adatmennyiség | 100 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha több adatra van szüksége, növelheti a portálon megjelenő korlátot, amely akár 1 000 GB is lehet. A 1 000 GB-nál nagyobb kapacitások esetében küldjön e-mailt AIDataCap@microsoft.com.
| Throttling | 32 000 esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzés | 90 nap | Ez az erőforrás a [Keresés](../articles/azure-monitor/app/diagnostic-search.md), [Elemzés](../articles/azure-monitor/app/analytics.md) és [Metrikaböngésző](../articles/azure-monitor/app/metrics-explorer.md) esetén használható.
| [Többlépéses rendelkezésre állási teszt](../articles/azure-monitor/app/availability-multistep.md) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| Esemény maximális mérete | 64 000 000 bájt |
| Tulajdonság- és metrikanév hossza | 150 | Lásd: [sémák beírása](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Tulajdonságérték-sztring hossza | 8,192 | Lásd: [sémák beírása](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| Nyomkövetési és kivételüzenet hossza | 32 768  | Lásd: [sémák beírása](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond/).
| [Rendelkezésre állási tesztek](../articles/azure-monitor/app/monitor-web-app-availability.md) száma alkalmazásonként | 100 |
| [Profiler](../articles/azure-monitor/app/profiler.md) adatmegőrzés | 5 nap |
| [Profiler](../articles/azure-monitor/app/profiler.md) -adat elküldve naponta | 10 GB |

További információt [az Application Insights árait és kvótáit](../articles/azure-monitor/app/pricing.md) ismertető cikkben talál.
