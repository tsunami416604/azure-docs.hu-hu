---
title: Kezelheti a tartós funkciók - Azure-példány
description: Útmutató a tartós funkciók bővítmény példánya kezelése az Azure Functions.
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
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 3c6602bdd90c82568a50ad7354d7abb7c6a472ae
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287748"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Példányok a tartós függvények (az Azure Functions) kezelése

[Tartós funkciók](durable-functions-overview.md) vezénylési példányok indítható, megszakadt, kérdezhetők le, és értesítési események küldése. Minden példány felügyeleti történik használatával a [vezénylési ügyfél kötésének](durable-functions-bindings.md). Ez a cikk hiányzóra változik, minden példány felügyeleti művelet részleteit.

## <a name="starting-instances"></a>Példányok indítása

A [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) elindítja az orchestrator funkcióinak egy új példányát. Ez az osztály példányai használatával lehet lekérni a `orchestrationClient` kötés. Belső, a módszer enqueues vezérlő várakozási, majd elindítja egy függvény a megadott nevű használó start üzenetet a `orchestrationTrigger` indítás kötés. 

A feladat befejezése a vezénylési folyamat indításakor. A vezénylési folyamat 30 másodperce kell kezdődnie. Ez hosszabb időt vesz igénybe, ha egy `TimeoutException` vált ki. 

A paraméterek [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) a következők:

* **Név**: ütemezése az orchestrator függvény neve.
* **Bemeneti**: a bemenetként az orchestrator szolgáltatás legyen átadva szerializálható JSON adatokat.
* **InstanceId**: példány (nem kötelező) a egyedi azonosítója. Ha nincs megadva, a véletlenszerű Példányazonosítót jön létre.

Íme egy egyszerű C# példa:

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

A .NET nyelv, a függvény kimeneti kötése elindítani az új példányokat is használható. Ebben az esetben bármilyen szerializálható JSON objektum, amely rendelkezik a fenti három paraméterek mezők használható. Vegyük példaként a következő JavaScript-funkció:

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
> Azt javasoljuk, hogy egy véletlenszerű azonosítót használja a példány azonosítója. Ez segít az egyenlőnek terhelést terjesztési győződjön meg arról, amikor az orchestrator funkciók skálázás több virtuális gépek között. A megfelelő időpontok nem véletlenszerű példány azonosítók akkor, ha az azonosító egy külső forrásból kell származnia, illetve végrehajtásakor a [egypéldányos orchestrator](durable-functions-singletons.md) mintát.

## <a name="querying-instances"></a>Példányok lekérdezése

