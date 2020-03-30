---
title: Tartós funkciók áttekintése - Azure
description: Bevezetés az Azure Functions tartós függvények bővítményébe.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 5d454aefaba89bef9dc9009ff442fa5543dae2ef
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241345"
---
# <a name="what-are-durable-functions"></a>Mik azok a tartós függvények?

*A Durable Functions* az [Azure Functions bővítménye,](../functions-overview.md) amely lehetővé teszi az állapotalapú függvények írását kiszolgáló nélküli számítási környezetben. A bővítmény lehetővé teszi az állapotalapú munkafolyamatok definiálását [*az orchestrator-függvények*](durable-functions-orchestrations.md) és állapotalapú entitások írásával [*entitásfüggvények*](durable-functions-entities.md) írásával az Azure Functions programozási modell használatával. A színfalak mögött a bővítmény kezeli az állapotot, az ellenőrzőpontokat és az újraindításokat, lehetővé téve, hogy az üzleti logikára összpontosítson.

## <a name="supported-languages"></a><a name="language-support"></a>Támogatott nyelvek

A Durable Functions jelenleg a következő nyelveket támogatja:

* **C#**: [előre lefordított osztálykönyvtárak](../functions-dotnet-class-library.md) és [C# parancsfájlok](../functions-reference-csharp.md)is .
* **JavaScript:** csak az Azure Functions futásidejű 2.x-es verziójához támogatott. A Durable Functions bővítmény 1.7.0-s vagy újabb verzióját igényli. 
* **F#**: előre lefordított osztálykönyvtárak és F# parancsfájl. Az F# parancsfájl csak az Azure Functions futásidejű 1.x-verziójához támogatott.

