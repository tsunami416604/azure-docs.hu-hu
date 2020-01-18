---
title: Példányok kezelése a Durable Functionsban – Azure
description: Megtudhatja, hogyan kezelheti a példányokat a Azure Functions Durable Functions bővítményében.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 43094fe91921d1399650d9cf47e7a84c47996cd5
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261568"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Durable Functions-példányok kezelése az Azure-ban

Ha a [Durable functions](durable-functions-overview.md) -bővítményt használja a Azure Functionshoz, vagy szeretné elindítani, győződjön meg róla, hogy a lehető leghatékonyabban használja ki. A Durable Functions-előkészítési példányok optimalizálásával további információkat tudhat meg a kezeléséről. Ez a cikk az egyes példányok kezelési műveleteinek részleteit ismerteti.

Elindíthatja és leállíthatja például a példányokat, és lekérdezheti a példányokat, beleértve az összes példány és a lekérdezési példányok lekérdezési lehetőségeit. Emellett elküldheti az eseményeket a példányokra, megvárhatja az előkészítési befejezést, és lekérheti a HTTP-kezelés webhook URL-címét. Ez a cikk más felügyeleti műveletekre is kiterjed, például a példányok újratekercselését, a példányok előzményeinek törlését és egy adott feladat központi törlését.

A Durable Functionsban lehetősége van arra, hogy hogyan kívánja megvalósítani ezeket a kezelési műveleteket. Ez a cikk példákat tartalmaz a [](../functions-run-local.md) .net (C#) és a JavaScript Azure functions Core Tools használatára.

## <a name="start-instances"></a>Példányok indítása

Fontos, hogy el tudja indítani a előkészítési példányát. Ez általában akkor történik, ha Durable Functions kötést használ egy másik függvény triggerében.

A koordinációs [ügyfél-kötés](durable-functions-bindings.md#orchestration-client) `StartNewAsync` (.net) vagy `startNew` (JavaScript) metódusa új példányt indít el. Belsőleg ez a metódus enqueues egy üzenetet a vezérlési várólistába, amely ezután elindítja egy függvény indítását a megadott névvel, amely a koordinációs [eseményindító kötését](durable-functions-bindings.md#orchestration-trigger)használja.

Ez az aszinkron művelet akkor fejeződik be, amikor a koordináló folyamat sikeresen ütemezve van.

Az új előkészítési példányok elindításának paraméterei a következők:

* **Name (név**): az ütemezni kívánt Orchestrator-függvény neve.
* **Bemenet**: minden olyan JSON-szerializálható adat, amelyet át kell adni a Orchestrator függvény bemenetének.
* **InstanceId**: (nem kötelező) a példány egyedi azonosítója. Ha nem megadja ezt a paramétert, a metódus véletlenszerű azonosítót használ.

> [!TIP]
> Használjon véletlenszerű azonosítót a példány-AZONOSÍTÓhoz. A véletlenszerű példányok azonosítói lehetővé teszik, hogy a Orchestrator függvények több virtuális gépen való skálázásakor egyenlő terhelési eloszlást biztosítson. A nem véletlenszerű példány-azonosítók használatának megfelelő ideje az, ha az AZONOSÍTÓnak külső forrásból kell származnia, vagy ha az [egyszeres Orchestrator](durable-functions-singletons.md) mintát alkalmazza.

A következő kód egy példaként szolgáló függvény, amely egy új összehangoló példányt indít el:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Ha másként nincs megadva, az ezen a lapon szereplő példák a HTTP-triggert használják a következő function. JSON fájl használatával.

**function. JSON**

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
> Ez a példa Durable Functions 2. x verziót céloz meg. Az 1. x verzióban `durableClient`helyett használja a `orchestrationClient`.

**index. js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A példányokat közvetlenül is elindíthatja a [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` parancs használatával. A következő paramétereket veszi figyelembe:

* **`function-name` (kötelező)** : az elindítani kívánt függvény neve.
* **`input` (nem kötelező)** : bemenet a függvénybe, vagy beágyazott, vagy egy JSON-fájlon keresztül. A fájlok esetében adjon hozzá egy előtagot a fájl elérési útjához `@`, például `@path/to/file.json`.
* **`id` (nem kötelező)** : a koordináló példány azonosítója. Ha nem megadja ezt a paramétert, a parancs véletlenszerű GUID azonosítót használ.
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. Az alapértelmezett érték a AzureWebJobsStorage.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. Az alapértelmezett érték a DurableFunctionsHub. Ezt a [Host. JSON](durable-functions-bindings.md#host-json) fájlban is megadhatja a DurableTask: HubName használatával.

> [!NOTE]
> A Core Tools parancsai feltételezik, hogy egy Function alkalmazás gyökérkönyvtárában futtatja őket. Ha explicit módon megadja a `connection-string-setting` és `task-hub-name` paramétereket, bármelyik címtárból futtathatja a parancsokat. Habár futtathatja ezeket a parancsokat egy futó Function app-gazdagép nélkül, előfordulhat, hogy bizonyos effektusok nem figyelhetők meg, kivéve, ha a gazdagép fut. Például az `start-new` parancs enqueues a cél feladatsorba, de a rendszer valójában nem futtatja a folyamatot, kivéve, ha van olyan Function app Host-folyamat, amely képes feldolgozni az üzenetet.

A következő parancs elindítja a HelloWorld nevű függvényt, és átadja a fájl tartalmát `counter-data.json`:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Lekérdezési példányok

A munkafolyamatok kezelésének részeként legvalószínűbb, hogy adatokat kell gyűjtenie egy összehangoló példány állapotáról (például azt, hogy az megfelelően fejeződött-e be vagy sikertelen volt-e).

A `GetStatusAsync` (.NET) vagy a `getStatus` (JavaScript) metódust a koordinációs [ügyfél kötése](durable-functions-bindings.md#orchestration-client) lekérdezi egy összehangoló példány állapotát.

`instanceId` (kötelező), `showHistory` (nem kötelező), `showHistoryOutput` (opcionális) és `showInput` (opcionális) paraméterként is igénybe vesz.

* **`showHistory`** : ha a `true`értékre van állítva, a válasz a végrehajtási előzményeket tartalmazza.
* **`showHistoryOutput`** : ha a `true`értékre van állítva, a végrehajtási előzmények tevékenységek kimenetét tartalmazzák.
* **`showInput`** : ha a `false`értékre van állítva, a válasz nem tartalmazza a függvény bemenetét. Az alapértelmezett érték 0.`true`

A metódus egy olyan objektumot ad vissza, amely a következő tulajdonságokkal rendelkezik:

* **Name (név**): a Orchestrator függvény neve.
* **InstanceId**: a folyamat példányának azonosítója (a `instanceId` bemenetével megegyezőnek kell lennie).
* **CreatedTime**: az az idő, amikor a Orchestrator-függvény futása megkezdődött.
* **LastUpdatedTime**: a folyamat utolsó ellenőrzőpontjának időpontja.
* **Bemenet**: a függvény BEMENETe JSON-értékként. Ez a mező nincs feltöltve, ha `showInput` hamis.
* **CustomStatus**: egyéni előkészítési állapot JSON formátumban.
* **Kimenet**: a függvény kimenete JSON-értékként (ha a függvény befejeződött). Ha a Orchestrator függvény nem sikerült, ez a tulajdonság tartalmazza a hiba részleteit. Ha a Orchestrator függvény meg lett szakítva, ez a tulajdonság tartalmazza a megszakítás okát (ha van ilyen).
* **RuntimeStatus**: az alábbi értékek egyike:
  * **Függőben**: a példány ütemezve van, de még nem indult el.
  * **Fut**: a példány elindult.
  * **Befejezett**: a példány szokásos módon befejeződött.
  * **ContinuedAsNew**: a példány újraindult egy új előzménysel. Ez az állapot átmeneti állapot.
  * **Sikertelen**: a példány hibával meghiúsult.
  * Leállítva: a példány leállítása váratlanul **megszakadt**.
* **Előzmények**: a folyamat végrehajtási előzményei. Ez a mező csak akkor van feltöltve, ha `showHistory` `true`értékre van állítva.

Ez a metódus `null` (.NET) vagy `undefined` (JavaScript) értéket ad vissza, ha a példány nem létezik.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Az [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` paranccsal közvetlenül is lekérheti egy összehangoló példány állapotát. A következő paramétereket veszi figyelembe:

* **`id` (kötelező)** : a koordináló példány azonosítója.
* **`show-input` (nem kötelező)** : ha a `true`értékre van állítva, a válasz tartalmazza a függvény bemenetét. Az alapértelmezett érték 0.`false`
* **`show-output` (nem kötelező)** : ha a `true`értékre van állítva, a válasz tartalmazza a függvény kimenetét. Az alapértelmezett érték 0.`false`
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

A következő parancs lekérdezi egy példány állapotát (beleértve a bemenetet és a kimenetet is), amely egy 0ab8c55a66644d68a3a8b220b12d209c-es előkészítési példány azonosítója. Feltételezi, hogy a `func` parancsot a Function alkalmazás gyökérkönyvtárában futtatja:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

A `durable get-history` parancs használatával lekérheti egy előkészítési példány előzményeit. A következő paramétereket veszi figyelembe:

* **`id` (kötelező)** : a koordináló példány azonosítója.
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A Host. JSON fájlban is megadható a durableTask: HubName használatával.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Összes példány lekérdezése

Ahelyett, hogy egyszerre egy példányt kelljen lekérdezni, érdemes lehet hatékonyabban lekérdezni őket egyszerre.

A `GetStatusAsync` (.NET) vagy a `getStatusAll` (JavaScript) metódus használatával kérdezheti le az összes összeszerelési példány állapotát. A .NET-ben átadhat egy `CancellationToken` objektumot arra az esetre, ha meg szeretné szakítani. A metódus azokat az objektumokat adja vissza, amelyek ugyanazokkal a tulajdonságokkal rendelkeznek, mint a paraméterekkel rendelkező `GetStatusAsync` metódus.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A példányokat közvetlenül is lekérdezheti az [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` parancs használatával. A következő paramétereket veszi figyelembe:

* **`top` (nem kötelező)** : Ez a parancs támogatja a lapozást. Ez a paraméter a beolvasott példányok számának felel meg. Az alapértelmezett érték 10.
* **`continuation-token` (nem kötelező)** : a lekérdezni kívánt példányok oldalát vagy szakaszát jelző token. Minden `get-instances` végrehajtás egy jogkivonatot ad vissza a következő példányoknak.
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Példányok lekérdezése szűrőkkel

Mi a teendő, ha nem igazán szükséges minden olyan információ, amelyet egy standard példány lekérdezése tud biztosítani? Tegyük fel például, hogy mi a helyzet, ha éppen csak a előkészítési időt vagy a koordináló futásidejű állapotát keresi? A lekérdezést a szűrők alkalmazásával szűkítheti.

A `GetStatusAsync` (.NET) vagy a `getStatusBy` (JavaScript) metódussal lekérheti az előre definiált szűrők készletének megfelelő összehangoló példányok listáját.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A Azure Functions Core Tools az `durable get-instances` parancsot is használhatja szűrőkkel. A fenti `top`, `continuation-token`, `connection-string-setting`és `task-hub-name` paraméterek mellett három szűrési paramétert is használhat (`created-after`, `created-before`és `runtime-status`).

* **`created-after` (nem kötelező)** : a dátum/idő (UTC) után létrehozott példányok beolvasása. ISO 8601 formázott dátum/idő.
* **`created-before` (nem kötelező)** : az ezen dátum/idő (UTC) előtt létrehozott példányok beolvasása. ISO 8601 formázott dátum/idő.
* **`runtime-status` (nem kötelező)** : a példányok beolvasása adott állapottal (például futó vagy befejezett). Több (szóközzel tagolt) állapotot is biztosíthat.
* **`top` (nem kötelező)** : a beolvasott példányok száma kérelem alapján. Az alapértelmezett érték 10.
* **`continuation-token` (nem kötelező)** : a lekérdezni kívánt példányok oldalát vagy szakaszát jelző token. Minden `get-instances` végrehajtás egy jogkivonatot ad vissza a következő példányoknak.
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

Ha nem ad meg szűrőket (`created-after`, `created-before`vagy `runtime-status`), a parancs egyszerűen lekéri `top` példányokat, és nem tekinti a futásidejű állapotot vagy a létrehozási időt.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Példányok leállítása

Ha olyan előkészítési példánnyal rendelkezik, amely túl sokáig tart, vagy csak le kell állítania, mielőtt bármilyen okból befejeződik, lehetősége van lemondani.

A példányok leállításához használhatja a koordinációs [ügyfél kötésének](durable-functions-bindings.md#orchestration-client) `TerminateAsync` (.net) vagy `terminate` (JavaScript) metódusát. A két paraméter egy `instanceId` és egy `reason` karakterlánc, amely a naplókba és a példányok állapotára van írva. A leállított példányok azonnal leállnak, amint eléri a következő `await` (.NET) vagy `yield` (JavaScript) pontot, vagy ha már egy `await` vagy `yield`.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

> [!NOTE]
> A példányok megszakítása jelenleg nem terjed ki. A tevékenység-és alfolyamatok a befejezésig futnak, függetlenül attól, hogy megszakította-e a hívó példányát.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` parancs használatával közvetlenül is leállíthatja az előkészítési példányt. A következő paramétereket veszi figyelembe:

* **`id` (kötelező)** : a leállítani kívánt összehangoló példány azonosítója.
* **`reason` (nem kötelező)** : a megszakítás oka.
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

A következő parancs leállítja a 0ab8c55a66644d68a3a8b220b12d209c AZONOSÍTÓval rendelkező előkészítési példányt:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Események küldése példányoknak

Bizonyos helyzetekben fontos, hogy a Orchestrator függvények várni tudják a külső események figyelését. Ez magában foglalja az [emberi interakcióra](durable-functions-overview.md#human)váró [figyelési funkciókat](durable-functions-overview.md#monitoring) és függvényeket.

Értesítéseket küldhet a példányok futtatásához a `RaiseEventAsync` (.NET) metódus vagy a koordinációs [ügyfél](durable-functions-bindings.md#orchestration-client)`raiseEvent` (JavaScript) metódusának használatával. Az ilyen eseményeket kezelő példányok olyanok, amelyek a `WaitForExternalEvent` (.NET) hívására várnak, vagy egy `waitForExternalEvent` (JavaScript-) hívást eredményeznek.

A `RaiseEventAsync` (.NET) és `raiseEvent` (JavaScript) paraméterek a következők:

* **InstanceId**: a példány egyedi azonosítója.
* **EventName**: a küldendő esemény neve.
* **EventData**: a példányba küldendő JSON-szerializálható hasznos adat.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

> [!NOTE]
> Ha nincs a megadott példány-AZONOSÍTÓval rendelkező előkészítési példány, az esemény üzenetét a rendszer elveti. Ha egy példány létezik, de még nem vár az eseményre, az esemény a példány állapotban lesz tárolva, amíg készen nem áll a fogadásra és a feldolgozásra.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` parancs használatával közvetlenül is felvehet egy eseményt egy előkészítési példányra. A következő paramétereket veszi figyelembe:

* **`id` (kötelező)** : a koordináló példány azonosítója.
* **`event-name`** : a felvenni kívánt esemény neve.
* **`event-data` (nem kötelező)** : az előkészítési példánynak küldendő adatértékek. Ez lehet egy JSON-fájl elérési útja, vagy közvetlenül a parancssorban is megadható az információ.
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Várakozás a koordinálás befejezésére

A hosszan futó előkészítésekben érdemes lehet megvárni, és beolvasni egy előkészítés eredményeit. Ezekben az esetekben hasznos lehet az időtúllépési időszak definiálása is az előkészítés során. Ha túllépi az időtúllépést, a rendszer az eredmények helyett visszaadja az előkészítési állapotot.

A `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) vagy a `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) metódussal lehet lekérni a tényleges kimenetet egy előkészítési példányból. Alapértelmezés szerint ezek a módszerek a `timeout`esetében 10 másodpercet, a `retryInterval`pedig 1 másodpercet használnak.  

Az alábbi példa egy HTTP-trigger függvényt mutat be, amely bemutatja, hogyan használhatja ezt az API-t:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

Hívja meg a függvényt a következő sorral. Az újrapróbálkozási időköz 2 másodpercét használja az időtúllépéshez és a 0,5 másodperchez:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Attól függően, hogy milyen időre van szükség a hanghívási példány válaszának beszerzéséhez, két eset létezik:

* A előkészítési példányok a megadott időkorláton belül (ebben az esetben 2 másodpercben) befejeződik, és a válasz a tényleges előkészítési példány kimenete, amely szinkron módon történik:

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

* A előkészítési példányok nem hajthatók végre a megadott időkorláton belül, és a válasz az alapértelmezett érték a [http API URL-címének felderítése](durable-functions-http-api.md)című témakörben:

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
> A webhook URL-címeinek formátuma eltérő lehet attól függően, hogy a Azure Functions gazdagép melyik verzióját futtatja. Az előző példa a Azure Functions 2,0 gazdagépre mutat.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP-felügyelet webhook URL-címeinek lekérése

Egy külső rendszer használatával figyelheti vagy kiemelheti az eseményeket egy előkészítési folyamatba. A külső rendszerek a [http API URL-címének felderítése](durable-functions-http-features.md#http-api-url-discovery)részben ismertetett alapértelmezett válasz részét képező Webhook URL-címeken keresztül kommunikálhatnak a Durable Functionsokkal. A webhook URL-címei elérhetővé tehetik programozott módon a koordináló [ügyfél kötésének](durable-functions-bindings.md#orchestration-client)használatával. A `CreateHttpManagementPayload` (.NET) vagy a `createHttpManagementPayload` (JavaScript) metódusok használhatók egy olyan szerializálható objektum lekérésére, amely tartalmazza ezeket a webhook URL-címeket.

A `CreateHttpManagementPayload` (.NET) és a `createHttpManagementPayload` (JavaScript) metódusoknak egyetlen paramétere van:

* **instanceId**: a példány egyedi azonosítója.

A metódusok egy objektumot adnak vissza a következő sztring tulajdonságokkal:

* **Azonosító**: a (z) összehangoló példány azonosítója (a `InstanceId` bemenettel megegyezőnek kell lennie).
* **StatusQueryGetUri**: a koordináló példány állapotának URL-címe.
* **SendEventPostUri**: az "esemény előléptetése" URL-cím az előkészítési példányhoz.
* **TerminatePostUri**: a megszakító példány URL-címe.
* **PurgeHistoryDeleteUri**: a előkészítési példány "törlési előzmények" URL-címe.

A függvények ezeket az objektumokat külső rendszerekre is elküldhetik, hogy az alábbi példákban látható módon figyelik vagy emeljék az eseményeket.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `IDurableActivityContext`helyett a `DurableActivityContext`t kell használnia, a `DurableClient` attribútum helyett a `OrchestrationClient` attribútumot kell használnia, és `DurableOrchestrationClient` helyett a `IDurableOrchestrationClient`paramétert kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

## <a name="rewind-instances-preview"></a>Példányok visszatekerése (előzetes verzió)

Ha nem várt okból végez előkészítési hibát *, a példányt* visszahelyezheti egy korábban Kifogástalan állapotba egy erre a célra létrehozott API használatával.

> [!NOTE]
> Ez az API nem helyettesíti a megfelelő hibakezelés és újrapróbálkozási házirendeket. Ehelyett csak olyan esetekben javasolt használni, ahol a hangszerelési példányok váratlan okok miatt sikertelenek. A hibakezelés és az újrapróbálkozási szabályzatokkal kapcsolatos további információkért tekintse meg [a hibakezelés című cikket.](durable-functions-error-handling.md)

A koordinációs [ügyfél-kötés](durable-functions-bindings.md#orchestration-client) `RewindAsync` (.net) vagy `rewind` (JavaScript) metódusának használatával állítsa vissza a koordinációt a *futó* állapotba. Ezzel a módszerrel a rendszer Újrafuttatja a tevékenység-vagy alfolyamatok végrehajtásával kapcsolatos hibákat is, amelyek az eljárási hibát okozták.

Tegyük fel például, hogy van egy munkafolyamata, amely egy sor [emberi jóváhagyást](durable-functions-overview.md#human)tartalmaz. Tegyük fel, hogy van olyan tevékenységi függvény, amely értesíti valakit, hogy jóváhagyása szükséges, és várjon a valós idejű válaszra. Ha az összes jóváhagyási tevékenység kapott választ vagy időtúllépés történt, tegyük fel, hogy egy másik tevékenység meghiúsul az alkalmazások helytelen konfigurációja miatt, például érvénytelen adatbázis-kapcsolati karakterlánc. Ennek eredményeképpen a munkafolyamat mélyen bekerül a munkafolyamatba. Ha a `RewindAsync` (.NET) vagy a `rewind` (JavaScript) API-t futtatja, az alkalmazás rendszergazdája kijavíthatja a konfigurációs hibát, és közvetlenül a hiba előtt visszatekerheti a sikertelen előkészítést az állapotba. Az emberi beavatkozást nem igénylő lépések egyikét sem kell újra jóváhagyni, és a folyamat sikeresen elvégezhető.

> [!NOTE]
> A *visszatekerés* funkció nem támogatja a tartós időzítőket használó előkészítési példányok használatát.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` parancs használatával közvetlenül is visszahelyezheti a összehangoló példányokat. A következő paramétereket veszi figyelembe:

* **`id` (kötelező)** : a koordináló példány azonosítója.
* **`reason` (nem kötelező)** : az előkészítési példány visszatekerésének oka.
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. Alapértelmezés szerint a rendszer a (z [) Host. JSON](durable-functions-bindings.md#host-json) fájlban lévő Task hub-nevet használja.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Példányok előzményeinek kiürítése

Ha el szeretné távolítani a folyamathoz társított összes adatmennyiséget, törölheti a példányok előzményeit. Előfordulhat például, hogy törölni szeretné a befejezett példányhoz társított összes Azure Table sort és nagyméretű üzenet-blobot. Ehhez használja a koordinációs [ügyfél kötésének](durable-functions-bindings.md#orchestration-client)`PurgeInstanceHistoryAsync` (.net) vagy `purgeInstanceHistory` (JavaScript) metódusát.

Ez a metódus két túlterheléssel rendelkezik. Az első túlterhelési kiüríti az előzményeket a koordináló példány azonosítója szerint:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Lásd: a function. JSON-konfiguráció [indítási példányai](#javascript-function-json) .

---

A következő példa egy időzítő által aktivált függvényt mutat be, amely kiüríti az összes olyan előkészítési példány előzményeit, amely a megadott időintervallum után fejeződött be. Ebben az esetben az összes példányhoz tartozó, 30 vagy több nappal ezelőtt befejezett összes példányra vonatkozó adatvesztést távolítja el. Napi egyszeri futtatásra van ütemezve, 12 ÓRAKOR:

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A `purgeInstanceHistoryBy` módszer használatával több példány esetében feltételesen törölheti a példányok előzményeit.

**function. JSON**

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
> Ez a példa Durable Functions 2. x verziót céloz meg. Az 1. x verzióban `durableClient`helyett használja a `orchestrationClient`.

**index. js**

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
> Ahhoz, hogy a törlési előzmények sikeresek legyenek, a célként megadott példány futásidejű állapotát be kell **fejezni**, meg kell **szakítani**, vagy **sikertelennek**kell lennie.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` parancs használatával törölheti az előkészítési példány előzményeit. Az előző szakaszban szereplő C# második példához hasonlóan az a megadott időintervallumban létrehozott összes összehangoló példány előzményeit is törli. A kitörölhető példányok tovább szűrhetők futtatókörnyezeti állapot alapján. A parancsnak több paramétere van:

* **`created-after` (nem kötelező)** : a dátum/idő (UTC) után létrehozott példányok előzményeinek kiürítése. ISO 8601 formázott dátum/idő.
* **`created-before` (nem kötelező)** : az ezen dátum/idő (UTC) előtt létrehozott példányok előzményeinek kiürítése. ISO 8601 formázott dátum/idő.
* **`runtime-status` (nem kötelező)** : a példányok előzményeinek kiürítése adott állapottal (például futó vagy befejezett). Több (szóközzel tagolt) állapotot is biztosíthat.
* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. Alapértelmezés szerint a rendszer a (z [) Host. JSON](durable-functions-bindings.md#host-json) fájlban lévő Task hub-nevet használja.

A következő parancs törli a 2018 november 14. előtt létrehozott összes sikertelen példány előzményeit az 7:35 PM (UTC) időpontban.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Feladat központ törlése

A [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` parancs használatával törölheti az adott feladathoz társított összes tárolási összetevőt, beleértve az Azure Storage-táblákat, a várólistákat és a blobokat is. A parancsnak két paramétere van:

* **`connection-string-setting` (nem kötelező)** : a használni kívánt tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* **`task-hub-name` (nem kötelező)** : a használni kívánt Durable functions Task hub neve. Alapértelmezés szerint a rendszer a (z [) Host. JSON](durable-functions-bindings.md#host-json) fájlban lévő Task hub-nevet használja.

A következő parancs törli az `UserTest` Task hub-hoz társított összes Azure Storage-adatát.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató a verziószámozás kezeléséhez](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Beépített HTTP API-referenciák a példányok kezeléséhez](durable-functions-http-api.md)