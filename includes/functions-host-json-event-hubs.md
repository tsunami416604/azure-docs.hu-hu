---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b5d8f67a70961aab21312b6f241081dcb33f66fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
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
|maxBatchMéret|64|A fogadási ciklusonként fogadott maximális eseményszám.|
|prefetchCount (előre fetchCount)|n/a|Az alapul szolgáló EventProcessorHost által használt alapértelmezett PrefetchCount.| 
|batchCheckpointFrequency|1|Az EventHub kurzor-ellenőrzőpont létrehozása előtt feldolgozandó eseménykötegek száma.| 
