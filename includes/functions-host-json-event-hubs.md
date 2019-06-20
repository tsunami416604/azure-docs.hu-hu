---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178913"
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
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpont létrehozása előtt feldolgozható kötegek esemény száma.| 
