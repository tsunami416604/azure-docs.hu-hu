---
title: "Azure Application Insights Telemetria-adatmodell - Kivételtelemetria |} Microsoft Docs"
description: "Application Insights – kivételtelemetria tartozó adatmodell"
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
ms.openlocfilehash: 3c3c3a39c7986cc771fe4baf60ad9b316888f6ed
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="exception-telemetry-application-insights-data-model"></a>– Kivételtelemetria: Application Insights adatmodell

A [Application Insights](app-insights-overview.md), kivétel példányának jelöli egy kezelt vagy nem kezelt kivétel a figyelt alkalmazás végrehajtása során.

## <a name="problem-id"></a>Problémaazonosító

Ahol a kivétel a kód azonosítója. Csoportosítás kivételek használatos. Általában kombinációja kivétel típusát és egy, a hívási verem származó függvényt.

Maximális hossz: 1024 karakter hosszú lehet

## <a name="severity-level"></a>Súlyossági szint

Nyomkövetési súlyossági szint. Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>A kivétel részletei

(A kiterjesztett)

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mértékek

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Következő lépések

- Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- Megtudhatja, hogyan [kivételek az Application insights szolgáltatással a webalkalmazások diagnosztizálásához](app-insights-asp-net-exceptions.md).
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
