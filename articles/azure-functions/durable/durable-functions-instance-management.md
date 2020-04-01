---
title: Példányok kezelése a tartós funkciókban – Azure
description: Ismerje meg, hogyan kezelheti a példányokat az Azure Functions Tartós függvények bővítményében.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476830"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Példányok kezelése az Azure-ban lévő tartós függvényekben

Ha az Azure Functions [tartós függvények](durable-functions-overview.md) bővítményt használja, vagy szeretné elkezdeni ezt, győződjön meg arról, hogy a lehető legjobb anameddig használja. Optimalizálhatja a durable functions vezénylési példányok további kezelésükkel. Ez a cikk az egyes példánykezelési műveletek részleteit ismerteti.

Például elindíthatja és megszüntetheti a példányokat, és lekérdezheti a példányokat, beleértve az összes példány és lekérdezési példány okainak lekérdezését szűrőkkel. Emellett eseményeket küldhet a példányoknak, megvárhatja a vezénylési befejezést, és lekérheti a HTTP-kezelés webhook URL-címeit. Ez a cikk más felügyeleti műveleteket is lefed, beleértve a példányok visszatekerését, a példányelőzmények törlését és a feladatközpont törlését.

A Tartós függvények párbeszédpanelen lehetőség van arra, hogy miként kívánja végrehajtani ezeket a felügyeleti műveleteket. Ez a cikk példákat, amelyek az [Azure Functions Core tools](../functions-run-local.md) a .NET (C#) és a JavaScript.

## <a name="start-instances"></a>Kezdő példányok

Fontos, hogy képes legyen elindítani a vezénylési példányt. Ez általában akkor történik, ha egy másik függvény eseményindítójában tartós függvények kötést használ.

A `StartNewAsync` `startNew` [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client) (.NET) vagy (JavaScript) metódusa új példányt indít el. Belsőleg ez a metódus egy üzenetet várólistára küld a vezérlővárólistába, amely ezután elindítja a megadott nevű függvény indítását, amely a [vezénylési eseményindító kötést](durable-functions-bindings.md#orchestration-trigger)használja.

Ez az aszinkron művelet akkor fejeződik be, amikor a vezénylési folyamat sikeresen ütemezve van.

Az új vezénylési példány indításának paraméterei a következők:

* **Név**: Az ütemezendő orchestrator függvény neve.
* **Bemenet:** Bármely JSON-szerializálható adatokat, amelyeket át kell adni, mint a bemenetaz orchestrator függvény.
* **InstanceId**: (Nem kötelező) A példány egyedi azonosítója. Ha nem adja meg ezt a paramétert, a metódus véletlenszerű azonosítót használ.

> [!TIP]
> Használjon véletlenszerű azonosítót a példányazonosítóhoz. A véletlenszerű példányazonosítók biztosítják az egyenlő terheléselosztást, ha több virtuális gépen skálázod az orchestrator függvényeket. A nem véletlenszerű példányazonosítók használatának megfelelő ideje az, amikor az azonosítónak külső forrásból kell származnia, vagy amikor az [egyes vezénylési](durable-functions-singletons.md) mintát valósítja meg.

A következő kód egy példafüggvény, amely új vezénylési példányt indít el:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Eltérő rendelkezés hiányában az ezen az oldalon található példák a HTTP-eseményindítót használják a következő function.json függvényekkel.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Ez a példa a Durable Functions 2.x-es verzióját célozza meg. Az 1.x verzióban `orchestrationClient` `durableClient`használja a helyett.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

Egy példányt közvetlenül is elindíthat az [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` paranccsal. A következő paramétereket veszi igénybe:

* (kötelező) : A kezdéshez szükséges függvény neve. ** `function-name` **
* (nem kötelező) : Bemenet a függvénybe, akár inline, akár JSON fájlon keresztül. ** `input` ** Fájlok esetén adjon előtagot a fájl elérési `@`útjának a segítségével, például `@path/to/file.json`.
* (nem kötelező) : A vezénylési példány azonosítója. ** `id` ** Ha nem adja meg ezt a paramétert, a parancs véletlenszerű GUID azonosítót használ.
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** Az alapértelmezett beállítás az AzureWebJobsStorage.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** Az alapértelmezett érték durablefunctionshub. Ezt a [host.json](durable-functions-bindings.md#host-json) ban is beállíthatja a durableTask:HubName használatával.

> [!NOTE]
> A Core Tools parancsok feltételezik, hogy egy függvényalkalmazás gyökérkönyvtárából futtatja őket. Ha explicit módon `connection-string-setting` adja `task-hub-name` meg a paramétereket, a parancsokat bármely könyvtárból futtathatja. Bár ezeket a parancsokat a függvényalkalmazás gazdagépfuttatása nélkül is futtathatja, előfordulhat, hogy bizonyos hatásokat csak akkor figyelhet meg, ha az állomás fut. Például a `start-new` parancs várólistára helyezi a célfeladat-központba, de a vezénylési ténylegesen nem fut, kivéve, ha egy függvényalkalmazás gazdafolyamat fut, amely fel tudja dolgozni az üzenetet.

A következő parancs elindítja a HelloWorld nevű függvényt, és átadja neki a fájl `counter-data.json` tartalmát:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Lekérdezési példányok

A vezénylések kezelésére tett erőfeszítés részeként valószínűleg információkat kell gyűjtenie egy vezénylési példány állapotáról (például arról, hogy a vezénylési folyamat befejeződött-e vagy sem).

A `GetStatusAsync` (.NET) `getStatus` vagy a (JavaScript) metódus a [vezénylési ügyfélkötésen](durable-functions-bindings.md#orchestration-client) egy vezénylési példány állapotát kérdezi.

Paraméterekként `instanceId` (kötelező), `showHistory` (nem `showHistoryOutput` kötelező), `showInput` (nem kötelező) és (nem kötelező) kerül.

* **`showHistory`**: Ha `true`a beállítás a, a válasz tartalmazza a végrehajtási előzményeket.
* **`showHistoryOutput`**: Ha `true`a beállítás, a végrehajtási előzmények tevékenységkimeneteket tartalmaznak.
* **`showInput`**: Ha `false`a beállítás, a válasz nem tartalmazza a függvény bemenetét. Az alapértelmezett érték `true`.

A metódus a következő tulajdonságokkal rendelkező objektumot adja vissza:

* **Név**: Az orchestrator függvény neve.
* **InstanceId**: A vezénylés példányazonosítója `instanceId` (meg kell egyeznie a bemeneti).
* **CreatedTime**: Az az időpont, amikor az orchestrator függvény futásba kezdett.
* **LastUpdatedTime**: A vezénylés utolsó ellenőrzőpontjának időpontja.
* **Bemenet**: A függvény JSON-értékként való bevitele. Ez a mező nem `showInput` kerül kitöltve, ha hamis.
* **CustomStatus**: Egyéni vezénylési állapot JSON formátumban.
* **Kimenet**: A függvény JSON-értékként való kimenete (ha a függvény befejeződött). Ha az orchestrator függvény sikertelen, ez a tulajdonság tartalmazza a hiba részleteit. Ha az orchestrator függvény leállt, ez a tulajdonság tartalmazza a megszüntetés okát (ha van ilyen).
* **RuntimeStatus**: Az alábbi értékek egyike:
  * **Függőben**: A példány ütemezve van, de még nem indult el.
  * **Futás**: A példány futása megkezdődött.
  * **Befejezett**: A példány a szokásos módon fejeződött be.
  * **ContinuedAsNew**: A példány újraindította magát egy új előzményekkel. Ez az állapot átmeneti állapot.
  * **Nem sikerült**: A példány hiba miatt nem sikerült.
  * **Terminated**: A példány hirtelen leállt.
* **Előzmények**: A vezénylés végrehajtási előzményei. Ez a mező `showHistory` csak akkor `true`lesz feltöltve, ha a .

Ez a `null` metódus (.NET) vagy `undefined` (JavaScript) értéket ad vissza, ha a példány nem létezik.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

Az [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` paranccsal is lehetővé teszi egy vezénylési példány állapotának leírása. A következő paramétereket veszi igénybe:

* (kötelező) : A vezénylési példány azonosítója. ** `id` **
* (nem kötelező) : `true`Ha a beállítás, a válasz tartalmazza a bemenet a függvény. ** `show-input` ** Az alapértelmezett érték `false`.
* (nem kötelező) : `true`Ha a beállítás, a válasz tartalmazza a függvény kimenetét. ** `show-output` ** Az alapértelmezett érték `false`.
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet állítani [a host.json](durable-functions-bindings.md#host-json), használatával durableTask:HubName.

A következő parancs lekéri egy 0ab8c55a66644d68a3a8b20b12d209c vezénylési példányazonosítóval rendelkező példány állapotát (beleértve a bemenetet és a kimenetet is). Feltételezi, hogy a parancsot a `func` függvényalkalmazás gyökérkönyvtárából futtatja:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

A `durable get-history` parancs segítségével lekérheti egy vezénylési példány előzményeit. A következő paramétereket veszi igénybe:

* (kötelező) : A vezénylési példány azonosítója. ** `id` **
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet állítani a host.json használatával durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Az összes példány lekérdezése

Ahelyett, hogy egyszerre egy példányt kérdezne le a vezénylési területen, hatékonyabbnak találhatja az összes lekérdezését egyszerre.

A (.NET) vagy `GetStatusAsync` `getStatusAll` a (JavaScript) metódus segítségével lekérdezheti az összes vezénylési példány állapotát. A .NET-ben átadhat egy objektumot `CancellationToken` arra az esetre, ha meg szeretné szakítani. A metódus a paraméterekkel rendelkező `GetStatusAsync` metódussal azonos tulajdonságokkal rendelkező objektumokat ad vissza.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

Az [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` paranccsal közvetlenül is lekérdezheti a példányokat. A következő paramétereket veszi igénybe:

* (nem kötelező) : Ez a parancs támogatja a lapozást. ** `top` ** Ez a paraméter a kérésenként lekért példányok számának felel meg. Az alapértelmezett érték 10.
* (nem kötelező) : A token, amely jelzi, hogy melyik oldal vagy a példányok szakasza letölteni. ** `continuation-token` ** Minden `get-instances` végrehajtás egy jogkivonatot ad vissza a következő példánykészletnek.
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet állítani [a host.json](durable-functions-bindings.md#host-json), használatával durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Lekérdezési példányok szűrőkkel

Mi a teendő, ha nincs szüksége az összes olyan információra, amelyet egy szabványos példánylekérdezés nyújthat? Például mi van, ha csak a vezénylési létrehozási időt vagy a vezénylési futásidejű állapotot keresi? Szűrők alkalmazásával szűkítheti a lekérdezést.

A `GetStatusAsync` (.NET) `getStatusBy` vagy a (JavaScript) metódus segítségével leírhatja az előre definiált szűrők készletének megfelelő vezénylési példányok listáját.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
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

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

Az Azure Functions Core Tools, a `durable get-instances` parancs szűrőkkel is használhatja. A `top`fent említett , `continuation-token`, `connection-string-setting`, `task-hub-name` és paraméterek mellett három szűrőparamétert`created-after` `created-before`( `runtime-status`, , és a ).

* (nem kötelező) : A dátum/idő (UTC) után létrehozott példányok lekérése. ** `created-after` ** ISO 8601 formázott datetimes elfogadva.
* (nem kötelező) : A dátum/idő (UTC) előtt létrehozott példányok lekérése. ** `created-before` ** ISO 8601 formázott datetimes elfogadva.
* (nem kötelező) : Az adott állapotú példányok lekérése (például futás vagy befejezett). ** `runtime-status` ** Több (szóközrel elválasztott) állapotot is megadhat.
* (nem kötelező) : Kérésenként lekért példányok száma. ** `top` ** Az alapértelmezett érték 10.
* (nem kötelező) : A token, amely jelzi, hogy melyik oldal vagy a példányok szakasza letölteni. ** `continuation-token` ** Minden `get-instances` végrehajtás egy jogkivonatot ad vissza a következő példánykészletnek.
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet állítani [a host.json](durable-functions-bindings.md#host-json), használatával durableTask:HubName.

Ha nem ad meg`created-after`szűrőket ( , `created-before`vagy `runtime-status` `top` ), a parancs egyszerűen lekéri a példányokat, tekintet nélkül a futásidejű állapotra vagy a létrehozási időre.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Példányok leállítása

Ha van egy vezénylési példány, amely túl sokáig tart a futtatása, vagy csak le kell állítania, mielőtt bármilyen okból befejeződik, lehetősége van arra, hogy megszünteti azt.

A `TerminateAsync` `terminate` [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client) (.NET) vagy a (JavaScript) metódus használatával leállítja a példányokat. A két paraméter `instanceId` egy `reason` és egy karakterlánc, amelyek a naplókba és a példány állapotába vannak írva.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

A megszüntetett példány végül `Terminated` átkerül az állapotba. Ez az átmenet azonban nem fog azonnal bekövetkezni. Ehelyett a leállítási művelet lesz várólistára a feladatközpontban, valamint más műveleteket az adott példányhoz. A példány [lekérdezési](#query-instances) API-k segítségével tudni, ha egy `Terminated` leszakított példány ténylegesen elérte az állapotot.

> [!NOTE]
> A példányok leállítása jelenleg nem propagálható. A tevékenységfüggvények és az alvezőtevékenységek a befejezésig futnak, függetlenül attól, hogy megszüntette-e az őket megnevező vezénylési példányt.

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

Az [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` paranccsal közvetlenül is leállíthat egy vezénylési példányt. A következő paramétereket veszi igénybe:

* (szükséges) : A vezénylési példány id-ja a leállításához. ** `id` **
* (nem kötelező) : a megszüntetés oka. ** `reason` **
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet állítani [a host.json](durable-functions-bindings.md#host-json), használatával durableTask:HubName.

A következő parancs 0ab8c55a6644d68a3a8b220b12d209c azonosítóval leállít egy vezénylési példányt:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Események küldése példányokra

Bizonyos esetekben fontos, hogy az orchestrator-függvények képesek legyenek várni és figyelni a külső eseményeket. Ez magában foglalja az emberi [interakcióra](durable-functions-overview.md#human)váró [monitorfunkciókat](durable-functions-overview.md#monitoring) és funkciókat .

Eseményértesítések küldése futó példányok `RaiseEventAsync` segítségével a (.NET) metódus vagy a `raiseEvent` (JavaScript) metódus a [vezénylési ügyfél kötés](durable-functions-bindings.md#orchestration-client). Azok a példányok, amelyek kezelni tudják ezeket az eseményeket, azok, amelyek hívásra `WaitForExternalEvent` vagy javascript-hívásra `waitForExternalEvent` várnak.

A (.NET) és `RaiseEventAsync` `raiseEvent` a (JavaScript) paraméterek a következők:

* **InstanceId**: A példány egyedi azonosítója.
* **EventName**: A küldendő esemény neve.
* **EventData**: A JSON-szerializálható hasznos adat a példánynak küld.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

> [!NOTE]
> Ha nincs vezénylési példány a megadott példányazonosítóval, az eseményüzenet elvetésre kerül. Ha egy példány létezik, de még nem vár az eseményre, az esemény a példány állapotában lesz tárolva, amíg készen nem áll a fogadásra és a feldolgozásra.

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

Az [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` paranccsal egy eseményt közvetlenül is megemelhet egy vezénylési példányra. A következő paramétereket veszi igénybe:

* (kötelező) : A vezénylési példány azonosítója. ** `id` **
* **`event-name`**: Az emelendő esemény neve.
* (nem kötelező) : A vezénylési példánynak küldő adatok. ** `event-data` ** Ez lehet egy JSON-fájl elérési útja, vagy közvetlenül a parancssorban is megadható.
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** A mező alapértelmezett értéke: `DurableFunctionsHub`. Azt is be lehet állítani [a host.json](durable-functions-bindings.md#host-json), használatával durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Várakozás a vezénylés befejezésére

A hosszú ideig futó vezénylések, érdemes lehet várni, és egy vezénylési eredményeket. Ezekben az esetekben az is hasznos, hogy képes legyen meghatározni egy időbeli elhangzatidási időszakot a vezénylési. Ha az időtúllépés túllépi, a vezénylés állapotát kell visszaadni az eredmények helyett.

A `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) `waitForCompletionOrCreateCheckStatusResponse` vagy a (JavaScript) metódus segítségével a tényleges kimenet egy vezénylési példány szinkron módon. Alapértelmezés szerint ezek a módszerek alapértelmezett értéke `timeout`10 másodperc `retryInterval`a esetén, a esetén pedig 1 másodperc.  

Íme egy példa a HTTP-trigger függvényre, amely bemutatja az API használatát:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

Hívja meg a függvényt a következő sokkal. Az újrapróbálkozási időközhöz 2 másodpercet, az újrapróbálkozási időközt pedig 0,5 másodpercet használjon:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Attól függően, hogy a válasz a vezénylési példány, két esetben:

* A vezénylési példányok a megadott időouton belül fejeződnek be (ebben az esetben 2 másodperc), és a válasz a tényleges vezénylési példány kimenete, szinkron módon kézbesítve:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* A vezénylési példányok nem fejezhetők be a megadott időtúlponton belül, és a válasz az alapértelmezett, amelyet a [HTTP API URL-felderítése](durable-functions-http-api.md)ismertet:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> A webhook URL-címek formátuma eltérő lehet attól függően, hogy az Azure Functions gazdagép melyik verzióját futtatja. Az előző példa az Azure Functions 2.0-s állomás.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP-kezelés webhook URL-címei

Egy külső rendszer segítségével figyelheti vagy emelheti az eseményeket egy vezénylési. A külső rendszerek a [HTTP API URL-felderítésében](durable-functions-http-features.md#http-api-url-discovery)leírt alapértelmezett válasz részét használó webhook URL-eken keresztül kommunikálhatnak a tartós funkciókkal. A webhook URL-címek programozott módon is elérhetők a [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client)használatával. A `CreateHttpManagementPayload` (.NET) `createHttpManagementPayload` vagy a (JavaScript) metódusok segítségével lejuthat egy szerializálható objektum, amely tartalmazza ezeket a webhook URL-eket.

A `CreateHttpManagementPayload` (.NET) `createHttpManagementPayload` és a (JavaScript) metódusok egy paraméterrel rendelkeznek:

* **instanceId**: A példány egyedi azonosítója.

A metódusok a következő karakterlánctulajdonságokkal rendelkező objektumot adnak vissza:

* **Azonosító:** A vezénylés példányazonosítója (meg `InstanceId` kell egyeznie a bemeneti).
* **StatusQueryGetUri**: A vezénylési példány állapotURL-címe.
* **SendEventPostUri**: A vezénylési példány "raise event" URL-címe.
* **TerminatePostUri**: A vezénylési példány "terminate" URL-címe.
* **PurgeHistoryDeleteUri**: A vezénylési példány "törlési előzmények" URL-címe.

A függvények külső rendszereknek küldhetnek példányokat a megfelelő vezénylési események figyelésére vagy elővetésére, ahogy az a következő példákban látható:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
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

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Tartós függvények 1.x `DurableActivityContext` esetén `IDurableActivityContext`a helyett `OrchestrationClient` az attribútumot `DurableClient` kell használnia az attribútum `DurableOrchestrationClient` helyett, `IDurableOrchestrationClient`és a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

## <a name="rewind-instances-preview"></a>Példányok visszatekerése (előzetes verzió)

Ha egy vezénylési hiba egy váratlan okból, *visszatekerheti* a példányt egy korábban kifogástalan állapotú egy erre a célra készült API használatával.

> [!NOTE]
> Ez az API nem helyettesíti a megfelelő hibakezelési és újrapróbálkozási házirendeket. Ehelyett csak olyan esetekben használható, ahol a vezénylési példányok váratlan okok miatt sikertelenek. A hibakezelésről és az újrapróbálkozási házirendekről a [Hibakezelés](durable-functions-error-handling.md) című cikkben olvashat bővebben.

Használja `RewindAsync` a `rewind` [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client) (.NET) vagy (JavaScript) metódust a vezénylési ügyfélkötéshez, hogy a vezénylési állapotot visszahelyezze a *futó* állapotba. Ez a módszer is újrafuttatja a tevékenység vagy al-vezénylési végrehajtási hibák, amelyek a vezénylési hibát okozott.

Tegyük fel például, hogy van egy munkafolyamata, amely emberi [jóváhagyások](durable-functions-overview.md#human)sorozatát foglalja magában. Tegyük fel, hogy vannak olyan tevékenységfüggvények, amelyek értesítik a valakit, hogy a jóváhagyásra van szükség, és várja ki a valós idejű választ. Miután az összes jóváhagyási tevékenység kapott válaszokat, vagy időtúllépés, tegyük fel, hogy egy másik tevékenység sikertelen egy alkalmazás helytelen konfigurációja miatt, például egy érvénytelen adatbázis-kapcsolati karakterlánc miatt. Az eredmény egy vezénylési hiba mélyen a munkafolyamatban. A `RewindAsync` (.NET) `rewind` vagy (JavaScript) API-val az alkalmazás rendszergazdája kijavíthatja a konfigurációs hibát, és visszatekerheti a sikertelen vezénylési fázist közvetlenül a hiba előtt. Az emberi interakciós lépések egyikét sem kell újra jóváhagyni, és a vezénylés most már sikeresen befejeződhet.

> [!NOTE]
> A *visszatekerési* szolgáltatás nem támogatja a tartós időzítőket használó vezénylési példányok visszatekerését.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

A vezénylési példány t közvetlenül az [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` paranccsal is visszatekerheti. A következő paramétereket veszi igénybe:

* (kötelező) : A vezénylési példány azonosítója. ** `id` **
* (nem kötelező) : A vezénylési példány visszatekerésének oka. ** `reason` **
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** Alapértelmezés szerint a feladatközpont neve a [host.json](durable-functions-bindings.md#host-json) fájlban használatos.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Példányelőzmények törlése

A vezényléshez társított összes adat eltávolításához törölheti a példányelőzményeket. Például érdemes törölni az Azure Table sorok és a befejezett példánytársított nagy üzenetblobok. Ehhez használja a `PurgeInstanceHistoryAsync` `purgeInstanceHistory` [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client)(.NET) vagy (JavaScript) metódusát.

Ez a módszer két túlterheléssel rendelkezik. Az első túlterhelés törli az előzményeket a vezénylési példány azonosítója szerint:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Lásd: A function.json konfiguráció [kezdő példányai.](#javascript-function-json)

---

A következő példa egy időzítő által aktivált függvényt jelenít meg, amely törli az összes vezénylési példány oktatását, amely a megadott időintervallum után fejeződött be. Ebben az esetben eltávolítja az adatokat az összes befejezett példány30 vagy több nappal ezelőtt. A tervek szerint naponta egyszer, 12:00 órakor fog futni:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
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
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A `purgeInstanceHistoryBy` módszer segítségével feltételesen kiürítése példányelőzmények több példány.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Ez a példa a Durable Functions 2.x-es verzióját célozza meg. Az 1.x verzióban `orchestrationClient` `durableClient`használja a helyett.

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> A kiürítési előzményművelet sikerességéhez a célpéldány futásidejű állapotának **befejezettnek**, **megszakítottnak**vagy **sikertelennek**kell lennie.

### <a name="azure-functions-core-tools"></a>Az Azure Functions alapvető eszközei

Az [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` paranccsal törölheti a vezénylési példány előzményeit. Az előző szakaszban a második C# példához hasonlóan törli az összes vezénylési példány előzményét, amelyet egy megadott időintervallumban hozott létre. A megtisztított példányokat tovább szűrheti futásidejű állapot szerint. A parancsnak több paramétere van:

* (nem kötelező) : A dátum/idő (UTC) után létrehozott példányok előzményeinek törlése. ** `created-after` ** ISO 8601 formázott datetimes elfogadva.
* (nem kötelező) : A dátum/idő (UTC) előtt létrehozott példányok előzményeinek törlése. ** `created-before` ** ISO 8601 formázott datetimes elfogadva.
* (nem kötelező) : Egy adott állapotú (például futás vagy befejezett) példányok előzményeinek törlése. ** `runtime-status` ** Több (szóközrel elválasztott) állapotot is megadhat.
* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** Alapértelmezés szerint a feladatközpont neve a [host.json](durable-functions-bindings.md#host-json) fájlban használatos.

A következő parancs törli a 2018.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Feladatközpont törlése

Az [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` paranccsal törölheti az adott feladatközponthoz társított összes tárolási összetevőt, beleértve az Azure storage-táblákat, várólistákat és blobokat. A parancsnak két paramétere van:

* (nem kötelező) : A használandó tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. ** `connection-string-setting` ** A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező) : A használandó Tartós funkciók feladatközpont neve. ** `task-hub-name` ** Alapértelmezés szerint a feladatközpont neve a [host.json](durable-functions-bindings.md#host-json) fájlban használatos.

A következő parancs törli a `UserTest` feladatközponthoz társított összes Azure storage-adatot.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a verziószámozás kezeléséről](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Beépített HTTP API-hivatkozás a példánykezeléshez](durable-functions-http-api.md)
