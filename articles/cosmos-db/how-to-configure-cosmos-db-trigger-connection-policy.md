---
title: Az Azure Functions eseményindító a Cosmos DB kapcsolati szabályzatához
description: Ismerje meg, hogyan konfigurálhatja az Azure Functions által használt kapcsolati szabályzatot a Cosmos DB-hez
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 953121a9a15d4fef56d381e3aab85329fadacce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604965"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Az Azure Functions által használt kapcsolati szabályzat konfigurálása a Cosmos DB-hez

Ez a cikk ismerteti, hogyan konfigurálhatja a kapcsolatszabályzatot, ha az Azure Functions eseményindító cosmos DB az Azure Cosmos-fiókhoz való csatlakozáshoz.

## <a name="why-is-the-connection-policy-important"></a>Miért fontos a kapcsolatházirend?

Két csatlakozási mód van - Közvetlen és Átjáró mód. A csatlakozási módokról a [teljesítménytippekről](./performance-tips.md#networking) szóló cikkben olvashat bővebben. Alapértelmezés szerint a **Gateway** az Azure Functions eseményindító a Cosmos DB összes kapcsolatlétrehozásához. Azonban előfordulhat, hogy nem a legjobb megoldás a teljesítmény-vezérelt forgatókönyvek.

## <a name="changing-the-connection-mode-and-protocol"></a>A kapcsolati mód és a protokoll módosítása

Az ügyfélkapcsolati házirend konfigurálásához két kulcskonfigurációs beállítás áll rendelkezésre: a **kapcsolatmód** és a **kapcsolatprotokoll.** Módosíthatja az Azure Functions eseményindító jap-ja által használt alapértelmezett kapcsolati módot és protokollt a Cosmos DB és az összes [Azure Cosmos DB-kötéshez).](../azure-functions/functions-bindings-cosmosdb-v2-output.md) Az alapértelmezett beállítások módosításához meg `host.json` kell keresnie a fájlt az Azure Functions projektben vagy az Azure Functions alkalmazásban, és hozzá kell adnia a következő [további beállítást:](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Ahol `connectionMode` a kívánt kapcsolati móddal (Közvetlen vagy átjáró) és `protocol` a kívánt kapcsolatprotokollal (Tcp vagy Https) kell rendelkeznie? 

Ha az Azure Functions projekt az Azure Functions V1 futásidejű használatával működik, `documentDB` a `cosmosDB`konfiguráció névkülönbséggel rendelkezik, a következők helyett kell használnia:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Az Azure Functions Consumption Plan Hosting csomaggal való munka során minden példány rendelkezik egy korláttal a karbantartásra alkalmas szoftvercsatorna-kapcsolatok mennyiségére vonatkozóan. Közvetlen/TCP módban végzett munka során a tervezés szerint további kapcsolatok jönnek létre, és elérik a [felhasználási terv korlátját,](../azure-functions/manage-connections.md#connection-limit)amely esetben használhatja az Átjáró módot, vagy futtathatja az Azure Functions-t [App Service módban.](../azure-functions/functions-scale.md#app-service-plan)

## <a name="next-steps"></a>További lépések

* [Kapcsolati korlátok az Azure Functionsben](../azure-functions/manage-connections.md#connection-limit)
* [Az Azure Cosmos DB teljesítménytippjei](./performance-tips.md)
* [Kódminták](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
