---
title: Durable Functions áttekintése – Azure
description: Az Azure Functions szolgáltatáshoz a Durable Functions bővítmény bemutatása.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/7/2018
ms.author: azfuncdf
ms.openlocfilehash: 14e959e4aa26b04ec70cbb03ea3feaf0e93f31c1
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344176"
---
# <a name="durable-functions-overview"></a>Durable Functions áttekintése

*Durable Functions* kiterjesztése [Azure Functions](../functions-overview.md) és [Azure webjobs-feladatok](../../app-service/web-sites-create-web-jobs.md) , amellyel írási állapot-nyilvántartó functions egy kiszolgáló nélküli környezetben. A bővítmény kezeli a állapot, ellenőrzőpontok és újraindul az Ön számára.

A bővítmény lehetővé teszi, hogy egy új típusú nevű függvény az állapot-nyilvántartó munkafolyamatok meghatározhatja egy [ *vezérlőfüggvény*](durable-functions-types-features-overview.md#orchestrator-functions). Íme néhány az orchestrator-funkciók előnyeit:

* A munkafolyamatok definiálják a kódban. JSON-sémáinak vagy tervezők nem szükségesek.
* Más funkciók is hívják meg szinkron és aszinkron módon történik. Meghívott függvényeken kimenete melyekbe menthetők a helyi változókhoz.
* Ezek automatikusan ellenőrzőpontot, amikor a függvény várja előrehaladás. Helyi állapot soha nem elvész, ha a folyamat újrahasznosítási eljárásának végrehajtásával vagy a virtuális gép újraindul.

> [!NOTE]
> Durable Functions az Azure Functions, amely nem megfelelő összes alkalmazás speciális kiterjesztése. Ez a cikk többi része feltételezi, hogy erős ismeretét [Azure Functions](../functions-overview.md) fogalmakat és kihívásai részt a kiszolgáló nélküli alkalmazások fejlesztését.

Durable Functions elsődleges használati eset van egyszerűsítse összetett, állapot-nyilvántartó koordináció kapcsolatos problémák megoldásához a kiszolgáló nélküli alkalmazásokat. A következő szakaszok ismertetik az egyes Durable Functions is kihasználó tipikus alkalmazásminták.

## <a name="pattern-1-function-chaining"></a>#1. minta: Függvényláncolás

*Függvény-láncolás* a minta egy adott sorrendben hajtsa végre a funkciók sorozatát jelenti. A kimenet egy függvény gyakran kell egy másik függvény a bemeneti alkalmazható.

![Függvény titkosításblokkoló diagramja](./media/durable-functions-overview/function-chaining.png)

Durable Functions lehetővé teszi, hogy ez a minta tömören programkódban implementálni.

### <a name="c-script"></a>C#-szkript

```cs
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
        // error handling/compensation goes here
    }
}
```

> [!NOTE]
> Nincsenek finom eltérések előre lefordított tartós függvények írása C# és a C# példaszkript előtt látható közben. C# előre lefordított függvény tartós paraméterek megfelelő attribútumokkal rendelkeznie kell rendelkeznie. Például `[OrchestrationTrigger]` az attribútum `DurableOrchestrationContext` paraméter. A paraméterek nem megfelelően kitüntetett, ha a futtatókörnyezet nem tudná a változókat a függvény beszúrása és adnák vissza hibát. Látogasson el [minta](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples) további példákat.

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

A "F1", "F2", "F3" és "F4" értékek más függvények, a függvényalkalmazás nevére. Átvitelvezérlés szerkezeteket kódolási normál imperatív segítségével van megvalósítva. Kód, végrehajtja a fentről lefelé, és meglévő nyelvi ellenőrzési folyamat szemantikát, például a feltételek és ciklusok is magában foglalhat.  Hibakezelési logika is részét képezhetik try/catch/végül egységekben.

A `context` paraméter ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) (.NET) és `context.df` objektum (JavaScript) adja meg a neve, a paraméterek átadása, más függvények meghívása és visszaadó függvény kimeneti módszereit. Minden alkalommal, amikor a kód meghívja `await` (C#) vagy `yield` (JavaScript), a Durable Functions-keretrendszer *ellenőrzőpontok* az aktuális függvény példány állapotát. Ha a folyamat vagy a virtuális gép újraindul a végrehajtási keresztül midway, a függvény példány folytatja az előző `await` vagy `yield` hívja. Ez a viselkedés később újraindíthatja.

> [!NOTE]
> A `context` javascriptben objektum képviseli a [függvény környezet egészében], nem a DurableOrchestrationContext. (.. functions – referencia-node.md / #context-objektumot).

## <a name="pattern-2-fan-outfan-in"></a>#2. minta: Elosztás/összevonás

*Fan-kimenő/fan-alatt* hivatkozik a minta több függvények végrehajtása párhuzamosan, és majd várakozás az összes befejezéséhez.  Néhány összesítés gyakran történik a függvények által visszaadott eredményeket.

![Fan-kimenő/fan-lévő diagram](./media/durable-functions-overview/fan-out-fan-in.png)

A normál funkciók szellőztető hajtható végre több üzenetküldés egy üzenetsor funkciót ellátó. Azonban vissza az szellőztető, sokkal nagyobb kihívást. Ha az üzenetsor által aktivált függvények záró és függvény kimenetek tárolásához kód írása kellene. A Durable Functions bővítmény kezeli ezt a mintát, viszonylag egyszerű kóddal.

### <a name="c-script"></a>C#-szkript

```cs
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // get a list of N work items to process in parallel
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

A logikájával munkahelyi terjesztése függvény több példányát `F2`, és követi nyomon a munkahelyi feladatokat dinamikus listáját használja. A .NET `Task.WhenAll` API-t vagy a JavaScript `context.df.Task.all` API-t, várjon, amíg befejeződik a meghívott függvényeken mindegyikét nevezzük. Ezután a `F2` függvény kimenete a dinamikus feladatlista összesíti, és az átadott a `F3` függvény.

Az automatikus ellenőrzőpont-készítés, amely történik, ha a `await` vagy `yield` hívja meg `Task.WhenAll` vagy `context.df.Task.all` biztosítja, hogy bármely összeomlása vagy újraindítás midway keresztül nem igényel újraindítást bármely már befejeződött feladatokat.

## <a name="pattern-3-async-http-apis"></a>#3. minta: Az aszinkron HTTP API-k

A harmadik egyik az összes olyan külső ügyfelek hosszú ideig futó műveletek állapotának koordinálása a problémáról. A hosszú ideig futó művelet egy HTTP-hívás által aktivált egyik gyakori módja a minta megvalósítása és a egy állapot-végpontot, amely, lekérdezheti a Ismerje meg, ha a művelet befejeződik, majd az ügyfél átirányítására.

![HTTP API-diagram](./media/durable-functions-overview/async-http-api.png)

Durable Functions biztosít beépített API-k, amelyek egyszerűbbé teszik a hosszú ideig futó függvénykivételek használatához írt kódot. A rövid útmutató minták ([C#](durable-functions-create-first-csharp.md), [JavaScript](quickstart-js-vscode.md)) megjelenítése egy egyszerű REST-parancs használható új orchestrator-funkció példányok elindításához. Miután elindult egy példányt, a bővítmény webhook HTTP API-kat, hogy az orchestrator függvény állapot lekérdezés tesz elérhetővé. Az alábbi példa bemutatja a REST-parancsokkal, az orchestrator és állapotának lekérdezéséhez. Az átláthatóság érdekében egyes részletei a példában nincs megadva.

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

Az állapot a Durable Functions runtime kezeli, mert nem kell saját Állapotkövető mechanizmus megvalósításához.

Annak ellenére, hogy a Durable Functions bővítmény rendelkezik beépített webhookok hosszú ideig futó vezénylések kezeléséhez, valósítható meg ez a minta saját magának a saját függvény eseményindítóit (például a HTTP, üzenetsor vagy Event Hub) és a `orchestrationClient` kötést. Például használhat egy üzenetsor-üzenetet aktiválhat megszűnése.  Vagy használhat a HTTP-trigger egy Azure Active Directory hitelesítési házirend helyett a beépített webhookok, amely a hitelesítéshez használni létrehozott kulcs által védett.

### <a name="c"></a>C#

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
// HTTP-triggered function to start a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // Function name comes from the request URL.
    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> A JavaScript fejlesztésének helyileg, kell beállítania a környezeti változót `WEBSITE_HOSTNAME` való `localhost:<port>`, például. `localhost:7071` a módszer használatához `DurableOrchestrationClient`. Ezzel a követelménnyel kapcsolatban további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-js/issues/28).

A .NET a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` paraméter értéke az a `orchestrationClient` kimeneti, kötelező, amely a Durable Functions bővítmény része. A JavaScript, ez az objektum hívás által visszaadott `df.getClient(context)`. Ezek az objektumok leáll, és új vagy meglévő orchestrator függvény példányok lekérdezése, kezdési küldő táborokat módszert biztosítanak.

Az előző példában egy HTTP által aktivált függvény fogadja a egy `functionName` a bejövő URL-cím és a pass ezt az értéket [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). A [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) API kötési majd választ, amely tartalmaz egy `Location` fejlécére, és a példány, és tekintse meg később használható további információt az elindított példány állapotának beállítása, vagy leállítása azt.

## <a name="pattern-4-monitoring"></a>#4. minta: Figyelés

A figyelő minta hivatkozik egy rugalmas *ismétlődő* folyamat például egy munkafolyamat - lekérdezés csak bizonyos feltételek teljesülnek-e. Egy normál [időzítő eseményindító](../functions-bindings-timer.md) cím egy egyszerű forgatókönyvet, mint a rendszeres karbantartási feladat, lehet, de az intervallumon statikus és összetett példány élettartam kezelésére válik. Durable Functions rugalmas ismétlődési időközök, feladat életciklusának kezelését és hozhat létre több figyelő folyamatok egyetlen vezénylési lehetővé teszi lehetővé.

Egy példa a korábbi aszinkron HTTP API-forgatókönyv lenne lehet kötelezőként. Helyett elérhetővé tenni a végpontokat figyelése egy hosszú ideig futó művelet egy külső ügyfél, a hosszú ideig futó figyelő használ fel külső végpont, néhány állapotváltozás vár.

![A figyelő diagramja](./media/durable-functions-overview/monitor.png)

Durable Functions használja, figyelje meg tetszőleges végpontok monitorok hozhatók néhány sornyi kóddal. A figyelők végrehajtási fejezheti be bizonyos feltétel teljesül, vagy felmondja a [DurableOrchestrationClient](durable-functions-instance-management.md), és a várakozási időköz is módosítható bizonyos feltétel (azaz exponenciális visszatartással.) alapján A következő kódot egy alapszintű figyelő valósítja meg.

### <a name="c-script"></a>C#-szkript

```cs
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
            // Perform action when condition met
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

Amikor kérelem érkezik, egy új vezénylési példány jön létre a feladat azonosítóját. A példány állapota kérdezi le, amíg egy feltétel teljesül, és a hurok van kilépett. Tartós időzítő segítségével szabályozhatja a lekérdezési időközt. További munka majd hajtható végre, vagy az orchestration fejezheti be. Ha a `context.CurrentUtcDateTime` (.NET) vagy `context.df.currentUtcDateTime` (JavaScript) meghaladja a `expiryTime`, a figyelő véget ér.

## <a name="pattern-5-human-interaction"></a>#5 a minta: Emberi beavatkozás

Sok folyamat magában foglalja az emberi beavatkozás. Kapcsolatos használata esetén egy automatizált folyamattal láthatják a bonyolult dolog, hogy személyek nem mindig állnak, magas rendelkezésre állású és rugalmas felhőalapú szolgáltatásként. Automatizált folyamatok engedélyeznie kell a, és gyakran ehhez a időtúllépések és kompenzációs logika használatával.

Például egy üzleti folyamat, amely magában foglalja az emberi beavatkozás olyan jóváhagyási folyamatra. Például egy manager jóváhagyása szükséges egy adott időtartamot meghaladó költségjelentés lehet. Ha a manager 72 órában (például azok szabadságon történt) nem hagyja jóvá, egy eszkalációs folyamat a jóváhagyást kérhet valaki (valószínűleg a felettes felettesének) lép működésbe.

![Emberi beavatkozás diagramja](./media/durable-functions-overview/approval.png)

Ez a minta egy orchestrator-funkció segítségével valósítható meg. Az orchestrator használna egy [tartós időzítő](durable-functions-timers.md) kérelem jóváhagyása és eszkalálása időtúllépés esetén. Akkor várjon, amíg egy [külső eseményre](durable-functions-external-events.md), amely az egyes emberi beavatkozás által létrehozott értesítés lenne.

### <a name="c-script"></a>C#-szkript

```cs
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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```js
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

A tartós időzítő jön létre meghívásával `context.CreateTimer` (.NET) vagy `context.df.createTimer`(JavaScript). Az értesítés érkezik `context.WaitForExternalEvent` (.NET) vagy `context.df.waitForExternalEvent` (JavaScript). És `Task.WhenAny` (.NET) vagy `context.df.Task.any` (JavaScript) hívja meg eszkalálni kell-e (időtúllépés történik először) vagy jóváhagyási feldolgozni (jóváhagyási időkorlát lejárta előtt kapott meg).

Egy külső ügyfél várakozási orchestrator függvény segítségével közvetíti az eseményértesítést a [beépített HTTP API-k](durable-functions-http-api.md#raise-event) vagy [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API-ja egy másik függvényt:

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

A színfalak mögött a Durable Functions bővítmény épül fel a a [tartós feladat keretrendszer](https://github.com/Azure/durabletask), egy nyílt forráskódú kódtár, a Githubon tartós feladat vezénylések létrehozásához. Hogyan Azure Functions az Azure webjobs-feladatok a kiszolgáló nélküli alakulása, például sokkal Durable Functions a kiszolgáló nélküli fejlődést szem előtt tartva a tartós feladat keretrendszer. Tartós feladat keretében szolgál az erősen Microsoft belül és kívül is automatizálhatja az üzleti szempontból alapvető fontosságú folyamatokat. Természetesen illeszkednek a az Azure Functions kiszolgáló nélküli környezetben.

### <a name="event-sourcing-checkpointing-and-replay"></a>Az Event sourcing, ellenőrzőpont és visszajátszás

Az orchestrator funkciók képesek megbízhatóan fenntartani a végrehajtási állapot, más néven tervezési minta használatával [Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Helyett közvetlenül az *aktuális* folyamattevékenységek vezénylése, a tartós bővítmény állapotát egy csak hozzáfűzéssel bővíthető tár használatával rögzíti a *műveletsorozat teljes* függvény vezénylési által készített. Számos előnnyel jár, többek között, javul a teljesítmény, méretezhetőség és válaszképességét képest "való kiírása" teljes futásidejű állapot azt. Más értékelemek közé tartozik a végleges konzisztencia biztosítása a tranzakciós adatoknak, és teljes körű naplók és előzmények fenntartása. Maguk a naplók megbízható kompenzáló műveleteinek engedélyezése.

Az Event Sourcing a bővítmény által használata átlátszó. Valójában a `await` (C#) vagy `yield` (JavaScript) operátor szerepel egy orchestrator-függvényt az orchestrator szál irányítását poskytne térjen vissza a tartós feladat keretrendszer dispatcher. A kézbesítő fel minden olyan új műveletek (például egy vagy több alárendelt függvények hívása, vagy egy tartós időzítő ütemezésének) ütemezett az orchestrator függvény ezután véglegesíti Storage. A transzparens véglegesítési művelet fűz a *futtatási előzményei* orchestration-példány. Az előzmények tárolása egy tárolótáblában. A véglegesítési művelet üzeneteket ad hozzá egy üzenetsorba, a tényleges feladatok ütemezéséhez. Ezen a ponton az orchestrator függvény memóriából lehet. A számlázás, leállítja a használata az Azure Functions Használatalapú csomagban.  Ha több munka elvégzéséhez, a függvény újraindítását, és állapotában újraépíti.

Miután egy vezénylési függvény van megadva ehhez további munkahelyi (például egy válaszüzenetet érkezik, vagy egy tartós időzítő lejár), az orchestrator felébred újra és újra végrehajtja a kezdetektől a teljes függvény annak érdekében, hogy építse újra a helyi állapotot. Ha az ismétlés során a kód megpróbálja meghívni a függvényt (vagy bármely más aszinkron munkahelyi), tartós feladat keretében a csúcskategóriás a *futtatási előzményei* , az aktuális vezénylési. Ha úgy találja, hogy a [tevékenység függvény](durable-functions-types-features-overview.md#activity-functions) eredménye már végrehajtott és kurzorműveletnek néhány, a függvény eredménye visszajátssza és az orchestrator kód fusson tovább. Ez továbbra is fennáll, addig, amíg a függvénykódot beolvasása, vagy befejeződött vagy ütemezett új aszinkron során van egy pontra történik.

### <a name="orchestrator-code-constraints"></a>Az orchestrator kód megkötései

A visszajátszás viselkedés hoz létre a kódot, amely az orchestrator függvényben csak írható típusú megkötéseket. Például az orchestrator kódnak kell lennie determinisztikus, mert fog többszöri megismétlését és kell bemutatniuk ugyanaz az eredmény minden alkalommal, amikor. A korlátok teljes listája megtalálható a [Orchestrator kód megkötések](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) szakaszában a **ellenőrzőpontok használata, és indítsa újra a** cikk.

## <a name="language-support"></a>Nyelvi támogatás

Jelenleg C# (Functions 1.x és a 2.x-es), F# és a JavaScript (csak 2.x Durable Functions-függvények 1.7.0-ás vagy nagyobb) vannak az egyetlen támogatott nyelvek Durable Functions. Ez magában foglalja a tevékenység és az orchestrator függvények. A jövőben hozzáadjuk meg, amely az Azure Functions támogatja az összes nyelv támogatását. Tekintse meg az Azure Functions [GitHub-tárház hibalistájában](https://github.com/Azure/azure-functions-durable-extension/issues) a legfrissebb állapotának megtekintéséhez a további nyelvi támogatja.

## <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

A Durable Functions bővítmény automatikusan bocsát ki strukturált nyomon követési adatok [Application Insights](../functions-monitoring.md) Ha a függvényalkalmazást az Application Insights-kialakítási kulcs van konfigurálva. Nyomon követési adatok viselkedését és a vezénylések állapotának figyelésére használható.

Íme egy példa a követési események Durable Functions kinézni az Application Insights portál felületén [Application Insights-elemzési](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![App Insights-lekérdezés eredményei](./media/durable-functions-overview/app-insights-1.png)

Sok hasznos a strukturált adatok elhelyezve a `customDimensions` minden naplóbejegyzés a mezőt. Íme egy példa az ilyen bonthatók ki teljesen egy bejegyzést.

![az App Insights-lekérdezés a customDimensions mező](./media/durable-functions-overview/app-insights-2.png)

A tartós feladat keretrendszer dispatcher visszajátszását viselkedését miatt várható megismételt műveletek redundáns naplóbejegyzés megtekintéséhez. Ez lehet a visszajátszás viselkedése a core-motor megismeréséhez. A [diagnosztikai](durable-functions-diagnostics.md) a cikk bemutatja, hogy szűrje ki a visszajátszás naplókat, így láthatja, hogy csak a "valós idejű" naplók mintalekérdezések.

## <a name="storage-and-scalability"></a>Tárolás és méretezhetőség

A Durable Functions bővítmény használja az Azure Storage-üzenetsorok, táblák és blobok megőrizni, végrehajtási előzményeinek állapot és az eseményindító függvény végrehajtása. Az alapértelmezett tárfiókot a függvényalkalmazás is használható, vagy konfigurálhatja egy önálló tárfiókot. Érdemes lehet egy külön fiókot, mert a tárolási teljesítmény korlátait. Az orchestrator kód írása nem kell (és nem javasolt) dolgozhat az entitásokat, a storage-fiókokban. Az entitások egy implementálási részlete, közvetlenül a tartós feladat keretrendszer által felügyelt.

Az orchestrator funkciók tevékenységfüggvényeket ütemezés, és azok belső üzenetsorbeli üzenetek keresztül válaszokat kaphatnak. Amikor egy függvényalkalmazást az Azure Functions Használatalapú csomagban fut, ezek a várólisták által figyelt a [Azure Functions méretezési vezérlő](../functions-scale.md#how-the-consumption-plan-works) és új számítási példányt szükség szerint adja hozzá. Horizontálisan felskálázott több virtuális gépet, ha egy orchestrator-függvényt egy virtuális gépen futtathatja, tevékenységfüggvényeket meghívja a számos különböző virtuális gépek futtatása közben. További részleteket talál a méretezési csoport működését a tartós függvények [teljesítmény és méretezhetőség](durable-functions-perf-and-scale.md).

A TABLE storage a futtatási előzményei, az orchestrator-fiókok tárolására szolgál. Minden alkalommal, amikor egy példányt az egy adott virtuális gép rehydrates, lekérdezi a futtatási előzményei table storage-ból, hogy a helyi állapotában újraépítését. A table storage-ban elérhető előzmények kapcsolatos kényelmes dolog, hogy figyelje, és tekintse meg az eszközök használatával, mint például a vezénylések előzményeit [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Storage-blobokat használhatók legfőképp pedig a bérlési mechanizmus koordinálja a horizontális felskálázási példányok vezénylési több virtuális gép között. Akkor is használhatók a nagy méretű üzenetek, amely közvetlenül a táblák és üzenetsorok nem tárolható adatok tárolásához.

![Képernyőkép az Azure Storage Explorerrel](./media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Bár könnyen és kényelmesen megtekintéséhez a futtatási előzményei, table storage-ban, kerülje a tábla minden olyan függőséget véve. Mivel a Durable Functions bővítmény haladásával is megváltoztathatja.

## <a name="known-issues-and-faq"></a>Ismert problémák és gyakori kérdések

Az összes ismert problémák nyomon követése a [GitHub-problémák](https://github.com/Azure/azure-functions-durable-extension/issues) listája. Ha problémába ütközik, és nem találja a problémát a Githubban, nyisson egy új problémát, és a hiba részletes leírását tartalmazza.

## <a name="next-steps"></a>További lépések

Durable Functions kapcsolatos további információkért lásd: [függvény típusok és tartós függvények (az Azure Functions) szolgáltatások áttekintése](durable-functions-types-features-overview.md), vagy...

> [!div class="nextstepaction"]
> [Az első tartós függvény létrehozása](durable-functions-create-first-csharp.md)