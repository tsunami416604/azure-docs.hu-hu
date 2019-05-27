---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131701"
---
```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------| 
|maxBatchSize|64|A ciklust a fogadás egy fogadott események maximális száma.|
|prefetchCount|n/a|Az alapértelmezett PrefetchCount, amely az alapul szolgáló EventProcessorHost használni fog.| 
|batchCheckpointFrequency|1.|Az EventHub kurzor ellenőrzőpont létrehozása előtt feldolgozható kötegek esemény száma.| 
