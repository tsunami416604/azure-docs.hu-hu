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
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 8dbf7b6f6741998972070234d90e87baca1154a4
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042461"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Az Azure-ban Durable Functions-példányok kezelése

[Durable Functions](durable-functions-overview.md) vezénylési példányok elindult, megszakadt, lekérdezhetők, és küldött értesítés eseményeket. Az összes példány felügyeleti történik használatával a [kötés vezénylési ügyfél](durable-functions-bindings.md). Ez a cikk hiányzóra változik az egyes példány felügyeleti műveletek részleteit.

## <a name="starting-instances"></a>-Példányok indítása

A [StartNewAsync] (https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) vagy `startNew` a a `DurableOrchestrationClient` (JavaScript) elindít egy orchestrator-függvényt egy új példányát. Ez az osztály példányainak használatával lehet lekérni a `orchestrationClient` kötést. Belsőleg, a metódus enqueues vezérlő várakozási, majd elindít egy függvény a megadott névvel, amely használja a start üzenetet a `orchestrationTrigger` kötés aktiválásához.

Az aszinkron művelet befejeződik, a vezénylési folyamat ütemezése sikeresen megtörtént. A vezénylési folyamat 30 másodpercen belül el kell indulnia. Ha ez hosszabb időt vesz igénybe, egy `TimeoutException` fordul elő.

> [!WARNING]
> A JavaScript fejlesztésének helyileg, kell beállítania a környezeti változót `WEBSITE_HOSTNAME` való `localhost:<port>`, például. `localhost:7071` a módszer használatához `DurableOrchestrationClient`. Ezzel a követelménnyel kapcsolatban további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

A paraméterek [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) az alábbiak szerint:

* **Név**: Az orchestrator funkció ütemezéséhez neve.
* **Bemeneti**: Bármilyen szerializálható JSON adatok, amelyek az orchestrator függvény bemeneti kell átadni.
* **instanceId**: (Nem kötelező) A példány egyedi azonosítója. Ha nincs megadva, véletlenszerű Példányazonosító jön létre.

Íme egy egyszerű C#-példa:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

A paraméterek `startNew` az alábbiak szerint:

* **Név**: Az orchestrator funkció ütemezéséhez neve.
* **instanceId**: (Nem kötelező) A példány egyedi azonosítója. Ha nincs megadva, véletlenszerű Példányazonosító jön létre.
* **Bemeneti**: (Nem kötelező) Bármilyen szerializálható JSON adatok, amelyek az orchestrator függvény bemeneti kell átadni.

Íme egy egyszerű JavaScript-példa:

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!NOTE]
> Egy véletlenszerű azonosítót használja a példány azonosítóját. Ez segít az orchestrator funkciók vertikális több virtuális gépen, győződjön meg arról, hogy egy azonos terheléselosztást. A megfelelő ideje, hogy nem véletlenszerű Példányazonosítók akkor, ha az azonosító egy külső forrásból kell származnia, vagy végrehajtása során a [egyszeres orchestrator](durable-functions-singletons.md) mintát.

### <a name="using-core-tools"></a>Core Tools használatával

