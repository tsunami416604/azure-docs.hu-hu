---
title: Durable Functions minták és műszaki fogalmak a Azure Functions
description: Ismerje meg, hogy a Azure Functions Durable Functions bővítménye milyen előnyökkel jár a Felhőbeli állapot-nyilvántartó programkódok végrehajtásában.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 828bcaa8c93454ba845c30c03c76144310891123
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098254"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Durable Functions minták és technikai fogalmak (Azure Functions)

Durable Functions [Azure functions](../functions-overview.md) és [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md)kiterjesztését. A Durable Functions használatával állapot-nyilvántartó függvényeket írhat kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat. 

Ez a cikk részletes információkat tartalmaz a Azure Functions és az általános megvalósítási minták Durable Functions-bővítményének viselkedéséről. Az információ segítségével meghatározhatja, hogyan használhatók a Durable Functions a fejlesztési problémák megoldásához.

> [!NOTE]
> A Durable Functions olyan Azure Functions speciális bővítménye, amely nem felel meg az összes alkalmazásnak. Ez a cikk azt feltételezi, hogy alapos ismerettel rendelkezik a [Azure functions](../functions-overview.md) kapcsolatos fogalmakkal és a kiszolgáló nélküli alkalmazások fejlesztésében felmerülő problémákkal kapcsolatban.

## <a name="patterns"></a>Minták

Ez a szakasz néhány gyakori alkalmazási mintát ismertet, ahol a Durable Functions hasznos lehet.

### <a name="chaining"></a>Minta #1: Függvényláncolás

A függvény láncolása mintában a függvények sorrendje egy adott sorrendben fut le. Ebben a mintában egy függvény kimenetét egy másik függvény bemenetére alkalmazza a rendszer.

![A függvény láncolási mintájának ábrája](./media/durable-functions-concepts/function-chaining.png)

Az Durable Functions használatával a függvény láncolása minta tömören valósítható meg, ahogy az alábbi példában is látható:

#### <a name="c-script"></a>C#-szkript

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> Az előre lefordított tartós függvények írása C# és az előre lefordított tartós függvények írása között finom különbségek vannak a C# példában látható parancsfájlban. Egy C# előre lefordított függvényben a tartós paramétereket megfelelő attribútumokkal kell megdíszíteni. Ilyen például a `[OrchestrationTrigger]` `DurableOrchestrationContext` paraméter attribútuma. Egy C# előre lefordított tartós függvényben, ha a paraméterek nincsenek megfelelően dekorálva, a futásidejű nem tudja beszúrni a változókat a függvénybe, és hiba történik. További példákért tekintse [meg az Azure-functions-tartós kiterjesztésű mintákat a githubon](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Ebben a példában a függvény alkalmazásban `F2`az `F3`értékek `F1`, `F4` a, és a más függvények nevei szerepelnek. A vezérlési folyamat normál, kötelező kódolási szerkezetek használatával valósítható meg. A kód felülről lefelé fut. A kód a meglévő nyelvi vezérlési folyamatokat, például a feltételes és a hurkokat is magában foglalja. A `try` blokkokban a `catch` /hibákkezeléséreszolgáló logikaisfelvehető/. `finally`

