---
title: Az Azure Cosmos DB-eseményindító kapcsolat-házirend
description: Ismerje meg, hogyan lehet az Azure Cosmos DB-eseményindító által használt kapcsolat-házirend konfigurálása
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755331"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Az Azure Cosmos DB-eseményindító által használt kapcsolat szabályzat konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a kapcsolódási szabályzat az Azure Cosmos DB-eseményindító használata az Azure Cosmos-fiókhoz való csatlakozáshoz.

## <a name="why-is-the-connection-policy-important"></a>Miért fontos a kapcsolódási szabályzat?

Nincsenek két csatlakozási módot - közvetlen és átjáró mód. E kapcsolat módokkal kapcsolatos további tudnivalókért tekintse meg a [teljesítménnyel kapcsolatos tippek](./performance-tips.md#networking) cikk. Alapértelmezés szerint **átjáró** az Azure Cosmos DB-eseményindító az összes kapcsolat létesítésére szolgál. Azt azonban nem feltétlenül a legjobb választás a teljesítmény-központú forgatókönyvek.

## <a name="changing-the-connection-mode-and-protocol"></a>A kapcsolati módot és a protokoll módosítása

Két fő konfigurációs beállítások konfigurálhatók a kapcsolat ügyfélházirend – a **kapcsolati módot** és a **csatlakozási protokoll**. Módosíthatja az alapértelmezett csatlakozási mód és az Azure Cosmos DB-eseményindító és az összes által használt protokoll a [Azure Cosmos DB-kötéseket](../azure-functions/functions-bindings-cosmosdb-v2.md#output)). Módosítsa az alapértelmezett beállításokat, keresse meg kell a `host.json` az Azure Functions-projektet vagy az Azure Functions fájlt, és adja hozzá a következő [további beállítás](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Ahol `connectionMode` rendelkeznie kell a megfelelő csatlakozási mód (közvetlenül vagy átjárókiszolgáló) és `protocol` a kívánt kapcsolat protocol (Tcp- vagy Https). 

Ha az Azure Functions-projektet az Azure Functions V1-futtatókörnyezetben működik, a konfigurációs van különbség a kis mértékben neve, használjon `documentDB` helyett `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Az Azure Functions Használatalapú csomaghoz kötődő üzemeltetés csomag használatakor minden példány esetében a kibocsátott, amely akkor is fenntartható a szoftvercsatorna-kapcsolatok. Az közvetlen használatakor / TCP mód tervezés több kapcsolat jön létre, és is eléri a [Használatalapú csomagban korlát](../azure-functions/manage-connections.md#connection-limit), ebben az esetben átjáró mód használatára, vagy az Azure Functions futtatása [App Service mód](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>További lépések

* [Az Azure Functions kapcsolat korlátai](../azure-functions/manage-connections.md#connection-limit)
* [Az Azure Cosmos DB teljesítménnyel kapcsolatos tippek](./performance-tips.md)
* [Kódminták](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
