---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791688"
---
### <a name="functions-2x-and-higher"></a>2. x és újabb függvények

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Tulajdonság  |Alapértelmezett | Leírás |
|---------|---------|---------|
|maxBatchSize|10|A fogadott események száma fogadási hurokban.|
|prefetchCount|300|A mögöttes `EventProcessorHost`alapértelmezett előzetes lehívási szám.|
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.|

> [!NOTE]
> A Host. JSON fájl Azure Functions 2. x és újabb verziójának hivatkozásához tekintse meg a [Azure functions gazdagép. JSON-referenciáját](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Functions 1.x

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
|maxBatchSize|64|A fogadott események száma fogadási hurokban.|
|prefetchCount|n/a|Az alapul szolgáló `EventProcessorHost`alapértelmezett előzetes lekérési művelet.| 
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.| 

> [!NOTE]
> A Host. JSON fájl Azure Functions 1. x fájlban való hivatkozásához tekintse meg a [Host. JSON-referenciát Azure functions 1. x](../articles/azure-functions/functions-host-json-v1.md)fájlban.

