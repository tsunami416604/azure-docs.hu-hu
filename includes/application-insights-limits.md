---
title: fájl belefoglalása
description: fájl belefoglalása
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 08/06/2019
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 14e2bd4af2616e9dd33fe8267de132ab6c0f1cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69982616"
---
Bizonyos korlátok vannak a metrikák és események száma alkalmazásonként, azaz instrumentation kulcsonként. A korlátozások a választott [díjszabási csomagtól](https://azure.microsoft.com/pricing/details/application-insights/) függően változnak.

| Erőforrás | Alapértelmezett korlát | Megjegyzés
| --- | --- | --- |
| Napi teljes adatmennyiség | 100 GB | Egy korlát beállításával csökkenthető az adatmennyiség. Ha további adatokra van szüksége, növelheti a korlátot a portálon, akár 1000 GB-ig. Az 1000 GB-nál nagyobb kapacitásesetén AIDataCap@microsoft.comküldjön e-mailt a-nak.
| Throttling | 32 000 esemény/másodperc | A korlát megállapítása egy percnyi mérés alapján történik.
| Adatmegőrzés | [30 - 730 nap](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period)  | Ez az erőforrás a [Keresés](../articles/azure-monitor/app/diagnostic-search.md), [Elemzés](../articles/azure-monitor/app/analytics.md) és [Metrikaböngésző](../articles/azure-monitor/app/metrics-explorer.md) esetén használható.
| [Többlépéses rendelkezésre állási teszt](../articles/azure-monitor/app/availability-multistep.md) a részletes eredmények megőrzésével | 90 nap | Ez az erőforrás minden lépésről részletes eredményeket biztosít.
| Telemetriai elem maximális mérete | 64 kB |
| Telemetriai cikkek maximális tételenként | 64 Ezer |
| Tulajdonság- és metrikanév hossza | 150 | Lásd [a típussémákat](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Tulajdonságérték-sztring hossza | 8,192  | Lásd [a típussémákat](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| Nyomkövetési és kivételüzenet hossza | 32,768  | Lásd [a típussémákat](https://github.com/microsoft/ApplicationInsights-Home/tree/master/EndpointSpecs/Schemas/Bond).
| [Rendelkezésre állási tesztek](../articles/azure-monitor/app/monitor-web-app-availability.md) száma alkalmazásonként | 100 |
| [Profilozó](../articles/azure-monitor/app/profiler.md) adatmegőrzése | 5 nap |
| Naponta küldött [profilozói](../articles/azure-monitor/app/profiler.md) adatok | 10 GB |

További információt [az Application Insights árait és kvótáit](../articles/azure-monitor/app/pricing.md) ismertető cikkben talál.