A Durable Functions célja az Azure Functions összes [nyelvének](../supported-languages.md)támogatása. További nyelvek támogatásához tekintse meg a [Tartós funkciók problémák listáját](https://github.com/Azure/azure-functions-durable-extension/issues) a munka legújabb állapotáról.

Az Azure Functionshez hasonlóan vannak olyan sablonok, amelyek segítenek a Durable Functions fejlesztésében a [Visual Studio 2019](durable-functions-create-first-csharp.md), [a Visual Studio Code](quickstart-js-vscode.md)és az Azure [Portal](durable-functions-create-portal.md)használatával.

## <a name="application-patterns"></a>Alkalmazásminták

A tartós függvények elsődleges használati esete az összetett, állapotalapú koordinációs követelmények egyszerűsítése a kiszolgáló nélküli alkalmazásokban. A következő szakaszok a tartós függvények által használható tipikus alkalmazásmintákat ismertetik:

* [Függvényláncolás](#chaining)
* [Elosztás/összevonás](#fan-in-out)
* [Aszinkron HTTP API-k](#async-http)
* [Megfigyelő](#monitoring)
* [Emberi beavatkozás](#human)
* [Aggregátor (állapotalapú entitások)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Minta #1: Függvényláncolás

A függvényláncolási mintában a függvények sorozata egy adott sorrendben hajtható végre. Ebben a mintában az egyik függvény kimenete egy másik függvény bemenetére lesz alkalmazva.

![A függvényláncolási minta diagramja](./media/durable-functions-concepts/function-chaining.png)

A Tartós függvények segítségével a függvényláncolási minta tömören implementálható, ahogy az a következő példában látható.

Ebben a példában `F1` `F2`a `F3`, `F4` , és az ugyanabban a függvényalkalmazásban lévő többi függvény neve. A vezérlési folyamatot normál imperatív kódolási konstrukciók használatával valósíthatja meg. A kód felülről lefelé fut. A kód magában foglalhatja a meglévő nyelvi vezérlési folyamat szemantikáját, például a feltételes beállításokat és a hurkokat. A hibakezelési logikát `try` / `catch` / `finally` blokkokba is belefoglalhatja.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
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

A `context` paraméter segítségével más függvényeket is meghívhat név szerint, paramétereket adhat át, és függvénykimenetet adhat vissza. Minden alkalommal, `await`amikor a kód hív, a Durable Functions keretrendszer ellenőrzőpontok az aktuális függvénypéldány előrehaladását. Ha a folyamat vagy a virtuális gép a végrehajtás felénél `await` újrahasznosul, a függvénypéldány az előző hívásból folytatódik. További információ: A következő, Minta #2: Fan out/fan in.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Az `context.df` objektum segítségével más függvényeket is meghívhat név szerint, paramétereket adhat át, és függvénykimenetet adhat vissza. Minden alkalommal, `yield`amikor a kód hív, a Durable Functions keretrendszer ellenőrzőpontok az aktuális függvénypéldány előrehaladását. Ha a folyamat vagy a virtuális gép a végrehajtás felénél `yield` újrahasznosul, a függvénypéldány az előző hívásból folytatódik. További információ: A következő, Minta #2: Fan out/fan in.

> [!NOTE]
> A `context` JavaScript-objektum a teljes [függvénykörnyezetet](../functions-reference-node.md#context-object)jelöli. A Tartós funkciók környezet `df` elérése a tulajdonság használatával a fő környezetben.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Minta #2: Fan out / fan

A ventilátor ki / ventilátor minta, akkor végre több funkciót párhuzamosan, majd várja meg az összes funkciót befejezni. Gyakran előfordul, hogy néhány összesítési munkát végeznek a függvényekből visszaadott eredményeken.

![A ventilátor/ventilátor mintájának diagramja](./media/durable-functions-concepts/fan-out-fan-in.png)

A normál funkciók, akkor a ventilátor ki azáltal, hogy a funkció több üzenetet küld egy sorban. A visszafelé való visszaes és a nagy kihívás. A ventilátor, egy normál funkció, akkor írjon kódot nyomon követni, ha a várólista által aktivált függvények vége, majd tárolja a függvény kimeneteket.

A Durable Functions bővítmény viszonylag egyszerű kóddal kezeli ezt a mintát:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
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

A lelezési munka a `F2` funkció több példányára oszlik el. A munkát a feladatok dinamikus listájának segítségével követi nyomon a munka. `Task.WhenAll`az összes hívott függvény befejezésére kell várnia. Ezután `F2` a függvény kimenetek összesítve vannak a `F3` dinamikus feladatlistából, és átkerülnek a függvénybe.

Az automatikus ellenőrzőpontok, `await` amelyek `Task.WhenAll` a hívás nál történik, biztosítják, hogy egy esetleges félúton összeomlás vagy újraindítás nem igényel egy már befejezett feladat újraindítását.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

A lelezési munka a `F2` funkció több példányára oszlik el. A munkát a feladatok dinamikus listájának segítségével követi nyomon a munka. `context.df.Task.all`Api hívása, hogy várjon az összes hívott függvények befejezéséhez. Ezután `F2` a függvény kimenetek összesítve vannak a `F3` dinamikus feladatlistából, és átkerülnek a függvénybe.

Az automatikus ellenőrzőpontok, `yield` amelyek `context.df.Task.all` a hívás nál történik, biztosítják, hogy egy esetleges félúton összeomlás vagy újraindítás nem igényel egy már befejezett feladat újraindítását.

---

> [!NOTE]
> Ritka körülmények között előfordulhat, hogy egy összeomlás történhet az ablakban egy tevékenységfüggvény befejezése után, de befejezése előtt menti a vezénylési előzmények. Ha ez történik, a tevékenység függvény a folyamat helyreállítása után az elejétől újra fut.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Minta #3: Aszinkron HTTP API-k

Az async HTTP API-minta a hosszú ideig futó műveletek külső ügyfelekkel való koordinálásának problémáját orvosolja. A minta megvalósításának gyakori módja, hogy egy HTTP-végpont elindítja a hosszú ideig futó műveletet. Ezután irányítsa át az ügyfelet egy állapotvégpontra, amelyet az ügyfél lekérdezi, hogy megtudja, mikor fejeződik be a művelet.

![A HTTP API-minta diagramja](./media/durable-functions-concepts/async-http-api.png)

A Durable Functions **beépített támogatást** nyújt ehhez a mintához, leegyszerűsítve vagy akár eltávolítva az íráshoz szükséges kódot a hosszú ideig futó függvény-végrehajtások kezeléséhez. Például a Durable Functions rövid útmutató minták ([C#](durable-functions-create-first-csharp.md) és [JavaScript](quickstart-js-vscode.md)) egy egyszerű REST parancsot jelenít meg, amely új orchestrator függvénypéldányok indításához használható. Egy példány indítása után a bővítmény elérhetővé teszi a webhook HTTP API-kat, amelyek lekérdezik az orchestrator függvény állapotát. 

A következő példa azokat a REST-parancsokat mutatja be, amelyek elindítanak egy vezénylőt, és lekérdezik annak állapotát. Az egyértelműség érdekében néhány protokoll részlet kimaradt a példából.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Mivel a Tartós funkciók futásidejű kezeli az állapotot, nem kell megvalósítania a saját állapotkövetési mechanizmusát.

A Durable Functions bővítmény elérhetővé teszi a beépített HTTP API-kat, amelyek hosszú ideig futó vezényléseket kezelnek. Ezt a mintát saját függvényeseményindítók (például HTTP, várólista vagy Azure Event Hubs) és a [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client)használatával is megvalósíthatja. Például előfordulhat, hogy egy várólista-üzenetet használ a megszüntetés aktiválásához. Vagy használhat egy HTTP-eseményindítót, amelyet egy Azure Active Directory hitelesítési szabályzat véd a létrehozott kulcsot a hitelesítéshez létrehozott API-k helyett.

További információt a [HTTP-szolgáltatások](durable-functions-http-features.md) ról szóló cikkben talál, amely bemutatja, hogyan teheti elérhetővé az aszinkron, hosszú ideig futó folyamatokat HTTP-n keresztül a Durable Functions bővítmény használatával.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Minta #4: Monitor

A monitorminta egy rugalmas, ismétlődő folyamatra utal a munkafolyamatban. Erre példa a lekérdezés, amíg bizonyos feltételek nem teljesülnek. Használhat egy rendszeres [időzítő eseményindítót](../functions-bindings-timer.md) egy alapforgatókönyv, például egy időszakos törlési feladat, de az időköz statikus és a példány élettartamának kezelése bonyolulttá válik. A Tartós függvények segítségével rugalmas ismétlődési időközöket hozhat létre, kezelheti a feladatélettartamokat, és egyetlen vezénylési folyamatból több figyelési folyamatot hozhat létre.

A figyelőminta például a korábbi aszinkron HTTP API-forgatókönyv megfordítása. Ahelyett, hogy egy külső ügyfél számára végpontot tenné ki egy hosszú ideig futó művelet figyeléséhez, a hosszú ideig futó figyelő egy külső végpontot használ fel, majd megvárja az állapotváltozást.

![A diagram a monitor minta](./media/durable-functions-concepts/monitor.png)

Néhány sornyi kódban a Tartós függvények segítségével több, tetszőleges végpontokat megfigyelő figyelőt hozhat létre. A figyelők befejezhetik a végrehajtást, ha egy feltétel teljesül, vagy egy másik függvény használhatja a tartós vezénylési ügyfél a figyelők leállításához. A figyelő `wait` időköze egy adott feltétel (például exponenciális visszalépés) alapján módosítható.) 

A következő kód egy alapfigyelőt valósít meg:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
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

---

Amikor egy kérelem érkezik, egy új vezénylési példány jön létre az adott feladatazonosítóhoz. A példány lekérdezi az állapotot, amíg egy feltétel nem teljesül, és a hurok kilép. A tartós időzítő szabályozza a lekérdezési időközt. Ezután további munka végezhető el, vagy a vezénylés befejezheti. Ha `nextCheck` túllépi a monitort, `expiryTime`a monitor befejeződik.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Minta #5: Emberi interakció

Sok automatizált folyamatok jár valamilyen emberi interakció. Az emberek bevonása egy automatizált folyamatba bonyolult, mert az emberek nem olyan magas rendelkezésre állásúak és olyan érzékenyek, mint a felhőszolgáltatások. Egy automatizált folyamat időtúllépésre és kompenzációs logikára vonatkozó anamtalán lehetővé teszi ezt az interakciót.

A jóváhagyási folyamat egy példa egy olyan üzleti folyamatra, amely emberi interakciót foglal magában. Egy bizonyos dollárösszeget meghaladó költségjelentéshez szükség lehet egy menedzser jóváhagyására. Ha a menedzser nem hagyja jóvá a költségjelentést 72 órán belül (talán a menedzser nyaralni ment), egy eszkalációs folyamat indul, hogy valaki más (talán a menedzser vezetője) jóváhagyását kapja.

![A diagram az emberi interakció minta](./media/durable-functions-concepts/approval.png)

Ebben a példában a mintát egy orchestrator függvény használatával valósíthatja meg. Az orchestrator [egy tartós időzítőt](durable-functions-timers.md) használ a jóváhagyás kéréséhez. Az orchestrator eszkalálódik, ha időout történik. Az orchestrator megvárja a [külső eseményt,](durable-functions-external-events.md)például egy emberi interakció által létrehozott értesítést.

Ezek a példák egy jóváhagyási folyamatot hoznak létre az emberi interakciós minta bemutatására:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
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
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

A tartós időzítő létrehozásához hívja meg a hívást. `context.CreateTimer` Az értesítést a. `context.WaitForExternalEvent` Ezután `Task.WhenAny` a feladata annak eldöntése, hogy eszkalálódik-e (az időtúllépés történik először), vagy feldolgozza a jóváhagyást (a jóváhagyás az időtúllépés előtt érkezik).

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

A tartós időzítő létrehozásához hívja meg a hívást. `context.df.createTimer` Az értesítést a. `context.df.waitForExternalEvent` Ezután `context.df.Task.any` a feladata annak eldöntése, hogy eszkalálódik-e (az időtúllépés történik először), vagy feldolgozza a jóváhagyást (a jóváhagyás az időtúllépés előtt érkezik).

---

Egy külső ügyfél a [beépített HTTP API-k](durable-functions-http-api.md#raise-event)használatával kézbesítheti az eseményértesítést egy várakozó orchestrator-függvénynek:

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Egy esemény is fellehet vetni a tartós vezénylési ügyfél egy másik függvény ugyanabban a függvényalkalmazásban:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Minta #6: Aggregátor (állapotalapú entitások)

A hatodik minta az eseményadatok egy adott időszakon történő egyetlen, címezhető *entitásba*történő összesítéséről szól. Ebben a mintában az összesített adatok több forrásból származhatnak, kötegekben szállíthatók, vagy hosszú ideig szétszóródhatnak. Előfordulhat, hogy az összesítőnek meg kell tennie a műveletet az eseményadatokon, amint megérkezik, és előfordulhat, hogy külső ügyfeleknek kell lekérdezniük az összesített adatokat.

![Aggregátor diagram](./media/durable-functions-concepts/aggregator.png)

A trükkös dolog próbál végrehajtani ezt a mintát a normál, állapotmentes funkciók, hogy az egyidejűség ellenőrzése lesz egy hatalmas kihívás. Nem csak meg kell aggódnia több szál módosítja ugyanazokat az adatokat egy időben, akkor is kell aggódnia annak biztosítása, hogy az összesítő csak fut egy virtuális gép egy időben.

[A tartós entitások](durable-functions-entities.md) segítségével egyszerűen valósíthatja meg ezt a mintát egyetlen függvényként.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

A tartós entitások a .NET-ben osztályokként is modellezhetők. Ez a modell akkor lehet hasznos, ha a műveletek listája rögzített, és nagy lesz. A következő példa az entitás `Counter` egyenértékű implementációja .

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

Az ügyfelek várólistára vehetik az entitásfüggvény *(más* néven "jelző") műveleteit az [entitásügyfél-kötés](durable-functions-bindings.md#entity-client)használatával.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> A dinamikusan létrehozott proxyk a .NET-ben is elérhetők a jelentitások típusszintű jelzésére. És a jelzés mellett az ügyfelek is lekérdezheti az entitás függvény állapotát a vezénylési ügyfélkötési [típus-biztonságos módszerek](durable-functions-bindings.md#entity-client-usage) használatával.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

---

Az entitásfüggvények a [Durable Functions 2.0](durable-functions-versions.md) és újabb verziókban érhetők el.

## <a name="the-technology"></a>A technológia

A színfalak mögött a Durable Functions bővítmény a [Durable Task Framework](https://github.com/Azure/durabletask), egy nyílt forráskódú könyvtárra épül a GitHubon, amely a munkafolyamatok kódban való létrehozásához használatos. Az Azure Functions hez hasonlóan az Azure WebJobs kiszolgáló nélküli fejlődése is, a durable functions a tartós feladatkeretrendszer kiszolgáló nélküli fejlődése. A Microsoft és más szervezetek széles körben használják a Durable Task Framework-et az alapvető fontosságú folyamatok automatizálására. Ez egy természetes illeszkedés a kiszolgáló nélküli Azure Functions környezetben.

## <a name="code-constraints"></a>Kódmegkötések

Annak érdekében, hogy megbízható és hosszú ideig futó végrehajtási garanciákat, orchestrator függvények egy sor kódolási szabályokat, amelyeket be kell tartani. További információt az [Orchestrator függvénykódmegkötések](durable-functions-code-constraints.md) című cikkben talál.

## <a name="billing"></a>Számlázás

A tartós függvények számlázása megegyezik az Azure Functions számláján. További információ: [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/). Az Azure Functions [consumptionú megmaszási tervében](../functions-scale.md#consumption-plan)az orchestrator-függvények végrehajtásakor vannak olyan számlázási viselkedések, amelyeket figyelembe kell venni. Ezekről a viselkedésről további információt a [Durable Functions számlázási](durable-functions-billing.md) cikkben talál.

## <a name="jump-right-in"></a>Ugrás jobbra

A Tartós funkciók 10 perc alatt elkezdhetők a következő nyelvspecifikus rövid útmutatók kitöltésével:

* [C# a Visual Studio 2019 használatával](durable-functions-create-first-csharp.md)
* [JavaScript a Visual Studio-kód használatával](quickstart-js-vscode.md)

Mindkét rövid útmutatóban helyileg hozhat létre és tesztelheti a "hello world" tartós függvényt. Ezután közzéteheti a függvénykódot az Azure-ban. A létrehozott függvény vezénylések és láncok együtt hív más függvényeket.

## <a name="learn-more"></a>Részletek

A következő videó kiemeli a tartós funkciók előnyeit:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

A tartós funkciók ról és az alapul szolgáló technológiáról az alábbi videóban (a .NET-re összpontosít, de a fogalmak más támogatott nyelvekre is vonatkoznak):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Mivel a Durable Functions az [Azure Functions](../functions-overview.md)speciális bővítménye, nem minden alkalmazás számára megfelelő. A többi Azure vezénylési technológiával való összehasonlításért olvassa el [az Azure-függvények és az Azure Logic Apps összehasonlítása.](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tartós funkciók funkciótípusok és funkciók](durable-functions-types-features-overview.md)
