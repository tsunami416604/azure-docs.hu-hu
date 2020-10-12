---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81791688"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

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
|prefetchCount|300|A mögöttes alapértelmezett előzetes lehívási szám `EventProcessorHost` .|
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.|

> [!NOTE]
> A host.jsAzure Functions 2. x vagy újabb verziójában való hivatkozáshoz lásd: [host.jsAzure functions](../articles/azure-functions/functions-host-json.md).

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
|prefetchCount|n/a|Az alapul szolgáló alapértelmezett előzetes lekérési művelet `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.| 

> [!NOTE]
> A host.jsAzure Functions 1. x verzióban való hivatkozását lásd: [host.jsAzure functions 1. x-re](../articles/azure-functions/functions-host-json-v1.md).

