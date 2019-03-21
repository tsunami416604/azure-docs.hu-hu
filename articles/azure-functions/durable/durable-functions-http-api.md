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
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 5bd977826f489ca8452432babe6126b8553450fb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137708"
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

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Ezek a függvények példa elő a következő JSON-adatokat. Az összes mező adattípusa `string`.

| Mező                   |Leírás                           |
|-------------------------|--------------------------------------|
| **`id`**                |Az orchestration-példány azonosítója. |
| **`statusQueryGetUri`** |Az orchestration-példány állapota URL-címe |
| **`sendEventPostUri`**  |A vezénylési példány "raise esemény" URL-címe |
| **`terminatePostUri`**  |A "le" példány URL-címét a vezénylési. |
| **`rewindPostUri`**     |A vezénylési példány "visszatekerés" URL-címe |

Íme egy példa választ:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> A webhook URL-címek formátumát az Azure Functions állomás verziójának függően eltérhet. A fenti példában az Azure Functions 2.x állomás van.

## <a name="async-operation-tracking"></a>Az aszinkron művelet nyomon követése

A korábban említett HTTP-válasz célja hosszú ideig futó HTTP aszinkron Durable Functions-API-k megvalósításához. Ez az iommu-t a *lekérdezési felhasználói mintán*. Az ügyfél-kiszolgáló a folyamat a következőképpen történik:

1. Az ügyfél kiad egy HTTP-kérelem egy hosszú ideig futó folyamat elindításához például egy orchestrator-függvényt.
2. A cél a HTTP-eseményindító a HTTP-202 választ adja vissza egy `Location` fejléc a `statusQueryGetUri` értéket.
3. Az ügyfél kérdezze le az URL-cím a `Location` fejléc. Tekintse meg a HTTP-202 válaszoknál, amelyeknél a továbbra is egy `Location` fejléc.
4. Ha a példány befejeződik (vagy sikertelen), a végpontot a `Location` fejlécet ad vissza 200-as HTTP.

Ez a protokoll lehetővé teszi a hosszú futású folyamatok, a külső ügyfelek vagy a lekérdezés egy HTTP-végpontot, és a következő támogató szolgáltatások összehangolása a `Location` fejléc. Az alapvető darab a Durable Functions HTTP API-k beépített.

