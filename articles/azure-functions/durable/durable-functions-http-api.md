---
title: HTTP-API-k a Durable Functions-ben – Azure
description: Megtudhatja, hogyan implementálhat HTTP API-kat a Azure Functions Durable Functions-bővítményében.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 11ae418ddbe007c6fd5aa44ef22ed7fddec9c702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087269"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>HTTP-API-k Durable Functions (Azure Functions)

A tartós feladathoz tartozó bővítmény olyan HTTP API-kat tesz elérhetővé, amelyek a következő feladatok végrehajtására használhatók:

* Egy előkészítési példány állapotának beolvasása.
* Esemény küldése várakozási összehangoló példánynak.
* Egy futó összehangoló példány leállítása.

A HTTP API-k mindegyike egy webhook-művelet, amelyet közvetlenül a tartós feladatok bővítménye kezel. Nem tartoznak a Function alkalmazás egyik függvényéhez sem.

> [!NOTE]
> Ezek a műveletek közvetlenül is meghívhatók a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály példány-felügyeleti API-jai használatával. További információ: példányok [kezelése](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>HTTP API URL-cím felderítése

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály egy [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API-t tesz elérhetővé, amely az összes támogatott műveletre mutató hivatkozásokat tartalmazó http-válasz adattartalom létrehozásához használható. Az alábbi példa egy HTTP-trigger függvényt mutat be, amely bemutatja, hogyan használhatja ezt az API-t:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Az alábbi példában szereplő függvények a JSON-válaszok következő adatait eredményezik. Az összes mező `string`adattípusa.

| Mező                   |Leírás                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |A koordináló példány azonosítója. |
| **`statusQueryGetUri`**     |A koordináló példány állapotának URL-címe |
| **`sendEventPostUri`**      |A hangolási példány "esemény előléptetése" URL-címe. |
| **`terminatePostUri`**      |A koordináló példány "leállítási" URL-címe. |
| **`purgeHistoryDeleteUri`** |A koordináló példány "törlési előzmények" URL-címe. |
| **`rewindPostUri`**         |előnézet A hangolási példány "Rewind" URL-címe. |

Példa erre a válaszra:

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
    "purgeHistoryDeleteUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> A webhook URL-címeinek formátuma eltérő lehet attól függően, hogy a Azure Functions gazdagép melyik verzióját futtatja. A fenti példa a Azure Functions 2. x gazdagépre mutat.

## <a name="async-operation-tracking"></a>Aszinkron művelet követése

A korábban említett HTTP-válasz úgy lett kialakítva, hogy segítse a hosszú ideig futó HTTP aszinkron API-k megvalósítását Durable Functions. Ezt más néven a *lekérdezési fogyasztói mintának*nevezzük. Az ügyfél/kiszolgáló folyamat a következőképpen működik:

1. Az ügyfél HTTP-kérést bocsát ki egy hosszú ideig futó folyamat elindításához, például egy Orchestrator függvényt.
2. A cél http-trigger egy http 202 választ ad vissza `Location` , amely egy `statusQueryGetUri` fejlécet tartalmaz az értékkel.
3. Az ügyfél lekérdezi az URL- `Location` címet a fejlécben. Továbbra is megjelenik `Location` a http 202-válaszok fejléctel.
4. Ha a példány befejeződik (vagy sikertelen), a `Location` fejlécben található végpont a http 200 értéket adja vissza.

Ez a protokoll lehetővé teszi a hosszan futó folyamatok koordinálását olyan külső ügyfelekkel vagy szolgáltatásokkal, amelyek támogatják a http- `Location` végpontok lekérdezését és a fejlécet követve. Az alapvető darabok már be vannak építve a Durable Functions HTTP API-khoz.

> [!NOTE]
> Alapértelmezés szerint a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/) által biztosított összes HTTP-alapú művelet támogatja a normál aszinkron műveleti mintát. Ez a funkció lehetővé teszi a hosszan futó tartós funkciók beágyazását egy Logic Apps munkafolyamat részeként. Az aszinkron HTTP-minták támogatásáról Logic Apps további részleteket a [Azure Logic apps munkafolyamat-műveletek és eseményindítók dokumentációjában](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)talál.

## <a name="http-api-reference"></a>HTTP API-referenciák

A bővítmény által megvalósított összes HTTP API a következő paramétereket használja. Az összes paraméter `string`adattípusa.

| Paraméter        | Paraméter típusa  | Leírás |
|------------------|-----------------|-------------|
| **`taskHub`**    | Lekérdezési sztring    | A [feladat hub](durable-functions-task-hubs.md)neve. Ha nincs megadva, a rendszer feltételezi, hogy az aktuális Function-alkalmazás feladatának hub-neve. |
| **`connection`** | Lekérdezési sztring    | A Storage-fiókhoz tartozó kapcsolatok karakterláncának **neve** . Ha nincs megadva, a rendszer a Function alkalmazás alapértelmezett kapcsolatok sztringjét feltételezi. |
| **`systemKey`**  | Lekérdezési sztring    | Az API meghívásához szükséges hitelesítési kulcs. |

`systemKey`a Azure Functions gazdagép által automatikusan generált engedélyezési kulcs. Kifejezetten hozzáférést biztosít a tartós feladathoz tartozó API-khoz, és ugyanúgy kezelhető, mint a [többi hitelesítési kulcs](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Az `systemKey` érték felderítésének legegyszerűbb módja a `CreateCheckStatusResponse` korábban említett API használata.

A következő néhány szakasz a bővítmény által támogatott HTTP API-kat fedi le, és példákat is tartalmaz a használatuk módjára.

### <a name="get-instance-status"></a>Példány állapotának beolvasása

Egy megadott összehangoló példány állapotának beolvasása.

#### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező                   | Paraméter típusa  | Leírás |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | A koordináló példány azonosítója. |
| **`showInput`**         | Lekérdezési sztring    | Nem kötelező paraméter. Ha a értékre `false`van állítva, a függvény bemenete nem fog szerepelni a válasz adattartalomban.|
| **`showHistory`**       | Lekérdezési sztring    | Nem kötelező paraméter. Ha a értékre `true`van állítva, a rendszer a hangfelvétel-végrehajtási előzményeket fogja tartalmazni a válasz adattartalomban.|
| **`showHistoryOutput`** | Lekérdezési sztring    | Nem kötelező paraméter. Ha a értékre `true`van állítva, a függvény kimenetei szerepelni fognak a előkészítési végrehajtási előzményekben.|
| **`createdTimeFrom`**   | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, szűri a megadott ISO8601 időbélyegzőn vagy azt követően létrehozott visszaadott példányok listáját.|
| **`createdTimeTo`**     | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, szűri a megadott ISO8601 időbélyegzőn vagy azt megelőzően létrehozott visszaadott példányok listáját.|
| **`runtimeStatus`**     | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, a visszaadott példányok listáját a futásidejű állapotuk alapján szűri. A lehetséges futásidejű állapotüzenetek listájának megtekintéséhez tekintse meg a [példányok lekérdezése](durable-functions-instance-management.md) témakört. |

#### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 200 (OK)** : A megadott példány befejezett állapotban van.
* **HTTP 202 (elfogadva)** : A megadott példány folyamatban van.
* **HTTP 400 (hibás kérelem)** : A megadott példány meghiúsult vagy meg lett szakítva.
* **HTTP 404 (nem található)** : A megadott példány nem létezik vagy nem indult el.
* **HTTP 500 (belső kiszolgálóhiba)** : A megadott példány nem kezelt kivétel miatt meghiúsult.

A **http 200** és a **http 202** esetek válaszának adattartalma egy JSON-objektum, amely a következő mezőket tartalmazhatja:

| Mező                 | Adattípus | Leírás |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | Karakterlánc    | A példány futtatókörnyezeti állapota. Az értékek közé tartoznak a következők: *Futtatás*, *függő*, *sikertelen*, megszakított, leállított, *befejezett*. |
| **`input`**           | JSON      | A példány inicializálásához használt JSON-adatbázis. Ez a mező `null` akkor van `showInput` beállítva, ha a lekérdezési `false`karakterlánc paraméter értéke.|
| **`customStatus`**    | JSON      | Az egyéni előkészítési állapothoz használt JSON-adatértékek. `null` Ha nincs beállítva ez a mező. |
| **`output`**          | JSON      | A példány JSON-kimenete. Ez a mező `null` akkor áll fenn, ha a példány nem befejezett állapotban van. |
| **`createdTime`**     | Karakterlánc    | A példány létrehozásának időpontja. ISO 8601 kiterjesztett jelölést használ. |
| **`lastUpdatedTime`** | Karakterlánc    | Az az időpont, amikor a példányt utoljára megtartották. ISO 8601 kiterjesztett jelölést használ. |
| **`historyEvents`**   | JSON      | Egy JSON-tömb, amely a előkészítési végrehajtási előzményeket tartalmazza. Ez a mező `null` csak akkor `showHistory` van beállítva, ha a lekérdezési `true`karakterlánc paraméter értéke. |

Íme egy példa a válasz adattartalomra, beleértve a előkészítési végrehajtási előzményeket és a tevékenységek kimeneteit (az olvashatóság érdekében formázva):

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

A **http 202** válasza tartalmaz egy **Location** Response fejlécet is, amely ugyanarra az URL `statusQueryGetUri` -címre hivatkozik, mint a korábban említett mező.

### <a name="get-all-instances-status"></a>Az összes példány állapotának lekérése

Az összes példány `instanceId` állapotát lekérdezheti a "példány állapotának beolvasása" kérelemből. Ebben az esetben az alapszintű paraméterek ugyanazok, mint a "példány lekérése" állapot. A szűréshez a lekérdezési karakterlánc paraméterei is támogatottak.

Az egyik dolog megjegyezni, hogy `connection` a `code` és a nem kötelező. Ha a függvényben névtelen hitelesítés van, akkor a kódnak nem kell megadnia.
Ha nem szeretne más tárolási kapcsolódási karakterláncot használni, mint a AzureWebJobsStorage alkalmazás beállítása, akkor nyugodtan figyelmen kívül hagyhatja a kapcsolódási lekérdezési karakterlánc paraméterét.

#### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

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

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

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

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező                   | Paraméter típusa  | Leírás |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | A koordináló példány azonosítója. |
| **`showInput`**         | Lekérdezési sztring    | Nem kötelező paraméter. Ha a értékre `false`van állítva, a függvény bemenete nem fog szerepelni a válasz adattartalomban.|
| **`showHistory`**       | Lekérdezési sztring    | Nem kötelező paraméter. Ha a értékre `true`van állítva, a rendszer a hangfelvétel-végrehajtási előzményeket fogja tartalmazni a válasz adattartalomban.|
| **`showHistoryOutput`** | Lekérdezési sztring    | Nem kötelező paraméter. Ha a értékre `true`van állítva, a függvény kimenetei szerepelni fognak a előkészítési végrehajtási előzményekben.|
| **`createdTimeFrom`**   | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, szűri a megadott ISO8601 időbélyegzőn vagy azt követően létrehozott visszaadott példányok listáját.|
| **`createdTimeTo`**     | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, szűri a megadott ISO8601 időbélyegzőn vagy azt megelőzően létrehozott visszaadott példányok listáját.|
| **`runtimeStatus`**     | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, a visszaadott példányok listáját a futásidejű állapotuk alapján szűri. A lehetséges futásidejű állapotüzenetek listájának megtekintéséhez tekintse meg a [példányok lekérdezése](durable-functions-instance-management.md) témakört. |
| **`top`**               | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, korlátozza a lekérdezés által visszaadott példányok számát. |

#### <a name="response"></a>Válasz

Íme egy példa a válasz hasznos adataira, beleértve az előkészítési állapotot (az olvashatóság érdekében formázva):

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
> Ez a művelet nagyon költséges lehet az Azure Storage I/O esetében, ha a példányok táblában sok sor van. A példányok táblával kapcsolatos további részleteket a [Durable functions (Azure functions)](durable-functions-perf-and-scale.md#instances-table) dokumentációjának teljesítményében és méretezésében találhat.
>

Ha több találat is létezik, a rendszer egy folytatási tokent ad vissza a válasz fejlécében.  A fejléc `x-ms-continuation-token`neve:.

Ha a folytatási jogkivonat értékét a következő kérelem fejlécében állítja be, akkor az eredmények következő oldalát szerezheti be. A kérelem fejlécének neve is `x-ms-continuation-token`.

### <a name="purge-single-instance-history"></a>Egypéldányos előzmények végleges törlése

Törli a megadott előkészítési példány előzményeit és a kapcsolódó összetevőket.

#### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A koordináló példány azonosítója. |

#### <a name="response"></a>Válasz

A következő HTTP-állapotkód értékei adhatók vissza.

* **HTTP 200 (OK)** : A példány előzményeinek törlése sikeresen megtörtént.
* **HTTP 404 (nem található)** : A megadott példány nem létezik.

A **HTTP 200** eset válaszának tartalma egy JSON-objektum, amely a következő mezővel rendelkezik:

| Mező                  | Adattípus | Leírás |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | A példányok száma törölve. Az Egypéldányos eset esetében ennek az értéknek mindig `1`a következőnek kell lennie:. |

Íme egy példa a válasz hasznos adataira (az olvashatóság érdekében formázva):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Több példány előzményeinek kiürítése

Az előzményeket és a kapcsolódó összetevőket is törölheti egy adott munkaközpontban található több példányhoz, `{instanceId}` ha eltávolítja az "egypéldány-előzmények kiürítése" kérést. A példányok előzményeinek szelektív törléséhez használja az "összes példány állapota" kérelemben ismertetett szűrőket.

#### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező                 | Paraméter típusa  | Leírás |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Lekérdezési sztring    | A megadott ISO8601 időbélyegzőn vagy azt követően létrehozott kiürített példányok listájának szűrése.|
| **`createdTimeTo`**   | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, szűri az adott ISO8601 időbélyegzőn vagy azt megelőzően létrehozott kiürített példányok listáját.|
| **`runtimeStatus`**   | Lekérdezési sztring    | Nem kötelező paraméter. Ha meg van adva, a rendszer kiszűri a törölt példányok listáját a futásidejű állapotuk alapján. A lehetséges futásidejű állapotüzenetek listájának megtekintéséhez tekintse meg a [példányok lekérdezése](durable-functions-instance-management.md) témakört. |

> [!NOTE]
> Ez a művelet nagyon költséges lehet az Azure Storage I/O esetében, ha a példányok és/vagy az előzmények táblázatában sok sor van. A táblázatokkal kapcsolatos további részletekért tekintse meg a [Durable functions (Azure functions) dokumentációjának teljesítményét és méretezését](durable-functions-perf-and-scale.md#instances-table) .

#### <a name="response"></a>Válasz

A következő HTTP-állapotkód értékei adhatók vissza.

* **HTTP 200 (OK)** : A példány előzményeinek törlése sikeresen megtörtént.
* **HTTP 404 (nem található)** : Nem találhatók olyan példányok, amelyek megfelelnek a szűrő kifejezésének.

A **HTTP 200** eset válaszának tartalma egy JSON-objektum, amely a következő mezővel rendelkezik:

| Mező                   | Adattípus | Leírás |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | A példányok száma törölve. |

Íme egy példa a válasz hasznos adataira (az olvashatóság érdekében formázva):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Esemény előléptetése

Esemény-értesítési üzenet küldése egy futó összehangoló példánynak.

#### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A koordináló példány azonosítója. |
| **`eventName`**   | URL             | Annak az eseménynek a neve, amelyre a cél-előkészítési példány várakozik. |
| **`{content}`**   | Tartalom kérése | A JSON-formátumú esemény hasznos adata. |

#### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (elfogadva)** : A felemelt esemény feldolgozásra lett elfogadva.
* **HTTP 400 (hibás kérelem)** : A kérelem tartalma nem típusú `application/json` , vagy nem volt érvényes JSON.
* **HTTP 404 (nem található)** : A megadott példány nem található.
* **HTTP 410 (eltűnt)** : A megadott példány befejeződött vagy meghiúsult, és nem tudja feldolgozni a felmerült eseményeket.

Íme egy példa egy olyan kérelemre, amely a `"incr"` JSON-karakterláncot egy olyan példányra küldi, amely a **művelet**elvégzésére vár:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Az API-val kapcsolatos válaszok nem tartalmaznak tartalmat.

### <a name="terminate-instance"></a>Példány leállítása

Leállítja a futó összehangoló példányt.

#### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméter.

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A koordináló példány azonosítója. |
| **`reason`**      | Lekérdezési sztring    | Nem kötelező. Az előkészítési példány megszakításának oka. |

#### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (elfogadva)** : A megszakítási kérelem el lett fogadva feldolgozásra.
* **HTTP 404 (nem található)** : A megadott példány nem található.
* **HTTP 410 (eltűnt)** : A megadott példány befejeződött vagy meghiúsult.

Íme egy példa egy futó példányt lezáró kérelemre, amely a **hibás**működés okát adja meg:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-val kapcsolatos válaszok nem tartalmaznak tartalmat.

### <a name="rewind-instance-preview"></a>Felcsévélési példány (előzetes verzió)

Visszaállítja a sikertelen előkészítési példányt egy futó állapotba a legutóbbi sikertelen műveletek visszajátszásával.

#### <a name="request"></a>Kérés

A functions futtatókörnyezet 1. x verziójában a kérelem formázása az alábbiak szerint történik (az érthetőség kedvéért több sor jelenik meg):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

A functions Runtime 2. x verziójában az URL-formátum ugyanazokkal a paraméterekkel rendelkezik, mint egy kissé eltérő előtaggal:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméter.

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | A koordináló példány azonosítója. |
| **`reason`**      | Lekérdezési sztring    | Nem kötelező. Az előkészítési példány újratekercselésének oka. |

#### <a name="response"></a>Válasz

Több lehetséges állapotkód-érték is visszaadható.

* **HTTP 202 (elfogadva)** : A rendszer visszacsévélési kérést fogadott a feldolgozáshoz.
* **HTTP 404 (nem található)** : A megadott példány nem található.
* **HTTP 410 (eltűnt)** : A megadott példány befejeződött vagy meg lett szakítva.

Íme egy példa egy sikertelen példány újratekercselésére, és a **rögzített**okot adja meg:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Az API-val kapcsolatos válaszok nem tartalmaznak tartalmat.

### <a name="signal-entity-preview"></a>Jel entitás (előzetes verzió)

Egy egyirányú műveleti üzenetet küld egy [tartós entitásnak](durable-functions-types-features-overview.md#entity-functions). Ha az entitás nem létezik, a rendszer automatikusan létrehozza.

#### <a name="request"></a>Kérés

A HTTP-kérés a következőképpen van formázva (az érthetőség kedvéért több sor jelenik meg):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Az API-hoz tartozó kérelmek paraméterei közé tartozik a korábban említett alapértelmezett készlet, valamint a következő egyedi paraméterek:

| Mező             | Paraméter típusa  | Leírás |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | Az entitás típusa. |
| **`entityKey`**   | URL             | Az entitás egyedi neve. |
| **`op`**          | Lekérdezési sztring    | Nem kötelező. A meghívni kívánt felhasználó által definiált művelet neve. |
| **`{content}`**   | Tartalom kérése | A JSON-formátumú esemény hasznos adata. |

Íme egy példa arra a kérelemre, amely a felhasználó által megadott "Hozzáadás" üzenetet `Counter` küld egy `steps`nevű entitásnak. Az üzenet tartalma az érték `5`. Ha az entitás még nem létezik, akkor ez a kérelem jön létre:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

#### <a name="response"></a>Válasz

A művelet több lehetséges választ tartalmaz:

* **HTTP 202 (elfogadva)** : A jel művelete aszinkron feldolgozásra lett elfogadva.
* **HTTP 400 (hibás kérelem)** : A kérelem tartalma nem típusú `application/json`, nem érvényes JSON, vagy érvénytelen `entityKey` értékkel rendelkezett.
* **HTTP 404 (nem található)** : A megadott `entityType` nem található.

A sikeres HTTP-kérések nem tartalmaznak semmilyen tartalmat a válaszban. A hibás HTTP-kérések tartalmazhatnak JSON-formátumú hibákat a válasz tartalmában.

### <a name="query-entity-preview"></a>Lekérdezési entitás (előzetes verzió)

Lekéri a megadott entitás állapotát.

#### <a name="request"></a>Kérés

A HTTP-kérés a következőképpen van formázva (az érthetőség kedvéért több sor jelenik meg):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>Válasz

A művelet két lehetséges választ tartalmaz:

* **HTTP 200 (OK)** : A megadott entitás létezik.
* **HTTP 404 (nem található)** : A megadott entitás nem található.

A sikeres válasz az entitás JSON-szerializált állapotát tartalmazza tartalomként.

#### <a name="example"></a>Példa
A következő példa egy olyan HTTP-kérésre mutat be, amely egy nevű `Counter` `steps`meglévő entitás állapotát kapja:

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Ha az `Counter` entitás egyszerűen több, egy `currentValue` mezőben mentett lépést tartalmaz, a válasz tartalma a következőhöz hasonlóan fog kinézni (az olvashatóság érdekében formázva):

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató a hibák kezeléséhez](durable-functions-error-handling.md)
