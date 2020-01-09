---
title: Kötések a Durable Functionshoz – Azure
description: Eseményindítók és kötések használata a Azure Functions Durable Functions bővítményéhez.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75410223"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions kötései (Azure Functions)

A [Durable functions](durable-functions-overview.md) bővítmény két új trigger kötést vezet be, amelyek a Orchestrator és a Activity függvények végrehajtását vezérlik. Emellett egy kimeneti kötést is bevezet, amely ügyfélként funkcionál az Durable Functions futtatókörnyezet számára.

## <a name="orchestration-trigger"></a>Előkészítési trigger

Az előkészítési trigger lehetővé teszi [tartós Orchestrator függvények](durable-functions-types-features-overview.md#orchestrator-functions)készítését. Ez az aktiválás támogatja az új Orchestrator-függvények indítását és a meglévő Orchestrator-függvények lefolytatását, amelyek "várnak" egy feladatot.

Ha a Visual Studio-eszközöket használja a Azure Functionshoz, a [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .net-attribútummal konfigurálhatja a előkészítési triggert.

Ha Orchestrator-függvényeket ír a parancsfájlkezelési nyelveken (például JavaScript C# vagy Scripting), a rendszer a következő JSON-objektummal definiálja az összehangoló triggert a *function. JSON* fájl `bindings` sorában:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` az ügyfelek által a Orchestrator-függvény új példányainak elindításához használni kívánt előkészítés neve. Ez a tulajdonság nem kötelező. Ha nincs megadva, a rendszer a függvény nevét használja.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólisták sorát kérdezi le. Ezek a várólisták a bővítmény belső implementációjának részletei, ezért nincsenek explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány Megjegyzés az előkészítési triggerről:

* **Egyszálas** – egyetlen kiosztó szál használatos egyetlen gazdagép-példányon az összes Orchestrator függvény végrehajtásához. Ezért fontos, hogy a Orchestrator funkció kódja hatékony legyen, és nem hajt végre semmilyen I/O-műveletet. Fontos továbbá, hogy a szál ne végezzen aszinkron munkát, kivéve, ha Durable Functions-specifikus feladattípusra vár.
* **Méreg – üzenetkezelés** – a rendszer nem támogatja a hangfeldolgozási eseményindítók használatát.
* **Üzenet láthatósága** – a hangelőkészítési trigger üzenetei el vannak különítve, és a konfigurálható időtartamra láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – a visszatérési értékek a JSON-ba vannak szerializálva, és az Azure Table Storage-ben megőrzött az előkészítési előzmények táblázata. Ezeket a visszaadott értékeket az összehangoló ügyfél kötése kérdezheti le, amely később van leírva.

> [!WARNING]
> A Orchestrator függvények soha nem használhatnak semmilyen bemeneti vagy kimeneti kötést, amely nem az előkészítési trigger kötése. Ennek köszönhetően problémák léphetnek fel a tartós feladattal, mivel ezek a kötések nem engedelmeskednek az egyszálas és az I/O-szabályoknak. Ha más kötéseket szeretne használni, vegye fel őket a Orchestrator függvény által hívott tevékenység-függvénybe.

> [!WARNING]
> A JavaScript Orchestrator functions soha nem deklarálható `async`.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

A előkészítési trigger kötése a bemeneteket és kimeneteket is támogatja. Íme néhány tudnivaló a bemeneti és kimeneti használatról:

* **bemenetek** – a .net-hangolási függvények csak a paraméter típusú `DurableOrchestrationContext` támogatják. A bemenetek közvetlenül a függvény aláírásában való deszerializálása nem támogatott. A kódnak a `GetInput<T>` (.NET) vagy a `getInput` (JavaScript) metódust kell használnia a Orchestrator függvények bemenetének beolvasásához. Ezeknek a bemeneteknek JSON-szerializálható típusúnak kell lenniük.
* **kimenetek** – a hangelőkészítési eseményindítók támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és csak JSON-szerializálható lehet. Ha egy .NET-függvény `Task` vagy `void`ad vissza, a rendszer a `null` értéket fogja menteni kimenetként.

### <a name="trigger-sample"></a>Példa triggerre

A következő mintakód azt szemlélteti, hogy a legegyszerűbb ""Helló világ!"alkalmazás" Orchestrator függvény a következőképpen néz ki:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> A JavaScriptben lévő `context` objektum nem a DurableOrchestrationContext, hanem a [teljes függvényt](../functions-reference-node.md#context-object)képviseli. A `context` objektum `df` tulajdonságán keresztül férhet hozzá a koordinálási módszerekhez.

> [!NOTE]
> A JavaScript-szervezőknek `return`kell használniuk. A `durable-functions` kódtár gondoskodik a `context.done` metódus meghívásáról.

A legtöbb Orchestrator függvény hívja a Activity functions funkciót, ezért itt látható egy ""Helló világ!"alkalmazás" példa, amely bemutatja, hogyan hívhat meg egy tevékenységi függvényt:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

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

Ha a Visual studiót használja, a tevékenység-trigger a `ActivityTriggerAttribute` .NET attribútummal van konfigurálva.

Ha a VS Code-ot vagy a Azure Portal fejlesztésre használja, a tevékenység-triggert a következő JSON-objektum határozza meg a *function. JSON*`bindings` tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` a tevékenység neve. Ez az érték annak a névnek a neve, amelyet a Orchestrator függvények a tevékenység függvényének meghívására használnak. Ez a tulajdonság nem kötelező. Ha nincs megadva, a rendszer a függvény nevét használja.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólistát kérdezi le. Ez a várólista a bővítmény belső implementációjának részletes adatai, ezért nincs explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány Megjegyzés a tevékenység-triggerről:

* **Szál** – a előkészítési eseményindítótól eltérően a tevékenység-eseményindítók nem rendelkeznek a Threading vagy az I/O műveletekkel kapcsolatos korlátozásokkal. Ugyanúgy kezelhetők, mint a hagyományos függvények.
* **Méreg – üzenetkezelés** – a tevékenység-eseményindítók nem rendelkeznek a Megmérgező üzenet támogatásával.
* **Üzenet láthatósága** – a tevékenység-trigger üzenetei el vannak különítve, és a konfigurálható időtartamig láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – a visszatérési értékek a JSON-ba vannak szerializálva, és az Azure Table Storage-ben megőrzött az előkészítési előzmények táblázata.

> [!WARNING]
> A Activity functions tárolási háttere a megvalósítás részletei, és a felhasználói kód nem használható közvetlenül ezekkel a tároló entitásokkal.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

A tevékenység-trigger kötés támogatja a bemeneteket és a kimeneteket is, ugyanúgy, mint a előkészítési trigger. Íme néhány tudnivaló a bemeneti és kimeneti használatról:

* **bemenetek** – a .net Activity functions natív módon használja a `DurableActivityContext` paraméterként. Azt is megteheti, hogy egy tevékenység-függvény deklarálható bármely olyan típusparaméter-típussal, amely JSON-szerializálható. `DurableActivityContext`használatakor meghívhatja `GetInput<T>` a tevékenységi függvény bemenetének beolvasására és deszerializálására.
* **kimenetek** – a tevékenységi függvények támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és csak JSON-szerializálható lehet. Ha egy .NET-függvény `Task` vagy `void`ad vissza, a rendszer a `null` értéket fogja menteni kimenetként.
* **metaadatok** – a .net-tevékenység funkciói egy `string instanceId` paraméterhez köthetők a szülő-előkészítési példány azonosítójának lekéréséhez.

### <a name="trigger-sample"></a>Példa triggerre

Az alábbi mintakód azt mutatja be, hogy egy egyszerű ""Helló világ!"alkalmazás" tevékenységi funkció hogyan nézhet ki:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a `IDurableActivityContext`helyett `DurableActivityContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

A .NET `ActivityTriggerAttribute` kötés alapértelmezett paraméterének típusa `IDurableActivityContext`. A .NET-tevékenység-eseményindítók azonban közvetlenül a JSON-serializeable-típusokhoz is támogatják a kötéseket (beleértve az egyszerű típusokat is), így ugyanezt a funkciót a következőképpen egyszerűsítheti:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

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

Ha a Visual studiót használja, a Durable Functions 1,0 .NET-attribútumának `OrchestrationClientAttribute` használatával kötést hozhat létre az összehangoló ügyfélhez. A Durable Functions 2,0-es verziótól kezdődően a `DurableClientAttribute` .NET-attribútum használatával lehet kötni a koordináló ügyféllel.

Ha programozási nyelveket (például *. CSX* vagy *. js* fájlt) használ a fejlesztéshez, az összehangoló triggert a következő JSON-objektum határozza meg a *function. JSON*`bindings` tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` – olyan helyzetekben használható, ahol több Function-alkalmazás osztozik ugyanazzal a Storage-fiókkal, de el kell különíteni egymástól. Ha nincs megadva, a rendszer az alapértelmezett értéket használja `host.json`. Ennek az értéknek meg kell egyeznie a cél Orchestrator függvények által használt értékkel.
* `connectionName` – egy, a Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A megadott Orchestrator függvények által használt Storage-fióknak meg kell egyeznie. Ha nincs megadva, a rendszer az alapértelmezett Storage-fiókhoz tartozó kapcsolatok karakterláncot használja a Function alkalmazáshoz.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki ezeket a tulajdonságokat, és használja az alapértelmezett viselkedést.

### <a name="client-usage"></a>Ügyfél használata

A .NET functions szolgáltatásban általában a `IDurableOrchestrationClient`hoz kötődik, ami teljes hozzáférést biztosít a Durable Functions által támogatott összes előkészítési ügyfél API-hoz. A régebbi Durable Functions 2. x kiadásokban Ehelyett a `DurableOrchestrationClient` osztályhoz kell kötnie. A JavaScriptben ugyanazokat az API-kat a `getClient`által visszaadott objektum teszi elérhetővé. Az ügyfél-objektum API-jai a következők:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Másik megoldásként a .NET functions olyan `IAsyncCollector<T>`hoz is köthető, ahol a `T` `StartOrchestrationArgs` vagy `JObject`.

További információ ezekről a műveletekről: `IDurableOrchestrationClient` API dokumentációja.

### <a name="client-sample-visual-studio-development"></a>Ügyfél minta (Visual Studio Development)

Itt látható egy üzenetsor által aktivált függvény, amely egy "HelloWorld" előkészítést indít el.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Az előző C# kód Durable functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Az előző JSON a Durable Functions 2. x. Durable Functions 1. x esetén a `durableClient` helyett `orchestrationClient`t kell használnia az trigger típusaként. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

Az alábbiakban olyan nyelvspecifikus példákat találhat, amelyek elindítják az új Orchestrator-függvények példányait.

#### <a name="c-script-sample"></a>C#Parancsfájl mintája

Az alábbi minta azt mutatja be, hogyan használható a tartós előkészítési ügyfél kötése egy új Function-példány elindításához egy C# üzenetsor által aktivált függvényből:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationClient`helyett a `DurableOrchestrationClient` paraméter típusát kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

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

Ha a Visual Studio-eszközöket használja a Azure Functionshoz, az entitás-trigger a `EntityTriggerAttribute` .NET attribútummal van konfigurálva.

> [!NOTE]
> Az entitás-eseményindítók a Durable Functions 2. x verziótól kezdődően érhetők el.

Belsőleg ez az aktiválási kötés a Function app alapértelmezett Storage-fiókjában lévő várólisták sorát kérdezi le. Ezek a várólisták a bővítmény belső implementációjának részletei, ezért nincsenek explicit módon konfigurálva a kötési tulajdonságok között.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány Megjegyzés az entitás-triggerről:

* **Egyszálas**: egyetlen diszpécser szál egy adott entitás műveleteinek feldolgozásához. Ha egyszerre több üzenetet küld egyetlen entitásnak, a rendszer a műveleteket egy-egy időben dolgozza fel.
* **Méreg – üzenetkezelés** – az entitás-eseményindítók nem támogatják az üzenetek megmérgezését.
* **Üzenet láthatósága** – az entitás-trigger üzenetei el vannak különítve, és a konfigurálható időtartamra láthatatlanok maradnak. Az üzenetek láthatósága automatikusan megújítható, amíg a Function alkalmazás fut és kifogástalan állapotú.
* **Visszatérési értékek** – az Entity functions nem támogatja a visszatérési értékeket. Vannak olyan API-k, amelyek segítségével az állapot menthető, vagy visszaadható az értékek az előkészítési folyamatoknak.

A végrehajtás során a rendszer automatikusan megőrzi a entitásban végrehajtott összes állapotot a végrehajtás befejezése után.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

Minden entitás függvényhez tartozik egy `IDurableEntityContext`paraméter, amely a következő tagokkal rendelkezik:

* **EntityName**: az aktuálisan végrehajtó entitás neve.
* **EntityKey**: a jelenleg végrehajtó entitás kulcsa.
* **EntityId**: a jelenleg végrehajtó entitás azonosítója.
* **OperationName**: az aktuális művelet neve.
* **HasState**: azt határozza meg, hogy az entitás létezik-e, azaz van-e valamilyen állapota. 
* **GetState\<tstate-> ()** : az entitás aktuális állapotát kapja meg. Ha még nem létezik, a rendszer létrehozza és inicializálja `default<TState>`ra. A `TState` paraméternek primitív vagy JSON-serializeable típusúnak kell lennie. 
* **GetState\<tstate-> (initfunction)** : az entitás aktuális állapotát kapja meg. Ha még nem létezik, a rendszer a megadott `initfunction` paraméter meghívásával hozza létre. A `TState` paraméternek primitív vagy JSON-serializeable típusúnak kell lennie. 
* **SetState (ARG)** : az entitás állapotát hozza létre vagy frissíti. A `arg` paraméternek JSON-serializeable objektumnak vagy primitívnek kell lennie.
* **DeleteState ()** : törli az entitás állapotát. 
* **GetInput\<TInput > ()** : beolvassa az aktuális művelet bemenetét. A `TInput` Type paraméternek primitív vagy JSON-serializeable típusúnak kell lennie.
* **Return (ARG)** : egy értéket ad vissza a műveletnek nevezett eljáráshoz. A `arg` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.
* **SignalEntity (EntityId, scheduledTimeUtc, művelet, bemenet)** : egyirányú üzenet küldése egy entitásnak. A `operation` paraméter nem lehet null értékű karakterlánc, a választható `scheduledTimeUtc`nak UTC datetime-értéknek kell lennie, amelyen a műveletet meg kell hívni, és a `input` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.
* **CreateNewOrchestration (orchestratorFunctionName, bemenet)** : új előkészítést indít el. A `input` paraméternek primitív vagy JSON-serializeable objektumnak kell lennie.

Az Entity függvénynek átadott `IDurableEntityContext` objektum a `Entity.Current` aszinkron-local tulajdonsággal érhető el. Ez a megközelítés az osztály alapú programozási modell használata esetén hasznos.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger minta (C# Function-based szintaxis)

A következő kód egy egyszerű, tartós funkcióként megvalósított *számlálós* entitásra mutat példát. Ez a függvény három műveletet határoz meg, `add`, `reset`és `get`, amelyek mindegyike egész számban működik.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

A függvény-alapú szintaxissal és annak használatával kapcsolatos további információkért lásd a [Function-based szintaxist](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger minta (C# osztály alapú szintaxis)

A következő példa az `Counter` entitás egyenértékű implementációját osztályok és metódusok használatával.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

Az entitás állapota `Counter`típusú objektum, amely egy olyan mezőt tartalmaz, amely a számláló aktuális értékét tárolja. Ha meg szeretné őrizni ezt az objektumot a tárolóban, a rendszer szerializálja és deszerializálja a [JSON.net](https://www.newtonsoft.com/json) -könyvtár. 

Az osztály-alapú szintaxissal és annak használatával kapcsolatos további információkért lásd: entitás- [osztályok meghatározása](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Az *`[FunctionName]` attribútummal* rendelkező függvény belépési pontjának metódusát `static` kell deklarálni az Entity classs használatakor. A nem statikus belépési pontok metódusai több objektum inicializálását és esetleg más nem definiált viselkedést okozhatnak.

Az entitások osztályai speciális mechanizmusokkal rendelkeznek a kötések és a .NET-függőségek befecskendezésével való interakcióhoz. További információ: [entitások kialakítása](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Példa a triggerre (JavaScript)

A következő kód egy egyszerű *számláló* entitás, amely a JavaScriptben írt tartós függvényként van megvalósítva. Ez a függvény három műveletet határoz meg, `add`, `reset`és `get`, amelyek mindegyike egész számban működik.

**function. JSON**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index. js**
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

> [!NOTE]
> A tartós entitások a `durable-functions` NPM csomag **1.3.0** kezdődően érhetők el a JavaScriptben.

## <a name="entity-client"></a>Entitás ügyfele

Az entitás-ügyfél kötése lehetővé teszi az [Entity functions](#entity-trigger)aszinkron aktiválását. Ezeket a függvényeket időnként ügyfél- [függvénynek](durable-functions-types-features-overview.md#client-functions)is nevezzük.

Ha a Visual studiót használja, az `DurableClientAttribute` .NET attribútum használatával kötést hozhat létre az entitás-ügyfélhez.

> [!NOTE]
> A `[DurableClientAttribute]` is felhasználható a koordináló [ügyfélhez](#orchestration-client)való kötéshez.

Ha programozási nyelveket (például *. CSX* vagy *. js* fájlt) használ a fejlesztéshez, az entitás-triggert a következő JSON-objektum határozza meg a *function. JSON*`bindings` tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` – olyan helyzetekben használható, ahol több Function-alkalmazás osztozik ugyanazzal a Storage-fiókkal, de el kell különíteni egymástól. Ha nincs megadva, a rendszer az alapértelmezett értéket használja `host.json`. Ennek az értéknek meg kell egyeznie a cél entitás függvények által használt értékkel.
* `connectionName` – egy, a Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A relációs sztring által képviselt Storage-fióknak meg kell egyeznie a TARGET Entity functions által használttal. Ha nincs megadva, a rendszer az alapértelmezett Storage-fiókhoz tartozó kapcsolatok karakterláncot használja a Function alkalmazáshoz.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki a nem kötelező tulajdonságokat, és használja az alapértelmezett viselkedést.

### <a name="entity-client-usage"></a>Entitás-ügyfél használata

A .NET-függvények általában a `IDurableEntityClient`hoz kötődnek, ami teljes hozzáférést biztosít a tartós entitások által támogatott összes ügyféloldali API-hoz. A `IDurableOrchestrationClient` felülethez is köthető, amely hozzáférést biztosít az ügyféloldali API-khoz mindkét entitáshoz és a munkafolyamatokhoz. Az ügyfél-objektum API-jai a következők:

* **ReadEntityStateAsync\<t >** : egy entitás állapotának beolvasása. Egy olyan választ ad vissza, amely jelzi, hogy létezik-e a célként megadott entitás, és ha igen, milyen állapotban van.
* **SignalEntityAsync**: egyirányú üzenetet küld egy entitásnak, és megvárja, amíg a várólistán lévő.
* **ListEntitiesAsync**: több entitás állapotának lekérdezése. Az entitásokat *név* és *utolsó működési idő*szerint lehet lekérdezni.

Nem kell létrehoznia a cél entitást a jel elküldése előtt – az entitás állapota a jelet kezelő entitás függvényből hozható létre.

> [!NOTE]
> Fontos tisztában lenni azzal, hogy az ügyféltől érkező "jelzések" egyszerűen várólistán lévő, és a későbbiekben aszinkron módon kell feldolgozni. A `SignalEntityAsync` általában csak akkor ad vissza, ha az entitás még a műveletet is elindítja, és nem lehet visszakapni a visszaadott értéket, vagy megfigyelni a kivételeket. Ha erősebb biztosítékokra van szükség (például a munkafolyamatok esetében), a *Orchestrator függvényeket* kell használni, amelyek megvárhatják az entitások műveleteinek befejeződését, és feldolgozhatják a visszatérési értékeket, és megfigyelheti a kivételeket.

### <a name="example-client-signals-entity-directly---c"></a>Példa: ügyfél-szignálok entitás közvetlenül –C#

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

### <a name="example-client-signals-entity-via-interface---c"></a>Példa: ügyfél-jelek entitás kapcsolaton keresztül –C#

Ha lehetséges, javasoljuk, hogy az [entitásokat a felületeken keresztül használja](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , mert több típusú ellenőrzést is biztosít. Tegyük fel például, hogy a korábban említett `Counter` entitás egy `ICounter` felületet adott meg, amelyet a következőképpen határozhat meg:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Az ügyfél kódja ezután a `SignalEntityAsync<ICounter>` használatával hozhatja meg a típus-biztonságos proxyt:

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

A `proxy` paraméter a `ICounter`dinamikusan generált példánya, amely belsőleg lefordítja a `Add`t a `SignalEntityAsync`nak megfelelő (nem típusos) hívásra.

> [!NOTE]
> A `SignalEntityAsync` API-k egyirányú műveleteket jelentenek. Ha az entitás-illesztőfelületek `Task<T>`adnak vissza, a `T` paraméter értéke mindig null vagy `default`lesz.

Különösen nem érdemes jelezni a `Get` műveletet, mivel a rendszer nem ad vissza értéket. Ehelyett az ügyfelek a `ReadStateAsync` használatával közvetlenül érhetik el a számláló állapotát, vagy elindíthatnak egy Orchestrator-függvényt, amely meghívja a `Get` műveletet.

### <a name="example-client-signals-entity---javascript"></a>Példa: ügyfél-jelek entitás – JavaScript

Az alábbi példa egy üzenetsor által aktivált függvényt mutat be, amely "számláló" entitást mutat be a JavaScriptben.

**function. JSON**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
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

**index. js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> A tartós entitások a `durable-functions` NPM csomag **1.3.0** kezdődően érhetők el a JavaScriptben.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>gazdagép. JSON-beállítások

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beépített HTTP API-referenciák a példányok kezeléséhez](durable-functions-http-api.md)
