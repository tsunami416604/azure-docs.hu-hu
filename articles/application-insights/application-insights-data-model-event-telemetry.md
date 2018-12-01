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
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 5883a8eb4a550c61251fa20331dbd433328c5d59
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724043"
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
