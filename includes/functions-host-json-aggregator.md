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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76279309"
---
Megadja, hogy hány függvénymeghívás összesítve az [Application Insights metrikák számításakor.](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator) 

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
|batchSize|1000|Az összesítésre vonatkozó kérelmek maximális száma.| 
|kiürítés|00:00:30|Az összesítésre vonatkozó maximális időtartam.| 

A függvénymeghívások összesítése a két korlát közül az első elérésekor lesz összesítve.
