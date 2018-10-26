---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133711"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxConcurrentCalls|16|Egyidejű hívás, amely az üzenet szivattyú kell kezdeményezni a visszahívás maximális számát. Alapértelmezés szerint a Functions futtatókörnyezete dolgozza fel a több üzenetet egy időben. A közvetlen egyszerre csak egy üzenetsor vagy témakör üzenetet feldolgozni a futtatókörnyezetet, állítsa `maxConcurrentCalls` 1-re. | 
|prefetchCount|n/a|Az alapértelmezett PrefetchCount, amely az alapul szolgáló MessageReceiver használni fog.| 
|autoRenewTimeout|00:05:00|A maximális időtartamot, amelyen belül az üzenet zárolási újul meg automatikusan.| 
