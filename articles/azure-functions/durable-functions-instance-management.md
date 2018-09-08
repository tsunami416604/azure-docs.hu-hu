---
title: Durable Functions - Azure-példányok kezelése
description: Megtudhatja, hogyan kezelheti a Durable Functions bővítmény-példányok az Azure Functions szolgáltatáshoz.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 72ea5e54bf86ce408700c0456f6d37f5f3c29924
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091782"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions)-példányok kezelése

[Durable Functions](durable-functions-overview.md) vezénylési példányok elindult, megszakadt, lekérdezhetők, és küldött értesítés eseményeket. Az összes példány felügyeleti történik használatával a [kötés vezénylési ügyfél](durable-functions-bindings.md). Ez a cikk hiányzóra változik az egyes példány felügyeleti műveletek részleteit.

## <a name="starting-instances"></a>-Példányok indítása

A [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) elindít egy orchestrator-függvényt egy új példányát. Ez az osztály példányainak használatával lehet lekérni a `orchestrationClient` kötést. Belsőleg, a metódus enqueues vezérlő várakozási, majd elindít egy függvény a megadott névvel, amely használja a start üzenetet a `orchestrationTrigger` kötés aktiválásához. 

A feladat befejeződik, a vezénylési folyamat elindításakor. A vezénylési folyamat 30 másodpercen belül el kell indulnia. Ha ez hosszabb időt vesz igénybe, egy `TimeoutException` fordul elő. 

A paraméterek [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) az alábbiak szerint:

* **Név**: ütemezése az orchestrator függvény neve.
* **A bemeneti**: szerializálható JSON adatokat, amelyek az orchestrator függvény bemeneti kell átadni.
* **InstanceId**: a példány (nem kötelező) az egyedi azonosítója. Ha nincs megadva, véletlenszerű Példányazonosító jön létre.

Íme egy egyszerű C#-példa:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    TraceWriter log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.Info($"Started orchestration with ID = '{instanceId}'.");
}
```

Nem .NET nyelvekhez, a függvény kimeneti kötésének elindítani az új példányokat is használható. Ebben az esetben bármilyen szerializálható JSON objektum, amely rendelkezik a fenti három paramétert mezők is használható. Vegyük példaként a következő JavaScript-függvény:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```

> [!NOTE]
> Azt javasoljuk, hogy egy véletlenszerű azonosítót használja-e a példány azonosítóját. Ez segít az orchestrator funkciók vertikális több virtuális gépen, győződjön meg arról, hogy egy azonos terheléselosztást. A megfelelő ideje, hogy nem véletlenszerű Példányazonosítók akkor, ha az azonosító egy külső forrásból kell származnia, vagy végrehajtása során a [egyszeres orchestrator](durable-functions-singletons.md) mintát.

## <a name="querying-instances"></a>Példányok lekérdezése

