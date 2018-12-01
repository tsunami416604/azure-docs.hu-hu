---
title: Az Azure Application Insights Telemetria adatmodell – Híváslánc-Telemetria |} A Microsoft Docs
description: Application Insights-adatmodell híváslánc-telemetria
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
ms.openlocfilehash: f87b2d1bd105a9a9b8eabb3f2f6686c36f71e6df
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721731"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Nyomkövetési telemetriai: Application Insights adatmodell

Nyomkövetési telemetriai adatok (a [Application Insights](app-insights-overview.md)) jelöli `printf` stílus nyomkövetési utasításokat, amelyek szöveget keres. `Log4Net`, `NLog`, és más szöveges naplófájl-bejegyzéseket fordítja az ilyen típusú példányok. A nyomkövetés nincs mérések,-bővíthetőségi.

## <a name="message"></a>Üzenet

Nyomkövetési üzenet.

Maximális hossz: 32768 karaktert

## <a name="severity-level"></a>Súlyossági szint

Nyomon követheti a súlyossági szintet. Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>További lépések

- [Ismerkedés a .NET hívásláncnaplók megtekintése az Application Insights](app-insights-asp-net-trace-logs.md).
- [Ismerje meg a nyomkövetési naplók az Application Insights Java](app-insights-java-trace-logs.md).
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights és modellhez.
- [A nyomkövetési egyéni telemetriát írhat](app-insights-api-custom-events-metrics.md#tracktrace)
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
