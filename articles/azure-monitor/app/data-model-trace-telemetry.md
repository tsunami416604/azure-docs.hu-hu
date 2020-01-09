---
title: Azure Application Insights adatmodell – nyomkövetési telemetria
description: Application Insights adatmodell nyomkövetési telemetria
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6e188039a86f4c655df3098be1d769668dcf3571
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407136"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Nyomkövetési telemetria: Application Insights adatmodell

A nyomkövetési telemetria ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)) a szöveg által keresett, `printf` stílusú nyomkövetési utasításokat jelöli. a `Log4Net`, `NLog`és más szöveg alapú naplófájl-bejegyzések ilyen típusú példányokra vannak lefordítva. A nyomkövetés nem rendelkezik bővíthetőségi mértékkel.

## <a name="message"></a>Üzenet

Nyomkövetési üzenet.

Maximális hossz: 32768 karakter

## <a name="severity-level"></a>Súlyossági szint

Nyomkövetés súlyossági szintje Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Következő lépések

- [Ismerkedjen meg a .net-nyomkövetési naplók Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Ismerkedjen meg a Java-nyomkövetési naplók Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- [Egyéni nyomkövetési telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
