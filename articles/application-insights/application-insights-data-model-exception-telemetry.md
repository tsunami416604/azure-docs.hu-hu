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
ms.author: bwren
ms.openlocfilehash: 6b220b0cb6719bac606f599d657d08ab847c7590
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
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
