---
title: Az Azure Application Insights Telemetria adatmodell - esemény Telemetriát |} A Microsoft Docs
description: Application Insights-adatmodell az esemény telemetriát
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 062478783465edc2d3afa4b80a22f119e68da049
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091706"
---
# <a name="event-telemetry-application-insights-data-model"></a>Esemény telemetriát: Application Insights adatmodell

Létrehozhat event telemetriai elem (a [Application Insights](app-insights-overview.md)) jelölésére, hogy az alkalmazás a következő esemény. Ez általában egy felhasználói beavatkozás például gombra kattintson, vagy a order checkout. Az inicializálás vagy a konfiguráció módosítása alkalmazás életciklusa eseményt is lehet. 

Események szemantikailag, előfordulhat, hogy, vagy nem feleltethető kérelmekre. Viszont ha megfelelően használja, esemény telemetriát, fontosabb, mint a kérések vagy a nyomkövetéseket. Események üzleti telemetriát, és a kevésbé agresszív lehet egy külön, tulajdonos [mintavételi](app-insights-api-filtering-sampling.md).

## <a name="name"></a>Name (Név)

Esemény neve. Ahhoz, hogy a megfelelő csoportosítási és hasznos metrikákat, korlátozza az alkalmazás, hogy létrejöjjön egy kis számú külön esemény nevét. Például ne használja az esemény minden egyes létrehozott példány külön nevét.

Maximális hossz: 512 karakter

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- Lásd: [adatmodell](application-insights-data-model.md) Application Insights és modellhez.
- [Egyéni esemény telemetriát írhat](app-insights-api-custom-events-metrics.md#trackevent)
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
