---
title: Azure Application Insights Telemetria adatmodell – Event telemetria | Microsoft Docs
description: Az Event telemetria Application Insights adatmodellje
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671880"
---
# <a name="event-telemetry-application-insights-data-model"></a>Event telemetria: Application Insights adatmodell

Az telemetria ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)) létrehozhat egy eseményt, amely az alkalmazásban bekövetkezett események ábrázolására használható. Általában ez egy felhasználói beavatkozás, például a Button Click vagy a Order pénztár. Az alkalmazás életciklus-eseménye (például inicializálás vagy konfiguráció frissítése) is lehet. 

Szemantikailag előfordulhat, hogy az események nem korrelálnak a kérelmekkel. Ha azonban megfelelően van használatban, az Event telemetria fontosabb, mint a kérelmek vagy a Nyomkövetések. Az események üzleti telemetria jelentenek, és külön, kevésbé agresszív [mintavételezéssel](../../azure-monitor/app/api-filtering-sampling.md)kell rendelkezniük.

## <a name="name"></a>Name (Név)

Esemény neve. A megfelelő csoportosítás és a hasznos mérőszámok engedélyezéséhez korlátozza az alkalmazást, hogy az csak kis számú különböző eseményt generáljon. Például ne használjon külön nevet az esemény minden egyes generált példánya számára.

Maximális hossz: 512 karakter

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- [Egyéni Event telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