A [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály egy vezénylési példány állapotát kérdezi le. Vesz egy `instanceId` (kötelező), `showHistory` (nem kötelező), és `showHistoryOutput` paraméterek (nem kötelező). Ha `showHistory` értékre van állítva `true`, a válasz tartalmazni fogja a futtatási előzményei. Ha `showHistoryOutput` értékre van állítva `true` , végrehajtási előzményeinek fogja tartalmazni a tevékenység kimeneteiből. A módszer a következő tulajdonságokkal rendelkező objektumot adja vissza:

* **Név**: az orchestrator függvény neve.
* **InstanceId**: a vezénylési Példányazonosítója (legyen ugyanaz, mint a `instanceId` bemeneti).
* **CreatedTime**: A indításakor, amikor az orchestrator függvény futtatása.
* **LastUpdatedTime**: az időpontot, amikor az orchestration utolsó alkulcsaihoz.
* **A bemeneti**: A bemeneti JSON értékként a függvény.
* **CustomStatus**: egyéni vezénylési állapot JSON formátumban. 
* **Kimeneti**: a függvény JSON értékként (Ha a függvény befejeződött) kimenetét. Ha az orchestrator függvénye sikertelen volt, ez a tulajdonság tartalmazza a hiba részletei. Ha az orchestrator-függvény meg lett szakítva, ez a tulajdonság (ha van ilyen) a megadott okának megszüntetése tartalmazza.
* **RuntimeStatus**: a következő értékek egyikét:
    * **Függőben lévő**: A példány van ütemezve, de még nem indult futtatása.
    * **Futó**: A példány elindult.
    * **Befejezett**: A példány a szokásos módon befejeződött.
    * **ContinuedAsNew**: A példányt egy új előzményekkel újraindult magát. Ez csak átmeneti állapot.
    * **Nem sikerült**: A példányt egy hiba miatt sikertelen volt.
    * **Lezárva**: A példány váratlanul leállt.
* **Előzmények**: a vezénylési végrehajtási előzményei. Ez a mező csak megadni, ha `showHistory` értékre van állítva `true`.
    
A metódus visszatérése `null` , ha a példány nem létezik, vagy még nem indult futtatása.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```
## <a name="querying-all-instances"></a>Minden példány lekérdezése

Használhatja a `GetStatusAsync` metódus a állapotok szoftverpéldányok vezénylési lekérdezéséhez. Ez nem használ paramétereket, vagy megadhat egy `CancellationToken` objektum abban az esetben, ha azt szeretné, és törölje azt. A metódus az azonos tulajdonságokkal rendelkező objektum adja vissza a `GetStatusAsync` paraméterekkel, kivéve azt metódus nem ad vissza az előzményeket. 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    TraceWriter log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.Info(JsonConvert.SerializeObject(instance));
    };
}
```

## <a name="terminating-instances"></a>Példány leállítása

Egy futó vezénylési példány használatával állítható le a [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) módszere a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály. A két paraméter egy `instanceId` és a egy `reason` karakterláncot, amely naplókat, és a példány állapota lesz írva. A leállított példány le fog állni, amint eléri a következő `await` pontot, vagy megszűnése azonnal esetén már be egy `await`. 

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Jelenleg nem propagál példány megszüntetése. Tevékenységfüggvényeket és alárendelt vezénylések végrehajtása, függetlenül attól, hogy le lett állítva a vezénylési példányt, amelynek a neve őket.

## <a name="sending-events-to-instances"></a>Események küldése az példányok

Eseményértesítések futó példányok használatával is küldhetők a [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) módszere a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály. Ezeket az eseményeket is kezelni példányai, amelyek elnyerésére hívása [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

A paraméterek [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) az alábbiak szerint:

* **InstanceId**:-példány egyedi azonosítója.
* **EventName**: küldése az esemény nevét.
* **EventData**: a példányt küldeni egy szerializálható JSON adattartalom.

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!WARNING]
> Ha nincs vezénylési példánya és a megadott *példány azonosítója* , vagy ha a példány nem vár a megadott *eseménynév*, a rendszer törli az eseményüzenet. Ezzel a viselkedéssel kapcsolatos további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Várakozás a befejezésre vezénylési

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály közzéteszi egy [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API, amely a szinkron módon történik a tényleges kimeneti beolvasni a vezénylési példány is használható. A metódus használja az alapértelmezett érték a 10 másodperces `timeout` és az 1 másodperc `retryInterval` Ha ezek nincsenek beállítva.  

Íme egy példa azt mutatja be, hogyan használható az API HTTP-eseményindító függvény:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

A függvény a következő kódsorral 2-másodperces időkorlát és 0,5 másodperces újrapróbálkozási időköz használatával nem hívható meg:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Függően lekérni a választ az orchestration példányban van két esetet:

1. A vezénylési példányok be a meghatározott idő (ebben az esetben 2 másodperc), a válasz a tényleges vezénylési példány kimeneti i szinkron módon történik:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. A vezénylési példányok a megadott időkorláton (ebben az esetben 2 másodperc) belül nem tudja végrehajtani, a válasz az egyik ismertetett alapértelmezett **HTTP API URL-cím-felderítési**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> A webhook URL-címek formátumát az Azure Functions állomás verziójának függően eltérhet. Az előző példában az Azure Functions 2.0 állomás van.

## <a name="retrieving-http-management-webhook-urls"></a>HTTP-felügyeleti Webhook URL-címek beolvasása

Külső rendszerek kommunikálhatnak a webhook URL-címeket, amelyek részei ismertetett alapértelmezett választ keresztül Durable Functions [HTTP API URL-cím-felderítési](durable-functions-http-api.md). Azonban a webhook URL-címeket is elérhető lesz programozott módon a vezénylési ügyfél vagy a tevékenység függvényének keresztül a [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) módszere a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)osztály. 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) egy paraméterrel rendelkezik:

* **InstanceId**:-példány egyedi azonosítója.

A metódus egy példányát adja vissza a [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) karakterlánc a következő tulajdonságokkal:

* **ID**: a vezénylési Példányazonosítója (legyen ugyanaz, mint a `InstanceId` bemeneti).
* **StatusQueryGetUri**: az állapot példány URL-címét a vezénylési.
* **SendEventPostUri**: A "raise esemény" példány URL-címét a vezénylési.
* **TerminatePostUri**: A "le" példány URL-címét a vezénylési.

Tevékenységfüggvényeket küldhet egy példányát [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) figyelésére, vagy a vezénylések eseményeket hoz létre a külső rendszerekhez:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, például felügyeleti HTTP API-k használata](durable-functions-http-api.md)
