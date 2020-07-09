---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 2604a1608f21d7239db755027e15b8198fb3f9f2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
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

|Tulajdonság  |Alapértelmezett | Description |
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

|Tulajdonság  |Alapértelmezett | Description |
|---------|---------|---------| 
|maxBatchSize|64|A fogadott események száma fogadási hurokban.|
|prefetchCount|n.a.|Az alapul szolgáló alapértelmezett előzetes lekérési művelet `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.| 

> [!NOTE]
> A host.jsAzure Functions 1. x verzióban való hivatkozását lásd: [host.jsAzure functions 1. x-re](../articles/azure-functions/functions-host-json-v1.md).