A `context` [DurableOrchestrationContext] .net\)és az Object`context.df` (JavaScript) paraméterrel más függvényeket hívhat meg név, pass paraméterek és Return Function kimenet használatával. \( Minden alkalommal, amikor a `await` kódC#meghívja `yield` () vagy (JavaScript), a Durable functions Framework ellenőrzőpontja az aktuális függvény példányának előrehaladását. Ha a folyamat vagy a virtuális gép a végrehajtás során újra újraindul, a függvény példánya az előző `await` vagy `yield` a hívásból folytatódik. További információkért lásd a következő, minta #2: Ventilátor ki-és bevezetését.

> [!NOTE]
> A `context` JavaScriptben lévő objektum a teljes [függvény kontextusát](../functions-reference-node.md#context-object)jelenti, nem csak a [DurableOrchestrationContext] paramétert.

### <a name="fan-in-out"></a>Minta #2: Ventilátor

A fan out/Fan in mintában több függvényt hajt végre párhuzamosan, majd várjon, amíg az összes függvény befejeződik. Gyakran előfordul, hogy egyes összesítési műveletek a függvények által visszaadott eredményeken is megtörténik.

![A ventilátor kikapcsolási/ventilátor mintázatának ábrája](./media/durable-functions-concepts/fan-out-fan-in.png)

A normál függvények segítségével kipróbálhatja, hogy a függvény több üzenetet küld egy várólistára. A Fanning sokkal nagyobb kihívást jelent. Ahhoz, hogy egy normál függvényben bekapcsolja a ventilátort, kódot írhat, amely nyomon követheti a várólista által aktivált függvények befejezését, majd tárolhatja a függvény kimeneteit. 

A Durable Functions bővítmény ezt a mintát viszonylag egyszerű kóddal kezeli:

#### <a name="c-script"></a>C#-szkript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

A rendszer a `F2` függvény több példányára terjeszti a kivezetési munkát. A rendszer a feladatok dinamikus listájának használatával követi nyomon a munkát. A rendszer `Task.WhenAll` meghívja a `context.df.Task.all` .NET API-t vagy a JavaScript API-t, hogy megvárja az összes meghívott függvény befejeződését. Ezután a rendszer `F2` összesíti a függvény kimeneteit a dinamikus feladatlistából, és átadja a `F3` függvénynek.

Az automatikus `await` ellenőrzőpontok, amelyek a vagy `yield` a hívásakor `Task.WhenAll` történnek, vagy `context.df.Task.all` biztosítják, hogy egy lehetséges Midway-összeomlás vagy-újraindítás nem igényli a már befejezett feladatok újraindítását.

### <a name="async-http"></a>Minta #3: Aszinkron HTTP API-k

Az aszinkron HTTP API-minta a hosszan futó műveletek állapotának a külső ügyfelekkel való koordinálásával kapcsolatos problémát tárgyalja. Ennek a mintának a megvalósításának általános módja, ha egy HTTP-hívás aktiválja a hosszan futó műveletet. Ezt követően irányítsa át az ügyfelet egy olyan állapot-végpontra, amelyet az ügyfél lekérdez, hogy megtudja, mikor fejeződött be a művelet.

![A HTTP API mintájának ábrája](./media/durable-functions-concepts/async-http-api.png)

A Durable Functions beépített API-kat biztosít, amelyek leegyszerűsítik a hosszú ideig futó függvények végrehajtásához szükséges kódot. A Durable Functions gyors üzembe helyezési minták ([C#](durable-functions-create-first-csharp.md) és [JavaScript](quickstart-js-vscode.md)) egy egyszerű Rest-parancsot mutatnak be, amelyet az új Orchestrator-függvények indításához használhat. Egy példány elindítása után a bővítmény elérhetővé teszi a Orchestrator függvény állapotát lekérdező webhook HTTP API-kat. 

Az alábbi példa olyan REST-parancsokat mutat be, amelyek elindítják a Orchestrator, és lekérdezik az állapotát. Az érthetőség kedvéért egyes részletek kimaradnak a példától.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Mivel az Durable Functions futtatókörnyezet kezeli az állapotot, nincs szükség saját állapot-követési mechanizmus megvalósítására.

A Durable Functions bővítmény olyan beépített webhookokkal rendelkezik, amelyek a hosszan futó munkafolyamatokat kezelik. Ezt a mintát saját függvény-eseményindítók (például http, üzenetsor vagy Azure Event Hubs) és a `orchestrationClient` kötés használatával is megvalósíthatja. Előfordulhat például, hogy üzenetsor-üzenetet használ a megszakítás elindításához. Vagy használhat egy Azure Active Directory hitelesítési házirend által védett HTTP-triggert olyan beépített webhookok helyett, amelyek generált kulcsot használnak a hitelesítéshez.

Íme néhány példa a HTTP API-minta használatára:

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

A .net-ben a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` paraméter a `orchestrationClient` kimeneti kötésből származó érték, amely a Durable functions bővítmény részét képezi. A JavaScriptben ezt az objektumot hívja `df.getClient(context)`vissza. Ezek az objektumok olyan metódusokat biztosítanak, amelyek segítségével elindíthatja, elküldheti az eseményeket az új vagy meglévő Orchestrator függvényekhez.

Az előző példákban egy http által aktivált függvény a beérkező URL- `functionName` cím értékét veszi át, és átadja az értéket a [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)értékre. A [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) kötési API ezután egy olyan választ ad vissza, `Location` amely tartalmaz egy fejlécet és további információkat a példányról. A később megjelenő információk alapján megkeresheti az elindított példány állapotát, vagy megszakíthatja a példányt.

### <a name="monitoring"></a>Minta #4: Figyelés

A figyelő minta egy rugalmas, ismétlődő folyamatra hivatkozik egy munkafolyamatban. Egy példa egy lekérdezésre, amíg az adott feltételek teljesülnek. Egy alapszintű forgatókönyv [](../functions-bindings-timer.md) , például egy rendszeres karbantartási feladat, az intervallum statikus, a példányok élettartamának kezelése pedig összetett lesz. A Durable Functions használatával rugalmas ismétlődési időközöket hozhat létre, kezelheti a feladatok élettartamát, és több figyelő folyamat is létrehozható egyetlen előkészítéssel.

A figyelő minta példája a korábbi aszinkron HTTP API-forgatókönyv fordítottja. Ahelyett, hogy egy külső ügyfél végpontját kitéve egy hosszan futó művelet figyelésére, a hosszan futó figyelő külső végpontot használ, majd megvárja az állapot változását.

![A figyelő mintájának ábrája](./media/durable-functions-concepts/monitor.png)

Néhány sornyi kódban a Durable Functions használatával több, tetszőleges végpontokat figyelő figyelőt hozhat létre. A figyelők a feltételek teljesülése esetén a végrehajtás végén, vagy a [DurableOrchestrationClient](durable-functions-instance-management.md) megszakítják a figyelőket. Egy adott feltétel alapján módosíthatja a `wait` figyelő intervallumát (például exponenciális leállítási.) 

A következő kód egy alapszintű figyelőt valósít meg:

#### <a name="c-script"></a>C#-szkript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

A kérés fogadásakor a rendszer létrehoz egy új előkészítési példányt az adott AZONOSÍTÓJÚ feladatokhoz. A példány lekérdezi az állapotot, amíg a feltétel teljesül, és a hurok ki van zárva. A tartós időzítő vezérli a lekérdezési időközt. Ezt követően több munka is elvégezhető, vagy az előkészítés véget ért. Ha a `context.CurrentUtcDateTime` (.net) vagy `context.df.currentUtcDateTime` (JavaScript) érték meghaladja `expiryTime` a értéket, a figyelő véget ér.

### <a name="human"></a>Minta #5: Emberi beavatkozás

Számos automatizált folyamat tartalmaz valamilyen emberi interakciót. Az emberek automatikus folyamatba való bevonása trükkös, mert az emberek nem a lehető legszélesebb körben elérhetők és a Cloud Services-ként reagálnak Az automatikus folyamat az időtúllépések és a kompenzációs logika használatával is engedélyezhető.

A jóváhagyási folyamat olyan üzleti folyamatra mutat példát, amely emberi interakciót is magában foglal. Előfordulhat, hogy a felettes jóváhagyása egy olyan Költségjelentés esetében szükséges, amely meghaladja az adott dollár mennyiségét. Ha a kezelő nem hagyja jóvá a költségjelentés 72 órán belül történő jóváhagyását (lehet, hogy a felettes a vakáción ment), a eszkalációs folyamat beolvassa a-t, hogy valaki más (például a felettes felettese) jóváhagyást kapjon.

![Az emberi interakció mintájának ábrája](./media/durable-functions-concepts/approval.png)

Ebben a példában egy Orchestrator függvény használatával valósítható meg a minta. A Orchestrator [tartós időzítőt](durable-functions-timers.md) használ a jóváhagyás kéréséhez. Ha időtúllépés történik, a Orchestrator megnőnek. A Orchestrator egy [külső eseményt](durable-functions-external-events.md)vár, például egy emberi interakció által generált értesítést.

Ezek a példák jóváhagyási folyamatot hoznak létre az emberi interakciós minta bemutatásához:

#### <a name="c-script"></a>C#-szkript

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Tartós időzítő, hívás `context.CreateTimer` (.net) vagy `context.df.createTimer` (JavaScript) létrehozásához. Az értesítést a `context.WaitForExternalEvent` (.net) vagy `context.df.waitForExternalEvent` a (JavaScript) fogadja. Ezt követően a `context.df.Task.any` `Task.WhenAny` (.net) vagy (JavaScript) metódust kell eldöntenie, hogy megtörténjen-e a kiterjesztés (időtúllépés történik), vagy dolgozza fel a jóváhagyást (a jóváhagyás az időkorlát előtt érkezik).

Egy külső ügyfél a [beépített http API](durable-functions-http-api.md#raise-event) -kkal vagy a [DurableOrchestrationClient. RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API-val egy másik függvény használatával kézbesítheti az esemény-értesítést a várakozó Orchestrator függvénynek:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

### <a name="aggregator"></a>Minta #6: Összesítő (előzetes verzió)

A hatodik minta az események adatainak egy adott, címezhető *entitásba*való összesítésére szolgál. Ebben a mintában az összesíteni kívánt adatok több forrásból származhatnak, a kötegekben is elhelyezhetők, vagy hosszú időn keresztül elszórtan lehetnek. Előfordulhat, hogy a gyűjtőnek műveleteket kell végeznie az események érkezésekor, és előfordulhat, hogy a külső ügyfeleknek le kell kérdezni az összesített adatokat.

![Összesítő diagram](./media/durable-functions-concepts/aggregator.png)

A minta a normál, állapot nélküli függvények használatával történő megvalósítására tett kísérlet során az, hogy a Egyidejűség-vezérlés óriási kihívás lesz. Nem csupán annyit kell aggódnia, hogy ugyanazokat az információkat egyszerre több szálra módosítja, azonban azt is meg kell aggódnia, hogy a gyűjtő egyszerre csak egyetlen virtuális gépen fut.

Egy [tartós entitás függvény](durable-functions-preview.md#entity-functions)használatával egyetlen függvényként egyszerűen megvalósíthatja ezt a mintát.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

A tartós entitások .NET-osztályként is modellezésre használhatók. Ez akkor lehet hasznos, ha a műveletek listája nagy lesz, és ha többnyire statikus. A következő példa az `Counter` entitás egyenértékű implementációját használja .net-osztályok és-metódusok használatával.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Az ügyfelek a `orchestrationClient` kötés használatával sorba helyezni műveleteket (más néven "jelzést") az entitások számára.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

A dinamikusan generált proxyk is elérhetők az entitások típus-biztonságos módon történő jelzéséhez. Továbbá a jelzéseken kívül az ügyfelek a `orchestrationClient` kötés metódusait használva is lekérhetik az entitások állapotát.

> [!NOTE]
> Az Entity functions jelenleg csak a [Durable Functions 2,0 előzetes](durable-functions-preview.md)verzióban érhető el.

## <a name="the-technology"></a>A technológia

A színfalak mögött a Durable Functions bővítmény a [tartós feladatok keretrendszerére](https://github.com/Azure/durabletask)épül, amely egy nyílt forráskódú, a githubon lévő, tartós feladatok előkészítésére szolgáló könyvtár. Például a Azure Functions a Azure WebJobs kiszolgáló nélküli fejlődése, Durable Functions a tartós feladat-keretrendszer kiszolgáló nélküli fejlődése. A Microsoft és más szervezetek széles körben használják a tartós feladatok keretrendszerét a kritikus fontosságú folyamatok automatizálására. A kiszolgáló nélküli Azure Functions környezet számára természetes módon illeszkedik.

### <a name="event-sourcing-checkpointing-and-replay"></a>Események beszerzése, ellenőrzőpontok és visszajátszás

Az Orchestrator függvények az [esemény](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) -beszerzések kialakítási mintája alapján megbízhatóan karbantartják végrehajtási állapotukat. A folyamat aktuális állapotának közvetlen tárolása helyett a Durable Functions-bővítmény egy csak Hozzáfűzéses tárolót használ a függvények összehangolása által végrehajtott műveletek teljes sorozatának rögzítéséhez. A csak Hozzáfűzéses tároló számos előnnyel jár, mint a teljes futtatókörnyezet állapotának "kiírása". Az előnyök többek között a teljesítmény, a méretezhetőség és a rugalmasság. A tranzakciós és a teljes naplózási nyomvonalak és előzmények végleges konzisztenciáját is biztosítjuk. A naplózási nyomvonalak támogatják a megbízható kompenzáló műveleteket.

A Durable Functions az események beszerzését transzparens módon használja. A színfalak mögött a `await` (C#) vagy `yield` a (JavaScript) operátor egy Orchestrator függvényben a Orchestrator-szál vezérlését eredményezi a tartós feladatokhoz. A diszpécser ezután véglegesít minden olyan új műveletet, amelyet a Orchestrator függvény ütemez (például egy vagy több alárendelt függvény hívása vagy tartós időzítő ütemezése) a tárolóba. Az átlátszó véglegesítő művelet hozzáfűzi a rendszerelőkészítési példány végrehajtási előzményeihez. Az előzmények tárolása egy tárolási táblában történik. A commit művelet ezután üzeneteket hoz létre egy várólistához a tényleges munka időzítése érdekében. Ezen a ponton a Orchestrator függvény eltávolítható a memóriából. 

A Orchestrator függvény számlázása leáll, ha a Azure Functions-felhasználási tervet használja. Ha további tennivalói vannak, a függvény újraindul, és a rendszer újraépíti az állapotát.

Ha egy összehangoló függvény több munkát tesz elérhetővé (például válaszüzenet érkezik vagy tartós időzítő lejár), a Orchestrator felébred, és újból végrehajtja a teljes függvényt az elejétől a helyi állapot újraépítéséhez. 

Ha a kód megpróbál meghívni egy függvényt (vagy bármilyen más aszinkron munkát hajt végre), akkor az állandó feladat-keretrendszer az aktuális előkészítés végrehajtási előzményeit kérdezi le. Ha úgy találja, hogy a [tevékenységi függvény](durable-functions-types-features-overview.md#activity-functions) már végre lett hajtva, és eredményként eredményezte, akkor az a függvény eredményét játssza le, és a Orchestrator-kód továbbra is futni fog. A visszajátszás addig folytatódik, amíg a függvény kódja be nem fejeződik, vagy amíg be nem ütemezi az új aszinkron munkát.

### <a name="orchestrator-code-constraints"></a>Orchestrator-kód megkötései

A Orchestrator kód újrajátszása a Orchestrator függvényben írható kód típusára vonatkozó korlátozásokat hoz létre. A Orchestrator-kódnak például determinisztikus kell lennie, mert többször is le kell játszani, és minden alkalommal ugyanazt az eredményt kell létrehoznia. A megkötések teljes listáját lásd: [Orchestrator-kódok](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints)megkötései.

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

A Durable Functions bővítmény automatikusan kibocsátja a strukturált követési adatait, hogy [Application Insights](../functions-monitoring.md) , ha a Function alkalmazást Azure Application Insights kialakítási kulccsal állítja be. A nyomkövetési adataival figyelheti a fellépéseinek műveleteit és előrehaladását.

Az alábbi példa azt szemlélteti, hogy a Durable Functions követési események hogyan jelennek meg a Application Insights portálon a [Application Insights Analytics](../../application-insights/app-insights-analytics.md)használatakor:

![Application Insights lekérdezés eredményei](./media/durable-functions-concepts/app-insights-1.png)

Az `customDimensions` egyes naplóbejegyzések mezőben hasznos strukturált adat található. Az alábbi példa egy teljesen kibontott bejegyzést mutat be:

![A customDimensions mező egy Application Insights lekérdezésben](./media/durable-functions-concepts/app-insights-2.png)

A tartós feladatokhoz készült Framework-diszpécser újrajátszása miatt várhatóan megtekintheti a visszajátszott műveletek redundáns naplójának bejegyzéseit. A redundáns naplóbejegyzések segítségével megismerheti az alapszintű motor újrajátszása viselkedését. A [diagnosztikai](durable-functions-diagnostics.md) cikk azokat a lekérdezéseket jeleníti meg, amelyek kiszűrik a Visszajátszási naplókat, így csak a "valós idejű" naplókat láthatja.

## <a name="storage-and-scalability"></a>Tárolás és méretezhetőség

Az Durable Functions bővítmény az Azure Storage-ban várólistákat, táblákat és blobokat használ a végrehajtási előzmények állapotának megőrzéséhez és a függvények végrehajtásához. Használhatja az alapértelmezett Storage-fiókot a Function alkalmazáshoz, vagy beállíthat egy különálló Storage-fiókot is. Előfordulhat, hogy külön fiókot szeretne használni a tárolási átviteli sebesség korlátai alapján. Az Ön által írt Orchestrator-kód nem kommunikál az ezekben a Storage-fiókokban lévő entitásokkal. Az állandó feladathoz tartozó keretrendszer az entitásokat közvetlenül implementációs részletességgel kezeli.

A Orchestrator függvények ütemezhetik a tevékenység-és a válaszokat a belső üzenetsor üzenetein keresztül. Amikor egy Function alkalmazás fut a Azure Functions használati tervben, a [Azure functions skálázási vezérlő](../functions-scale.md#how-the-consumption-and-premium-plans-work) figyeli ezeket a várólistákat. Szükség szerint új számítási példányok lesznek hozzáadva. Ha több virtuális gépre bővíti a méretezést, egy Orchestrator-függvény futhat egy virtuális gépen, míg a Orchestrator függvény által meghívást igénylő tevékenység-függvények több különböző virtuális gépen is futhatnak. A Durable Functions méretezési viselkedésével kapcsolatos további információkért lásd: [teljesítmény és skálázás](durable-functions-perf-and-scale.md).

A Orchestrator-fiókok végrehajtási előzményeit a Table Storage tárolja. Ha egy példány egy adott virtuális gépen kiszárad, a Orchestrator beolvassa a táblázatos tárolóból a végrehajtási előzményeit, hogy újra tudja építeni a helyi állapotát. A Table Storage-ban elérhető előzmények egyik kényelmes aspektusa az, hogy az eszközök, például a [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) segítségével megtekintheti az előkészítési előzményeket.

A Storage-Blobok elsődlegesen olyan lízing mechanizmusként használatosak, amely összehangolja a több virtuális gépre kiterjedő Felskálázási példányokat. A tárolási Blobok olyan nagyméretű üzenetek tárolására szolgálnak, amelyek nem tárolhatók közvetlenül a táblákban vagy a várólistákban.

![Azure Storage Explorer képernyőképe](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Bár a táblázatos tárolásban egyszerűen és kényelmesen láthatja a végrehajtási előzményeket, ne végezzen függőségeket ezen a táblán. Előfordulhat, hogy a táblázat Durable Functions bővítményének fejlődése megváltozik.

## <a name="known-issues"></a>Ismert problémák

Az összes ismert problémát nyomon kell követni a [GitHub-problémák](https://github.com/Azure/azure-functions-durable-extension/issues) listájában. Ha probléma lép fel, és a GitHubon nem találja a problémát, nyisson meg egy új problémát. Adja meg a probléma részletes leírását.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Durable Functionsről, tekintse meg a [Durable functions függvények típusai és funkciói](durable-functions-types-features-overview.md)című témakört. 

Első lépések:

> [!div class="nextstepaction"]
> [Az első tartós függvény létrehozása](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