> [!NOTE]
> Alapértelmezés szerint az összes HTTP-alapú művelet által biztosított [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) támogatja a standard szintű művelet aszinkron minta. Ez a funkció lehetővé teszi egy hosszú ideig futó tartós függvény beágyazásához a Logic Apps-munkafolyamat részeként. Aszinkron HTTP-minták találhat további részleteket a Logic Apps támogatása a [Azure Logic Apps munkafolyamat-műveletek és eseményindítók dokumentáció](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>HTTP API-referencia

Minden HTTP API-k megvalósítva a végezze el a bővítményt a következő paramétereket. Az összes paraméter adattípusa `string`.

| Paraméter        | Paraméter típusa  | Leírás |
|------------------|-----------------|-------------|
| **`taskHub`**    | Lekérdezési sztring    | Neve a [feladat hub](durable-functions-task-hubs.md). Ha nincs megadva, a rendszer feltételezi a jelenlegi függvényalkalmazás feladat eseményközpont neve. |
| **`connection`** | Lekérdezési sztring    | A **neve** a tárfiók kapcsolati karakterlánca. Ha nincs megadva, a rendszer feltételezi a függvényalkalmazás alapértelmezett kapcsolati karakterláncára. |
| **`systemKey`**  | Lekérdezési sztring    | A hitelesítési kulcs az API meghívásához szükséges. |

`systemKey` az Azure Functions-állomás által automatikusan létrehozott van egy engedélyezési kulcsot. Kifejezetten hozzáférést biztosít a tartós feladat bővítmény API-k és azonos módon felügyelhetők [más hitelesítési kulcsok](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Fedezze fel a legegyszerűbb módszer a `systemKey` érték használatával a `CreateCheckStatusResponse` API azt korábban említettük.

A következő néhány szakaszban terjed ki az adott HTTP API-k a bővítmény által támogatott, és adja meg a példát, hogyan lehet használni őket.

### <a name="get-instance-status"></a>Példány állapotának beolvasása

Egy megadott vezénylési példány állapotát olvassa be.

#### <a name="request"></a>Kérés

A verzió a Functions-futtatókörnyezet, a kérelem 1.x-es (több sorok jelennek meg az átláthatóság érdekében) a következők szerint van formázva:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

A verzió 2.x verzióját a Functions-futtatókörnyezet, az URL-formátum ugyanazokat paraméterekkel rendelkezik, de egy kis mértékben eltérő előtagot:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

A kérelem paraméterekkel az API az alapértelmezett készlet korábban már említettük, valamint az alábbi egyedi paramétereket tartalmazza:

| Mező                   | Paraméter típusa  | Leírás |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL-cím             | Az orchestration-példány azonosítója. |
| **`showInput`**         | Lekérdezési sztring    | Nem kötelező paraméter. Ha beállítása `false`, a függvény bemeneti nem fog szerepelni a válasz hasznos adatban.|
| **`showHistory`**       | Lekérdezési sztring    | Nem kötelező paraméter. Ha beállítása `true`, a vezénylési futtatási előzményei fog szerepelni a válasz hasznos adatban.|
| **`showHistoryOutput`** | Lekérdezési sztring    | Nem kötelező paraméter. Ha beállítása `true`, a függvény kimenete fog szerepelni a vezénylési futtatási előzményei.|
| **`createdTimeFrom`**   | Lekérdezési sztring    | Nem kötelező paraméter. Megadása esetén a visszaadott, vagy a megadott időbélyegnél ISO8601 létrehozott példányok listájának szűrése.|
| **`createdTimeTo`**     | Lekérdezési sztring    | Nem kötelező paraméter. Megadása esetén a visszaadott, vagy a megadott időbélyegnél ISO8601 létrehozott példányok listájának szűrése.|
| **`runtimeStatus`**     | Lekérdezési sztring    | Nem kötelező paraméter. Megadása esetén a visszaadott-példányok listájának alapján szűri a futásidejű állapot. Lehetséges futásidejű állapot értékek listáját, olvassa el a [példányok lekérdezése](durable-functions-instance-management.md) témakör. |

#### <a name="response"></a>Válasz

Számos lehetséges állapota kódértékek adhatók vissza.

* **HTTP 200 (OK)**: A megadott példány egy befejezett állapotban van.
* **202 (elfogadva) HTTP**: A megadott példány folyamatban van.
* **HTTP 400 (hibás kérés)**: A megadott példány nem sikerült, vagy meg lett szakítva.
* **A HTTP 404 (nem található)**: A megadott példány nem létezik, vagy nem kezdődött meg.
* **A HTTP 500-as (belső kiszolgálóhiba)**: A megadott példány nem kezelt kivétel miatt nem sikerült.

A válasz-adattartalomra vonatkozó a **HTTP 200** és **HTTP 202** esetben egy JSON-objektum a következő mezőket:

| Mező                 | Adattípus | Leírás |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | sztring    | A példány futásidejű állapotát. Értékek: *futó*, *függőben lévő*, *sikertelen*, *megszakított*, *kilépett*, *Befejeződött*. |
| **`input`**           | JSON      | A példány inicializálásához használt JSON-adatokat. Ez a mező `null` Ha a `showInput` lekérdezési karakterlánc paraméterének értéke `false`.|
| **`customStatus`**    | JSON      | Egyéni vezénylési állapot használt JSON-adatokat. Ez a mező `null` Ha nincs beállítva. |
| **`output`**          | JSON      | A példány JSON-kimenetét. Ez a mező `null` , ha a példány nem egy befejezett állapotban van. |
| **`createdTime`**     | sztring    | Az az idő, amikor a példány létrehozása. ISO 8601 jelöléssel kiterjesztett használ. |
| **`lastUpdatedTime`** | sztring    | Az az időpont, amikor a példány utolsó megőrzött. ISO 8601 jelöléssel kiterjesztett használ. |
| **`historyEvents`**   | JSON      | A vezénylési végrehajtási előzményeket tartalmazó JSON-tömböt. Ez a mező `null` , kivéve, ha a `showHistory` lekérdezési karakterlánc paraméterének értéke `true`. |

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

Minden példány állapotának eltávolításával is lekérdezheti a `instanceId` a "Get-példány állapota" kérelemből. Ebben az esetben a alapszintű paraméterek nem ugyanaz, mint a "Get instance status". Lekérdezési karakterlánc paraméterei szűréshez is támogatottak.

Ne feledje, hogy az egyik dolog, hogy `connection` és `code` megadása nem kötelező. Ha a függvény a névtelen hitelesítés kód nincs szükség.
Ha nem szeretné használni a különböző tárolási kapcsolati karakterlánc nem definiált AzureWebJobsStorage Alkalmazásbeállítás, majd biztonságosan figyelmen kívül hagyhatja a kapcsolat lekérdezésisztring-paraméter.

#### <a name="request"></a>Kérés

A verzió a Functions-futtatókörnyezet, a kérelem 1.x-es (több sorok jelennek meg az átláthatóság érdekében) a következők szerint van formázva:

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

A verzió 2.x verzióját a Functions-futtatókörnyezet, az URL-formátum ugyanazokat paraméterekkel rendelkezik, de egy kis mértékben eltérő előtagot:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

A kérelem paraméterekkel az API az alapértelmezett készlet korábban már említettük, valamint az alábbi egyedi paramétereket tartalmazza:

| Mező                   | Paraméter típusa  | Leírás |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL-cím             | Az orchestration-példány azonosítója. |
| **`showInput`**         | Lekérdezési sztring    | Nem kötelező paraméter. Ha beállítása `false`, a függvény bemeneti nem fog szerepelni a válasz hasznos adatban.|
| **`showHistory`**       | Lekérdezési sztring    | Nem kötelező paraméter. Ha beállítása `true`, a vezénylési futtatási előzményei fog szerepelni a válasz hasznos adatban.|
| **`showHistoryOutput`** | Lekérdezési sztring    | Nem kötelező paraméter. Ha beállítása `true`, a függvény kimenete fog szerepelni a vezénylési futtatási előzményei.|
| **`createdTimeFrom`**   | Lekérdezési sztring    | Nem kötelező paraméter. Megadása esetén a visszaadott, vagy a megadott időbélyegnél ISO8601 létrehozott példányok listájának szűrése.|
| **`createdTimeTo`**     | Lekérdezési sztring    | Nem kötelező paraméter. Megadása esetén a visszaadott, vagy a megadott időbélyegnél ISO8601 létrehozott példányok listájának szűrése.|
| **`runtimeStatus`**     | Lekérdezési sztring    | Nem kötelező paraméter. Megadása esetén a visszaadott-példányok listájának alapján szűri a futásidejű állapot. Lehetséges futásidejű állapot értékek listáját, olvassa el a [példányok lekérdezése](durable-functions-instance-management.md) témakör. |
| **`top`**               | Lekérdezési sztring    | Nem kötelező paraméter. Megadása esetén a lekérdezés által visszaadott példányok számát korlátozza. |

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
> Ez a művelet teljesítményigényesek lehetnek Azure tárolási i/o-tekintetében van-e nagy mennyiségű a példányok táblában lévő sorokat. Példány táblán további részletek találhatók a [teljesítményt és méretet (az Azure Functions) Durable Functions](durable-functions-perf-and-scale.md#instances-table) dokumentációját.
>

Ha további találatok létezik, a válasz fejléce a folytatási kód adja vissza.  A fejléc neve `x-ms-continuation-token`.

Ha a folytatási token értékét állítja be a következő kérés fejlécében, beszerezheti a következő lapra az eredmények. A kérelem fejlécét, ez a név egyben `x-ms-continuation-token`.

### <a name="purge-single-instance-history"></a>Egypéldányos előzmények törlése

Törli az előzmények és a egy megadott vezénylési példányhoz kapcsolódó összetevők felhasználásával.

#### <a name="request"></a>Kérés

A verzió a Functions-futtatókörnyezet, a kérelem 1.x-es (több sorok jelennek meg az átláthatóság érdekében) a következők szerint van formázva:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

A verzió 2.x verzióját a Functions-futtatókörnyezet, az URL-formátum ugyanazokat paraméterekkel rendelkezik, de egy kis mértékben eltérő előtagot:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

A kérelem paraméterekkel az API az alapértelmezett készlet korábban már említettük, valamint az alábbi egyedi paramétereket tartalmazza:

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | Az orchestration-példány azonosítója. |

#### <a name="response"></a>Válasz

A következő HTTP-állapot kód értékek adhatók vissza.

* **HTTP 200 (OK)**: Példány előzményeinek törlése sikeresen megtörtént.
* **A HTTP 404 (nem található)**: A megadott példány nem létezik.

A válasz-adattartalomra vonatkozó a **HTTP 200** esetben egy JSON-objektum a következő mezőt:

| Mező                  | Adattípus | Leírás |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | egész szám   | A törölt példányok száma. Az Egypéldányos esetben ez az érték mindig kell `1`. |

Íme egy példa válasz-adattartalomra (az olvashatóság érdekében formázva):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Több példány előzményeinek törlése

Törölheti is az előzmények és a egy feladat központ belül több példány esetén kapcsolódó összetevők eltávolításával a `{instanceId}` a "Egypéldányos előzmények törlése" kérelemből. Szelektív törlése példány előzményei, használja a "Get minden példány állapota" kérelem ismertetett ugyanazokat a szűrőket.

#### <a name="request"></a>Kérés

A verzió a Functions-futtatókörnyezet, a kérelem 1.x-es (több sorok jelennek meg az átláthatóság érdekében) a következők szerint van formázva:

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

A verzió 2.x verzióját a Functions-futtatókörnyezet, az URL-formátum ugyanazokat paraméterekkel rendelkezik, de egy kis mértékben eltérő előtagot:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

A kérelem paraméterekkel az API az alapértelmezett készlet korábban már említettük, valamint az alábbi egyedi paramétereket tartalmazza:

| Mező                 | Paraméter típusa  | Leírás |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Lekérdezési sztring    | Nem kötelező paraméter. Adja meg, amikor a példányok törlődnek, vagy a megadott időbélyegnél ISO8601 létrehozott listájának szűrése.|
| **`createdTimeTo`**   | Lekérdezési sztring    | Nem kötelező paraméter. Adja meg, amikor a példányok törlődnek, vagy a megadott időbélyegnél ISO8601 létrehozott listájának szűrése.|
| **`runtimeStatus`**   | Lekérdezési sztring    | Nem kötelező paraméter. Megadása esetén a példányok törlődnek a lista alapján szűri futásidejű állapotát. Lehetséges futásidejű állapot értékek listáját, olvassa el a [példányok lekérdezése](durable-functions-instance-management.md) témakör. |

Ha nincsenek paraméterek meg van adva, a feladat hub szereplő összes példányt lesz kiürítve.

> [!NOTE]
> Ez a művelet teljesítményigényesek lehetnek Azure tárolási i/o-tekintetében van-e sokkal azoknak a soroknak a példányok és/vagy előzmények táblák. Ezek a táblák a további részletek találhatók a [teljesítményt és méretet (az Azure Functions) Durable Functions](durable-functions-perf-and-scale.md#instances-table) dokumentációját.

#### <a name="response"></a>Válasz

A következő HTTP-állapot kód értékek adhatók vissza.

* **HTTP 200 (OK)**: Példány előzményeinek törlése sikeresen megtörtént.
* **A HTTP 404 (nem található)**: Példány sem található, amely megfelel a szűrőkifejezést.

A válasz-adattartalomra vonatkozó a **HTTP 200** esetben egy JSON-objektum a következő mezőt:

| Mező                   | Adattípus | Leírás |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | egész szám   | A törölt példányok száma. |

Íme egy példa válasz-adattartalomra (az olvashatóság érdekében formázva):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Esemény előléptetése

Egy esemény értesítési üzenetet küld egy futó vezénylési példány.

#### <a name="request"></a>Kérés

A verzió a Functions-futtatókörnyezet, a kérelem 1.x-es (több sorok jelennek meg az átláthatóság érdekében) a következők szerint van formázva:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

A verzió 2.x verzióját a Functions-futtatókörnyezet, az URL-formátum ugyanazokat paraméterekkel rendelkezik, de egy kis mértékben eltérő előtagot:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

A kérelem paraméterekkel az API az alapértelmezett készlet korábban már említettük, valamint az alábbi egyedi paramétereket tartalmazza:

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | Az orchestration-példány azonosítója. |
| **`eventName`**   | URL-cím             | Az eseményt, amely a célpéldány vezénylési vár a neve. |
| **`{content}`**   | Tartalomkérelem | A JSON-formátumú eseménytartalom. |

#### <a name="response"></a>Válasz

Számos lehetséges állapota kódértékek adhatók vissza.

* **202 (elfogadva) HTTP**: A bekövetkezett esemény lett elfogadva feldolgozásra.
* **HTTP 400 (hibás kérés)**: A kérelem tartalma nem volt típusú `application/json` vagy nem érvényes JSON volt.
* **A HTTP 404 (nem található)**: A megadott példány nem található.
* **A HTTP 410-es (megszűnt)**: A megadott példány befejeződött vagy meghiúsult, és nem tudja feldolgozni a bekövetkezett eseményeket.

Íme egy példa kérelmet, amely a JSON-karakterlánc küld `"incr"` egy példánnyal, hogy egy adott esemény nevű **művelet**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Az API-hoz a válaszok nem tartalmaznak minden tartalom.

### <a name="terminate-instance"></a>Példány leállítása

Egy futó vezénylési példány leáll.

#### <a name="request"></a>Kérés

A verzió a Functions-futtatókörnyezet, a kérelem 1.x-es (több sorok jelennek meg az átláthatóság érdekében) a következők szerint van formázva:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

A verzió 2.x verzióját a Functions-futtatókörnyezet, az URL-formátum ugyanazokat paraméterekkel rendelkezik, de egy kis mértékben eltérő előtagot:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Kérelem, az API paraméternek számít a korábban már említettük, valamint a következő egyedi paraméter alapértelmezett beállítása.

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | Az orchestration-példány azonosítója. |
| **`reason`**      | Lekérdezési sztring    | Választható. Az orchestration-példány leállításához okát. |

#### <a name="response"></a>Válasz

Számos lehetséges állapota kódértékek adhatók vissza.

* **202 (elfogadva) HTTP**: A megszakítási kérés elfogadva feldolgozásra.
* **A HTTP 404 (nem található)**: A megadott példány nem található.
* **A HTTP 410-es (megszűnt)**: A megadott példány befejeződött vagy meghiúsult.

Íme egy példa kérelmet, amely egy futó példány leáll, és adja meg az okot **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-hoz a válaszok nem tartalmaznak minden tartalom.

## <a name="rewind-instance-preview"></a>Visszatekerés példány (előzetes verzió)

A futtatási állapot egy sikertelen vezénylési példány visszaállítja a legutóbbi sikertelen műveletek visszajátszása alapján.

### <a name="request"></a>Kérés

A verzió a Functions-futtatókörnyezet, a kérelem 1.x-es (több sorok jelennek meg az átláthatóság érdekében) a következők szerint van formázva:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

A verzió 2.x verzióját a Functions-futtatókörnyezet, az URL-formátum ugyanazokat paraméterekkel rendelkezik, de egy kis mértékben eltérő előtagot:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Kérelem, az API paraméternek számít a korábban már említettük, valamint a következő egyedi paraméter alapértelmezett beállítása.

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL-cím             | Az orchestration-példány azonosítója. |
| **`reason`**      | Lekérdezési sztring    | Választható. A vezénylési példány visszatekerése okát. |

### <a name="response"></a>Válasz

Számos lehetséges állapota kódértékek adhatók vissza.

* **202 (elfogadva) HTTP**: A visszatekerés kérelem feldolgozásra elfogadva.
* **A HTTP 404 (nem található)**: A megadott példány nem található.
* **A HTTP 410-es (megszűnt)**: A megadott példány befejeződött, vagy meg lett szakítva.

Íme egy példa kérelmet, amely gyors visszatekerés a hibás szolgáltatáspéldányt, és adja meg az okot **rögzített**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-hoz a válaszok nem tartalmaznak minden tartalom.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan hibáinak kezelése](durable-functions-error-handling.md)
