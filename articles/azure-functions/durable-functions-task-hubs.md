---
title: A tartós funkciók - Azure feladat hubok
description: Ismerje meg, milyen feladat hubot megtalálható-e az Azure Functions tartós funkciók bővítményét. Megtudhatja, hogyan konfigurálhatja a feladat hubs konfigurálása.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 563667684accf8b434052cd412bf6e93c77ea63a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762326"
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
* Egy példány tábla.
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan legyen kezelve versioning](durable-functions-versioning.md)
