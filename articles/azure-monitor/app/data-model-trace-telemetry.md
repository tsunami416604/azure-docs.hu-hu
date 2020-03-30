---
title: Azure Application Insights-adatmodell – nyomkövetési telemetria
description: Az Application Insights-adatmodell nyomkövetési telemetriai adatokhoz
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 31958b26cdb8a7897cf0051af6600014c07949fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671953"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Nyomkövetési telemetriai adatok: Application Insights-adatmodell

Nyomkövetési telemetriai adatok (az [Application Insights)](../../azure-monitor/app/app-insights-overview.md)a `printf` szöveges keresésstílus-nyomkövetési utasítások. `Log4Net`, `NLog`és az egyéb szövegalapú naplófájl-bejegyzéseket a program ilyen típusú példányokra fordítja le. A nyomkövetés nem rendelkezik bővíthetőségi mérésekkel.

## <a name="message"></a>Üzenet

Nyomkövetési üzenet.

Maximális hossz: 32768 karakter

## <a name="severity-level"></a>Súlyossági szint

A súlyossági szint nyomon követése. Az érték `Verbose` `Information`lehet `Warning` `Error`, `Critical`, , , .

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>További lépések

- [Fedezze fel a .NET nyomkövetési naplókat az Application Insights ban.](../../azure-monitor/app/asp-net-trace-logs.md)
- [Fedezze fel a Java-nyomkövetési naplókat az Application Insights ban.](../../azure-monitor/app/java-trace-logs.md)
- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](data-model.md)
- [Egyéni nyomkövetési telemetriai adatok írása](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Tekintse meg az Application Insights által támogatott [platformokat.](../../azure-monitor/app/platforms.md)
