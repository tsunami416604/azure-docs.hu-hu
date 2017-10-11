---
title: "Azure Application Insights Telemetria-adatmodell - függőségi Telemetria |} Microsoft Docs"
description: "Application Insights – függőségi telemetria tartozó adatmodell"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: bwren
ms.openlocfilehash: 2e97c3f951f46c32802aea543b93d5ab1bb76228
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="dependency-telemetry-application-insights-data-model"></a>– Függőségi telemetria: Application Insights adatmodell

– Függőségi Telemetria (a [Application Insights](app-insights-overview.md)) a figyelt összetevő egy távoli összetevő, például az SQL- vagy HTTP-végponttal való jelöli.

## <a name="name"></a>Név

Ez a függőségi hívás értékkel kezdeményezték a parancs nevét. Alacsony cardinality értéke. Többek között a tárolt eljárás nevét és URL-cím elérési út sablont.

## <a name="id"></a>ID (Azonosító)

A függőségi hívás példány azonosítója. Ez a függőségi hívás megfelelő kérelem telemetriai elemmel használható korrelációhoz. További információkért lásd: [korrelációs](application-insights-correlation.md) lap.

## <a name="data"></a>Adatok

Ez a függőségi hívás által kezdeményezett parancsot. Többek között az SQL-utasítást, és HTTP URL-cím és az összes lekérdezési paramétert.

## <a name="type"></a>Típus

A függőségi típusnév. A függőségek logikai jellegű csoportosítását és más mezők, például a commandName és resultCode értelmezése alacsony cardinality értéke. Többek között az SQL Azure-tábla és a HTTP.

## <a name="target"></a>cél

A függőségi hívás célhelyre. Például a kiszolgáló neve, a gazdagép címe. További információkért lásd: [korrelációs](application-insights-correlation.md) lap.

## <a name="duration"></a>Időtartam

Időtartam formátumú kérelem: `DD.HH:MM:SS.MMMMMM`. Lehet kisebb, mint `1000` nap.

## <a name="result-code"></a>Eredmény kódja

A függőségi hívás eredménykódja. Többek között az SQL-hibakód és a HTTP-állapotkódot.

## <a name="success"></a>Sikeres

Sikeres vagy sikertelen hívás megjelölése.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mértékek

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Következő lépések

- Állítsa be a követési függőségi [.NET](app-insights-asp-net-dependencies.md).
- Állítsa be a követési függőségi [Java](app-insights-java-agent.md).
- [Egyéni függőségi telemetria írása](app-insights-api-custom-events-metrics.md#trackdependency)
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