A [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály vezénylési példány állapotának lekérdezése. Tart egy `instanceId` (kötelező), `showHistory` (nem kötelező), és `showHistoryOutput` paraméterekként (nem kötelező). Ha `showHistory` értéke `true`, a válasz tartalmazza a futtatási előzményei. Ha `showHistoryOutput` értéke `true` , valamint a futtatási előzményei tevékenység kimeneteiből fogja tartalmazni. A metódus visszaadja egy objektum a következő tulajdonságokkal:

* **Név**: az orchestrator függvény neve.
* **InstanceId**: a vezénylési Példányazonosítója (meg kell egyeznie a `instanceId` bemeneti).
* **CreatedTime**: az orchestrator függvény kezdésének ideje futó.
* **LastUpdatedTime**: az időpontot, amikor az orchestration utolsó alkulcsaihoz.
* **Bemeneti**: A bemeneti JSON értékként a függvény.
* **CustomStatus**: egyéni vezénylési állapot JSON formátumban. 
* **Kimeneti**: a függvényt, egy JSON-érték (Ha a függvény befejeződött) kimenetét. Az orchestrator függvény futása sikertelen, ha ez a tulajdonság tartalmazza a hiba részletei. Ha az orchestrator-funkció le lett állítva, ez a tulajdonság (ha van ilyen) a megadott okának megszüntetése tartalmazza.
* **RuntimeStatus**: a következő értékek egyikét:
    * **Futó**: A példány elindult.
    * **Befejeződött**: Ez a példány általában befejeződött.
    * **ContinuedAsNew**: A példány újraindítása magát az új előzményeit. Ez egy átmeneti állapotban.
    * **Nem sikerült**: A példány egy hiba miatt sikertelen volt.
    * **Megszakadt**: A példány váratlanul leállt.
* **Előzmények**: a vezénylési végrehajtási előzményei. Ez a mező csak megadni, ha `showHistory` értéke `true`.
    
Ez a metódus visszaadja `null` , ha a példány nem létezik, vagy még nem kezdődött el futtatása.

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

Használhatja a `GetStatusAsync` metódus szoftverpéldányok vezénylési állapotának lekérdezése. Azt nem helyez el paramétereket, vagy megadhat egy `CancellationToken` abban az esetben, ha meg kívánja szakítani az objektumot. A metódus visszaadja objektumok ugyanazok a tulajdonságok, mint a `GetStatusAsync` paraméterekkel, kivéve azt metódus nem ad vissza az előzményeket. 

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

## <a name="terminating-instances"></a>Leállítja a példányok

Egy futó példány vezénylési használatával kell szüntetni a [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metódusában a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály. A két paraméter egy `instanceId` és egy `reason` karakterláncot, amely a naplókat, és a példány állapota lesz írva. A leállított példánya, amint a következő eléri le fog állni `await` pontot, vagy megszünteti az azonnal Ha már be egy `await`. 

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
> Jelenleg nem propagál példány megszüntetése. Tevékenység funkciók és alárendelt álló üzenettípusok összehangolását végrehajtása, függetlenül attól, hogy a vezénylési példány nevezett azokat le lett állítva.

## <a name="sending-events-to-instances"></a>Események küldése példányok

Eseményértesítések futó példányait használatával is küldhetők a [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metódusában a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály. Ezek az események kezelhető példányok megegyeznek hívása váró [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

A paraméterek [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) a következők:

* **InstanceId**: példány egyedi azonosítója.
* **EventName**: küldendő esemény neve.
* **EventData**: A szerializálható JSON adattartalom küldéséhez példányához.

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
> Ha nincs vezénylési példány a megadott *-példány azonosítója* , vagy ha a példány nem vár a megadott *eseménynév*, a rendszer törli az eseményüzenet. Ez a viselkedés kapcsolatos további információkért tekintse meg a [GitHub probléma](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Várakozás a befejezésre vezénylési

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály tesz elérhetővé a [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API, amely az beszerzése szinkron módon történik a tényleges kimeneti vezénylési példány is használható. Az alapértelmezett érték 10 másodperc, a metódusnak `timeout` és az 1 másodperc `retryInterval` ha azok nincsenek beállítva.  

Íme egy példa a HTTP-funkció, amely bemutatja, hogyan használható az API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

A függvény hívása a következő sorral 2-másodperces időtúllépési és 0,5 másodperces újrapróbálkozási időköz:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

A válasz lekérése a vezénylési példány időigényétől függően két olyan eset létezik:

1. A vezénylési példányok be a meghatározott idő (ebben az esetben 2 másodperc), a rendszer a választ, a tényleges vezénylési példány kimeneti kézbesíteni szinkron módon:

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

2. A vezénylési példányok nem tudja végrehajtani a megadott időkorláton (ebben az esetben 2 másodperc) belül, a rendszer a választ, az egyik ismertetett alapértelmezett **HTTP API URL-cím felderítési**:

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
> A webhook URL-címének formátuma eltérőek lehetnek attól függően, hogy az Azure Functions állomás verziójának. A fenti példában az Azure Functions 2.0 állomás van.

## <a name="retrieving-http-management-webhook-urls"></a>HTTP felügyeleti Webhook URL-címek lekérése

Külső rendszerek kommunikálhatnak a webhook URL-címeket, amelyek szerepelnek az alapértelmezés szerinti válasz ismertetett keresztül tartós funkciók [HTTP API URL-cím felderítési](durable-functions-http-api.md). Azonban a webhook URL-címeket is elérhető programozott módon a vezénylési ügyfél vagy egy tevékenység függvényben keresztül a [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) metódusában a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)osztály. 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) egy paraméterrel rendelkezik:

* **InstanceId**: példány egyedi azonosítója.

A metódus egy példányát adja vissza a [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) karakterlánc a következő tulajdonságokkal:

* **Azonosító**: a vezénylési Példányazonosítója (meg kell egyeznie a `InstanceId` bemeneti).
* **StatusQueryGetUri**: az állapot a vezénylési példány URL-CÍMÉT.
* **SendEventPostUri**: A "rhető esemény" a vezénylési példány URL-CÍMÉT.
* **TerminatePostUri**: A "leáll" példány URL-CÍMÉT a vezénylési.

Tevékenység funkciók küldhet példányának [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) a külső rendszerekkel figyelni, vagy az orchestration az események:

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
> [Például a felügyeleti HTTP API-k használata](durable-functions-http-api.md)
