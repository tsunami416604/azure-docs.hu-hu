---
title: Azure Functions trigger Cosmos DB-kapcsolatok házirendjéhez
description: Megtudhatja, hogyan konfigurálhatja a Azure Functions trigger által használt kapcsolódási szabályzatot Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 6e4c50e5dcc35450463d02bbed040754ea778e70
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093620"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>A Azure Functions trigger által használt kapcsolódási szabályzat konfigurálása Cosmos DBhoz

Ez a cikk azt ismerteti, hogyan konfigurálhatja a kapcsolati házirendet, ha a Azure Functions triggert használja Cosmos DB az Azure Cosmos-fiókhoz való csatlakozáshoz.

## <a name="why-is-the-connection-policy-important"></a>Miért fontos a kapcsolatkérelem-házirend?

Két kapcsolattípus van – közvetlen mód és átjáró mód. Ha többet szeretne megtudni ezekről a kapcsolódási módokról, tekintse meg a [teljesítménnyel kapcsolatos tippeket](./performance-tips.md#networking) ismertető cikket. Alapértelmezés szerint az **átjáró** a Cosmos db Azure functions trigger összes kapcsolatának létrehozására szolgál. Előfordulhat azonban, hogy nem a legjobb megoldás a teljesítmény-vezérelt forgatókönyvek esetében.

## <a name="changing-the-connection-mode-and-protocol"></a>A kapcsolati mód és a protokoll módosítása

Az ügyfélkapcsolati házirend konfigurálásához két kulcsfontosságú konfigurációs beállítás érhető el – a **kapcsolati mód** és a **kapcsolati protokoll**. A Azure Functions trigger által használt alapértelmezett kapcsolati mód és protokoll a Cosmos DB és az összes [Azure Cosmos db kötés](../azure-functions/functions-bindings-cosmosdb-v2.md#output)esetében módosítható. Az alapértelmezett beállítások módosításához meg kell keresnie a `host.json` fájlt a Azure functions projektben vagy Azure functions alkalmazásban, és hozzá kell adnia a következő [extra beállítást](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Ahol `connectionMode` a kötelező kapcsolati mód (közvetlen vagy átjáró) és `protocol` a kívánt kapcsolati protokoll (TCP vagy https) szükséges. 

Ha a Azure functions-projekt a Azure functions v1 futtatókörnyezettel dolgozik, a konfigurációnak kisebb a neve, akkor a következők `documentDB` `cosmosDB`helyett érdemes használni:

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
