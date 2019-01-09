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
ms.openlocfilehash: df85aafc81b199610c02f0faecb06e804fda24bb
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118064"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Híváslánc-telemetria: Application Insights-adatmodell

Nyomkövetési telemetriai adatok (a [Application Insights](../../azure-monitor/app/app-insights-overview.md)) jelöli `printf` stílus nyomkövetési utasításokat, amelyek szöveget keres. `Log4Net`, `NLog`, és más szöveges naplófájl-bejegyzéseket fordítja az ilyen típusú példányok. A nyomkövetés nincs mérések,-bővíthetőségi.

## <a name="message"></a>Üzenet

Nyomkövetési üzenet.

Maximális hossz: 32768 karaktert

## <a name="severity-level"></a>Súlyossági szint

Nyomon követheti a súlyossági szintet. Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>További lépések

- [Ismerkedés a .NET hívásláncnaplók megtekintése az Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Ismerje meg a nyomkövetési naplók az Application Insights Java](../../azure-monitor/app/java-trace-logs.md).
- Lásd: [adatmodell](data-model.md) Application Insights és modellhez.
- [A nyomkövetési egyéni telemetriát írhat](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Tekintse meg [platformok](../../azure-monitor/app/platforms.md) Application Insights által támogatott.
