---
title: Az Azure Application Insights Telemetria Data Model – Kivételtelemetria |} A Microsoft Docs
description: Application Insights-adatmodell – kivételtelemetria
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
ms.openlocfilehash: eaba4572d9b7a83d27c98cf83eeef8fbd699c467
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812282"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetria. kivétel: Application Insights-adatmodell

A [Application Insights](../../application-insights/app-insights-overview.md), kivétel példányát jelöli egy kezelt vagy nem kezelt kivétellel, hogy a figyelt alkalmazásban végrehajtása közben lépett fel.

## <a name="problem-id"></a>Probléma azonosítója

Azonosítója, ahol a kivétel lépett fel a kódban. Csoportosítás kivételek használja. Általában kombinációját kivétel típusát és a egy függvény érkezett a hívási veremben.

Maximális hossz: 1024 karakternél

## <a name="severity-level"></a>Súlyossági szint

Nyomon követheti a súlyossági szintet. Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Kivétel részletei

(A kiterjesztett)

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- Lásd: [adatmodell](data-model.md) Application Insights és modellhez.
- Ismerje meg, hogyan [az Application insights segítségével a webalkalmazások kivételeinek diagnosztizálása](../../azure-monitor/app/asp-net-exceptions.md).
- Tekintse meg [platformok](../../application-insights/app-insights-platforms.md) Application Insights által támogatott.
