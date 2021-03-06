---
title: Azure Application Insights kivétel telemetria adatmodell
description: Application Insights adatmodell a kivételek telemetria
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6732fb3dde79abd5c69e2b0a91710bdd2356d6aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320595"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Kivétel telemetria: Application Insights adatmodell

[Application Insights](./app-insights-overview.md)a kivétel egy példánya egy kezelt vagy nem kezelt kivételt jelöl, amely a figyelt alkalmazás végrehajtása során fordult elő.

## <a name="problem-id"></a>Probléma azonosítója

A kivétel kódjában való kidobásának azonosítója. A kivételek csoportosításához használatos. Jellemzően a kivétel típusának és a függvénynek a hívási veremből való kombinációja.

Maximális hossz: 1024 karakter

## <a name="severity-level"></a>Súlyossági szint

Nyomkövetés súlyossági szintje Az érték lehet:,,, `Verbose` `Information` `Warning` `Error` , `Critical` .

## <a name="exception-details"></a>Kivétel részletei

(Kiterjeszthető)

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Ismerje meg, hogyan [diagnosztizálhatja a webalkalmazások kivételeit Application Insights](./asp-net-exceptions.md)használatával.
- Tekintse meg Application Insights által támogatott [platformokat](./platforms.md) .

