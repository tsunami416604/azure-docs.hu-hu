---
title: Példányok kezelése a Durable Functionsban – Azure
description: Megtudhatja, hogyan kezelheti a példányokat a Azure Functions Durable Functions bővítményében.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 3db0cd3dd01e3f5f6af6b4b668d1ccac094624a2
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735171"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Durable Functions-példányok kezelése az Azure-ban

Ha a [Durable functions](durable-functions-overview.md) -bővítményt használja a Azure Functionshoz, vagy szeretné elindítani, győződjön meg róla, hogy a lehető leghatékonyabban használja ki. A Durable Functions-előkészítési példányok optimalizálásával további információkat tudhat meg a kezeléséről. Ez a cikk az egyes példányok kezelési műveleteinek részleteit ismerteti.

Elindíthatja és leállíthatja például a példányokat, és lekérdezheti a példányokat, beleértve az összes példány és a lekérdezési példányok lekérdezési lehetőségeit. Emellett elküldheti az eseményeket a példányokra, megvárhatja az előkészítési befejezést, és lekérheti a HTTP-kezelés webhook URL-címét. Ez a cikk más felügyeleti műveletekre is kiterjed, például a példányok újratekercselését, a példányok előzményeinek törlését és egy adott feladat központi törlését.

A Durable Functionsban lehetősége van arra, hogy hogyan kívánja megvalósítani ezeket a kezelési műveleteket. Ez a cikk példákat tartalmaz a [](../functions-run-local.md) .net (C#) és a JavaScript Azure functions Core Tools használatára.

## <a name="start-instances"></a>Példányok indítása

Fontos, hogy el tudja indítani a előkészítési példányát. Ez általában akkor történik, ha Durable Functions kötést használ egy másik függvény triggerében.

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) (.net) `startNew` `DurableOrchestrationClient` vagy a (JavaScript) [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metódusa új példányt indít el. Ennek az osztálynak a példányait a `orchestrationClient` kötés használatával szerzi be. Belsőleg ez a metódus enqueues egy üzenetet a vezérlési várólistába, amely ezután elindítja egy függvény indítását az `orchestrationTrigger` eseményindító kötést használó megadott névvel.

Ez az aszinkron művelet akkor fejeződik be, amikor a koordináló folyamat sikeresen ütemezve van. A előkészítési folyamatnak 30 másodpercen belül el kell indulnia. Ha a szolgáltatás tovább tart, megjelenik a `TimeoutException`következő:.

> [!WARNING]
> Amikor helyileg fejleszt a JavaScriptben, állítsa a környezeti `WEBSITE_HOSTNAME` `localhost:<port>` változót (például `localhost:7071`) a metódusok `DurableOrchestrationClient`használatára. Erről a követelményről a [GitHub-probléma](https://github.com/Azure/azure-functions-durable-js/issues/28)című cikkben olvashat bővebben.

### <a name="net"></a>.NET

A [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) paraméterek a következők:

* **Név**: Az ütemezni kívánt Orchestrator-függvény neve.
* **Bemenet**: Minden olyan JSON-szerializálható adat, amelyet át kell adni a Orchestrator függvény bemenetének.
* **InstanceId**: Választható A példány egyedi azonosítója. Ha nem megadja ezt a paramétert, a metódus véletlenszerű azonosítót használ.

Néhány példa:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

A paraméterek `startNew` a következők:

* **Név**: Az ütemezni kívánt Orchestrator-függvény neve.
* **InstanceId**: Választható A példány egyedi azonosítója. Ha nem megadja ezt a paramétert, a metódus véletlenszerű azonosítót használ.
* **Bemenet**: Választható Minden olyan JSON-szerializálható adat, amelyet át kell adni a Orchestrator függvény bemenetének.

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
> Használjon véletlenszerű azonosítót a példány-AZONOSÍTÓhoz. Ezzel biztosítható, hogy a Orchestrator függvények több virtuális gépen való skálázásakor a terheléselosztás egyenlő legyen. A nem véletlenszerű példány-azonosítók használatának megfelelő ideje az, ha az AZONOSÍTÓnak külső forrásból kell származnia, vagy ha az [egyszeres Orchestrator](durable-functions-singletons.md) mintát alkalmazza.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A példányokat közvetlenül is elindíthatja a [Azure functions Core Tools](../functions-run-local.md) `durable start-new` parancs használatával. A következő paramétereket veszi figyelembe:

* (kötelező): **`function-name`** Az elindítani kívánt függvény neve.
* (nem kötelező): **`input`** Adja meg a függvényt a beágyazott vagy egy JSON-fájlon keresztül. A fájlok esetében adjon hozzá egy előtagot a fájl `@`elérési útjához, `@path/to/file.json`például:.
* (nem kötelező): **`id`** A koordináló példány azonosítója. Ha nem megadja ezt a paramétert, a parancs véletlenszerű GUID azonosítót használ.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. Az alapértelmezett érték a AzureWebJobsStorage.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. Az alapértelmezett érték a DurableFunctionsHub. Ezt a [Host. JSON](durable-functions-bindings.md#host-json) fájlban is megadhatja a DurableTask: HubName használatával.

> [!NOTE]
> A Core Tools parancsai feltételezik, hogy egy Function alkalmazás gyökérkönyvtárában futtatja őket. Ha explicit módon megadja a és `connection-string-setting` `task-hub-name` a paramétereket, bármelyik címtárból futtathatja a parancsokat. Habár futtathatja ezeket a parancsokat egy futó Function app-gazdagép nélkül, előfordulhat, hogy bizonyos effektusok nem figyelhetők meg, kivéve, ha a gazdagép fut. A `start-new` parancs például enqueues a cél tevékenység központba, de a rendszer valójában nem fut, kivéve, ha van olyan Function app Host-folyamat, amely képes feldolgozni az üzenetet.

A következő parancs elindítja a HelloWorld nevű függvényt, és átadja a fájl `counter-data.json` tartalmát:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Lekérdezési példányok

A munkafolyamatok kezelésének részeként legvalószínűbb, hogy adatokat kell gyűjtenie egy összehangoló példány állapotáról (például azt, hogy az megfelelően fejeződött-e be vagy sikertelen volt-e).

A [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metódus a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztályban (.net `getStatus` ) vagy a (z) `DurableOrchestrationClient` osztályban (JavaScript) metódusa lekérdezi egy Meghívási példány állapotát.

Paraméterekként (kötelező), (nem kötelező `showHistoryOutput` ), (opcionális) és `showInput` (opcionális, .net). `instanceId` `showHistory`

* **`showHistory`** : Ha a értékre `true`van állítva, a válasz a végrehajtási előzményeket tartalmazza.
* **`showHistoryOutput`** : Ha a értékre `true`van állítva, a végrehajtási előzmények tevékenységek kimenetét tartalmazzák.
* **`showInput`** : Ha a értékre `false`van állítva, a válasz nem tartalmazza a függvény bemenetét. Az alapértelmezett érték `true`. (Csak .NET)

A metódus egy JSON-objektumot ad vissza, amely a következő tulajdonságokkal rendelkezik:

* **Név**: A Orchestrator függvény neve.
* **InstanceId**: A folyamat példányának azonosítója (a `instanceId` bemenetnek meg kell egyeznie).
* **CreatedTime**: Az az idő, amikor a Orchestrator függvény futása megkezdődött.
* **LastUpdatedTime**: Az az időpont, amikor a folyamat utolsó ellenőrzőpontja megvolt.
* **Bemenet**: A függvény bemenete JSON-értékként. Ez a mező nem töltődik fel `showInput` , ha hamis.
* **CustomStatus**: Egyéni előkészítési állapot JSON formátumban.
* **Kimenet**: A függvény kimenete JSON-értékként (ha a függvény befejeződött). Ha a Orchestrator függvény nem sikerült, ez a tulajdonság tartalmazza a hiba részleteit. Ha a Orchestrator függvény meg lett szakítva, ez a tulajdonság tartalmazza a megszakítás okát (ha van ilyen).
* **RuntimeStatus**: A következő értékek egyike:
  * **Függőben**: A példány ütemezve van, de még nem indult el.
  * **Futtatás**: A példány futása megkezdődött.
  * **Befejezve**: A példány szokásos módon befejeződött.
  * **ContinuedAsNew**: A példány újraindult egy új előzménysel. Ez egy átmeneti állapot.
  * **Sikertelen**: A példány hibával meghiúsult.
  * **Megszakítva**: A példány váratlanul leállt.
* **Előzmények**: A folyamat végrehajtási előzményei. Ez a mező csak akkor van feltöltve, `showHistory` ha `true`a értéke.

Ez a metódus `null` akkor tér vissza, ha a példány nem létezik, vagy még nem indult el.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Az [Azure functions Core Tools](../functions-run-local.md) `durable get-runtime-status` paranccsal közvetlenül is lekérheti egy összehangoló példány állapotát. A következő paramétereket veszi figyelembe:

* (kötelező): **`id`** A koordináló példány azonosítója.
* (nem kötelező): **`show-input`** Ha a értékre `true`van állítva, a válasz tartalmazza a függvény bemenetét. Az alapértelmezett érték `false`.
* (nem kötelező): **`show-output`** Ha a értékre `true`van állítva, a válasz tartalmazza a függvény kimenetét. Az alapértelmezett érték `false`.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

A következő parancs lekérdezi egy példány állapotát (beleértve a bemenetet és a kimenetet is), amely egy 0ab8c55a66644d68a3a8b220b12d209c-es előkészítési példány azonosítója. Feltételezi, hogy a `func` parancsot a Function alkalmazás gyökérkönyvtárában futtatja:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

A `durable get-history` parancs használatával lekérheti egy előkészítési példány előzményeit. A következő paramétereket veszi figyelembe:

* (kötelező): **`id`** A koordináló példány azonosítója.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A Host. JSON fájlban is megadható a durableTask: HubName használatával.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Összes példány lekérdezése

Ahelyett, hogy egyszerre egy példányt kelljen lekérdezni, érdemes lehet hatékonyabban lekérdezni őket egyszerre.

A (.net) `GetStatusAsync` vagy `getStatusAll` a (JavaScript) metódus használatával kérdezheti le az összes előkészítési példány állapotát. A .net-ben átadhat egy `CancellationToken` objektumot arra az esetre, ha meg szeretné szüntetni. A metódus azokat az objektumokat adja vissza, amelyek ugyanazokkal a tulajdonságokkal rendelkeznek, mint a `GetStatusAsync` paraméterekkel rendelkező metódus.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A példányokat közvetlenül is lekérdezheti az [Azure functions Core Tools](../functions-run-local.md) `durable get-instances` parancs használatával. A következő paramétereket veszi figyelembe:

* (nem kötelező): **`top`** Ez a parancs támogatja a lapozást. Ez a paraméter a beolvasott példányok számának felel meg. Az alapértelmezett érték 10.
* (nem kötelező): **`continuation-token`** A lekérdezni kívánt példányok lapját vagy szakaszát jelző jogkivonat. Mindegyik `get-instances` végrehajtás egy jogkivonatot ad vissza a következő példányoknak.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Példányok lekérdezése szűrőkkel

Mi a teendő, ha nem igazán szükséges minden olyan információ, amelyet egy standard példány lekérdezése tud biztosítani? Tegyük fel például, hogy mi a helyzet, ha éppen csak a előkészítési időt vagy a koordináló futásidejű állapotát keresi? A lekérdezést a szűrők alkalmazásával szűkítheti.

A `GetStatusAsync` (.net) vagy `getStatusBy` a (JavaScript) metódus használatával lekérheti az előre definiált szűrőket tartalmazó összehangoló példányok listáját.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A Azure functions Core Tools a `durable get-instances` parancsot szűrők segítségével is használhatja. A `top`fenti`created-after` `created-before` `runtime-status`, `continuation-token`, és Paraméterek`task-hub-name` mellett három szűrési paramétert (,, és) is használhat. `connection-string-setting`

* (nem kötelező): **`created-after`** A dátum/idő (UTC) után létrehozott példányok beolvasása. ISO 8601 formázott dátum/idő.
* (nem kötelező): **`created-before`** A dátum/idő (UTC) előtt létrehozott példányok beolvasása. ISO 8601 formázott dátum/idő.
* (nem kötelező): **`runtime-status`** A példányok beolvasása adott állapottal (például futó vagy befejezett). Több (szóközzel tagolt) állapotot is biztosíthat.
* (nem kötelező): **`top`** A beolvasott példányok száma kérelem szerint. Az alapértelmezett érték 10.
* (nem kötelező): **`continuation-token`** A lekérdezni kívánt példányok lapját vagy szakaszát jelző jogkivonat. Mindegyik `get-instances` végrehajtás egy jogkivonatot ad vissza a következő példányoknak.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

Ha nem ad meg szűrőket (`created-after`, `created-before`vagy `runtime-status`), a parancs egyszerűen lekéri `top` a példányokat, és nem számít a futtatókörnyezet állapota vagy a létrehozás ideje.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Példányok leállítása

Ha olyan előkészítési példánnyal rendelkezik, amely túl sokáig tart, vagy csak le kell állítania, mielőtt bármilyen okból befejeződik, lehetősége van lemondani.

Használhatja a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.net) `terminate` [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metódusát vagy az `DurableOrchestrationClient` osztály metódusát (JavaScript). A két paraméter egy `instanceId` és egy `reason` karakterlánc, amely a naplókba és a példányok állapotára van írva. A leállított példányok leállnak, amint elérik a következő `await` (.net) vagy `yield` (JavaScript) pontot, vagy azonnal leáll, ha az már egy `await` vagy `yield`.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> A példányok megszakítása jelenleg nem terjed ki. A tevékenység-és alfolyamatok a befejezésig futnak, függetlenül attól, hogy megszakította-e a hívó példányát.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A [Azure functions Core Tools](../functions-run-local.md) `durable terminate` parancs használatával közvetlenül is leállíthatja az előkészítési példányt. A következő paramétereket veszi figyelembe:

* (kötelező): **`id`** A leállítani kívánt összehangoló példány azonosítója.
* (nem kötelező): **`reason`** A megszüntetés oka.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

A következő parancs leállítja a 0ab8c55a66644d68a3a8b220b12d209c AZONOSÍTÓval rendelkező előkészítési példányt:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Események küldése példányoknak

Bizonyos helyzetekben fontos, hogy a Orchestrator függvények várni tudják a külső események figyelését. Ez magában foglalja az [emberi interakcióra](durable-functions-concepts.md#human)váró [figyelési funkciókat](durable-functions-concepts.md#monitoring) és függvényeket.

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.net `raiseEvent` ) [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metódusának vagy a `DurableOrchestrationClient` Class (JavaScript) metódusának használatával küldheti el az eseményekre vonatkozó értesítéseket a példányok futtatásához. Azok a példányok, amelyek képesek kezelni ezeket az eseményeket, a [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.net) vagy `waitForExternalEvent` (JavaScript) hívására várnak.

A [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.net) és `raiseEvent` a (JavaScript) paraméterek a következők:

* **InstanceId**: A példány egyedi azonosítója.
* **EventName**: A küldendő esemény neve.
* **EventData**: A példányba küldendő JSON-szerializálható hasznos adat.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!IMPORTANT]
> Ha nincs a megadott AZONOSÍTÓJÚ előkészítési példány, vagy ha a példány nem várakozik a megadott esemény nevére, az esemény üzenetét a rendszer elveti. További információ erről a viselkedésről: GitHub- [probléma](https://github.com/Azure/azure-functions-durable-extension/issues/29).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A [Azure functions Core Tools](../functions-run-local.md) `durable raise-event` parancs használatával közvetlenül is felvehet egy eseményt egy előkészítési példányra. A következő paramétereket veszi figyelembe:

* (kötelező): **`id`** A koordináló példány azonosítója.
* (nem kötelező): **`event-name`** A emelni kívánt esemény neve. A mező alapértelmezett értéke: `$"Event_{RandomGUID}"`.
* (nem kötelező): **`event-data`** Az előkészítési példánynak küldendő adatértékek. Ez lehet egy JSON-fájl elérési útja, vagy közvetlenül a parancssorban is megadható az információ.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Várakozás a koordinálás befejezésére

A hosszan futó előkészítésekben érdemes lehet megvárni, és beolvasni egy előkészítés eredményeit. Ezekben az esetekben hasznos lehet az időtúllépési időszak definiálása is az előkészítés során. Ha túllépi az időtúllépést, a rendszer az eredmények helyett visszaadja az előkészítési állapotot.

A [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály egy [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API-t tesz elérhetővé a .net-ben. Ezzel az API-val szinkron módon lekérheti a tényleges kimenetet egy összehangoló példányból. A JavaScriptben az `DurableOrchestrationClient` osztály ugyanazon célra teszi `waitForCompletionOrCreateCheckStatusResponse` elérhetővé az API-t. Ha nincsenek beállítva, a metódusok alapértelmezett értékként 10 másodpercet `timeout`, a pedig 1 `retryInterval`másodpercet használnak.  

Az alábbi példa egy HTTP-trigger függvényt mutat be, amely bemutatja, hogyan használhatja ezt az API-t:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Hívja meg a függvényt a következő sorral. Az újrapróbálkozási időköz 2 másodpercét használja az időtúllépéshez és a 0,5 másodperchez:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Attól függően, hogy milyen időre van szükség a hanghívási példány válaszának beszerzéséhez, két eset létezik:

* A előkészítési példányok a megadott időkorláton belül (ebben az esetben 2 másodpercben) befejeződik, és a válasz a tényleges előkészítési példány kimenete, amely szinkron módon történik:

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

* A előkészítési példányok nem hajthatók végre a megadott időkorláton belül, és a válasz az alapértelmezett érték a [http API URL-címének felderítése](durable-functions-http-api.md)című témakörben:

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
> A webhook URL-címeinek formátuma eltérő lehet attól függően, hogy a Azure Functions gazdagép melyik verzióját futtatja. Az előző példa a Azure Functions 2. x gazdagépre mutat.

## <a name="retrieve-http-management-webhook-urls"></a>HTTP-felügyelet webhook URL-címeinek lekérése

Egy külső rendszer használatával figyelheti vagy növelheti az eseményeket egy előkészítési folyamaton keresztül. A külső rendszerek a [http API URL-címének felderítése](durable-functions-http-api.md)részben ismertetett alapértelmezett válasz részét képező Webhook URL-címeken keresztül kommunikálhatnak a Durable Functionsokkal. A webhook URL-címei azonban programozott módon is elérhetők a koordináló ügyfélben vagy egy tevékenység-függvényben. Ezt a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) osztály (.net) `createHttpManagementPayload` [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) metódusával vagy az `DurableOrchestrationClient` osztály (JavaScript) metódusának használatával teheti meg.

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) és `createHttpManagementPayload` egy paraméterrel:

* **instanceId**: A példány egyedi azonosítója.

A metódusok a [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) (.net) vagy egy objektum (JavaScript) egy példányát adják vissza a következő sztring tulajdonságokkal:

* **Azonosító**: A folyamat példányának azonosítója (a `InstanceId` bemenetnek meg kell egyeznie).
* **StatusQueryGetUri**: A koordináló példány állapotának URL-címe
* **SendEventPostUri**: A hangolási példány "esemény előléptetése" URL-címe.
* **TerminatePostUri**: A koordináló példány "leállítási" URL-címe.
* **RewindPostUri**: A hangolási példány "Rewind" URL-címe.

A Activity functions ezen objektumok egy példányát külső rendszereknek küldi el az események figyeléséhez vagy az eseményekre való előléptetéséhez:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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

## <a name="rewind-instances-preview"></a>Példányok visszatekerése (előzetes verzió)

Ha nem várt okból végez előkészítési hibát *, a példányt* visszahelyezheti egy korábban Kifogástalan állapotba egy erre a célra létrehozott API használatával.

> [!NOTE]
> Ez az API nem helyettesíti a megfelelő hibakezelés és újrapróbálkozási házirendeket. Ehelyett csak olyan esetekben javasolt használni, ahol a hangszerelési példányok váratlan okok miatt sikertelenek. A hibakezelés és az újrapróbálkozási szabályzatokkal kapcsolatos további információkért tekintse [meg a hibakezelés](durable-functions-error-handling.md) témakört.

A [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.net) vagy `rewindAsync` a (JavaScript) API használatával állítsa vissza a hangelőkészítést a *futó* állapotba. Futtassa újra a felépítési hibát okozó tevékenységek vagy alfolyamatok végrehajtási hibáit.

Tegyük fel például, hogy van egy munkafolyamata, amely egy sor [emberi jóváhagyást](durable-functions-concepts.md#human)tartalmaz. Tegyük fel, hogy van olyan tevékenységi függvény, amely értesíti valakit, hogy jóváhagyása szükséges, és várjon a valós idejű válaszra. Ha az összes jóváhagyási tevékenység kapott választ vagy időtúllépés történt, tegyük fel, hogy egy másik tevékenység meghiúsul az alkalmazások helytelen konfigurációja miatt, például érvénytelen adatbázis-kapcsolati karakterlánc. Ennek eredményeképpen a munkafolyamat mélyen bekerül a munkafolyamatba. A ( `RewindAsync` .net) vagy `rewindAsync` a (JavaScript) API használatával az alkalmazás rendszergazdája kijavíthatja a konfigurációs hibát, és a hiba előtt visszatekerheti a sikertelen előkészítést az állapotba. Az emberi beavatkozást nem igénylő lépések egyikét sem kell újra jóváhagyni, és a folyamat sikeresen elvégezhető.

> [!NOTE]
> A *visszatekerés* funkció nem támogatja a tartós időzítőket használó előkészítési példányok használatát.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A [Azure functions Core Tools](../functions-run-local.md) `durable rewind` parancs használatával közvetlenül is visszahelyezheti az előkészítési példányokat. A következő paramétereket veszi figyelembe:

* (kötelező): **`id`** A koordináló példány azonosítója.
* (nem kötelező): **`reason`** Az előkészítési példány visszatekerésének oka.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Példányok előzményeinek kiürítése

Ha el szeretné távolítani a folyamathoz társított összes adatmennyiséget, törölheti a példányok előzményeit. Előfordulhat például, hogy szeretné megszabadulni az Azure Table-sorokból és a nagyméretű üzenet-blobokból, ha vannak ilyenek. Ehhez használja a [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) API-t.

> [!NOTE]
> Az `PurgeInstanceHistoryAsync` API jelenleg csak a esetében C#érhető el.

 A metódus két túlterheléssel rendelkezik. Az első kiüríti az előzményeket a rendszerindítási példány azonosítója szerint:

### <a name="c"></a>C#

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

A második példa egy időzítő által aktivált függvényt mutat be, amely a megadott időintervallum után befejezett összes előkészítési példány előzményeit törli. Ebben az esetben az összes példányhoz tartozó, 30 vagy több nappal ezelőtt befejezett összes példányra vonatkozó adatvesztést távolítja el. Napi egyszeri futtatásra van ütemezve, 12 ÓRAKOR:

### <a name="c"></a>C#

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
> Ahhoz, hogy az idő által aktivált függvény sikeres legyen, a futásidejű állapotot be kell **fejezni**, le kell **zárni**vagy **sikertelennek**kell lennie.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

A [Azure functions Core Tools](../functions-run-local.md) `durable purge-history` parancs használatával törölheti az előkészítési példány előzményeit. Az előző szakaszban szereplő C# második példához hasonlóan az a megadott időintervallumban létrehozott összes összehangoló példány előzményeit is törli. A kitörölhető példányok tovább szűrhetők futtatókörnyezeti állapot alapján. A parancsnak több paramétere van:

* (nem kötelező): **`created-after`** A dátum/idő (UTC) után létrehozott példányok előzményeinek kiürítése. ISO 8601 formázott dátum/idő.
* (nem kötelező): **`created-before`** A dátum/idő (UTC) előtt létrehozott példányok előzményeinek kiürítése. ISO 8601 formázott dátum/idő.
* (nem kötelező): **`runtime-status`** Egy adott állapotú példányok előzményeinek kiürítése (például futó vagy befejezett). Több (szóközzel tagolt) állapotot is biztosíthat.
* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

A következő parancs törli a 2018 november 14. előtt létrehozott összes sikertelen példány előzményeit az 7:35 PM (UTC) időpontban.

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Feladat központ törlése

Az [Azure functions Core Tools](../functions-run-local.md) `durable delete-task-hub` parancs használatával törölheti az adott feladathoz társított összes tárolási összetevőt. Ez magában foglalja az Azure Storage-táblákat,-várólistákat és-blobokat. A parancsnak két paramétere van:

* (nem kötelező): **`connection-string-setting`** A használandó tárolási kapcsolódási karakterláncot tartalmazó Alkalmazásbeállítás neve. A mező alapértelmezett értéke: `AzureWebJobsStorage`.
* (nem kötelező): **`task-hub-name`** A használni kívánt Durable Functions feladat-központ neve. A mező alapértelmezett értéke: `DurableFunctionsHub`. A [Host. JSON](durable-functions-bindings.md#host-json)fájlban is megadható a DurableTask: HubName használatával.

A következő parancs törli a `UserTest` tevékenység központhoz társított összes Azure Storage-adatát.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan használhatja a HTTP API-kat a példányok kezeléséhez](durable-functions-http-api.md)