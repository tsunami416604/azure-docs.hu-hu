---
title: Azure Application Insights Telemetria adatmodell – kivétel telemetria | Microsoft Docs
description: Application Insights adatmodell a kivételek telemetria
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0ba1c94ee8dc78b937d650cff32e1518a7ca5a12
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677419"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Kivétel telemetria: Application Insights adatmodell

[Application Insights](../../azure-monitor/app/app-insights-overview.md)a kivétel egy példánya egy kezelt vagy nem kezelt kivételt jelöl, amely a figyelt alkalmazás végrehajtása során fordult elő.

## <a name="problem-id"></a>Probléma azonosítója

A kivétel kódjában való kidobásának azonosítója. A kivételek csoportosításához használatos. Jellemzően a kivétel típusának és a függvénynek a hívási veremből való kombinációja.

Maximális hossz: 1024 karakter

## <a name="severity-level"></a>Súlyossági szint

Nyomkövetés súlyossági szintje Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Kivétel részletei

(Kiterjeszthető)

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Következő lépések

- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Ismerje meg, hogyan [diagnosztizálhatja a webalkalmazások kivételeit Application Insights](../../azure-monitor/app/asp-net-exceptions.md)használatával.
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
