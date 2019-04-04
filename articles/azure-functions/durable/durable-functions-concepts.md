---
title: Durable Functions-minták és az Azure Functions technikai kulcsfogalmak
description: Ismerje meg, hogyan a Durable Functions bővítmény, az Azure Functions lehetővé teszi a felhőben állapot-nyilvántartó kódfuttatás előnyeit.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: e54fe17e80382348bcf463624043f7922a29d1c1
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892755"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Durable Functions-minták és technikai kulcsfogalmak (az Azure Functions)

Durable Functions bővítmény [Azure Functions](../functions-overview.md) és [Azure webjobs-feladatok](../../app-service/web-sites-create-web-jobs.md). Durable Functions használatával kiszolgáló nélküli környezetben állapot-nyilvántartó függvények írása. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat. 

Ez a cikk nyújt részletes információt tartalmaznak a működéséről a Durable Functions bővítmény az Azure Functions és a közös megvalósítási mintákat. Az információk segítségével eldöntheti, Durable Functions használatát a fejlesztési megoldásokkal kapcsolatos problémák megoldásához.

> [!NOTE]
> Durable Functions az Azure Functions, amely nem megfelelő összes alkalmazás speciális kiterjesztése. Ez a cikk feltételezi, hogy rendelkezik-e a fogalmak erős ismeretét [Azure Functions](../functions-overview.md) és kihívásai vesz részt a kiszolgáló nélküli alkalmazások fejlesztését.

## <a name="patterns"></a>Minták

Ez a szakasz ismerteti a gyakori alkalmazás minták ahol Durable Functions hasznos lehet.

### <a name="chaining"></a>#1. minta: Függvényláncolás

A minta-láncolás függvényben funkciók sorozatát egy adott sorrendben hajtja végre. Ebben a mintában egy függvény kimenete egy másik függvény a bemeneti van alkalmazva.

![A diagram a minta-láncolás függvény](./media/durable-functions-concepts/function-chaining.png)

Durable Functions segítségével megvalósítani a mintát a következő példában látható módon tömören láncolási függvény:

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
> Finom eltérések között található előre lefordított tartós függvények írásához C# található előre lefordított tartós függvények írásához és a C# parancsfájlt, amely a példában látható. Az egy C# függvény, előre lefordított tartós paraméterek rendelkeznie kell a megfelelő attribútumokat. Például a `[OrchestrationTrigger]` az attribútum a `DurableOrchestrationContext` paraméter. Az egy C# előre lefordított tartós funkciót, ha a paraméterek nem megfelelően kitüntetett, a futtatókörnyezet nem tudja a változók behelyezése a függvény és a hiba akkor fordul elő. További példákért lásd a [azure-functions-durable-extension példák a Githubon](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Ebben a példában az értékek `F1`, `F2`, `F3`, és `F4` más a függvényalkalmazás a függvények nevei. Átvitelvezérlés szerkezeteket kódolási normál imperatív használatával is alkalmazható. Kód felülről lefelé hajtja végre. A kód a meglévő nyelvi ellenőrzési folyamat szemantikát, például a feltételek és ciklusok is magában foglalhat. Hozzáadhatja a hibakezelési logika `try` / `catch` / `finally` blokkokat.