Akkor is elindítani egy példányt keresztül közvetlenül a [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` parancsot. Ez a következő paramétereket fogadja:

* **`function-name` (kötelező)** : Indítsa el a függvény neve
* **`input` (nem kötelező)** : Adjon meg a függvényt vagy beágyazott vagy egy JSON-fájlt. A fájlok esetében a fájl elérési útját előtag `@`, mint például `@path/to/file.json`.
* **`id` (nem kötelező)** : Az orchestration-példány azonosítója. Ha nincs megadva, véletlenszerű GUID jön létre.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

> [!NOTE]
> Core Tools parancsok feltételezik, hogy gyökérkönyvtárában található egy függvényalkalmazást a végrehajtás folyamatban van. Ha `connection-string-setting` és `task-hub-name` explicit módon vannak megadva, a parancsok bármelyik könyvtárból futtathatja. Ezek a parancsok futtatása egy függvény alkalmazásgazdája nélkül hajthatók végre, amíg néhány hatást nem lehet figyelni, kivéve, ha a gazdagép fut. Ha például a `start-new` parancs lesz sorba start üzenetet a céloldali tevékenység eseményközpontba, de a vezénylési nem ténylegesen működik, ha nincs a függvény alkalmazás gazdagépen futó folyamat az üzenet feldolgozására képes.

A következő paranccsal indítsa el a HelloWorld nevű függvény és illesztheti be a fájl tartalmát "teljesítményszámláló-data.json" hozzá:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>Példányok lekérdezése

A [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.NET) vagy a `getStatus` metódust a `DurableOrchestrationClient` osztály (JavaScript) egy vezénylési állapotának lekérdezése a példány.

Vesz egy `instanceId` (kötelező), `showHistory` (nem kötelező), `showHistoryOutput` (nem kötelező), és `showInput` (nem kötelező, csak a .NET) paraméterekként.

* **`showHistory`**: Ha beállítása `true`, a válasz tartalmazni fogja a futtatási előzményei.
* **`showHistoryOutput`**: Ha beállítása `true`, végrehajtási előzményeinek tartalmazni fogja a tevékenység kimeneteiből.
* **`showInput`**: Ha beállítása `false`, a válasz nem tartalmazza majd a függvény a bemeneti. Az alapértelmezett érték `true`. (Csak a .NET)

A metódus visszaadja a JSON-objektum, a következő tulajdonságokkal:

* **Név**: Az orchestrator függvény neve.
* **instanceId**: A vezénylési Példányazonosítója (legyen ugyanaz, mint a `instanceId` bemeneti).
* **CreatedTime**: Az az időpont, amikor az orchestrator függvény elindításának.
* **LastUpdatedTime**: Az időpontot, amikor az orchestration utolsó alkulcsaihoz.
* **Bemeneti**: A bemeneti JSON értékként a függvény. Ez a mező nem lehet megadni, ha `showInput` false (hamis).
* **CustomStatus**: Egyéni vezénylési állapot JSON formátumban.
* **Kimeneti**: A kimenet JSON értékként (Ha a függvény befejeződött) függvény. Ha az orchestrator függvénye sikertelen volt, ez a tulajdonság tartalmazza a hiba részletei. Ha az orchestrator-függvény meg lett szakítva, ez a tulajdonság (ha van ilyen) a megadott okának megszüntetése tartalmazza.
* **runtimeStatus**: A következő értékek egyikét:
  * **Függőben lévő**: A példány van ütemezve, de még nem indult futtatása.
  * **Futó**: A példány elindult.
  * **Befejezett**: A példány a szokásos módon befejeződött.
  * **ContinuedAsNew**: A példány újraindult magát egy új előzményekkel. Ez csak átmeneti állapot.
  * **Nem sikerült**: A példány egy hiba miatt nem sikerült.
  * **Lezárva**: A példány a váratlanul leállt.
* **Előzmények**: A vezénylési végrehajtási előzményei. Ez a mező csak megadni, ha `showHistory` értékre van állítva `true`.

A metódus visszatérése `null` , ha a példány nem létezik, vagy még nem indult futtatása.

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="using-core-tools"></a>Core Tools használatával

Egy vezénylési példány-on keresztül állapotának lekéréséhez lehetőség arra is a [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` parancsot. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Orchestration-példány azonosítója
* **`show-input` (nem kötelező)** : Ha beállítása `true`, a válasz tartalmazni fogja a függvény a bemeneti. Az alapértelmezett érték `false`.
* **`show-output` (nem kötelező)** : Ha beállítása `true`, a válasz tartalmazni fogja a függvény kimenete. Az alapértelmezett érték `false`.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

A következő parancsot kellene beolvasni egy példányát a vezénylési Példányazonosítót 0ab8c55a66644d68a3a8b220b12d209c állapotát (beleértve a bemeneti és kimeneti). Azt feltételezi, hogy a `func` parancs fut, a függvényalkalmazás gyökérkönyvtárából:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

A `durable get-history` beolvasni a vezénylési példány előzményeinek parancs használható. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Orchestration-példány azonosítója
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Is megadható a host.json durableTask:HubName keresztül.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>Minden példány lekérdezése

Használhatja a `GetStatusAsync` (.NET) vagy `getStatusAll` (JavaScript) módszer a állapotok szoftverpéldányok vezénylési lekérdezéséhez. A .NET-ben megadhat egy `CancellationToken` objektum abban az esetben, ha azt szeretné, és törölje azt. A metódus az azonos tulajdonságokkal rendelkező objektum adja vissza a `GetStatusAsync` paraméterekkel rendelkező metódus.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-core-tools"></a>Core Tools használatával

-N keresztül közvetlenül a lekérdezés példányok lehetőség arra is a [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` parancsot. Ez a következő paramétereket fogadja:

* **`top` (nem kötelező)** : Ez a parancs támogatja a lapozást. Ez a paraméter egy kérelemre példányok felel meg. Az alapértelmezett érték 10.
* **`continuation-token` (nem kötelező)** : A token példányokat beolvasni mely lap/szakasz jelzésére. Minden egyes `get-instances` végrehajtási, a következő példánycsoportot egy tokent ad vissza.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>Példányok lekérdezése szűrőkkel

Is használhatja a `GetStatusAsync` (.NET) vagy `getStatusBy` (JavaScript) metódus, amelyek megfelelnek egy az orchestration-példányok listájának lekéréséhez az előre meghatározott szűrőket. Lehetséges szűrő lehetőségek között a vezénylési létrehozásának idejét és a vezénylési futásidejű állapot.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-the-functions-core-tools"></a>A Functions Core Tools használatával

A `durable get-instances` parancs is használható szűrőket. A fent említettek mellett `top`, `continuation-token`, `connection-string-setting`, és `task-hub-name` paraméterek, három szűrőparaméterek (`created-after`, `created-before`, és `runtime-status`), használható.

* **`created-after` (nem kötelező)** : Az a dátum/idő (UTC) után létrehozott példányokat beolvasni. ISO 8601 formátumú időpontok elfogadva.
* **`created-before` (nem kötelező)** : Az a dátum/idő (UTC) előtt létrehozott példányokat beolvasni. ISO 8601 formátumú időpontok elfogadva.
* **`runtime-status` (nem kötelező)** : A példányok, amelynek az állapotát ezen ("fut", "kész" stb.) megfelelő lekéréséhez. Több (szóközzel elválasztva) állapotot is biztosítanak.
* **`top` (nem kötelező)** : Példányok száma a kérelmenként beolvasni. Az alapértelmezett érték 10.
* **`continuation-token` (nem kötelező)** : A token példányokat beolvasni mely lap/szakasz jelzésére. Minden egyes `get-instances` végrehajtási, a következő példánycsoportot egy tokent ad vissza.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

Ha nincs szűrő (`created-after`, `created-before`, vagy `runtime-status`) vannak megadva, majd `top` példányok kérhető le, nem tekintettel a futásidejű állapot vagy a létrehozás ideje.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>Példány leállítása

Egy futó vezénylési példány használatával állíthatók a [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) módszer a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.NET), vagy a `terminate` módszere a `DurableOrchestrationClient` osztály () JavaScript-). A két paraméter egy `instanceId` és a egy `reason` karakterláncot, amely naplókat, és a példány állapota lesz írva. A leállított példány le fog állni, amint eléri a következő `await` (.NET) vagy `yield` (JavaScript) pontot, vagy pedig megszűnése azonnal esetén már be egy `await` (.NET) vagy `yield` (JavaScript).

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Jelenleg nem propagál példány megszüntetése. Tevékenységfüggvényeket és alárendelt vezénylések végrehajtása, függetlenül attól, hogy le lett állítva a vezénylési példányt, amelynek a neve őket.

### <a name="using-core-tools"></a>Core Tools használatával

Akkor is közvetlenül keresztül vezénylési példány leáll a [Core Tools](../functions-run-local.md) `durable terminate` parancsot. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Leállítja a vezénylési példányának azonosítója
* **`reason` (nem kötelező)** : Okának megszüntetése
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

A következő parancsot kellene leáll 0ab8c55a66644d68a3a8b220b12d209c Azonosítóval rendelkező egy vezénylési példány:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>Események küldése az példányok

Eseményértesítések elküldött futó példányok használatával a [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) módszer a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.NET) vagy a `raiseEvent` módszer a `DurableOrchestrationClient` osztály () JavaScript-). Ezeket az eseményeket is kezelni példányai, amelyek elnyerésére hívása [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) vagy `waitForExternalEvent` (JavaScript).

A paraméterek [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) és `raiseEvent` (JavaScript) az alábbiak szerint:

* **instanceId**: A példány egyedi azonosítója.
* **EventName**: Az esemény küldése neve.
* **EventData**: A példány küldendő szerializálható JSON adattartalom.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!WARNING]
> Ha nincs vezénylési példánya és a megadott *példány azonosítója* , vagy ha a példány nem vár a megadott *eseménynév*, a rendszer törli az eseményüzenet. Ezzel a viselkedéssel kapcsolatos további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="using-core-tools"></a>Core Tools használatával

Egy vezénylési példányon keresztül közvetlenül eseményt létrehozni, lehetőség arra is a [Core Tools](../functions-run-local.md) `durable raise-event` parancsot. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Orchestration-példány azonosítója
* **`event-name` (nem kötelező)** : Az esemény emelése neve. Az alapértelmezett érték `$"Event_{RandomGUID}"`
* **`event-data` (nem kötelező)** : Adatok küldése a vezénylési példányhoz. Ez lehet egy JSON-fájl elérési útját, vagy az adatok közvetlenül a parancssort az adható meg
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Várakozás a befejezésre vezénylési

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály közzéteszi egy [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API segítségével lekérheti egy vezénylési szinkron módon történik a tényleges kimenetét .NET-keretrendszerben. a példány. A JavaScript a `DurableOrchestrationClient` osztály közzéteszi egy `waitForCompletionOrCreateCheckStatusResponse` API ugyanazt a célt. A módszereket használhat alapértelmezett értéket a 10 másodperces `timeout` és az 1 másodperc `retryInterval` Ha ezek nincsenek beállítva.  

Íme egy példa azt mutatja be, hogyan használható az API HTTP-eseményindító függvény:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

A függvény a következő kódsorral 2-másodperces időkorlát és 0,5 másodperces újrapróbálkozási időköz használatával nem hívható meg:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Attól függően, a válasz beolvasni a vezénylési példány szükséges időt két olyan eset létezik:

* A vezénylési példányok be a meghatározott idő (ebben az esetben 2 másodperc), a válasz a tényleges vezénylési példány kimeneti i szinkron módon történik:

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

* A vezénylési példányok a megadott időkorláton (ebben az esetben 2 másodperc) belül nem tudja végrehajtani, a válasz az egyik ismertetett alapértelmezett **HTTP API URL-cím-felderítési**:

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
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> A webhook URL-címek formátumát az Azure Functions állomás verziójának függően eltérhet. Az előző példában az Azure Functions 2.x állomás van.

## <a name="retrieving-http-management-webhook-urls"></a>HTTP-felügyeleti Webhook URL-címek beolvasása

Külső rendszerek kommunikálhatnak a webhook URL-címeket, amelyek részei ismertetett alapértelmezett választ keresztül Durable Functions [HTTP API URL-cím-felderítési](durable-functions-http-api.md). Azonban a webhook URL-címeket is elérhető lesz programozott módon a vezénylési ügyfél vagy a tevékenység függvényének keresztül a [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) módszere a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)osztály (.NET) vagy a `createHttpManagementPayload` módszere a `DurableOrchestrationClient` osztály (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) és `createHttpManagementPayload` egy paraméterrel rendelkezik:

* **instanceId**: A példány egyedi azonosítója.

A módszer egy példányát adja vissza [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) vagy egy olyan objektum (JavaScript) a következő karakterláncot:

* **ID**: A vezénylési Példányazonosítója (legyen ugyanaz, mint a `InstanceId` bemeneti).
* **StatusQueryGetUri**: Az orchestration-példány állapota URL-címe
* **SendEventPostUri**: A vezénylési példány "raise esemény" URL-címe
* **TerminatePostUri**: A "le" példány URL-címét a vezénylési.
* **RewindPostUri**: A vezénylési példány "visszatekerés" URL-címe

Tevékenységfüggvényeket küldheti külső rendszerek figyelése, vagy az események folyamattevékenységek vezénylése, ezek az objektumok egy példányát:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewinding-instances-preview"></a>Visszatekerése instances (előzetes verzió)

A sikertelen vezénylési példány *visszatekerése* be egy korábban megfelelő állapotba a a [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) vagy `rewindAsync` (JavaScript) API-t. Írja be újra a vezénylési működik a *futó* állapotot és a vezénylési meghibásodás kiváltó tevékenység és/vagy suborchestration végrehajtási hibák újrafuttatása.

> [!NOTE]
> Ez az API nem helyettesíti a megfelelő hibakezelést és újrapróbálkozási szabályzatok készült. Célja, azt csak abban az esetben, ha a vezénylési példányok váratlan okokból nem használható. Hiba hibakezelést és újrapróbálkozási szabályzatok további információkért tekintse meg a [hibakezelés](durable-functions-error-handling.md) témakör.

Például a kis-és nagybetűhasználattal *visszatekerés* egy munkafolyamat eseteken [emberi jóváhagyások](durable-functions-concepts.md#human). Tegyük fel, hogy vannak, amelyek értesítik valaki, hogy szükség van-e a jóváhagyási tevékenységet függvények sorozatát, és várjon, amíg a valós idejű választ ki. Amikor az összes jóváhagyási tevékenységek kapott válaszok vagy túllépte az időkorlátot, egy másik tevékenység meghiúsul egy alkalmazás helytelen konfiguráció, például egy érvénytelen adatbázis-kapcsolati karakterlánc miatt. Az eredmény egy vezénylési hiba a mély a munkafolyamatba. Az a `RewindAsync` (.NET) vagy `rewindAsync` (JavaScript) API-t, az alkalmazás-rendszergazda megoldhatja a konfigurációs hiba és *visszatekerés* sikertelen vezénylési vissza az állapot azonnal a meghibásodása előtt. Az emberi-interakció lépések egyike sem kell felhasználnák, és a vezénylési most már képes sikerrel befejeződni.

> [!NOTE]
> A *visszatekerés* funkció nem támogatja a visszatekerése orchestration-példányok tartós időzítők használja.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="using-core-tools"></a>Core Tools használatával

A vezénylési példány-on keresztül visszatekerés lehetőség arra is a [Core Tools](../functions-run-local.md) `durable rewind` parancsot. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Orchestration-példány azonosítója
* **`reason` (nem kötelező)** : A vezénylési példány visszatekerése okát
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Példány előzményeinek törlése

> [!NOTE]
> A `PurgeInstanceHistoryAsync` API érhető el jelenleg csak C#. Ez hozzáadódik a JavaScript egy soron következő kiadásban.

Vezénylési előzmények használatával is kiüríthetők [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_). A funkciót eltávolítja egy vezénylési – az Azure Table sort és nagy méretű üzenetek blobokat, ha vannak ilyenek társított minden adatot. A módszer két túlterheléssel rendelkezik. Az első utótagcímkéjét kiürítése előzmények vezénylési példány azonosítója:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

A második példa bemutatja egy időzítő által aktivált függvényt, amely az előzmények befejezése után a megadott időtartam alatt orchestration-példányok esetén kiürítése. Ebben az esetben eltávolítja az összes példány, vagy további 30 nappal ezelőtt befejezett adatokat. Ütemezés szerint naponta egyszer, 12-kor fut:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> A *PurgeInstanceHistory* túlterhelési elfogadásával időszakban, a paraméter csak vezénylési példányok egy futásidejű állapot - befejeződött, kilépett vagy sikertelen dolgozza fel.

### <a name="using-core-tools"></a>Core Tools használatával

Egy vezénylési példány előzményeinek törlése lehetséges használatával a [Core Tools](../functions-run-local.md) `durable purge-history` parancsot. A második hasonló C# a fenti példában kiürítése egy adott időtartam során létrehozott összes vezénylési példányok előzményeit. A példányok törlődnek tovább szűrhetők futásidejű állapot szerint. A parancs több paraméterekkel rendelkezik:

* **`created-after` (nem kötelező)** : Törölje az a dátum/idő (UTC) után létrehozott példányok előzményeit. ISO 8601 formátumú időpontok elfogadva.
* **`created-before` (nem kötelező)** : Törölje az a dátum/idő (UTC) előtt létrehozott példányok előzményeit. ISO 8601 formátumú időpontok elfogadva.
* **`runtime-status` (nem kötelező)** : Törölje a példányok, amelynek az állapotát ezen ("fut", "kész" stb.) megfelelő előzményeit. Több (szóközzel elválasztva) állapotot is biztosítanak.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

A következő paranccsal törölhető este 7:35-kor (UTC) 2018. November 14. előtt létrehozott összes sikertelen példányok előzményeit.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>Egy feladat központ törlése

Használatával a [Core Tools](../functions-run-local.md) `durable delete-task-hub` parancsot, egy adott feladat hubhoz társított összes tároló-összetevők törlése lehetséges. Ez magában foglalja az Azure storage-táblák, üzenetsorok és blobok. A parancs két paraméterrel rendelkezik:

* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó tartós feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Azt is be lehet [host.json](durable-functions-bindings.md#host-json) durableTask:HubName keresztül.

A következő parancsot a "UserTest" feladat hubhoz társított összes Azure storage adat törölhető.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, például felügyeleti HTTP API-k használata](durable-functions-http-api.md)
