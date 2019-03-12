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
ms.openlocfilehash: ee96bc5e17051ab37be34eecbb8e4fe35599cd5d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547311"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Az Azure-ban Durable Functions-példányok kezelése

Ha használja a [Durable Functions](durable-functions-overview.md) kiterjesztése az Azure Functions vagy szeretné elindítani, ilyen esetben győződjön meg arról, ki abból, használható a leghatékonyabban kap. Optimalizálhatja a Durable Functions vezénylési példányok által mikéntjét többet. Ez a cikk hiányzóra változik az egyes példány felügyeleti műveletek részleteit.

Indítsa el, és leállítja a példányokat, például és lekérdezheti a példányokat, beleértve az összes példányok és lekérdezések példányok lekérdezése szűrőket. Ezenkívül példányok eseményeket küldeni, Várakozás a befejezésre vezénylési, és HTTP felügyeleti webhook URL-címek lekérése. Ez a cikk ismerteti az egyéb felügyeleti műveleteket, is, beleértve visszatekerése példányok, a példány előzményeinek törlése és a egy feladat központ törlése.

A tartós függvények hogyan szeretné megvalósítani felügyeleti műveletekhez lehetősége van. Ez a cikk példákat, amelyek használják a [Azure Functions Core Tools](../functions-run-local.md) mindkét .NET-hez készült (C#) és a JavaScript.

## <a name="start-instances"></a>Példányok indítása

Fontos a az adatelőkészítés-példány indul el. Ez gyakran azért történik, Durable Functions kötés egy másik függvény triggerben használata esetén.

A [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.NET) vagy `startNew` a a `DurableOrchestrationClient` (JavaScript) elindítja egy új példányát. Ez az osztály példányainak használatával szerez be a `orchestrationClient` kötést. Belsőleg, a metódus enqueues vezérlő várakozási, majd elindít egy függvény a megadott névvel, amely használja a start üzenetet a `orchestrationTrigger` kötés aktiválásához.

Az aszinkron művelet befejeződik, a vezénylési folyamat ütemezése sikeresen megtörtént. A vezénylési folyamat 30 másodpercen belül el kell indulnia. Ha hosszabb ideig tart, akkor megjelenik egy `TimeoutException`.

> [!WARNING]
> Helyi fejlesztésének javascriptben, állítsa be a környezeti változó `WEBSITE_HOSTNAME` való `localhost:<port>` (például `localhost:7071`) módszer használatához az `DurableOrchestrationClient`. Ezzel a követelménnyel kapcsolatban további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="net"></a>.NET

A paraméterek [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) az alábbiak szerint:

* **Név**: Az orchestrator funkció ütemezéséhez neve.
* **Bemeneti**: Bármilyen szerializálható JSON adatok, amelyek az orchestrator függvény bemeneti kell átadni.
* **instanceId**: (Nem kötelező) A példány egyedi azonosítója. Ha nem adja meg ezt a paramétert, a módszert használja egy véletlenszerű azonosítót.

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
* **instanceId**: (Nem kötelező) A példány egyedi azonosítója. Ha nem adja meg ezt a paramétert, a módszert használja egy véletlenszerű azonosítót.
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

> [!TIP]
> Egy véletlenszerű azonosítót használja a példány azonosítóját. Ez segít, Ön vertikális orchestrator funkciók több virtuális gépen, győződjön meg arról, hogy egy azonos terheléselosztást. A megfelelő ideje, hogy nem véletlenszerű Példányazonosítók akkor, ha az azonosító egy külső forrásból kell származnia, vagy ha implementálja a [egyszeres orchestrator](durable-functions-singletons.md) mintát.

### <a name="azure-functions-core-tools"></a>Az Azure Functions Core Tools

Egy példány segítségével közvetlenül is elindíthatja a [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` parancsot. Ez a következő paramétereket fogadja:

* **`function-name` (kötelező)** : Indítsa el a függvény neve.
* **`input` (nem kötelező)** : Adjon meg a függvényt vagy beágyazott vagy egy JSON-fájlt. A fájlok esetében a fájl elérési útját egy előtag hozzáadása `@`, mint például `@path/to/file.json`.
* **`id` (nem kötelező)** : Az orchestration-példány azonosítója. Ha nem adja meg ezt a paramétert, a parancs egy véletlenszerű GUID Azonosítót.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. Az alapértelmezett érték AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. Az alapértelmezett érték DurableFunctionsHub. Akkor is állíthatja ezt [host.json](durable-functions-bindings.md#host-json) durableTask:HubName használatával.

> [!NOTE]
> Core Tools parancsok feltételezik, hogy futtatja azokat a függvényalkalmazás gyökérkönyvtárában. Ha explicit módon adja meg a `connection-string-setting` és `task-hub-name` paraméterek, a futtathatja a parancsok bármelyik könyvtárból. Bár ezek a parancsok futtatása egy függvény alkalmazásgazdája nélkül is futtathatja, előfordulhat, hogy néhány hatást, kivéve, ha a gazdagép fut, azt nem megfigyelheti. Ha például a `start-new` parancs enqueues egy indítási üzenetre, a céloldali tevékenység hub, de a vezénylési valójában nem fut, kivéve, ha van egy függvény alkalmazás gazdagép folyamat fut, amely képes feldolgozni az üzenetet.

A következő parancsot a HelloWorld nevű függvény elindul, és átadja a fájl tartalmát `counter-data.json` hozzá:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Lekérdezés-példányok

Kezelheti a vezénylések erőfeszítés mellett részeként valószínűleg szüksége (például hogy általában befejeződött vagy meghiúsult) vezénylési példány állapotára vonatkozó információkat gyűjthet.

A [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metódust a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.NET) vagy a `getStatus` metódust a `DurableOrchestrationClient` osztály (JavaScript) egy vezénylési állapotának lekérdezése a példány.

Vesz egy `instanceId` (kötelező), `showHistory` (nem kötelező), `showHistoryOutput` (nem kötelező), és `showInput` (nem kötelező, csak a .NET) paraméterekként.

* **`showHistory`**: Ha beállítása `true`, a válasz tartalmazza a futtatási előzményei.
* **`showHistoryOutput`**: Ha beállítása `true`, végrehajtási előzményeinek tartalmazza a tevékenység kimeneteiből.
* **`showInput`**: Ha beállítása `false`, a válasz nem tartalmazza a függvény a bemeneti. Az alapértelmezett érték `true`. (Csak a .NET)

A metódus visszaadja a JSON-objektum, a következő tulajdonságokkal:

* **Név**: Az orchestrator függvény neve.
* **instanceId**: A vezénylési Példányazonosítója (legyen ugyanaz, mint a `instanceId` bemeneti).
* **CreatedTime**: Az az időpont, amikor az orchestrator függvény elindításának.
* **LastUpdatedTime**: Az időpontot, amikor az orchestration utolsó alkulcsaihoz.
* **Bemeneti**: A bemeneti JSON értékként a függvény. Ez a mező nincs kitöltve, ha `showInput` false (hamis).
* **CustomStatus**: Egyéni vezénylési állapot JSON formátumban.
* **Kimeneti**: A kimenet JSON értékként (Ha a függvény befejeződött) függvény. Ha az orchestrator függvénye sikertelen volt, ez a tulajdonság tartalmazza a hiba részletei. Az orchestrator-függvény meg lett szakítva, ha ez a tulajdonság tartalmazza okának megszüntetése a (ha van).
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

### <a name="azure-functions-core-tools"></a>Az Azure Functions Core Tools

Akkor is lehet beszerezni a vezénylési példány állapotát, közvetlenül a a [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` parancsot. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Az orchestration-példány azonosítója.
* **`show-input` (nem kötelező)** : Ha beállítása `true`, a válasz tartalmazza a függvény a bemeneti. Az alapértelmezett érték `false`.
* **`show-output` (nem kötelező)** : Ha beállítása `true`, a válasz tartalmazza a függvény kimenete. Az alapértelmezett érték `false`.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet [host.json](durable-functions-bindings.md#host-json), durableTask:HubName használatával.

A következő parancsot egy példányát a vezénylési Példányazonosítót 0ab8c55a66644d68a3a8b220b12d209c állapotát (beleértve a bemeneti és kimeneti) kérdezi le. Feltételezi, hogy futtatja a `func` gyökérkönyvtárában található a függvényalkalmazás parancsot:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Használhatja a `durable get-history` parancs használatával kérje le a vezénylési példány előzményeit. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Az orchestration-példány azonosítója.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Is beállítható a host.json, durableTask:HubName használatával.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Minden példány lekérdezése

Ahelyett, hogy a lekérdezés egy példány a vezénylési egyszerre akkor előfordulhat, hogy hatékonyabb lekérdezéséhez mindegyiket egyszerre.

Használhatja a `GetStatusAsync` (.NET) vagy `getStatusAll` (JavaScript) módszer a állapotok szoftverpéldányok vezénylési lekérdezéséhez. A .NET-ben, amelyet továbbíthat egy `CancellationToken` objektum abban az esetben, ha azt szeretné, és törölje azt. A metódus az azonos tulajdonságokkal rendelkező objektum adja vissza a `GetStatusAsync` paraméterekkel rendelkező metódus.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
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

### <a name="azure-functions-core-tools"></a>Az Azure Functions Core Tools

Lehetőség arra is, közvetlenül a lekérdezés példányok használatával a [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` parancsot. Ez a következő paramétereket fogadja:

* **`top` (nem kötelező)** : Ez a parancs támogatja a lapozást. Ez a paraméter egy kérelemre példányok felel meg. Az alapértelmezett érték 10.
* **`continuation-token` (nem kötelező)** : A token jelzi, hogy melyik lapot vagy lekérdezni a példányok szakaszában. Minden egyes `get-instances` végrehajtási, a következő példánycsoportot egy tokent ad vissza.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet [host.json](durable-functions-bindings.md#host-json), durableTask:HubName használatával.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Lekérdezés példányok szűrőkkel

Mi történik, ha nem feltétlenül szükséges összes információt, amely a standard szintű példány lekérdezés képes biztosítani? Például mi történik, ha csak keres a vezénylési létrehozásának idejét, vagy az orchestration futásidejű állapot? A lekérdezés szűrők alkalmazásával szűkítheti.

Használja a `GetStatusAsync` (.NET) vagy `getStatusBy` (JavaScript) metódus, amelyek megfelelnek egy az orchestration-példányok listájának lekéréséhez az előre meghatározott szűrőket.

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

### <a name="azure-functions-core-tools"></a>Az Azure Functions Core Tools

Az Azure Functions Core Tools is használhat a `durable get-instances` szűrők parancsot. A fent említettek mellett `top`, `continuation-token`, `connection-string-setting`, és `task-hub-name` használható paramétereket, három szűrőparaméterek (`created-after`, `created-before`, és `runtime-status`).

* **`created-after` (nem kötelező)** : Az a dátum/idő (UTC) után létrehozott példányokat beolvasni. ISO 8601 formátumú időpontok elfogadva.
* **`created-before` (nem kötelező)** : Az a dátum/idő (UTC) előtt létrehozott példányokat beolvasni. ISO 8601 formátumú időpontok elfogadva.
* **`runtime-status` (nem kötelező)** : A példányok adott állapottal (például fut, vagy completed) lekéréséhez. Több (szóközzel elválasztva) állapotot is biztosítanak.
* **`top` (nem kötelező)** : Példányok száma a kérelmenként beolvasni. Az alapértelmezett érték 10.
* **`continuation-token` (nem kötelező)** : A token jelzi, hogy melyik lapot vagy lekérdezni a példányok szakaszában. Minden egyes `get-instances` végrehajtási, a következő példánycsoportot egy tokent ad vissza.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet [host.json](durable-functions-bindings.md#host-json), durableTask:HubName használatával.

Ha nem ad meg szűrők (`created-after`, `created-before`, vagy `runtime-status`), egyszerűen lekéri a parancs `top` példányok futásidejű állapot vagy a létrehozás ideje nincs tekintetében.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Példány leállítása

Ha túl sokáig tart futtatásához vezénylési példányt, vagy csak le kell állítania, mielőtt az befejezné bármilyen okból, lehetősége van, leáll.

Használhatja a [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) módszer a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.NET), vagy a `terminate` módszere a `DurableOrchestrationClient` osztály (JavaScript). A két paraméter egy `instanceId` és a egy `reason` karakterláncot, amely írták naplói és a példány állapota. A leállított példány leáll, amint eléri a következő `await` (.NET) vagy `yield` (JavaScript) pontot, vagy megszakítja azonnal, ha már az kerül egy `await` vagy `yield`.

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
> Példány lezárást jelenleg nem propagálása. Tevékenységfüggvényeket és alárendelt vezénylések futtassa befejezésekor, függetlenül attól, hogy már leállt a vezénylési példányt, amelynek a neve őket.

### <a name="azure-functions-core-tools"></a>Az Azure Functions Core Tools

Is leállíthatja egy vezénylési példány közvetlenül, az a [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` parancsot. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Leállítja a vezénylési példány azonosítója.
* **`reason` (nem kötelező)** : Lemondási okát.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet [host.json](durable-functions-bindings.md#host-json), durableTask:HubName használatával.

A következő parancs leállítja 0ab8c55a66644d68a3a8b220b12d209c Azonosítóval rendelkező egy vezénylési példány:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Események küldése az példányok

Bizonyos esetekben fontos az orchestrator függvények, várjon, és a külső események figyelésére. Ez magában foglalja [functions monitorozása](durable-functions-concepts.md#monitoring) , amelyeket vár [emberi beavatkozás](durable-functions-concepts.md#human).

Eseményértesítések küldése futó példányok a [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) módszer a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.NET) vagy a `raiseEvent` módszere a `DurableOrchestrationClient` osztály () JavaScript-). Ezeket az eseményeket is kezelni példányai, amelyek elnyerésére hívása [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) vagy `waitForExternalEvent` (JavaScript).

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

> [!IMPORTANT]
> Ha nem sikerül nincs vezénylési példány a megadott példány azonosítóval, vagy ha a példány nem vár a megadott eseménynapló nevére, a rendszer törli az eseményüzenet. Ezzel a viselkedéssel kapcsolatos további információkért lásd: a [GitHub-problémát](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Az Azure Functions Core Tools

Is tehet egy esemény egy vezénylési példányára, közvetlenül használatával a [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` parancsot. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Az orchestration-példány azonosítója.
* **`event-name` (nem kötelező)** : Az esemény emelése neve. A mező alapértelmezett értéke: `$"Event_{RandomGUID}"`.
* **`event-data` (nem kötelező)** : Adatok küldése a vezénylési példányhoz. Ez lehet egy JSON-fájl elérési útját, vagy megadhatja az adatok közvetlenül a parancssorban.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet [host.json](durable-functions-bindings.md#host-json), durableTask:HubName használatával.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Várakozás a befejezésre vezénylési

Hosszan futó kihasználását érdemes, várjon, és a egy vezénylési eredményeinek lekérése. Ezekben az esetekben hasznos is meghatározhat egy bizonyos időkorláton a vezénylési. Ha túllépi az időkorlátot, a vezénylési állapotát a rendszer visszalépteti találatai helyett.

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály közzéteszi egy [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API a .NET-ben. Ez az API segítségével lekérheti a tényleges kimenetét egy vezénylési példány szinkron módon történik. A JavaScript a `DurableOrchestrationClient` osztály közzéteszi egy `waitForCompletionOrCreateCheckStatusResponse` API ugyanazt a célt. Ha ezek nincsenek beállítva, a módszereket használja-e 10 másodpercet, az alapértelmezett érték `timeout`, és az 1 másodperc `retryInterval`.  

Íme egy példa azt mutatja be, hogyan használható az API HTTP-eseményindító függvény:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

A következő sort a függvény hívása. Az időkorlát és 0,5 másodperc 2 másodperc használja az újrapróbálkozási időköz:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Attól függően, a válasz beolvasni a vezénylési példány szükséges időt két olyan eset létezik:

* A vezénylési példányok be a meghatározott idő (ebben az esetben 2 másodperc), és a válasz a tényleges vezénylési példány kimeneti i szinkron módon történik:

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

* A vezénylési példányok nem tudja végrehajtani a megadott időkorláton belül, és a válasz egy ismertetett alapértelmezett [HTTP API URL-cím-felderítési](durable-functions-http-api.md):

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
> A webhook URL-címek formátumát függően eltérőek lehetnek, az Azure Functions állomás verziójának. Az előző példában az Azure Functions 2.x állomás van.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP-felügyeleti webhook URL-címek lekérése

Használhatja valamely külső rendszerre vagy folyamattevékenységek vezénylése, az események monitorozásához. Külső rendszerek kommunikálhatnak a webhook URL-címeket, amelyek részei ismertetett alapértelmezett választ keresztül Durable Functions [HTTP API URL-cím-felderítési](durable-functions-http-api.md). Azonban a webhook URL-címeket is elérhetők programozott módon a vezénylési ügyfél vagy egy tevékenység függvényben. Ezt úgy teheti meg a [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) módszere a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.NET), vagy a `createHttpManagementPayload` módszere a `DurableOrchestrationClient` osztály (JavaScript).

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) és `createHttpManagementPayload` egy paraméterrel rendelkezik:

* **instanceId**: A példány egyedi azonosítója.

A módszer egy példányát adja vissza [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.NET) vagy egy olyan objektum (JavaScript), a következő karakterláncot:

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

## <a name="rewind-instances-preview"></a>Visszatekerés instances (előzetes verzió)

Ha az orchestration meghibásodása nem várt hiba miatt, akkor is *visszatekerés* a példány egy API-val korábban megfelelő állapotba erre a célra készült.

> [!NOTE]
> Ez az API nem helyettesíti a megfelelő hibakezelést és újrapróbálkozási szabályzatok készült. Célja, azt csak abban az esetben, ha a vezénylési példányok váratlan okokból nem használható. Hiba történt az állapotkezelés és az újrapróbálkozási szabályzatok a további részletekért lásd: a [hibakezelés](durable-functions-error-handling.md) témakör.

Használja a [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) vagy `rewindAsync` (JavaScript) API-t a vezénylési helyezze vissza a szolgáltatásba a *futó* állapota. Futtassa újra a tevékenység vagy suborchestration végrehajtási hibák, amelyek a vezénylési hibát okozta.

Például tegyük fel, hogy a munkafolyamat eseteken [emberi jóváhagyások](durable-functions-concepts.md#human). Tegyük fel, hogy vannak, amelyek valaki engedélyezésük van szükség, és várja meg, a valós idejű válasz tevékenység függvények sorozatát. A jóváhagyási tevékenységet, amikor az összes kapott válaszok, vagy a túllépte az időkorlátot, feltételezve, hogy egy másik tevékenység meghiúsul egy alkalmazás helytelen konfiguráció, például egy érvénytelen adatbázis-kapcsolati karakterlánc miatt. Az eredmény egy vezénylési hiba a mély a munkafolyamatba. Az a `RewindAsync` (.NET) vagy `rewindAsync` (JavaScript) API-alkalmazás rendszergazda javítsa ki a konfigurációs hibát, és térjen vissza az állapot azonnal a hiba előtt a sikertelen vezénylési visszatekerés. Az emberi-interakció lépések egyike sem kell felhasználnák, és a vezénylési most már képes sikerrel befejeződni.

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

### <a name="azure-functions-core-tools"></a>Az Azure Functions Core Tools

Egy vezénylési példányt is visszatekerés módja, ha közvetlenül a [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` parancsot. Ez a következő paramétereket fogadja:

* **`id` (kötelező)** : Az orchestration-példány azonosítója.
* **`reason` (nem kötelező)** : A vezénylési példány visszatekerése okát.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet [host.json](durable-functions-bindings.md#host-json), durableTask:HubName használatával.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Példány előzményeinek törlése

Szeretné eltávolítani egy vezénylési társított minden adatot, törölheti a példány előzményei. Például előfordulhat, hogy szeretné távolíthatja el az Azure Table sort és nagy méretű üzenetek blobokat, ha vannak ilyenek. Ehhez használja a [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API-t.

> [!NOTE]
> A `PurgeInstanceHistoryAsync` API érhető el jelenleg csak C#.

 A módszer két túlterheléssel rendelkezik. Az első utótagcímkéjét kiürítése előzményei a vezénylési-példány azonosítója:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

A második példa bemutatja egy időzítő által aktivált függvényt, amely az előzmények befejezése után a megadott időtartam alatt orchestration-példányok esetén kiürítése. Ebben az esetben eltávolítja az összes példány, vagy további 30 nappal ezelőtt befejezett adatokat. Minden nap, Éjfélkor egyszer futtatása van ütemezve:

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
> Az idő-eseményindítóval aktivált függvényt folyamat sikeres legyen, a futásidejű állapot kell **befejezve**, **kilépett**, vagy **sikertelen**.

### <a name="azure-functions-core-tools"></a>Az Azure Functions Core Tools

Egy vezénylési példány előzményeinek használatával törölheti az [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` parancsot. A második hasonló C# az előző szakaszban például kiürítése egy adott időtartam során létrehozott összes vezénylési példányok előzményeit. Példányok törlődnek további futásidejű állapot szerint is szűrheti. A parancs több paraméterekkel rendelkezik:

* **`created-after` (nem kötelező)** : Törölje az a dátum/idő (UTC) után létrehozott példányok előzményeit. ISO 8601 formátumú időpontok elfogadva.
* **`created-before` (nem kötelező)** : Törölje az a dátum/idő (UTC) előtt létrehozott példányok előzményeit. ISO 8601 formátumú időpontok elfogadva.
* **`runtime-status` (nem kötelező)** : Törölje az adott állapottal (például fut, vagy completed) példányok előzményeit. Több (szóközzel elválasztva) állapotot is biztosítanak.
* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet [host.json](durable-functions-bindings.md#host-json), durableTask:HubName használatával.

A következő parancs törli az összes sikertelen példányok este 7:35-kor (UTC) 2018. November 14. előtt létrehozott előzményeit.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Egy feladat központ törlése

Használatával a [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` paranccsal törölheti az adott feladat hubhoz társított összes tárolási összetevők. Ez magában foglalja az Azure storage-táblák, üzenetsorok és blobok. A parancs két paraméterrel rendelkezik:

* **`connection-string-setting` (nem kötelező)** : Az alkalmazás beállítást, a tárolási kapcsolati sztringjét tartalmazó neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : A használandó Durable Functions feladat hub nevét. A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet [host.json](durable-functions-bindings.md#host-json), durableTask:HubName használatával.

A következő parancsot az Azure storage társított minden adatot töröl a `UserTest` feladat hub.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, például felügyeleti HTTP API-k használata](durable-functions-http-api.md)
