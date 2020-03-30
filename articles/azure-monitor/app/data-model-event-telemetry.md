---
title: Azure Application Insights telemetriai adatmodell – Eseménytelemetria | Microsoft dokumentumok
description: Application Insights-adatmodell az eseménytelemetria
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671880"
---
# <a name="event-telemetry-application-insights-data-model"></a>Eseménytelemetria: Application Insights-adatmodell

Létrehozhat eseménytelemetriai elemeket (az [Application Insightsban)](../../azure-monitor/app/app-insights-overview.md)az alkalmazásban bekövetkezett esemény ábrázolására. Általában ez egy felhasználói beavatkozás, mint például a gombkattintás vagy a pénztár megrendelése. Ez is lehet egy alkalmazás életciklus-esemény, például inicializálás vagy konfigurációs frissítés. 

Szemantikailag az események összefügghetnek a kérésekkel, vagy nem. Azonban, ha megfelelően használják, esemény telemetriai fontosabb, mint a kérelmek vagy nyomkövetések. Az események üzleti telemetriai adatokat képviselnek, és különálló, kevésbé agresszív [mintavételezésnek](../../azure-monitor/app/api-filtering-sampling.md)kell lenniük.

## <a name="name"></a>Név

Esemény neve. A megfelelő csoportosítás és hasznos metrikák engedélyezéséhez korlátozza az alkalmazást úgy, hogy az néhány különálló eseménynevet hozzon létre. Például ne használjon külön nevet egy esemény minden létrehozott példányához.

Maximális hossz: 512 karakter

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>További lépések

- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](data-model.md)
- [Egyéni eseménytelemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Tekintse meg az Application Insights által támogatott [platformokat.](../../azure-monitor/app/platforms.md)
