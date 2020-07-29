---
title: Azure Functions trigger Cosmos DB-kapcsolatok házirendjéhez
description: Megtudhatja, hogyan konfigurálhatja a Azure Functions trigger által használt kapcsolódási szabályzatot Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: df30a0ddca58e6d7bd74184fa7287df6818cfc37
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117149"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>A Azure Functions trigger által használt kapcsolódási szabályzat konfigurálása Cosmos DBhoz

Ez a cikk azt ismerteti, hogyan konfigurálhatja a kapcsolati házirendet, ha a Azure Functions triggert használja Cosmos DB az Azure Cosmos-fiókhoz való csatlakozáshoz.

## <a name="why-is-the-connection-policy-important"></a>Miért fontos a kapcsolatkérelem-házirend?

Két kapcsolattípus van – közvetlen mód és átjáró mód. Ha többet szeretne megtudni ezekről a kapcsolódási módokról, tekintse meg a [teljesítménnyel kapcsolatos tippeket](./performance-tips.md#networking) ismertető cikket. Alapértelmezés szerint az **átjáró** a Cosmos db Azure functions trigger összes kapcsolatának létrehozására szolgál. Előfordulhat azonban, hogy nem a legjobb megoldás a teljesítmény-vezérelt forgatókönyvek esetében.

## <a name="changing-the-connection-mode-and-protocol"></a>A kapcsolati mód és a protokoll módosítása

Az ügyfélkapcsolati házirend konfigurálásához két kulcsfontosságú konfigurációs beállítás érhető el – a **kapcsolati mód** és a **kapcsolati protokoll**. A Azure Functions trigger által használt alapértelmezett kapcsolati mód és protokoll a Cosmos DB és az összes [Azure Cosmos db kötés](../azure-functions/functions-bindings-cosmosdb-v2-output.md)esetében módosítható. Az alapértelmezett beállítások módosításához meg kell keresnie a fájlt a `host.json` Azure functions projektben vagy Azure functions alkalmazásban, és hozzá kell adnia a következő [extra beállítást](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Ahol `connectionMode` a kötelező kapcsolati mód (közvetlen vagy átjáró) és `protocol` a kívánt kapcsolati protokoll (TCP vagy https) szükséges. 

Ha a Azure Functions-projekt a Azure Functions v1 futtatókörnyezettel dolgozik, a konfigurációnak kisebb a neve, akkor a következők `documentDB` helyett érdemes használni `cosmosDB` :

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Azure Functions használati terv üzemeltetési tervének használatakor az egyes példányok esetében a szoftvercsatorna-kapcsolatok mennyisége korlátozva lehet. A közvetlen/TCP mód használata esetén több kapcsolat jön létre, és elérheti a használati [terv korlátját](../azure-functions/manage-connections.md#connection-limit). ebben az esetben használhatja az átjáró üzemmódot, vagy [app Service módban](../azure-functions/functions-scale.md#app-service-plan)futtathatja a Azure functions.

## <a name="next-steps"></a>További lépések

* [A Azure Functions a kapcsolatok korlátai](../azure-functions/manage-connections.md#connection-limit)
* [Teljesítménnyel kapcsolatos tippek Azure Cosmos DB](./performance-tips.md)
* [Kódminták](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
