---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4d0a61bf7f4ee9e441a49e21ce6535dc3bff0edc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608216"
---
Megadja, hogy hány függvény meghívásához összesített mikor [kiszámítása a metrikákat az Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

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
|batchSize|1000|Kérelmek maximális számát az összesítést.| 
|flushTimeout|00:00:30|Maximális idő időszak alatt, és összesítést.| 

Összesítjük függvény meghívásához, ha a kettő közül az első korlátozza elérésekor.