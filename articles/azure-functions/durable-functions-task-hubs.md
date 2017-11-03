---
title: "A tartós funkciók - Azure feladat hubok"
description: "Ismerje meg, milyen feladat hubot megtalálható-e az Azure Functions tartós funkciók bővítményét. Megtudhatja, hogyan konfigurálhatja a feladat hubs konfigurálása."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: b241bad7b0060551eba5e78efbb1b729bf5d0098
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>A tartós függvények (az Azure Functions) feladat hubok

A *feladat hub* a [tartós funkciók](durable-functions-overview.md) álló üzenettípusok összehangolását használt Azure Storage-erőforrások logikai tárolója. Az orchestrator- és tevékenységkezelési funkciók tevékenység egy központban tartoznak, csak is kapcsolatba egymással.

Minden függvény alkalmazás rendelkezik egy külön feladat központot. Ha több függvény alkalmazást egy tárfiókot, a tárfiók a több feladat hubok tartalmazza. A következő ábra szemlélteti a megosztott és dedikált tárfiókokban függvény alkalmazásonkénti egy feladat központ.

![Diagram ábrázoló megosztott, és a storage-fiókok dedikált.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-erőforrások

Egy feladat központ a következő tároló-erőforrások áll: 

* Egy vagy több vezérlő várólisták.
* Egy munkaelem várólistát.
* Egy előzménytábla.
* Egy tároló tartalmazó egy vagy több bérleti blobot.

Minden forrás jönnek létre automatikusan az alapértelmezett Azure Storage-fiók az orchestrator vagy tevékenység függvények futtatása vagy ütemezett. A [teljesítmény és méretezhetőség](durable-functions-perf-and-scale.md) a cikk azt ismerteti, hogyan használják ezeket az erőforrásokat.

## <a name="task-hub-names"></a>Feladatnév hub

Feladat hubok azonosítják a nevet, amely deklarálva van a *host.json* fájl, a következő példában látható módon:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Feladatnév hub kell betűvel kezdődhet, és csak betűkből és számokból állhat. Ha nincs megadva, az alapértelmezett név az **DurableFunctionsHub**.

> [!NOTE]
> A név nem egy feladatütemezési központi másik kódjába, ha egy megosztott tárfiókot több feladat hubok. Ha egy megosztott tárfiókot megosztása több függvény alkalmazást, be kell állítania a minden feladat központ eltérő nevet a *host.json* fájlokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan legyen kezelve versioning](durable-functions-versioning.md)
