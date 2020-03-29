---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a925076dfccd30c73febb2aadc8692667ea01525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279448"
---
Szabályozza a [mintavételi funkciót az Application Insights ban.](../articles/azure-functions/functions-monitoring.md#configure-sampling)

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

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|engedélyezve van|igaz|Engedélyezi vagy letiltja a mintavételezést.| 
|maxTelemetryItemsPerSecond|5|Az a küszöbérték, amelynél a mintavétel megkezdődik.| 
