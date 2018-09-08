---
title: Durable Functions – Azure HTTP API-k
description: Ismerje meg, hogy a HTTP API-k megvalósítása a Durable Functions bővítmény az Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 3fa4f230f5e2d15e815c47792c3955aa93d29fc4
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094739"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) HTTP API-k

A tartós feladat bővítmény tesz elérhetővé, HTTP API-k, amelyek segítségével a következő feladatokat:

* Beolvasni a vezénylési példány állapotát.
* Küldünk egy eseményt egy várakozási vezénylési példányt.
* Egy futó vezénylési példány leáll.


Minden HTTP API-k használ, amely közvetlenül kezeli a tartós feladat bővítmény használandó webhookművelet. Ezek nem konkrétan a bármely függvény a függvényalkalmazásban.

> [!NOTE]
> Ezek a műveletek is meghívhatók közvetlenül a példány felügyeleti API-k az a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály. További információkért lásd: [példány felügyeleti](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>HTTP API URL-Címének felderítése

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály közzéteszi egy [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API, amely egy HTTP-válasz tartalma, a támogatott műveleteket mutató hivatkozásokat tartalmazó létrehozásához használható. Íme egy példa azt mutatja be, hogyan használható az API HTTP-eseményindító függvény:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Ez a példa a függvény a következő JSON-adatokat eredményez. Az összes mező adattípusa `string`.

| Mező             |Leírás                           |
|-------------------|--------------------------------------|
| id                |Az orchestration-példány azonosítója. |
| statusQueryGetUri |Az orchestration-példány állapota URL-címe |
| sendEventPostUri  |A vezénylési példány "raise esemény" URL-címe |
| terminatePostUri  |A "le" példány URL-címét a vezénylési. |

Íme egy példa választ:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> A webhook URL-címek formátumát az Azure Functions állomás verziójának függően eltérhet. A fenti példában az Azure Functions 2.0 állomás van.

## <a name="async-operation-tracking"></a>Az aszinkron művelet nyomon követése

A korábban említett HTTP-válasz célja hosszú ideig futó HTTP aszinkron Durable Functions-API-k megvalósításához. Ez az iommu-t a *lekérdezési felhasználói mintán*. Az ügyfél-kiszolgáló a folyamat a következőképpen történik:

1. Az ügyfél kiad egy HTTP-kérelem egy hosszú ideig futó folyamat elindításához például egy orchestrator-függvényt.
2. A cél a HTTP-eseményindító a HTTP-202 választ adja vissza egy `Location` fejléc a `statusQueryGetUri` értéket.
3. Az ügyfél kérdezze le az URL-cím a `Location` fejléc. Tekintse meg a HTTP-202 válaszoknál, amelyeknél a továbbra is egy `Location` fejléc.
4. Ha a példány befejeződik (vagy sikertelen), a végpontot a `Location` fejlécet ad vissza 200-as HTTP.

Ez a protokoll lehetővé teszi a hosszú futású folyamatok, a külső ügyfelek vagy a lekérdezés egy HTTP-végpontot, és a következő támogató szolgáltatások összehangolása a `Location` fejléc. Az alapvető darab a Durable Functions HTTP API-k beépített.

> [!NOTE]
> Alapértelmezés szerint az összes HTTP-alapú művelet által biztosított [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) támogatja a standard szintű művelet aszinkron minta. Ez a funkció lehetővé teszi egy hosszú ideig futó tartós függvény beágyazásához a Logic Apps-munkafolyamat részeként. Aszinkron HTTP-minták találhat további részleteket a Logic Apps támogatása a [Azure Logic Apps munkafolyamat-műveletek és eseményindítók dokumentáció](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>HTTP API-referencia

Minden HTTP API-k megvalósítva a végezze el a bővítményt a következő paramétereket. Az összes paraméter adattípusa `string`.

| Paraméter  | Paraméter típusa  | Leírás |
|------------|-----------------|-------------|
| instanceId | URL-cím             | Az orchestration-példány azonosítója. |
| taskHub    | Lekérdezési sztring    | Neve a [feladat hub](durable-functions-task-hubs.md). Ha nincs megadva, a rendszer feltételezi a jelenlegi függvényalkalmazás feladat eseményközpont neve. |
| kapcsolat | Lekérdezési sztring    | A **neve** a tárfiók kapcsolati karakterlánca. Ha nincs megadva, a rendszer feltételezi a függvényalkalmazás alapértelmezett kapcsolati karakterláncára. |
| systemKey  | Lekérdezési sztring    | A hitelesítési kulcs az API meghívásához szükséges. |
| showHistory| Lekérdezési sztring    | Nem kötelező paraméter. Ha beállítása `true`, a vezénylési futtatási előzményei fog szerepelni a válasz hasznos adatban.| 
| showHistoryOutput| Lekérdezési sztring    | Nem kötelező paraméter. Ha beállítása `true`, a tevékenység kimenete fog szerepelni a vezénylési futtatási előzményei.| 

`systemKey` az Azure Functions-állomás által automatikusan létrehozott van egy engedélyezési kulcsot. Kifejezetten hozzáférést biztosít a tartós feladat bővítmény API-k és azonos módon felügyelhetők [más hitelesítési kulcsok](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Fedezze fel a legegyszerűbb módszer a `systemKey` érték használatával a `CreateCheckStatusResponse` API azt korábban említettük.

A következő néhány szakaszban terjed ki az adott HTTP API-k a bővítmény által támogatott, és adja meg a példát, hogyan lehet használni őket.

### <a name="get-instance-status"></a>Példány állapotának beolvasása

Egy megadott vezénylési példány állapotát olvassa be.

#### <a name="request"></a>Kérés

A Functions 1.0 a kérés formátuma a következőképpen történik:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

A Functions 2.0 formátum ugyanazokat paraméterekkel rendelkezik, de egy némileg különböző URL-cím előtagja:

```http
GET /runtime/webhooks/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Válasz

Számos lehetséges állapota kódértékek adhatók vissza.

* **HTTP 200 (OK)**: A megadott példány befejezett állapotban van.
* **202 (elfogadva) HTTP**: A megadott példány folyamatban van.
* **HTTP 400 (hibás kérés)**: A megadott példány nem sikerült, vagy meg lett szakítva.
* **A HTTP 404 (nem található)**: A megadott példány nem létezik, vagy nem kezdődött.

A válasz-adattartalomra vonatkozó a **HTTP 200** és **HTTP 202** esetben egy JSON-objektum a következő mezőket:

| Mező           | Adattípus | Leírás |
|-----------------|-----------|-------------|
| runtimeStatus   | sztring    | A példány futásidejű állapotát. Értékek: *futó*, *függőben lévő*, *sikertelen*, *megszakított*, *kilépett*, *Befejeződött*. |
| bemenet           | JSON      | A példány inicializálásához használt JSON-adatokat. |
| customStatus    | JSON      | Egyéni vezénylési állapot használt JSON-adatokat. Ez a mező `null` Ha nincs beállítva. |
| output          | JSON      | A példány JSON-kimenetét. Ez a mező `null` , ha a példány nem egy befejezett állapotban van. |
| createdTime     | sztring    | Az az idő, amikor a példány létrehozása. ISO 8601 jelöléssel kiterjesztett használ. |
| lastUpdatedTime | sztring    | Az az időpont, amikor a példány utolsó megőrzött. ISO 8601 jelöléssel kiterjesztett használ. |
| historyEvents   | JSON      | A vezénylési végrehajtási előzményeket tartalmazó JSON-tömböt. Ez a mező `null` , kivéve, ha a `showHistory` lekérdezési karakterlánc paraméterének értéke `true`.  | 

Íme egy példa válasz a tartalmat, beleértve a vezénylési futtatási előzmények és tevékenység kimenetek (az olvashatóság érdekében formázva):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

A **HTTP 202** válasz is tartalmaz egy **hely** válaszfejléc, amely az URL-CÍMÉRE hivatkozik a `statusQueryGetUri` azt korábban említettük a mező.

### <a name="get-all-instances-status"></a>Az összes példány állapotának beolvasása

Minden példány állapota is lekérdezheti. Távolítsa el a `instanceId` a "Get-példány állapota" kérelemből. A paraméterek ugyanazok, mint a "Get példány állapota." 

#### <a name="request"></a>Kérés

A Functions 1.0 a kérés formátuma a következőképpen történik:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

A Functions 2.0 formátum ugyanazokat egy némileg különböző URL-előtagot azonban paraméterekkel rendelkezik: 

```http
GET /runtime/webhooks/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>Válasz

Íme egy példa a válasz hasznos adatforgalom elemzéséhez, beleértve a vezénylési állapot (az olvashatóság érdekében formázva):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Ez a művelet teljesítményigényesek lehetnek Azure tárolási i/o-tekintetében van-e nagy mennyiségű a példányok táblában lévő sorokat. Példány táblán további részletek találhatók a [teljesítményt és méretet (az Azure Functions) Durable Functions](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-perf-and-scale#instances-table) dokumentációját.
> 

### <a name="raise-event"></a>Esemény előléptetése

Egy esemény értesítési üzenetet küld egy futó vezénylési példány.

#### <a name="request"></a>Kérés

A Functions 1.0 a kérés formátuma a következőképpen történik:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

A Functions 2.0 formátum ugyanazokat paraméterekkel rendelkezik, de egy némileg különböző URL-cím előtagja:

```http
POST /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

A kérelem paraméterekkel az API az alapértelmezett készlet korábban már említettük, valamint az alábbi egyedi paramétereket tartalmazza:

| Mező       | Paraméter típusa  | Adatok tType | Leírás |
|-------------|-----------------|-----------|-------------|
| EventName   | URL-cím             | sztring    | Az eseményt, amely a célpéldány vezénylési vár a neve. |
| {tartalom}   | Tartalomkérelem | JSON      | A JSON-formátumú eseménytartalom. |

#### <a name="response"></a>Válasz

Számos lehetséges állapota kódértékek adhatók vissza.

* **202 (elfogadva) HTTP**: A bekövetkezett esemény lett fogadva feldolgozásra.
* **HTTP 400 (hibás kérés)**: A kérelem tartalma nem volt típusú `application/json` vagy nem érvényes JSON volt.
* **A HTTP 404 (nem található)**: A megadott példány nem található.
* **A HTTP 410 (Gone)**: A megadott példány befejeződött vagy meghiúsult, és nem tudja feldolgozni a bekövetkezett eseményeket.

Íme egy példa kérelmet, amely a JSON-karakterlánc küld `"incr"` egy példánnyal, hogy egy adott esemény nevű **művelet**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Az API-hoz a válaszok nem tartalmaznak minden tartalom.

### <a name="terminate-instance"></a>Példány leállítása

Egy futó vezénylési példány leáll.

#### <a name="request"></a>Kérés

A Functions 1.0 a kérés formátuma a következőképpen történik:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

A Functions 2.0 formátum ugyanazokat paraméterekkel rendelkezik, de egy némileg különböző URL-cím előtagja:

```http
DELETE /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Kérelem, az API paraméternek számít a korábban már említettük, valamint a következő egyedi paraméter alapértelmezett beállítása.

| Mező       | Paraméter típusa  | Adattípus | Leírás |
|-------------|-----------------|-----------|-------------|
| reason      | Lekérdezési sztring    | sztring    | Választható. Az orchestration-példány leállításához okát. |

#### <a name="response"></a>Válasz

Számos lehetséges állapota kódértékek adhatók vissza.

* **202 (elfogadva) HTTP**: A megszakítási kérés elfogadva feldolgozásra.
* **A HTTP 404 (nem található)**: A megadott példány nem található.
* **A HTTP 410 (Gone)**: A megadott példány befejeződött vagy meghiúsult.

Íme egy példa kérelmet, amely egy futó példány leáll, és adja meg az okot **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-hoz a válaszok nem tartalmaznak minden tartalom.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan hibáinak kezelése](durable-functions-error-handling.md)
