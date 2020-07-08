---
title: fájlbefoglalás
description: fájlbefoglalás
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a925076dfccd30c73febb2aadc8692667ea01525
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76279448"
---
A [Application Insights mintavételi funkcióját](../articles/azure-functions/functions-monitoring.md#configure-sampling)vezérli.

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Tulajdonság  |Alapértelmezett | Description |
|---------|---------|---------| 
|isEnabled|igaz|Engedélyezheti vagy letilthatja a mintavételezést.| 
|maxTelemetryItemsPerSecond|5|A mintavételezés megkezdésének küszöbértéke.| 
