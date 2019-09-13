---
title: Kötések a Durable Functionshoz – Azure
description: Eseményindítók és kötések használata a Azure Functions Durable Functions bővítményéhez.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: ba3a30328df488dfe79bf445da550b5c96019081
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933486"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions kötései (Azure Functions)

A [Durable functions](durable-functions-overview.md) bővítmény két új trigger kötést vezet be, amelyek a Orchestrator és a Activity függvények végrehajtását vezérlik. Emellett egy kimeneti kötést is bevezet, amely ügyfélként funkcionál az Durable Functions futtatókörnyezet számára.

## <a name="orchestration-trigger"></a>Előkészítési trigger

Az előkészítési trigger lehetővé teszi [tartós Orchestrator függvények](durable-functions-types-features-overview.md#orchestrator-functions)készítését. Ez az aktiválás támogatja az új Orchestrator-függvények indítását és a meglévő Orchestrator-függvények lefolytatását, amelyek "várnak" egy feladatot.

Ha a Visual Studio-eszközöket használja a Azure Functionshoz, a [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .net-attribútummal konfigurálhatja a előkészítési triggert.

Ha Orchestrator-függvényeket ír a parancsfájlkezelési nyelveken (például JavaScript C# vagy Scripting), a rendszer az összehangoló triggert a `bindings` *function. JSON* fájl tömbben a következő JSON-objektummal definiálja:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`a-előkészítés neve. Ez az az érték, amelyet az ügyfeleknek használniuk kell, amikor el szeretnék indítani a Orchestrator függvény új példányait. Ez a tulajdonság nem kötelező. Ha nincs megadva, a rendszer a függvény nevét használja.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólisták sorát kérdezi le. Ezek a várólisták a bővítmény belső implementációjának részletei, ezért nincsenek explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Trigger viselkedése

Íme néhány Megjegyzés az előkészítési triggerről:

* Egyszálas – egyetlen kiosztó szál használatos egyetlen gazdagép-példányon az összes Orchestrator függvény végrehajtásához. Ezért fontos, hogy a Orchestrator funkció kódja hatékony legyen, és nem hajt végre semmilyen I/O-műveletet. Fontos továbbá, hogy a szál ne végezzen aszinkron munkát, kivéve, ha Durable Functions-specifikus feladattípusra vár.
* **Méreg – üzenetkezelés** – a rendszer nem támogatja a hangfeldolgozási eseményindítók használatát.
* **Üzenet láthatósága** – a hangelőkészítési trigger üzenetei el vannak különítve, és a konfigurálható időtartamra láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – a visszatérési értékek a JSON-ba vannak szerializálva, és az Azure Table Storage-ben megőrzött az előkészítési előzmények táblázata. Ezeket a visszaadott értékeket az összehangoló ügyfél kötése kérdezheti le, amely később van leírva.

> [!WARNING]
> A Orchestrator függvények soha nem használhatnak semmilyen bemeneti vagy kimeneti kötést, amely nem az előkészítési trigger kötése. Ennek köszönhetően problémák léphetnek fel a tartós feladattal, mivel ezek a kötések nem engedelmeskednek az egyszálas és az I/O-szabályoknak. Ha más kötéseket szeretne használni, vegye fel őket a Orchestrator függvény által hívott tevékenység-függvénybe.

> [!WARNING]
> A JavaScript Orchestrator funkcióit soha nem `async`lehet deklarálni.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

A előkészítési trigger kötése a bemeneteket és kimeneteket is támogatja. Íme néhány tudnivaló a bemeneti és kimeneti használatról:

* **bemenetek** – a .net-hangolási függvények csak a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) támogatják. A bemenetek közvetlenül a függvény aláírásában való deszerializálása nem támogatott. A kódnak a [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.net) vagy `getInput` a (JavaScript) metódust kell használnia a Orchestrator függvény bemenetének beolvasásához. Ezeknek a bemeneteknek JSON-szerializálható típusúnak kell lenniük.
* **kimenetek** – a hangelőkészítési eseményindítók támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és csak JSON-szerializálható lehet. Ha egy .net-függvény `Task` visszaadja `void`a `null` értéket, a rendszer a kimenetként menti az értékeket.

### <a name="trigger-sample"></a>Példa triggerre

A következő mintakód azt szemlélteti, hogy a legegyszerűbb ""Helló világ!"alkalmazás" Orchestrator függvény a következőképpen néz ki:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> A `context` JavaScriptben lévő objektum nem a DurableOrchestrationContext, hanem az [egész függvény kontextusát](../functions-reference-node.md#context-object)jelöli. A koordináló metódusok az `context` `df` objektum tulajdonságán keresztül érhetők el.

> [!NOTE]
> A JavaScript-rendszerszervezőket érdemes használni `return`. A `durable-functions` kódtár gondoskodik a `context.done` metódus meghívásáról.

A legtöbb Orchestrator függvény hívja a Activity functions funkciót, ezért itt látható egy ""Helló világ!"alkalmazás" példa, amely bemutatja, hogyan hívhat meg egy tevékenységi függvényt:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Tevékenység-trigger

A tevékenység-trigger lehetővé teszi olyan függvények készítését, amelyeket a Orchestrator függvények, más néven a [tevékenység-függvények](durable-functions-types-features-overview.md#activity-functions)hívnak.

Ha a Visual studiót használja, a tevékenység-trigger a [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET attribútum használatával van konfigurálva.

Ha a vs Code-ot vagy a Azure Portal fejlesztési célokra használja, a tevékenység-triggert a `bindings` *function. JSON*tömbben a következő JSON-objektum határozza meg:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`a tevékenység neve. Ez az érték annak a névnek a neve, amelyet a Orchestrator függvények a tevékenység függvényének meghívására használnak. Ez a tulajdonság nem kötelező. Ha nincs megadva, a rendszer a függvény nevét használja.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólistát kérdezi le. Ez a várólista a bővítmény belső implementációjának részletes adatai, ezért nincs explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Trigger viselkedése

Íme néhány Megjegyzés a tevékenység-triggerről:

* **Szál** – a előkészítési eseményindítótól eltérően a tevékenység-eseményindítók nem rendelkeznek a Threading vagy az I/O műveletekkel kapcsolatos korlátozásokkal. Ugyanúgy kezelhetők, mint a hagyományos függvények.
* **Méreg – üzenetkezelés** – a tevékenység-eseményindítók nem rendelkeznek a Megmérgező üzenet támogatásával.
* **Üzenet láthatósága** – a tevékenység-trigger üzenetei el vannak különítve, és a konfigurálható időtartamig láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – a visszatérési értékek a JSON-ba vannak szerializálva, és az Azure Table Storage-ben megőrzött az előkészítési előzmények táblázata.

> [!WARNING]
> A Activity functions tárolási háttere a megvalósítás részletei, és a felhasználói kód nem használható közvetlenül ezekkel a tároló entitásokkal.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

A tevékenység-trigger kötés támogatja a bemeneteket és a kimeneteket is, ugyanúgy, mint a előkészítési trigger. Íme néhány tudnivaló a bemeneti és kimeneti használatról:

* **bemenetek** – a .net-tevékenység funkciói natív módon használják a [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) paraméter típusúként. Azt is megteheti, hogy egy tevékenység-függvény deklarálható bármely olyan típusparaméter-típussal, amely JSON-szerializálható. A használatakor `DurableActivityContext`meghívhatja a [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) a tevékenységi függvény bemenetének beolvasásához és deszerializálásához.
* **kimenetek** – a tevékenységi függvények támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és csak JSON-szerializálható lehet. Ha egy .net-függvény `Task` visszaadja `void`a `null` értéket, a rendszer a kimenetként menti az értékeket.
* **metaadatok** – a .net-tevékenység functions kötést tud kötni egy `string instanceId` paraméterrel, hogy lekérje a szülő-előkészítés példányának azonosítóját.

### <a name="trigger-sample"></a>Példa triggerre

Az alábbi mintakód azt mutatja be, hogy egy egyszerű ""Helló világ!"alkalmazás" tevékenységi funkció hogyan nézhet ki:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

A .net `ActivityTriggerAttribute` - `DurableActivityContext`kötés alapértelmezett paramétere a következő:. A .NET-tevékenység-eseményindítók azonban közvetlenül a JSON-serializeable-típusokhoz is támogatják a kötéseket (beleértve az egyszerű típusokat is), így ugyanezt a funkciót a következőképpen egyszerűsítheti:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

A JavaScript-kötések további paraméterekként is átadhatók, így az alábbi módon lehet egyszerűsíteni a műveletet:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Bemeneti és kimeneti kötések használata

A tevékenység-trigger kötés mellett normál bemeneti és kimeneti kötéseket is használhat. Például megteheti a bemenetet a tevékenység kötéséhez, és üzenetet küldhet egy EventHub a EventHub kimeneti kötés használatával:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Előkészítési ügyfél

A hangelőkészítési ügyfél kötése lehetővé teszi a Orchestrator functions szolgáltatással kommunikáló függvények írását. Ezeket a függvényeket időnként ügyfél- [függvénynek](durable-functions-types-features-overview.md#client-functions)is nevezzük. Például a következő módokon végezheti el az előkészítési példányok megszervezését:

* Indítsa el őket.
* Az állapotuk lekérdezése.
* Megszakítja őket.
* Események küldése a futtatásuk közben.
* Példányok előzményeinek kiürítése.

Ha a Visual studiót használja, a Durable Functions 1,0 [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net-attribútumának használatával kötést hozhat létre a koordináló ügyfélhez. A Durable functions 2,0 előzetes verziótól kezdődően a `DurableClientAttribute` .NET-attribútum használatával köthető az előkészítési ügyfélhez.

Ha programozási nyelveket (például *. CSX* vagy *. js* fájlt) használ a fejlesztéshez, a rendszer az összehangoló triggert a `bindings` *function. JSON*tömbben lévő következő JSON-objektummal definiálja:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`– Olyan helyzetekben használatos, amikor több Function-alkalmazás osztozik ugyanazzal a Storage-fiókkal, de el kell különíteni egymástól. Ha nincs megadva, a `host.json` rendszer az alapértelmezett értéket használja. Ennek az értéknek meg kell egyeznie a cél Orchestrator függvények által használt értékkel.
* `connectionName`– Egy, a Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazó Alkalmazásbeállítás neve. A megadott Orchestrator függvények által használt Storage-fióknak meg kell egyeznie. Ha nincs megadva, a rendszer az alapértelmezett Storage-fiókhoz tartozó kapcsolatok karakterláncot használja a Function alkalmazáshoz.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki ezeket a tulajdonságokat, és használja az alapértelmezett viselkedést.

### <a name="client-usage"></a>Ügyfél használata

A .net-függvények esetében általában a `DurableOrchestrationClient`következőhöz kötődik, ami teljes hozzáférést biztosít a Durable functions által támogatott összes ügyféloldali API-hoz. A Durable functions 2,0-től kezdődően Ehelyett a `IDurableOrchestrationClient` csatolóhoz kell kötnie. A JavaScriptben ugyanazokat az API-kat teszi elérhetővé a `getClient`rendszer által visszaadott objektum. Az ügyfél-objektum API-jai a következők:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)

Másik megoldásként a .net functions `IAsyncCollector<T>` is `T` köthető a [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) -hez vagy `JObject`a-hoz.

A műveletekkel kapcsolatos további információkért tekintse meg a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API dokumentációját.

### <a name="client-sample-visual-studio-development"></a>Ügyfél minta (Visual Studio Development)

Itt látható egy üzenetsor által aktivált függvény, amely egy "HelloWorld" előkészítést indít el.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Ügyfél minta (nem Visual Studio)

Ha nem a Visual studiót használja a fejlesztéshez, a következő *function. JSON* fájlt hozhatja létre. Ebből a példából megtudhatja, hogyan konfigurálhat egy üzenetsor által aktivált függvényt, amely a tartós előkészítési ügyfél kötését használja:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Az alábbiakban olyan nyelvspecifikus példákat találhat, amelyek elindítják az új Orchestrator-függvények példányait.

#### <a name="c-sample"></a>C#-minta

Az alábbi minta azt mutatja be, hogyan használható a tartós előkészítési ügyfél kötése egy új Function-példány C# elindításához egy parancsfájl-függvényből:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript-minta

Az alábbi minta azt mutatja be, hogyan használható a tartós előkészítési ügyfél kötése egy új Function-példány elindításához egy JavaScript-függvényből:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

A példányok elindításával kapcsolatos további részletek a [példányok kezelése](durable-functions-instance-management.md)oldalon találhatók.

## <a name="entity-trigger"></a>Entitás-trigger

Az entitás-eseményindítók lehetővé teszik az [Entity functions](durable-functions-entities.md)létrehozását. Ez az aktiválás támogatja az adott entitás-példányok feldolgozási eseményeit.

Ha a Visual Studio-eszközöket használja a Azure Functionshoz, az entitás-trigger a `EntityTriggerAttribute` .NET attribútum használatával van konfigurálva.

> [!NOTE]
> Az entitás-eseményindítók Durable Functions 2,0-es és újabb verziókban érhetők el. Az entitás-eseményindítók még nem érhetők el a JavaScripthez.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólisták sorát kérdezi le. Ezek a várólisták a bővítmény belső implementációjának részletei, ezért nincsenek explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Trigger viselkedése

Íme néhány Megjegyzés az entitás-triggerről:

* **Egyszálas**: Egy adott entitás műveleteinek feldolgozására egyetlen diszpécser szál használható. Ha egyszerre több üzenetet küld egyetlen entitásnak, a rendszer a műveleteket egy-egy időben dolgozza fel.
* **Méreg – üzenetkezelés** – az entitás-eseményindítók nem támogatják az üzenetek megmérgezését.
* **Üzenet láthatósága** – az entitás-trigger üzenetei el vannak különítve, és a konfigurálható időtartamra láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – az Entity functions nem támogatja a visszatérési értékeket. Vannak olyan API-k, amelyek segítségével az állapot menthető, vagy visszaadható az értékek az előkészítési folyamatoknak.

A végrehajtás során a rendszer automatikusan megőrzi a entitásban végrehajtott összes állapotot a végrehajtás befejezése után.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

Minden Entity függvényhez tartozik egy paraméter `IDurableEntityContext`, amely a következő tagokkal rendelkezik:

* **EntityName**: Beolvassa az aktuálisan végrehajtó entitás nevét.
* **EntityKey**: A jelenleg végrehajtó entitás kulcsának beolvasása.
* **EntityId**: A jelenleg végrehajtó entitás AZONOSÍTÓjának beolvasása.
* **OperationName**: lekéri az aktuális művelet nevét.
* **IsNewlyConstructed**: azt `true` adja vissza, hogy az entitás nem létezett-e a művelet előtt.
* **GetState\<TState-> ()** : az entitás aktuális állapotát kapja meg. A `TState` paraméternek primitív vagy JSON-serializeable típusúnak kell lennie.
* **SetState (objektum)** : frissíti az entitás állapotát. A `object` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.
* **GetInput\<TInput > ()** : beolvassa az aktuális művelet bemenetét. A `TInput` Type paraméternek primitív vagy JSON-serializeable típusúnak kell lennie.
* **Return (objektum)** : visszaadja a műveletet meghívó előkészítési értéket. A `object` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.
* **DestructOnExit ()** : törli az entitást az aktuális művelet befejezése után.
* **SignalEntity (EntityId, string, Object)** : egyirányú üzenet küldése egy entitásnak. A `object` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.

Az osztály alapú entitások programozási módjának használatakor az `IDurableEntityContext` objektum a `Entity.Current` Thread-static tulajdonság használatával hivatkozhat.

### <a name="trigger-sample---entity-function"></a>A minta-Entity függvény elindítása

A következő kód egy egyszerű, szabványos függvényként megvalósított számlálós entitásra mutat példát. Ez a függvény három *műveletet* `add` `reset`határoz meg:, `get`és, amelyek `currentValue`mindegyike egész állapot értékre van kialakítva.

```csharp
[FunctionName(nameof(Counter))]
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

### <a name="trigger-sample---entity-class"></a>Minta-entitás osztály elindítása

A következő példa az előző `Counter` entitás egyenértékű implementációja .net-osztályok és-metódusok használatával.

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

> [!NOTE]
> A függvény belépési pontjának `[FunctionName]` metódusát *az attribútummal* deklarálni `static` kell az Entity classs használatakor. A nem statikus belépési pontok metódusai több objektum inicializálását és esetleg más nem definiált viselkedést okozhatnak.

Az entitások osztályai speciális mechanizmusokkal rendelkeznek a kötések és a .NET-függőségek befecskendezésével való interakcióhoz. További információ: [tartós entitások](durable-functions-entities.md) cikk.

## <a name="entity-client"></a>Entitás ügyfele

Az entitás-ügyfél kötése lehetővé teszi az [Entity functions](#entity-trigger)aszinkron aktiválását. Ezeket a függvényeket időnként ügyfél- [függvénynek](durable-functions-types-features-overview.md#client-functions)is nevezzük.

Ha a Visual studiót használja, az entitás-ügyfélhez a `DurableClientAttribute` .NET-attribútum használatával lehet kötni.

> [!NOTE]
> A `[DurableClientAttribute]` is használható a koordináló [ügyfélhez](#orchestration-client)való kötéshez.

Ha programozási nyelveket (például *. CSX* vagy *. js* fájlt) használ a fejlesztéshez, az entitás-triggert a `bindings` *function. JSON*tömb következő JSON-objektuma határozza meg:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub`– Olyan helyzetekben használatos, amikor több Function-alkalmazás osztozik ugyanazzal a Storage-fiókkal, de el kell különíteni egymástól. Ha nincs megadva, a `host.json` rendszer az alapértelmezett értéket használja. Ennek az értéknek meg kell egyeznie a cél entitás függvények által használt értékkel.
* `connectionName`– Egy, a Storage-fiókhoz tartozó kapcsolatok sztringjét tartalmazó Alkalmazásbeállítás neve. A relációs sztring által képviselt Storage-fióknak meg kell egyeznie a TARGET Entity functions által használttal. Ha nincs megadva, a rendszer az alapértelmezett Storage-fiókhoz tartozó kapcsolatok karakterláncot használja a Function alkalmazáshoz.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki a nem kötelező tulajdonságokat, és használja az alapértelmezett viselkedést.

### <a name="entity-client-usage"></a>Entitás-ügyfél használata

A .net functions szolgáltatásban általában a `IDurableEntityClient`következőhöz kötődik, ami teljes hozzáférést biztosít a tartós entitások által támogatott összes ügyféloldali API-hoz. Emellett az `IDurableClient` interfészhez is köthető, amely hozzáférést biztosít az ügyfél API-khoz mindkét entitáshoz és a munkafolyamatokhoz. Az ügyfél-objektum API-jai a következők:

* **ReadEntityStateAsync\<T >** : egy entitás állapotának beolvasása.
* **SignalEntityAsync**: egyirányú üzenetet küld egy entitásnak, és megvárja, amíg a várólistán lévő.
* **SignalEntityAsync\<TEntityInterface >** : ugyanaz, `SignalEntityAsync` mint a generált proxy típusú `TEntityInterface`objektum.
* **CreateEntityProxy\<TEntityInterface >** : dinamikusan generál egy típusú `TEntityInterface` dinamikus proxyt, amely típus-biztonságos hívásokat tesz az entitásoknak.

> [!NOTE]
> Fontos tisztában lenni azzal, hogy a korábbi "Signal" műveletek mindegyike aszinkron módon történik. Nem lehet meghívni egy entitás-függvényt, és vissza kell kérni egy ügyfél visszatérési értékét. Hasonlóképpen, a `SignalEntityAsync` visszatérhet, mielőtt az entitás elindítja a műveletet. Csak a Orchestrator függvények hivatkozhatnak szinkronban az Entity functions szolgáltatásba, és feldolgozhatják a visszatérési értékeket.

Az `SignalEntityAsync` API-k az entitás `EntityId`egyedi azonosítójának megadását igénylik. Ezek az API-k igény szerint megadhatják az entitások műveletének `string` nevét és a művelet hasznos adatait JSON-serializeable. `object` Ha a cél entitás nem létezik, a rendszer automatikusan létrehozza a megadott AZONOSÍTÓJÚ entitással.

### <a name="client-sample-untyped"></a>Ügyfél-minta (nem típusos)

Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely egy "számláló" entitást hív meg.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>Ügyfél minta (típus)

A típushoz való biztonságos hozzáféréshez létre lehet hozni egy proxy objektumot. Egy típus – biztonságos proxy létrehozásához az entitás típusának meg kell valósítania egy felületet. Tegyük fel például, `Counter` hogy a korábban említett entitás `ICounter` egy felületet adott meg, amelyet a következőképpen határoz meg:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Az ügyfél kódja ezután felhasználható `SignalEntityAsync<TEntityInterface>` , és a Type paraméterként megadható az `ICounter` illesztőfelület típus-biztonságos proxy létrehozásához. A típus-biztonságos proxyk használatát a következő mintakód mutatja be:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

Az előző példában `proxy` a paraméter a dinamikusan generált `ICounter`példánya, amely belsőleg `Add` lefordítja a hívást az `SignalEntityAsync`egyenértékű (nem típusos) hívásra.

Az entitás-illesztőfelületek definiálásának néhány szabálya van:

* A típus `TEntityInterface` paraméterének `SignalEntityAsync<TEntityInterface>` illesztőfelületnek kell lennie.
* Az entitás-illesztőfelületeknek csak metódusokat kell meghatározniuk.
* Az entitás-illesztőfelületi metódusok nem határozhatnak meg egynél több paramétert.
* Az entitás-illesztőfelületi `void`metódusoknak vissza `Task<T>` kell `T` térniük `Task`, vagy hol kell visszaadni egy értéket.
* Az entitás-illesztőfelületeknek pontosan egy konkrét megvalósítási osztálynak kell lenniük ugyanazon a szerelvényen belül (azaz az Entity osztályon).

Ha a szabályok bármelyike meg van sértve, `InvalidOperationException` a rendszer futásidőben fogja kidobni. A kivételt jelző üzenetből megtudhatja, melyik szabály lett megszakítva.

> [!NOTE]
> Az `SignalEntityAsync` API-k egyirányú műveleteket jelentenek. Ha az entitás-illesztőfelületek visszatérnek `Task<T>`, a `T` paraméter értéke mindig null vagy `default`lesz.

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Host.JSON-beállítások

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beépített HTTP API-referenciák a példányok kezeléséhez](durable-functions-http-api.md)