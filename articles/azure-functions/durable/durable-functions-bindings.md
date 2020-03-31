---
title: Kötések tartós függvényekhez - Azure
description: Az eseményindítók és kötések használata az Azure Functions tartós függvénybővítményéhez.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278219"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Tartós függvények kötései (Azure-függvények)

A [Durable Functions](durable-functions-overview.md) bővítmény két új eseményindító-kötést vezet be, amelyek az orchestrator és a tevékenységfüggvények végrehajtását szabályozzák. Emellett egy kimeneti kötést is bevezet, amely a Durable Functions futásidejű ügyfélként működik.

## <a name="orchestration-trigger"></a>Vezénylési eseményindító

A vezénylési eseményindító lehetővé teszi, hogy [tartós orchestrator függvényeket.](durable-functions-types-features-overview.md#orchestrator-functions) Ez az eseményindító támogatja az új orchestrator függvénypéldányok indítását és a feladatra "várakozó" meglévő orchestrator-függvénypéldányok folytatását.

Ha a Visual Studio-eszközöket használja az Azure Functionshez, a vezénylési eseményindító az [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET attribútum használatával konfigurálva van.

Amikor orchestrator függvényeket ír parancsfájlnyelveken (például JavaScript vagy C# parancsfájlok), a vezénylési eseményindítót a következő JSON-objektum határozza meg a `bindings` *function.json* fájl tömbjében:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`A vezénylési, hogy az ügyfelek kell használni, ha új példányokat akarnak kezdeni az orchestrator függvény. Ez a tulajdonság nem kötelező. Ha nincs megadva, a függvény neve lesz használva.

Belsőleg ez az eseményindító kötés lekérdezi a függvényalkalmazás alapértelmezett tárfiókjában lévő várólisták sorozatát. Ezek a várólisták a bővítmény belső implementációs részletei, ezért nincsenek explicit módon konfigurálva a kötési tulajdonságokban.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány megjegyzés a vezénylési eseményindítóról:

* **Egyszálas működés** – egyetlen feladási szál at használ minden orchestrator függvény végrehajtása egyetlen gazdagéppéldányon. Emiatt fontos annak biztosítása, hogy az orchestrator függvénykód hatékony, és nem végez i/o-t. Fontos annak biztosítása is, hogy ez a szál ne végezjön aszinkron munkát, kivéve, ha a Tartós függvény-specifikus feladattípusokra vár.
* **Poison-message handling** – Nincs méreg üzenet támogatása vezénylési eseményindítók.
* **Üzenet láthatósága** – A vezénylési eseményindító üzenetek várólistán maradnak, és egy konfigurálható ideig láthatatlanok maradnak. Ezek az üzenetek láthatósága automatikusan megújul, amíg a függvényalkalmazás fut, és kifogástalan.
* **Visszatérési értékek** – A visszatérési értékek szerializáltak a JSON-ba, és megmaradnak az Azure Table storage vezénylési előzménytáblájában. Ezeket a visszatérési értékeket a vezénylési ügyfélkötés lekérdezheti, később ismertetett.

> [!WARNING]
> Az Orchestrator függvények soha nem használhatnak a vezénylési eseményindító kötésén kívül más bemeneti vagy kimeneti kötést. Ezzel problémákat okozhat a tartós feladat bővítmény, mert ezek a kötések nem tartják be az egyszálas és I/O szabályokat. Ha más kötéseket szeretne használni, adja hozzá őket az Orchestrator függvényből hívott tevékenység függvényhez.

> [!WARNING]
> A JavaScript orchestrator függvényeket `async`soha nem szabad deklarálni.

### <a name="trigger-usage-net"></a>Eseményindító használata (.NET)

A vezénylési eseményindító kötés támogatja a bemenetek és kimenetek. Íme néhány tudnivaló a bemeneti és kimeneti kezelésről:

* **bemenetek** – a .NET `DurableOrchestrationContext` vezénylési függvények csak paramétertípusként támogatottak. A bemenetek deszerializálása közvetlenül a függvényaláírásban nem támogatott. A kódnak `GetInput<T>` a (.NET) vagy `getInput` a (JavaScript) metódust kell használnia az orchestrator függvénybemenetek lekéréséhez. Ezeknek a bemeneteknek JSON-szerializálható típusoknak kell lenniük.
* **outputok** – Az vezénylési eseményindítók támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és JSON-szerializálhatónak kell lennie. Ha a .NET `Task` `void`függvény `null` t a d. vagy a kimenetként egy értéket ment.

### <a name="trigger-sample"></a>Eseményindító minta

A következő példakód bemutatja, hogyan nézhet ki a legegyszerűbb "Hello World" orchestrator függvény:

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
> Az előző kód a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions Versions (Tartós függvényverziók)](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-functions-20-only"></a>JavaScript (csak a 2.0-s függvények nél)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> A `context` JavaScript-objektum nem a DurableOrchestrationContext, hanem a [függvénykörnyezet egészét](../functions-reference-node.md#context-object)jelöli. Az objektum tulajdonán keresztül `context` elérheti `df` a vezénylési módszereket.

> [!NOTE]
> JavaScript orchestrators `return`kell használni . A `durable-functions` könyvtár gondoskodik `context.done` a metódus hívásáról.

A legtöbb orchestrator függvény tevékenységfüggvényeket hív meg, így itt van egy "Hello World" példa, amely bemutatja, hogyan kell hívni egy tevékenységfüggvényt:

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
> Az előző kód a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

#### <a name="javascript-functions-20-only"></a>JavaScript (csak a 2.0-s függvények nél)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Tevékenység eseményindító

A tevékenységeseményindító lehetővé teszi az orchestrator függvények által hívott függvények, más néven [tevékenységfüggvények szerzői engedélyezését.](durable-functions-types-features-overview.md#activity-functions)

Visual Studio használata esetén a tevékenységeseményindító a `ActivityTriggerAttribute` .NET attribútum használatával van konfigurálva.

Ha a VS Code vagy az Azure Portal fejlesztéshez használja, a tevékenységeseményindítót `bindings` a következő JSON-objektum határozza meg a *function.json*tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`a tevékenység neve. Ez az érték az a név, amelyet az orchestrator függvények a tevékenységfüggvény meghívására használnak. Ez a tulajdonság nem kötelező. Ha nincs megadva, a függvény neve lesz használva.

Ez az eseményindító kötési rendszer belső legeltetése egy várólistát a függvényalkalmazás alapértelmezett tárfiókjában. Ez a várólista a bővítmény belső végrehajtási részletei, ezért nincs explicit módon konfigurálva a kötési tulajdonságokban.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány megjegyzés a tevékenységeseményindítóról:

* **Threading** - Ellentétben a vezénylési eseményindító, tevékenység eseményindítók nincs korlátozás körül threading vagy I/O. Úgy kezelhetők, mint a rendszeres funkciók.
* **Méreg-üzenet kezelése** - Nincs méreg üzenet támogatása tevékenység kiváltó.
* **Az üzenetek láthatósága** – A tevékenységindító üzenetek várólistán maradnak, és egy konfigurálható ideig láthatatlanok maradnak. Ezek az üzenetek láthatósága automatikusan megújul, amíg a függvényalkalmazás fut, és kifogástalan.
* **Visszatérési értékek** – A visszatérési értékek szerializáltak a JSON-ba, és megmaradnak az Azure Table storage vezénylési előzménytáblájában.

> [!WARNING]
> A tárolási háttérrendszer a tevékenység függvények egy megvalósítási részletesen, és a felhasználói kód nem léphet kölcsönhatásba ezekkel a tárolóentitások közvetlenül.

### <a name="trigger-usage-net"></a>Eseményindító használata (.NET)

A tevékenység eseményindító kötelező támogatja a bemenetek és kimenetek, csakúgy, mint a vezénylési eseményindító. Íme néhány tudnivaló a bemeneti és kimeneti kezelésről:

* **bemenetek** – a .NET `DurableActivityContext` tevékenység paramétertípusként natív módon használható. Másik lehetőségként egy tevékenységfüggvény deklarálható bármilyen JSON-serializable paramétertípussal. Ha a `DurableActivityContext`használata, `GetInput<T>` hívhatja, hogy lekéri és deszerializálja a tevékenység függvény bemenet.
* **outputok** - Az aktivitási függvények támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és JSON-szerializálhatónak kell lennie. Ha a .NET `Task` `void`függvény `null` t a d. vagy a kimenetként egy értéket ment.
* **metaadatok** – a .NET tevékenységfüggvények egy `string instanceId` paraméterhez köthetők a szülő vezénylési példányazonosítójának lekért.

### <a name="trigger-sample"></a>Eseményindító minta

A következő példakód bemutatja, hogyan nézhet ki egy egyszerű "Hello World" tevékenységfunkció:

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
> Az előző kód a Durable Functions 2.x. A tartós függvények 1.x `DurableActivityContext` esetén `IDurableActivityContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions Versions (Tartós függvényverziók)](durable-functions-versions.md) című cikkben olvashat bővebben.

A .NET `ActivityTriggerAttribute` kötés alapértelmezett paramétertípusa a `IDurableActivityContext`. A .NET tevékenységeseményindítók azonban közvetlenül a JSON-szerializálható típusokhoz (beleértve a primitív típusokat is) való kötést is támogatják, így ugyanaz a funkció a következőképpen egyszerűsíthető:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (csak a 2.0-s függvények nél)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

A JavaScript-kötések további paraméterekként is átadhatók, így ugyanaz a funkció a következőképpen egyszerűsíthető:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Bemeneti és kimeneti kötések használata

A tevékenységeseményindító-kötés mellett rendszeres bemeneti és kimeneti kötéseket is használhat. Például átviheti a bemenetet a tevékenységkötéshez, és üzenetet küldhet egy EventHubnak az EventHub kimeneti kötéshasználatával:

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

## <a name="orchestration-client"></a>Vezénylési ügyfél

A vezénylési ügyfélkötés lehetővé teszi, hogy olyan függvényeket írjon, amelyek együttműködnek az orchestrator függvényekkel. Ezeket a függvényeket néha [ügyfélfüggvényeknek is nevezik.](durable-functions-types-features-overview.md#client-functions) A vezénylési példányokat például a következő módokon használhatja:

* Indítsd el őket.
* Kérdezze meg az állapotukat.
* Semmisen.
* Eseményeket küldeni nekik futás közben.
* Példányelőzmények törlése.

Visual Studio használata esetén a .NET attribútum használatával `OrchestrationClientAttribute` kötheti a vezénylési ügyfelet az 1.0-s szinthez. A Durable Functions 2.0-s kezdésétől a .NET attribútum használatával kötődhet a `DurableClientAttribute` vezénylési ügyfélhez.

Ha parancsfájlnyelveket (például *.csx* vagy *.js fájlokat)* használ a fejlesztéshez, a vezénylési eseményindítót a következő JSON-objektum határozza meg a `bindings` *function.json*tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`- Olyan esetekben használatos, ahol több függvényalkalmazás ugyanazt a tárfiókot használja, de el kell különíteni egymástól. Ha nincs megadva, a `host.json` program a program az alapértelmezett értéket használja. Ennek az értéknek meg kell egyeznie a célorchestrator-függvények által használt értékkel.
* `connectionName`- A tárfiók kapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve. A kapcsolati karakterlánc által képviselt tárfióknak meg kell egyeznie a célorchestrator-függvények által használt fiókkal. Ha nincs megadva, a függvényalkalmazás alapértelmezett tárfiók-kapcsolati karakterlánca lesz használva.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki ezeket a tulajdonságokat, és az alapértelmezett viselkedésre támaszkodik.

### <a name="client-usage"></a>Ügyfél használat

A .NET függvényekben általában `IDurableOrchestrationClient`kötődik a hoz, amely teljes hozzáférést biztosít a durable functions által támogatott összes vezénylési ügyfél API-hoz. A régebbi Durable Functions 2.x kiadásokban inkább `DurableOrchestrationClient` az osztályhoz kötődik. A JavaScript ben ugyanazokat az API-kat `getClient`teszi ki a . Az ügyfélobjektum API-i a következők:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Másik lehetőségként a .NET `IAsyncCollector<T>` `T` függvények a helyhez `StartOrchestrationArgs` vagy `JObject`a hoz köthetők.

Ezekről a műveletekről további `IDurableOrchestrationClient` információt az API dokumentációjában talál.

### <a name="client-sample-visual-studio-development"></a>Ügyfélminta (Visual Studio-fejlesztés)

Íme egy példa a várólista által aktivált függvény, amely elindítja a "HelloWorld" vezénylési.

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
> Az előző C# kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions Versions (Tartós függvényverziók)](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="client-sample-not-visual-studio"></a>Ügyfélminta (nem Visual Studio)

Ha nem a Visual Studio-t használja fejlesztésre, létrehozhatja a következő *function.json* fájlt. Ez a példa bemutatja, hogyan konfigurálható egy várólista által aktivált függvény, amely a tartós vezénylési ügyfélkötést használja:

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
> Az előző JSON a Durable Functions 2.x.-hez készült. Tartós függvények 1.x esetén `orchestrationClient` az `durableClient` eseményindító típusa helyett a használnia kell. A verziók közötti különbségekről a [Durable Functions Versions (Tartós függvényverziók)](durable-functions-versions.md) című cikkben olvashat bővebben.

A következőkben olyan nyelvspecifikus mintákat, amelyek új orchestrator-függvénypéldányokat indítanak el.

#### <a name="c-script-sample"></a>C# parancsfájlminta

A következő minta bemutatja, hogyan használhatja a tartós vezénylési ügyfélkötést egy új függvénypéldány indításához egy várólista által aktivált C# függvényből:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Az előző kód a Durable Functions 2.x. A Tartós függvények 1.x `DurableOrchestrationClient` esetén a `IDurableOrchestrationClient`paraméter típusát kell használni a helyett. A verziók közötti különbségekről a [Durable Functions Versions (Tartós függvényverziók)](durable-functions-versions.md) című cikkben olvashat bővebben.

#### <a name="javascript-sample"></a>JavaScript-minta

A következő minta bemutatja, hogyan használhatja a tartós vezénylési ügyfélkötést egy új függvénypéldány javascript-függvényből történő indításához:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

A kezdőpéldányokkal kapcsolatos további részletek a [Példánykezelés](durable-functions-instance-management.md)ben találhatók.

## <a name="entity-trigger"></a>Entitás eseményindítója

Az entitáseseményindítók lehetővé teszik [entitásfüggvények szerkesztését.](durable-functions-entities.md) Ez az eseményindító támogatja az események feldolgozását egy adott entitáspéldányhoz.

Ha a Visual Studio-eszközöket használja az Azure Functionshez, az entitás eseményindító a `EntityTriggerAttribute` .NET attribútum használatával konfigurálva van.

> [!NOTE]
> Az entitáseseményindítók a Durable Functions 2.x verziótól kezdve érhetők el.

Belsőleg ez az eseményindító kötés lekérdezi a függvényalkalmazás alapértelmezett tárfiókjában lévő várólisták sorozatát. Ezek a várólisták a bővítmény belső implementációs részletei, ezért nincsenek explicit módon konfigurálva a kötési tulajdonságokban.

### <a name="trigger-behavior"></a>Eseményindító működése

Íme néhány megjegyzés az entitás eseményindítójáról:

* **Egyszálas:** Egy szállítószál egy adott entitás műveleteinek feldolgozására szolgál. Ha egyidejűleg több üzenetet küld egy entitásnak, a műveletek feldolgozása egy időben lesz feldolgozva.
* **Méreg-üzenet kezelése** – Nincs méreg üzenet támogatása entitás eseményindítók.
* **Az üzenetek láthatósága** – Az entitásindító üzenetek várólistán maradnak, és egy konfigurálható ideig láthatatlanok maradnak. Ezek az üzenetek láthatósága automatikusan megújul, amíg a függvényalkalmazás fut, és kifogástalan.
* **Visszatérési értékek** – Az entitásfüggvények nem támogatják a visszatérési értékeket. Vannak speciális API-k, amelyek segítségével mentse az állapotot, vagy átadja az értékeket vissza vezénylési.

Az entitáson a végrehajtás során végrehajtott állapotmódosítások automatikusan megmaradnak a végrehajtás befejezése után.

### <a name="trigger-usage-net"></a>Eseményindító használata (.NET)

Minden entitásfüggvény nek van `IDurableEntityContext`egy paramétertípusa , amelynek a következő tagjai vannak:

* **EntityName**: az éppen végrehajtó entitás neve.
* **EntityKey**: a jelenleg végrehajtó entitás kulcsa.
* **EntityId**: a jelenleg végrehajtó entitás azonosítója.
* **OperationName**: az aktuális művelet neve.
* **HasState**: az entitás létezik-e, azaz van-e valamilyen állapot. 
* **GetState\<TState>()**: leadja az entitás aktuális állapotát. Ha még nem létezik, akkor létrejön, és inicializálva `default<TState>`lesz. A `TState` paraméternek primitív vagy JSON-szerializálható típusnak kell lennie. 
* **GetState\<TState>(initfunction)**: az entitás aktuális állapotát kapja. Ha még nem létezik, akkor a `initfunction` megadott paraméter hívásával jön létre. A `TState` paraméternek primitív vagy JSON-szerializálható típusnak kell lennie. 
* **SetState(arg)**: létrehozza vagy frissíti az entitás állapotát. A `arg` paraméternek JSON-szerializálható objektumnak vagy primitívnek kell lennie.
* **DeleteState()**: törli az entitás állapotát. 
* **GetInput\<TInput>()**: lekéri az aktuális művelet bemenetét. A `TInput` típusparaméternek primitív vagy JSON-szerializálható típusnak kell lennie.
* **Return(arg)**: értéket ad vissza a műveletnek nevezett vezénylési értéknek. A `arg` paraméternek primitív vagy JSON-szerializálható objektumnak kell lennie.
* **SignalEntity(EntityId, scheduledTimeUtc, operation, input):**: egyirányú üzenetet küld egy entitásnak. A `operation` paraméternek nem null karakterláncnak `scheduledTimeUtc` kell lennie, a nem kötelező értéknek UTC-dátumnak kell lennie, amelyen a műveletet meg lehet hívni, és a `input` paraméternek primitív vagy JSON-szerializálható objektumnak kell lennie.
* **CreateNewOrchestration(orchestratorFunctionName, input)**: új vezénylési indítást indít. A `input` paraméternek primitív vagy JSON-szerializálható objektumnak kell lennie.

Az `IDurableEntityContext` entitásfüggvénynek átadott objektum az `Entity.Current` aszinkron-helyi tulajdonság használatával érhető el. Ez a megközelítés az osztályalapú programozási modell használatakor kényelmes.

### <a name="trigger-sample-c-function-based-syntax"></a>Eseményindító minta (C# függvényalapú szintaxis)

A következő kód egy példa egy egyszerű *counter* entitás tartós függvényként megvalósított. Ez a függvény három `add`műveletet határoz meg, , `reset`és `get`mindegyike egész állapotban működik.

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

A függvényalapú szintaxisról és annak használatáról a [Függvényalapú szintaxis](durable-functions-dotnet-entities.md#function-based-syntax)című témakörben talál további információt.

### <a name="trigger-sample-c-class-based-syntax"></a>Eseményindító minta (C# osztályalapú szintaxis)

A következő példa az entitás `Counter` azonos implementációja osztályok és módszerek használatával.

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

Ennek az entitásnak az állapota `Counter`egy típusú objektum, amely a számláló aktuális értékét tároló mezőt tartalmaz. Az objektum tárolásához szerializálódik, és a [Json.NET](https://www.newtonsoft.com/json) könyvtár szerializálódik. 

Az osztályalapú szintaxisról és használatáról az [Entitásosztályok definiálása](durable-functions-dotnet-entities.md#defining-entity-classes)című témakörben talál további információt.

> [!NOTE]
> Entitásosztályok használatakor deklarálni `[FunctionName]` `static` *kell* az attribútummal rendelkező függvénybelépési pont metódust. A nem statikus belépési pont metódusok több objektum inicializálását és más meg határozatlan viselkedést eredményezhetnek.

Az entitásosztályok speciális mechanizmusokkal rendelkeznek a kötésekkel és a .NET függőségi injektálással való interakcióhoz. További információ: [Entity construction](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Eseményindító minta (JavaScript)

A következő kód egy példa egy egyszerű *counter* entitás javascriptben írt tartós függvényként megvalósítva. Ez a függvény három `add`műveletet határoz meg, , `reset`és `get`mindegyike egész állapotban működik.

**function.json**
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

**index.js**
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
> A tartós entitások JavaScript-ben érhetők el az `durable-functions` npm-csomag **1.3.0-s** verziójától kezdve.

## <a name="entity-client"></a>Entitásügyfél

Az entitásügyfél-kötés lehetővé teszi entitásfüggvények aszinkron [aktiválását.](#entity-trigger) Ezeket a függvényeket néha [ügyfélfüggvényeknek is nevezik.](durable-functions-types-features-overview.md#client-functions)

Ha a Visual Studio alkalmazást használja, a .NET `DurableClientAttribute` attribútum használatával kötést köthet az entitásügyfélhez.

> [!NOTE]
> A `[DurableClientAttribute]` is használható, hogy kötődik a [vezénylési ügyfél.](#orchestration-client)

Ha parancsfájlnyelveket (például *.csx* vagy *.js fájlokat)* használ a fejlesztéshez, az entitáseseménytriggert `bindings` a következő JSON-objektum határozza meg a *function.json*tömbben:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub`- Olyan esetekben használatos, ahol több függvényalkalmazás ugyanazt a tárfiókot használja, de el kell különíteni egymástól. Ha nincs megadva, a `host.json` program a program az alapértelmezett értéket használja. Ennek az értéknek meg kell egyeznie a célentitás-függvények által használt értékkel.
* `connectionName`- A tárfiók kapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve. A kapcsolati karakterlánc által képviselt tárfióknak meg kell egyeznie a célentitás-függvények által használt fiókkal. Ha nincs megadva, a függvényalkalmazás alapértelmezett tárfiók-kapcsolati karakterlánca lesz használva.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki a választható tulajdonságokat, és az alapértelmezett viselkedésre támaszkodjon.

### <a name="entity-client-usage"></a>Entitásügyfél-használat

A .NET függvényekben általában `IDurableEntityClient`a rendszerhez kötődik, amely teljes hozzáférést biztosít a tartós entitások által támogatott összes ügyfélAPI-hoz. A kapcsolathoz is `IDurableOrchestrationClient` kötődhet, amely hozzáférést biztosít az entitásokhoz és a vezénylésekhez egyaránt az ügyfél API-khoz. Az ügyfélobjektum API-i a következők:

* **ReadEntityStateAsync\<T>: **egy entitás állapotát olvassa be. Olyan választ ad vissza, amely jelzi, hogy a célentitás létezik-e, és ha igen, mi az állapota.
* **SignalEntityAsync**: egyirányú üzenetet küld egy entitásnak, és megvárja, amíg várólistára kerül.
* **ListEntitiesAsync**: több entitás állapotának lekérdezései. Az entitások lekérdezhetők *név* és *az utolsó művelet időpontja*szerint.

Nincs szükség a célentitás létrehozásához a jel küldése előtt – az entitásállapot a jelet kezelő entitásfüggvényből hozható létre.

> [!NOTE]
> Fontos megérteni, hogy az ügyféltől küldött "jelek" egyszerűen várólistára kerülnek, és későbbi időpontban aszinkron módon kerülnek feldolgozásra. Különösen az `SignalEntityAsync` általában visszatér, mielőtt az entitás még a művelet et is elindítja, és nem lehet visszakapni a visszatérési értéket, vagy megfigyelni kivételeket. Ha erősebb garanciákra van szükség (például munkafolyamatok esetén), *orchestrator függvényeket* kell használni, amelyek megvárhatják az entitásműveletek befejezését, és feldolgozhatják a visszatérési értékeket, és megfigyelhetik a kivételeket.

### <a name="example-client-signals-entity-directly---c"></a>Példa: ügyféljelek entitás közvetlenül - C #

Íme egy példa a várólista által aktivált függvény, amely meghívja a "Counter" entitás.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Példa: ügyféljelek entitás interfészen keresztül - C #

Ahol lehetséges, azt javasoljuk, [hogy az entitások interfészeken keresztül való elérése,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) mert több típusellenőrzést biztosít. Tegyük fel `Counter` például, hogy `ICounter` a korábban említett entitás a következőként meghatározott felületet valósította meg:

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

Az ügyfélkód `SignalEntityAsync<ICounter>` ezután típusbiztos proxy létrehozásához használható:

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

A `proxy` paraméter a dinamikusan generált `ICounter`példánya, amely belsőleg `Add` fordítja a hívást a `SignalEntityAsync`megfelelő (nem gépelt) hívás.

> [!NOTE]
> Az `SignalEntityAsync` API-k egyirányú műveleteket jelölnek. Ha egy entitásillesztő visszatér, `Task<T>` `T` a paraméter értéke `default`mindig null vagy .

Különösen nincs értelme jelezni a `Get` műveletet, mivel nem ad vissza értéket. Ehelyett az ügyfelek `ReadStateAsync` használhatják vagy a számláló állapotának közvetlen eléréséhez, `Get` vagy elindíthatnak egy orchestrator függvényt, amely meghívja a műveletet.

### <a name="example-client-signals-entity---javascript"></a>Példa: ügyféljelek entitás - JavaScript

Íme egy példa a várólista által aktivált függvényre, amely egy "Számláló" entitást jelez a JavaScriptben.

**function.json**
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

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> A tartós entitások JavaScript-ben érhetők el az `durable-functions` npm-csomag **1.3.0-s** verziójától kezdve.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json beállítások

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beépített HTTP API-hivatkozás a példánykezeléshez](durable-functions-http-api.md)
