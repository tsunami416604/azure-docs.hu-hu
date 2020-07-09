---
title: fájlbefoglalás
description: fájlbefoglalás
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76279309"
---
Azt határozza meg, hogy hány függvényt kell összesíteni a [Application Insights metrikáinak kiszámításakor](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Tulajdonság |Alapértelmezett  | Description |
|---------|---------|---------| 
|batchSize|1000|Az összesíteni kívánt kérelmek maximális száma.| 
|flushTimeout|00:00:30|Az összesíteni kívánt időszak maximális időtartama.| 

A függvény meghívásait a rendszer összesíti, ha eléri a két korlát első számát.