Használhatja a `context` paraméter [DurableOrchestrationContext] \(.NET\) és a `context.df` más függvények meghívása név alapján, adja át a paramétereket, és a függvény objektum (JavaScript) kimenet. Minden alkalommal, amikor a kód meghívja `await` (C#) vagy `yield` (JavaScript), a Durable Functions keretrendszer ellenőrzőpontokat a jelenlegi függvény-példány állapotát. Ha a folyamat vagy a virtuális gép újraindul a végrehajtási keresztül midway, a függvény példány folytatja az előző `await` vagy `yield` hívja. További információkért tekintse meg a következő szakaszban, a 2. minta: Ventilátor out/összegyűjtésének.

> [!NOTE]
> A `context` a JavaScript object jelenti. a teljes [függvény helyi](../functions-reference-node.md#context-object), nem csak a [DurableOrchestrationContext] paraméter.

### <a name="fan-in-out"></a>#2. minta: Ventilátor out/ventilátor

A ventilátor out/ventilátor mintában, hajtsa végre több funkciók párhuzamosan, és ezután Várjon, amíg befejeződik a függvények. Néhány összesítés gyakran az a függvények visszaadott eredmények alapján történik.

![A diagram a ventilátor out/ventilátor minta](./media/durable-functions-concepts/fan-out-fan-in.png)

A normál funkciók akkor is kézbesítenek több üzenetküldés egy üzenetsor funkciót ellátó által. Térjen vissza szellőztető sokkal nagyobb kihívást. Egy normál függvényben, ventilátor nyomon követheti az üzenetsor által aktivált függvények végén, és ezután store függvény kimenetek kódot írnia. 

A Durable Functions bővítmény kezeli ezt a mintát, viszonylag egyszerű kóddal:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

A logikájával munkahelyi több példánya van-e terjesztve a `F2` függvény. A munkahelyi feladatokat dinamikus listáját használja követi nyomon. A .NET `Task.WhenAll` API-t vagy a JavaScript `context.df.Task.all` API-t hívja meg, várjon, amíg a meghívott függvényeken befejezéséhez. Ezt követően a `F2` függvény kimenete összesítve a dinamikus lemezzé feladatütemezés listából, és átadni a `F3` függvény.

Az automatikus ellenőrzőpont-készítés, amely történik, ha a `await` vagy `yield` hívja meg `Task.WhenAll` vagy `context.df.Task.all` biztosítja, hogy egy potenciális középen összeomlása vagy újraindítás nem szükséges a már befejezett feladat újraindítása.

### <a name="async-http"></a>#3. minta: Az aszinkron HTTP API-k

Az aszinkron minta HTTP API-k a problémára a koordináló olyan külső ügyfelek hosszú ideig futó műveletek állapotának. A minta megvalósítása gyakori módja egy HTTP-trigger meghívása a hosszú ideig futó művelet egyik. Ezután átirányítja az ügyfelet egy állapot-végpontot, amely az ügyfél lekérdezi a további, miután befejezte a műveletet.

![A diagram a HTTP API-minta](./media/durable-functions-concepts/async-http-api.png)

Durable Functions biztosít beépített API-k, amelyek egyszerűbbé teszik a hosszú ideig futó függvénykivételek interakcióba írt kódot. Durable Functions rövid minták ([ C# ](durable-functions-create-first-csharp.md) és [JavaScript](quickstart-js-vscode.md)) egy egyszerű REST-parancs, amely segítségével indítsa el az új orchestrator-funkció példányok megjelenítése. Egy példány indítása után a bővítmény webhook HTTP API-k, amelyek az orchestrator függvény állapotát.%12%n%nLehetséges tesz elérhetővé. 

Az alábbi példa bemutatja, hogy indítsa el az orchestrator és állapotának lekérdezéséhez REST-parancsokkal. Az átláthatóság érdekében egyes részletei a példában nincs megadva.

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

Mivel a Durable Functions runtime állapotát kezeli, nem kell saját állapotát követő mechanizmus megvalósításához.

A Durable Functions bővítmény rendelkezik beépített webhookok, amelyek hosszú ideig futó vezénylések kezelni. Is meg, hogy ez a minta saját maga a saját függvény eseményindítók, (például HTTP-, üzenetsor, vagy az Azure Event Hubs) használatával, és a `orchestrationClient` kötést. Például használhat egy üzenetsor-üzenetet aktiválhat megszűnése. Vagy használhat a HTTP-trigger egy Azure Active Directory hitelesítési házirend helyett a beépített webhookok, amely a hitelesítéshez használni létrehozott kulcs által védett.

Íme néhány példa bemutatja, hogyan használja a HTTP API-mintát:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

> [!WARNING]
> Ha Ön helyi fejlesztés a JavaScript, a módszer használatához `DurableOrchestrationClient`, kell beállítania a környezeti változót `WEBSITE_HOSTNAME` való `localhost:<port>` (például `localhost:7071`). Ezzel a követelménnyel kapcsolatban további információkért lásd: [GitHub-problémát 28](https://github.com/Azure/azure-functions-durable-js/issues/28).

A .NET a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` paraméter értéke az a `orchestrationClient` kimeneti, kötelező, amely a Durable Functions bővítmény része. A JavaScript, ez az objektum hívás által visszaadott `df.getClient(context)`. Ezek az objektumok módszert használhat start, eseményeket küld, leállítása és lekérdezése az új vagy meglévő orchestrator függvény példányai adja meg.

A fenti példákban egy HTTP-eseményindítóval aktivált függvényt vesz igénybe egy `functionName` értéket a bejövő URL-címről, és átadja a értéket [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). A [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) API kötési majd választ, amely tartalmaz egy `Location` fejlécére, és a példánnyal kapcsolatos további információk. Használhatja az adatokat később keresse ki a lépések példány állapotát vagy a példány leáll.

### <a name="monitoring"></a>#4. minta: Figyelés

A figyelő a minta egy rugalmas, ismétlődő folyamat a munkafolyamat hivatkozik. Példa lekérdezési mindaddig, amíg az adott feltételek teljesülése. Használhatja a szokványos [időzítő eseményindító](../functions-bindings-timer.md) megoldása egy alapszintű forgatókönyv, mint a rendszeres karbantartási feladat, de az intervallumon statikus és összetett példány élettartam kezelésére válik. Durable Functions segítségével rugalmas ismétlődési időközök létre, kezelheti a feladat élettartam és egyetlen vezénylési több figyelő folyamatok létrehozása.

A figyelő a minta egy példát, hogy a korábbi aszinkron HTTP API-forgatókönyv fordított. Helyett egy végpontot egy külső ügyfél figyelése egy hosszú ideig futó művelet, a hosszú ideig futó figyelő használ egy külső végpont, és megvárja, állapotváltozás.

![A figyelő a minta egy ábrája](./media/durable-functions-concepts/monitor.png)

Néhány kódsorral, az Durable Functions segítségével hozzon létre több képernyő, amely tetszőleges végpontok figyelje meg. A figyelők fejezheti végrehajtása, ha teljesül egy feltétel, vagy a [DurableOrchestrationClient](durable-functions-instance-management.md) leállíthatja a figyelőket. Módosíthatja a figyelő `wait` intervallum alapján egy adott feltételnek (például exponenciális visszatartással.) 

A következő kódot egy alapszintű figyelő valósít meg:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

Amikor kérelem érkezik, egy új vezénylési példány jön létre a feladat azonosítóját. A példány állapota kérdezi le, amíg egy feltétel teljesül, és a hurok van kilépett. Tartós időzítő szabályozza a lekérdezési időközt. Ezt követően további munkát végezhet el, vagy fejezheti be a vezénylési. Ha a `context.CurrentUtcDateTime` (.NET) vagy `context.df.currentUtcDateTime` (JavaScript) meghaladja a `expiryTime` érték, a figyelő véget ér.

### <a name="human"></a>#5 a minta: Emberi beavatkozás

Automatizált folyamatok emberi beavatkozás járnak. Személyek nem magas rendelkezésre állásúként, és a rugalmas felhőalapú szolgáltatásként is érintő automatikus folyamat láthatják azért bonyolult feladat. Egy automatikus folyamat időtúllépések és kompenzációs logika használatával lehetővé teheti a.

Egy olyan jóváhagyási folyamatra, amelyek egy üzleti folyamat, amely magában foglalja az emberi beavatkozást igényel. Vezető jóváhagyásának lehet szükséges, amely meghalad egy bizonyos dollár összeget költségjelentés. Ha a kezelő nem hagyja jóvá a költségelszámolás (esetleg a kezelő szabadságon történt) 72 órán belül, egy eszkalációs folyamat a jóváhagyást kérhet valaki (valószínűleg a felettes felettesének) lép működésbe.

![Egy az emberi kommunikációs ábrája](./media/durable-functions-concepts/approval.png)

Ebben a példában egy orchestrator-funkció segítségével Megvalósíthat a minta. Az orchestrator használ egy [tartós időzítő](durable-functions-timers.md) jóváhagyás kérését. Az orchestrator kivizsgálásához, ha időtúllépés történik. Megvárja, amíg az orchestrator- [külső eseményre](durable-functions-external-events.md), például egy értesítést, amely emberi beavatkozás hozza létre.

Ezekben a példákban az emberi kommunikációs bemutatásához jóváhagyási folyamat létrehozása:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

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

A tartós időzítő létrehozásához hívja `context.CreateTimer` (.NET) vagy `context.df.createTimer` (JavaScript). Az értesítés érkezik `context.WaitForExternalEvent` (.NET) vagy `context.df.waitForExternalEvent` (JavaScript). Ezt követően `Task.WhenAny` (.NET) vagy `context.df.Task.any` (JavaScript) hívja meg eszkalálni kell-e (időtúllépés történik először) vagy feldolgozni a jóváhagyás (a jóváhagyás időkorlát lejárta előtt kapott meg).

Egy külső ügyfél közvetíti az eseményértesítés várakozási orchestrator függvény használatával a [beépített HTTP API-k](durable-functions-http-api.md#raise-event) vagy a [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API-ja egy másik függvényt:

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

## <a name="the-technology"></a>A technológia

A színfalak mögött a Durable Functions bővítmény épül fel a a [tartós feladat keretrendszer](https://github.com/Azure/durabletask), egy nyílt forráskódú kódtár, a Githubon, tartós feladat vezénylések létrehozásához használt. A LIKE Azure Functions kiszolgáló nélküli Azure webjobs-feladatok alakulását, Durable Functions a kiszolgáló nélküli alakulása tartós feladat keretében. A Microsoft és más szervezetek használható a tartós feladat keretrendszer nagymértékben automatizálhatja az üzleti szempontból alapvető fontosságú folyamatokat. Természetesen illeszkednek a az Azure Functions kiszolgáló nélküli környezetben.

### <a name="event-sourcing-checkpointing-and-replay"></a>Az Event sourcing, ellenőrzőpont és visszajátszás

Az orchestrator funkciók képesek megbízhatóan fenntartani a végrehajtási állapot használatával a [az event sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) tervezési minta alapján. Helyett közvetlenül az orchestration aktuális állapotát, a Durable Functions bővítmény segítségével egy csak hozzáfűzéssel bővíthető tár jegyezze fel a műveletek teljes sorozatát is a függvény vezénylési vesz igénybe. Egy csak hozzáfűzéssel bővíthető tár "való kiírása" teljes futásidejű állapot képest számos előnye van. Értékelemek közé tartozik a nagyobb teljesítmény, méretezhetőség és válaszképességét. Végleges konzisztencia esetében a tranzakciós adatokat, és teljes körű naplók és előzmények is kaphat. A naplók megbízható kompenzáló műveletek végrehajtását támogatja.

Durable Functions használ az események forráskezelése transzparens módon. A színfalak mögött a `await` (C#) vagy `yield` (JavaScript) operátor szerepel egy orchestrator-függvényt az orchestrator szál irányítását poskytne térjen vissza a tartós feladat keretrendszer dispatcher. A kézbesítő fel minden olyan új műveletek (például egy vagy több alárendelt függvények hívása, vagy egy tartós időzítő ütemezésének) ütemezett az orchestrator függvény ezután véglegesíti Storage. A transzparens véglegesítési művelet hozzáfűzi a vezénylési-példány futtatási előzményei. Az előzmények tárolása egy tárolótáblában. A véglegesítési művelet üzeneteket ad hozzá egy üzenetsorba, a tényleges feladatok ütemezéséhez. Ezen a ponton az orchestrator függvény memóriából lehet. 

Az orchestrator függvény számlázás használata az Azure Functions használatalapú tarifacsomagjának leáll. Ha szeretné elvégezni, a függvény újraindul, több munkát és állapotában újraépíti.

Ha egy vezénylési függvény van megadva ehhez további munkahelyi (például egy válaszüzenetet érkezik, vagy egy tartós időzítő lejár), az orchestrator felébred, és újból végrehajtja a helyi állapot újraépítése a kezdetektől a teljes függvény. 

Során a visszajátszás, ha a kód megpróbálja meghívni a függvényt (vagy bármely más aszinkron munkahelyi), tartós feladat keretében consults szintűre frissül az aktuális vezénylési végrehajtási előzményei. Ha úgy találja, hogy a [tevékenység függvény](durable-functions-types-features-overview.md#activity-functions) már végrehajtott és kurzorműveletnek eredményt, azt visszajátssza a függvény eredménye és az orchestrator kód továbbra is fusson. Ismétlés továbbra is fennáll, amíg befejeződik a függvénykódot, vagy új aszinkron során van ütemezve.

### <a name="orchestrator-code-constraints"></a>Az orchestrator kód megkötései

Az orchestrator kód visszajátszását viselkedését a kódot, amely egy orchestrator-függvény írhat típusú megkötéseket hoz létre. Például az orchestrator-kódot kell determinisztikus, mert azt fogja megismétlését többször is feldolgozza, és az azt kell ugyanaz az eredmény minden alkalommal, amikor. Korlátok teljes listáját lásd: [Orchestrator kód megkötések](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

A Durable Functions bővítmény automatikusan bocsát ki strukturált nyomon követési adatok [Application Insights](../functions-monitoring.md) Ha úgy állítja be a függvényalkalmazást és egy Azure Application Insights-kialakítási kulcsot. Használhatja a nyomkövető adatokat a műveletek és a vezénylések előrehaladásának figyeléséhez.

Íme egy példa a követési események Durable Functions kinézni az Application Insights portál használata esetén [Application Insights-elemzési](../../application-insights/app-insights-analytics.md):

![Application Insights-lekérdezés eredményei](./media/durable-functions-concepts/app-insights-1.png)

Strukturált adatok hasznosnak találja a `customDimensions` minden naplóbejegyzés a mezőt. Íme egy példa, amely teljes mértékben ki van bontva bejegyzés:

![Egy Application Insights-lekérdezés a customDimensions mezője](./media/durable-functions-concepts/app-insights-2.png)

A tartós feladat keretrendszer dispatcher visszajátszását viselkedését miatt várható megismételt műveletek redundáns naplóbejegyzés megtekintéséhez. Redundáns naplóbejegyzések a visszajátszás viselkedése a core-motor megismerésében is segítenek. A [diagnosztikai](durable-functions-diagnostics.md) a cikk bemutatja, amely kiszűrhetők a visszajátszás naplókat, így láthatja, hogy csak a "valós idejű" naplók mintalekérdezések.

## <a name="storage-and-scalability"></a>Tárolás és méretezhetőség

A Durable Functions bővítmény az Azure Storage-blobok, táblák és üzenetsorok végrehajtási előzmények állapot és az eseményindító függvény végrehajtása megőrzéséhez használja. A függvényalkalmazás az alapértelmezett tárfiókot is használhat, vagy konfigurálhatja egy önálló tárfiókot. Érdemes lehet egy külön fiókot, a tárolási teljesítmény korlátok alapján. Az orchestrator kód írása az entitásokat a ezeket a storage-fiókok nem dolgozhat. A tartós feladat keretrendszer közvetlenül egy megvalósítási részletei, az entitások kezeli.

Az orchestrator funkciók tevékenységfüggvényeket ütemezés, és azok belső üzenetsorbeli üzenetek keresztül válaszokat kaphatnak. Ha egy függvényalkalmazáshoz az Azure Functions használatalapú csomagban fut a [Azure Functions méretezési vezérlő](../functions-scale.md#how-the-consumption-and-premium-plans-work) ezek a várólisták figyeli. Új számítási példányok kerülnek, igény szerint. Horizontálisan felskálázott több virtuális gépet, ha egy orchestrator-függvényt egy virtuális gép, amelyek az orchestrator függvényhívások számos különböző gépeken futhatnak tevékenységfüggvényeket során előfordulhat, hogy futtassa. Durable Functions, a méretezési csoport működésével kapcsolatos további információkért lásd: [teljesítmény és méretezhetőség](durable-functions-perf-and-scale.md).

A futtatási előzményei, az orchestrator-fiókok esetében a table storage tárolja. Minden alkalommal, amikor egy példányt az egy adott virtuális gép rehydrates, az orchestrator olvas be a futtatási előzményei a table storage helyi állapotában újraépítését, így. Egy kényelmes szempont, hogy a table storage-ban elérhető előzmények, hogy használhatja-e és hasonló eszközökkel való [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) a vezénylések előzményeinek megtekintéséhez.

Storage-blobokat elsősorban bérlési mechanizmus, a horizontális felskálázási példányok vezénylési koordinálására több virtuális gép között. Storage-blobokat tárolják az adatokat nem lehet tárolni, közvetlenül a táblák és üzenetsorok nagy méretű üzenetek esetében.

![Azure Storage Explorer képernyőképe](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> Bár könnyen és kényelmesen megtekintéséhez a futtatási előzményei, table storage-ban, ne függőségek a táblánál. A tábla lehet módosítani, mert a Durable Functions bővítmény haladásával.

## <a name="known-issues"></a>Ismert problémák

Az összes ismert problémák nyomon követése a [GitHub-problémák](https://github.com/Azure/azure-functions-durable-extension/issues) listája. Ha problémába ütközik, és nem találja a problémát a Githubban, nyisson meg egy új problémát. A hiba részletes leírását tartalmazza.

## <a name="next-steps"></a>További lépések

Durable Functions kapcsolatos további információkért lásd: [Durable Functions típusok és a szolgáltatások működéséhez](durable-functions-types-features-overview.md). 

Első lépések:

> [!div class="nextstepaction"]
> [Az első tartós függvény létrehozása](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
