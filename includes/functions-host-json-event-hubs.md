---
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104449"
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

