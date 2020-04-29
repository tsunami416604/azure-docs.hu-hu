---
title: Azure Application Insights kivétel telemetria adatmodell
description: Application Insights adatmodell a kivételek telemetria
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: fa9bc4efd9549cbcb1d50439989c5dea79d9d60c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671936"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Kivétel telemetria: Application Insights adatmodell

[Application Insights](../../azure-monitor/app/app-insights-overview.md)a kivétel egy példánya egy kezelt vagy nem kezelt kivételt jelöl, amely a figyelt alkalmazás végrehajtása során fordult elő.

## <a name="problem-id"></a>Probléma azonosítója

A kivétel kódjában való kidobásának azonosítója. A kivételek csoportosításához használatos. Jellemzően a kivétel típusának és a függvénynek a hívási veremből való kombinációja.

Maximális hossz: 1024 karakter

## <a name="severity-level"></a>Súlyossági szint

Nyomkövetés súlyossági szintje Az érték lehet `Verbose` `Information` `Warning`:,, `Error`, `Critical`,.

## <a name="exception-details"></a>Kivétel részletei

(Kiterjeszthető)

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Ismerje meg, hogyan [diagnosztizálhatja a webalkalmazások kivételeit Application Insights](../../azure-monitor/app/asp-net-exceptions.md)használatával.
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
