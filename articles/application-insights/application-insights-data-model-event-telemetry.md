---
title: "Azure Application Insights Telemetria-adatmodell - esemény Telemetriai |} Microsoft Docs"
description: "Application Insights adatmodell esemény telemetriai adat"
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
ms.openlocfilehash: dc6ef1771f4f09b274f12606d1278e1f29c23103
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="event-telemetry-application-insights-data-model"></a>Esemény telemetriai: Application Insights adatmodell

Esemény telemetriai elemek is létrehozhat (a [Application Insights](app-insights-overview.md)) képviselő esemény történt az alkalmazásban. Az általában egy felhasználói beavatkozást például kattintson vagy sorrend kivételt. Például az inicializálási vagy a konfiguráció módosítása alkalmazás életciklusa esemény is lehet. 

Események szemantikailag, előfordulhat, hogy, vagy a kérelmekre nem feleltethető. Azonban ha megfelelően használják, esemény telemetriai fontosabb, mint a kéréseket, és a nyomkövetési adatokat. Események üzleti telemetriai jelöl, és külön, a tulajdonos kevesebb agresszív [mintavételi](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Név

Az esemény neve. Ahhoz, hogy a megfelelő csoportosítás és hasznos metrikákat, korlátozhatja az alkalmazás így külön esemény nevek kis számú generál. Például ne használja az esemény minden egyes létrehozott példány külön nevét.

Maximális hossz: 512 karakter

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mértékek

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Következő lépések

- Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- [Egyéni esemény telemetriai adatok írása](app-insights-api-custom-events-metrics.md#trackevent)
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
