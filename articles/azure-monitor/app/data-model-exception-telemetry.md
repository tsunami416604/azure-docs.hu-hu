---
title: Azure Application Insights-kivételtelemetriai adatmodell
description: Application Insights-adatmodell a kivételtelemetriai adatokhoz
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671936"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Kivételtelemetria: Application Insights-adatmodell

Az [Application Insights](../../azure-monitor/app/app-insights-overview.md)ban egy kivétel példánya egy felügyelt vagy nem kezelt kivételt jelöl, amely a figyelt alkalmazás végrehajtása során történt.

## <a name="problem-id"></a>Problémaazonosító

Annak azonosítója, hogy a kivétel hol lett feldobva a kódban. Kivételcsoportosításhoz használatos. Általában a kivételtípus és a hívási verem függvényének kombinációja.

Maximális hossz: 1024 karakter

## <a name="severity-level"></a>Súlyossági szint

A súlyossági szint nyomon követése. Az érték `Verbose` `Information`lehet `Warning` `Error`, `Critical`, , , .

## <a name="exception-details"></a>Kivétel részletei

(Kikell terjeszteni)

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](data-model.md)
- Megtudhatja, hogyan diagnosztizálhatja a [kivételeket a webalkalmazásokban az Application Insights segítségével.](../../azure-monitor/app/asp-net-exceptions.md)
- Tekintse meg az Application Insights által támogatott [platformokat.](../../azure-monitor/app/platforms.md)
