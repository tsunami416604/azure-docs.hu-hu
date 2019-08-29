---
title: Kötések a Durable Functionshoz – Azure
description: Eseményindítók és kötések használata a Azure Functions Durable Functions bővítményéhez.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbd645ef9f5e687e71ce110fc84b8342e31defed
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087534"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions kötései (Azure Functions)

A [Durable functions](durable-functions-overview.md) bővítmény két új trigger kötést vezet be, amelyek a Orchestrator és a Activity függvények végrehajtását vezérlik. Emellett egy kimeneti kötést is bevezet, amely ügyfélként funkcionál az Durable Functions futtatókörnyezet számára.

## <a name="orchestration-triggers"></a>Előkészítési eseményindítók

Az előkészítési trigger lehetővé teszi tartós Orchestrator függvények készítését. Ez az aktiválás támogatja az új Orchestrator-függvények indítását és a meglévő Orchestrator-függvények lefolytatását, amelyek "várnak" egy feladatot.

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
> A Orchestrator függvények soha nem használhatnak semmilyen bemeneti vagy kimeneti kötést, amely nem az előkészítési trigger kötése. Ennek köszönhetően problémák léphetnek fel a tartós feladattal, mivel ezek a kötések nem engedelmeskednek az egyszálas és az I/O-szabályoknak.

> [!WARNING]
> A JavaScript Orchestrator funkcióit soha nem `async`lehet deklarálni.

### <a name="trigger-usage-net"></a>Trigger használata (.NET)

A előkészítési trigger kötése a bemeneteket és kimeneteket is támogatja. Íme néhány tudnivaló a bemeneti és kimeneti használatról:

* **bemenetek** – a .net-hangolási függvények csak a [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) támogatják. A bemenetek közvetlenül a függvény aláírásában való deszerializálása nem támogatott. A kódnak a [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.net) vagy `getInput` a (JavaScript) metódust kell használnia a Orchestrator függvény bemenetének beolvasásához. Ezeknek a bemeneteknek JSON-szerializálható típusúnak kell lenniük.
* **kimenetek** – a hangelőkészítési eseményindítók támogatják a kimeneti értékeket és a bemeneteket. A függvény visszatérési értéke a kimeneti érték hozzárendelésére szolgál, és csak JSON-szerializálható lehet. Ha egy .net-függvény `Task` visszaadja `void`a `null` értéket, a rendszer a kimenetként menti az értékeket.

### <a name="trigger-sample"></a>Példa triggerre

Az alábbi példa azt szemlélteti, hogy a legegyszerűbb ""Helló világ!"alkalmazás" Orchestrator függvény a következőhöz hasonló lehet:

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
> A `context` JavaScriptben lévő objektum nem a DurableOrchestrationContext, hanem a [teljes függvény kontextusa.](../functions-reference-node.md#context-object) A koordináló metódusok az `context` `df` objektum tulajdonságán keresztül érhetők el.

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

## <a name="activity-triggers"></a>Tevékenység-eseményindítók

A tevékenység-trigger lehetővé teszi a Orchestrator functions által hívott függvények készítését.

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

* `activity`a tevékenység neve. Ez az az érték, amelyet a Orchestrator függvények a tevékenység függvényének meghívására használnak. Ez a tulajdonság nem kötelező. Ha nincs megadva, a rendszer a függvény nevét használja.

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

Az alábbi példa egy egyszerű ""Helló világ!"alkalmazás" tevékenységi funkció megjelenését szemlélteti:

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

### <a name="passing-multiple-parameters"></a>Több paraméter átadása

Közvetlenül nem lehet több paramétert átadni egy tevékenység-függvénynek. Ebben az esetben a javaslat egy objektum tömbjét adja át, vagy [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) objektumokat használ a .net-ben.

A következő minta a [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) új funkcióit használja, amelyeket a [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>Előkészítési ügyfél

A koordináló ügyfél kötése lehetővé teszi olyan függvények írását, amelyek a Orchestrator függvényekkel működnek együtt. Például a következő módokon végezheti el az előkészítési példányok megszervezését:

* Indítsa el őket.
* Az állapotuk lekérdezése.
* Megszakítja őket.
* Események küldése a futtatásuk közben.
* Példányok előzményeinek kiürítése.

Ha a Visual studiót használja, a [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET-attribútum használatával kötést hozhat létre a koordináló ügyfélhez.

Ha programozási nyelveket (például. *CSX* -vagy *. js* -fájlokat) használ a fejlesztéshez, az összehangoló triggert a `bindings` *function. JSON*tömbben található következő JSON-objektum határozza meg:

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

A .net-függvények esetében általában a `DurableOrchestrationClient`következőhöz kötődik, ami teljes hozzáférést biztosít a Durable functions által támogatott összes ügyféloldali API-hoz. A JavaScriptben ugyanazokat az API-kat teszi `DurableOrchestrationClient` elérhetővé a `getClient`rendszer által visszaadott objektum. Az ügyfél-objektum API-jai a következők:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (jelenleg csak .NET)

Másik megoldásként a .net functions `IAsyncCollector<T>` is `T` köthető a [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) -hez vagy `JObject`a-hoz.

A műveletekkel kapcsolatos további részletekért tekintse meg a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API dokumentációját.

> [!WARNING]
> A JavaScriptben helyileg történő fejlesztéskor a környezeti változót `WEBSITE_HOSTNAME` `localhost:<port>`is be kell állítania. `localhost:7071`metódusok használata a `DurableOrchestrationClient`alkalmazásban. Erről a követelményről a [GitHub-probléma](https://github.com/Azure/azure-functions-durable-js/issues/28)című cikkben olvashat bővebben.

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

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Host.JSON-beállítások

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az ellenőrzőpontok és a visszajátszás viselkedésének megismerése](durable-functions-checkpointing-and-replay.md)