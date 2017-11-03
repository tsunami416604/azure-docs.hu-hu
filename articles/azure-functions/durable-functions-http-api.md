---
title: "HTTP API-k tartós funkciók – Azure"
description: "Útmutató az Azure Functions a tartós funkciók bővítmény HTTP API-k valósít meg."
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
ms.openlocfilehash: bb5361022e4c9693812753ae33df5aeb037b5aaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>HTTP API-k tartós funkciók (az Azure Functions)

A tartós feladatkiterjesztés tesz elérhetővé, HTTP API-k, amelyek segítségével a következő feladatokat:

* Vezénylési példány állapotának beolvasása.
* Esemény küldése a várakozási vezénylési példánya.
* Egy futó vezénylési példány leáll.

Összes ezen HTTP API-k kell közvetlenül a tartós feladatkiterjesztés által végrehajtott műveletek webhook. Nincsenek semmilyen feladatot, a függvény alkalmazás jellemző.

> [!NOTE]
> Ezek a műveletek is meghívhatók közvetlenül a példány felügyeleti API-k a a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály. További információkért lásd: [Példánykezelés](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>API-URL HTTP-felderítés

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály tesz elérhetővé a [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) egy HTTP-válasz a támogatott műveletek hivatkozásokat tartalmazó hasznos létrehozásához használt API. Íme egy példa a HTTP-funkció, amely bemutatja, hogyan használható az API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Ez a példa függvény hoz létre a következő JSON érkezett válasz adatait. Az összes mező adattípusa `string`.

| Mező             |Leírás                           |
|-------------------|--------------------------------------|
| id                |Az orchestration-példány azonosítója. |
| statusQueryGetUri |A vezénylési példány állapota URL-CÍMÉT. |
| sendEventPostUri  |A vezénylési példány "rhető event" URL-CÍMÉT. |
| terminatePostUri  |A "leáll" példány URL-CÍMÉT a vezénylési. |

Íme egy példa egy válasz:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> A webhook URL-címének formátuma eltérőek lehetnek attól függően, hogy az Azure Functions állomás verziójának. A fenti példában az Azure Functions 2.0 állomás van.

## <a name="async-operation-tracking"></a>Aszinkron művelet nyomon követése

A korábban említett HTTP-válasz célja segíteni a hosszan futó HTTP aszinkron tartós függvényekkel API-k végrehajtására. A rendszer más néven a *lekérdezési felhasználói mintát*. Az ügyfél-kiszolgáló folyamat a következőképpen működik:

1. Az ügyfél elindításához egy hosszú ideig tartó folyamatot például egy orchestrator függvény HTTP-kérelmet ad ki.
2. A célként megadott HTTP-eseményindító adja vissza egy HTTP 202 válasz egy `Location` fejlécet a `statusQueryGetUri` érték.
3. Az ügyfél kérdezi le az URL-címet a `Location` fejléc. Továbbra is fennáll, a HTTP 202 válaszok megjelenítéséhez a `Location` fejléc.
4. Ha a példány (vagy sikertelen teljesítése után), a következőben szereplő végpontnál: a `Location` fejléc 200-as HTTP adja vissza.

Ez a protokoll lehetővé teszi, hogy hosszú futású folyamatokat a külső ügyfeleket vagy, amely támogatja a HTTP-végponttal lekérdezés és a következő szolgáltatások összehangolása a `Location` fejléc. Az alapvető eleme a tartós funkciók HTTP API-k beépített.

> [!NOTE]
> Alapértelmezés szerint az összes HTTP-alapú művelet által biztosított [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) támogatja a szabványos aszinkron művelet szabályt. Ez lehetővé teszi a hosszan futó tartós függvény beágyazása a Logic Apps munkafolyamat részeként. További részleteket a Logic Apps támogatása aszinkron HTTP-minták megtalálhatók a [Azure Logic Apps munkafolyamat műveletek és eseményindítók dokumentáció](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>HTTP API-referencia

Minden HTTP API valósítják meg a bővítmény hajtsa végre a következő paraméterek. Az összes paraméter adattípusa `string`.

| Paraméter  | A paraméter típusa  | Leírás |
|------------|-----------------|-------------|
| instanceId | URL-CÍME             | Az orchestration-példány azonosítója. |
| taskHub    | Lekérdezési karakterlánc    | Neve a [feladat hub](durable-functions-task-hubs.md). Ha nincs megadva, az aktuális függvény app hub feladatnév feltételezi. |
| kapcsolat | Lekérdezési karakterlánc    | A **neve** a tárfiók kapcsolati karakterlánc. Ha nincs megadva, a függvény alkalmazás alapértelmezett kapcsolati karakterláncában feltételezzük. |
| systemKey  | Lekérdezési karakterlánc    | A hitelesítési kulcs a API meghívásához szükséges. |

`systemKey`az Azure Functions állomás által automatikusan létrehozott van egy engedélyezési kulcsot. Kifejezetten engedélyezi a hozzáférést a tartós feladatkiterjesztés API-k és azonos módon kezelhetők [más engedélyezési kulcsok](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). A legegyszerűbben úgy felderítése a `systemKey` értéke használatával a `CreateCheckStatusResponse` azt korábban említettük a API.

A következő néhány szakasz foglalkozik az egyes, a bővítmény által támogatott HTTP API-k, és adja meg a példák hogyan is használhatók.

### <a name="get-instance-status"></a>Példány állapotának beolvasása

A megadott vezénylési példány állapotát olvassa be.

#### <a name="request"></a>Kérés

A funkciók 1.0 a kérelem formátuma a következő:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

A funkciók 2.0 formátumban ugyanazokat paraméterek, de van egy némileg eltérő URL-előtagját:

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>Válasz

Néhány lehetséges kód állapotértékek adhatók vissza.

* **HTTP (OK) 200**: A megadott példány befejezett állapotban van.
* **HTTP (elfogadható) 202**: A megadott példány van folyamatban.
* **A HTTP 400 (hibás kérés)**: A megadott példány nem sikerült, vagy le lett állítva.
* **HTTP 404-es (nem található)**: A megadott példány nem létezik, vagy még nem kezdődött el.

A válasz hasznos a **200-as HTTP** és **HTTP 202** esetben egy JSON-objektum a következő mezőket.

| Mező           | Adattípus | Leírás |
|-----------------|-----------|-------------|
| runtimeStatus   | Karakterlánc    | A példány futtatási állapota. Értékek: *futtató*, *függőben lévő*, *sikertelen*, *visszavonva*, *kilépett*, *Befejeződött*. |
| Bemeneti           | JSON      | A példány inicializáló JSON-adatokat. |
| Kimeneti          | JSON      | A példány JSON-kimenetét. Ez a mező `null` Ha a példány nem kész állapotú. |
| createdTime     | Karakterlánc    | Az az idő, ahol a példány létrehozása. ISO 8601 notation kiterjesztett használja. |
| LastUpdatedTime | Karakterlánc    | Az az idő, ahol a példány utolsó megőrzött. ISO 8601 notation kiterjesztett használja. |

Íme egy példa válasz forgalma (az olvashatóság formázott):

```json
{
  "runtimeStatus": "Completed",
  "input": null,
  "output": [
    "Hello Tokyo!",
    "Hello Seattle!",
    "Hello London!"
  ],
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T18:30:30Z"
}
```

A **HTTP 202** válasz is tartalmaz egy **hely** válaszfejléc, amely az URL-CÍMÉRE hivatkozik a `statusQueryGetUri` azt korábban említettük a mező.

### <a name="raise-event"></a>Esemény előléptetése

Egy esemény értesítési üzenetet küld egy futó példány vezénylési.

#### <a name="request"></a>Kérés

A funkciók 1.0 a kérelem formátuma a következő:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

A funkciók 2.0 formátumban ugyanazokat paraméterek, de van egy némileg eltérő URL-előtagját:

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Kérelem ehhez az API paraméternek számít a korábban említett, valamint a következő egyedi paraméterek alapértelmezett beállítása.

| Mező       | A paraméter típusa  | Adatok tType | Leírás |
|-------------|-----------------|-----------|-------------|
| EventName   | URL-CÍME             | Karakterlánc    | Az eseményt, amely a célpéldány vezénylési vár a neve. |
| {tartalom}   | Tartalomkérelem | JSON      | A JSON-formátumú eseménytartalom. |

#### <a name="response"></a>Válasz

Néhány lehetséges kód állapotértékek adhatók vissza.

* **HTTP (elfogadható) 202**: feldolgozásra elfogadva a megemelt esemény.
* **A HTTP 400 (hibás kérés)**: A kérés tartalma nem típusú `application/json` , vagy nem érvényes JSON-adatokat.
* **HTTP 404-es (nem található)**: A megadott példány nem található.
* **A HTTP 410 (Gone)**: A megadott példány befejeződött vagy sikertelen volt, és nem tudja feldolgozni a megemelt eseményeket.

Íme egy példa egy kérelem által küldött a JSON-karakterláncban `"incr"` nevű esemény Várakozás példányhoz **művelet** (venni a [számláló](durable-functions-counter.md) minta):

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

A válaszok ehhez az API nem tartalmaznak minden tartalmat.

### <a name="terminate-instance"></a>Példány leáll

A vezénylési futó példány leállítása.

#### <a name="request"></a>Kérés

A funkciók 1.0 a kérelem formátuma a következő:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

A funkciók 2.0 formátumban ugyanazokat paraméterek, de van egy némileg eltérő URL-előtagját:

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Kérelem ehhez az API paraméternek számít a korábban említett, valamint a következő egyedi paraméter alapértelmezett beállítása.

| Mező       | A paraméter típusa  | Adattípus | Leírás |
|-------------|-----------------|-----------|-------------|
| OK      | Lekérdezési karakterlánc    | Karakterlánc    | Választható. Leállítja a vezénylési példány okát. |

#### <a name="response"></a>Válasz

Néhány lehetséges kód állapotértékek adhatók vissza.

* **HTTP (elfogadható) 202**: A megszakítási kérés elfogadva feldolgozásra.
* **HTTP 404-es (nem található)**: A megadott példány nem található.
* **A HTTP 410 (Gone)**: A megadott példány már befejeződött vagy nem sikerült.

Íme egy példa egy kérelem egy futó példány leállítása és azok egy indokkal **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

A válaszok ehhez az API nem tartalmaznak minden tartalmat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg a hibák kezelésének módját](durable-functions-error-handling.md)
