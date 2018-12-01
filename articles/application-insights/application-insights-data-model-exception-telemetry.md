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
ms.openlocfilehash: 672eb1100731d636ae7c34f16d371c4f93b5716c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721816"
---
# <a name="exception-telemetry-application-insights-data-model"></a>– Kivételtelemetria: Application Insights adatmodell

A [Application Insights](app-insights-overview.md), kivétel példányát jelöli egy kezelt vagy nem kezelt kivétellel, hogy a figyelt alkalmazásban végrehajtása közben lépett fel.

## <a name="problem-id"></a>Probléma azonosítója

Azonosítója, ahol a kivétel lépett fel a kódban. Csoportosítás kivételek használja. Általában kombinációját kivétel típusát és a egy függvény érkezett a hívási veremben.

Maximális hossz: 1024 karakter

## <a name="severity-level"></a>Súlyossági szint

Nyomon követheti a súlyossági szintet. Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Kivétel részletei

(A kiterjesztett)

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- Lásd: [adatmodell](application-insights-data-model.md) Application Insights és modellhez.
- Ismerje meg, hogyan [az Application insights segítségével a webalkalmazások kivételeinek diagnosztizálása](app-insights-asp-net-exceptions.md).
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
