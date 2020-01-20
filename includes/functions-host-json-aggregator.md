---
title: fájl belefoglalása
description: fájl belefoglalása
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
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

|Tulajdonság |Alapértelmezett  | Leírás |
|---------|---------|---------| 
|batchSize|1000|Az összesíteni kívánt kérelmek maximális száma.| 
|flushTimeout|00:00:30|Az összesíteni kívánt időszak maximális időtartama.| 

A függvény meghívásait a rendszer összesíti, ha eléri a két korlát első számát.
