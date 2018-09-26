---
title: Durable Functions – Azure kötései
description: Hogyan eseményindítók és kötések a tartós Functons bővítmény használata az Azure Functions szolgáltatáshoz.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 10e6c440fe669f4bc8a18eb0a7a0f833675ab923
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182208"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) kötései

A [Durable Functions](durable-functions-overview.md) bővítmény bevezeti az orchestrator, illetve a tevékenység funkciók végrehajtását szabályozó két új eseményindító kötéseket. Emellett egy kimeneti kötést, amely indextáblaként a Durable Functions futtatókörnyezetének ügyfél vezet be.

## <a name="orchestration-triggers"></a>Vezénylési eseményindítók

Az orchestration eseményindító lehetővé teszi az orchestrator durable functions hozhat létre. Az eseményindító támogatja az orchestrator-funkció új példányok elindításából és az orchestrator függvény meglévő példányai, amely a "elnyerésére" feladat folytatása.

A Visual Studio-eszközök az Azure Functions használatakor az orchestration eseményindító van konfigurálva, használja a [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET attribútum.

Ír az orchestrator-funkciók (például az Azure Portalon) programozási nyelven, amikor az orchestration eseményindító határozza meg a következő JSON-objektumában a `bindings` tömbje a *function.json* fájlt:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` a vezénylés neve van. Ez az érték ügyfelek szeretne elindítani az új példányokat az orchestrator-funkció segítségével. Ez a tulajdonság nem kötelező. Ha nincs megadva, a függvény neve szolgál.

A trigger kötés belsőleg az alapértelmezett tárfiókot a függvényalkalmazás az üzenetsorok sorozatát kérdezi le. Ezek a várólisták belső megvalósítási részletei a bővítményt, ezért azok nem explicit módon konfigurált a kötési tulajdonságok.

### <a name="trigger-behavior"></a>Az eseményindító viselkedés

Íme néhány az orchestration eseményindító kapcsolatos megjegyzések:

* **Single-threading** – egy egyetlen dispatcher szálon egyetlen gazdagép-példány összes orchestrator függvény végrehajtásához. Ezért fontos, hogy az orchestrator függvénykód hatékony, és nem hajtja végre az összes i/o. Fontos továbbá győződjön meg arról, hogy ez a hozzászóláslánc nem aszinkron munka, kivéve, ha vár a Durable Functions-specifikus tevékenységtípust.
* **Poison üzenetkezelés** -vezénylési eseményindítók nem ártalmas üzenetek támogatottak.
* **Üzenet látható-e** -Vezénylési eseményindító üzenetek el távolítva a sorból, és egy konfigurálható időtartamának láthatatlan marad. Ezek az üzenetek látható-e automatikusan megújul, mindaddig, amíg a függvényalkalmazás fut, és kifogástalan állapotban.
* **Visszatérési értékek** -értékek JSON-szerializált, és megőrzi a az Azure Table storage a vezénylési előzménytábla adja vissza. A visszaadott értékeket a vezénylési ügyfél kötelező érvényű, a később ismertetett kérdezhetők le.

> [!WARNING]
> Orchestrator funkciók soha nem kell használni minden olyan bemeneti vagy kimeneti kötés eltérő a vezénylési kötés aktiválásához. Tartós feladat kiterjesztésű problémákat okozhat, ezeket a kötések nem lehetséges, hogy betartani, a single-threading és i/o-szabályokat mivel így van.

### <a name="trigger-usage"></a>Eseményindító-használat

A bemeneti és kiírja a vezénylési trigger kötés támogatja. Néhány dolog tudni a bemeneti és kimeneti kezelése:

* **bemenetek** -Vezénylési funkciók támogatása csak [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) paraméter típusaként. Közvetlenül a függvényaláíráshoz a bemenet deszerializálása nem támogatott. Kódot kell használnia a [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) metódussal beolvassa az orchestrator függvény bemenetei között. Ezeket a bemeneteket JSON-szerializálható típusok kell lennie.
* **outputs** -Vezénylési eseményindítók támogatják a kimeneti értékeket, valamint bemenetei között. A függvény visszatérési értéke a kimeneti értéket használja, és JSON-szerializálható kell lennie. Ha olyan függvényt ad vissza `Task` vagy `void`, amely egy `null` értékét menti a rendszer a kimenetként.

### <a name="trigger-sample"></a>Eseményindító-minta

Az alábbiakban látható egy példa a "Hello World" orchestrator legegyszerűbb függvény néz:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> A JavaScript vezénylők használjon `return`. A `durable-functions` tár meghívása nélkül gondoskodik a `context.done` metódust.

A legtöbb orchestrator hívnia a tevékenység funkciók, így a "Hello World" példa azt mutatja be, hogyan hívhat meg egy tevékenység függvényt:

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivityAsync("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Tevékenység-eseményindítók

A tevékenység eseményindító lehetővé teszi az orchestrator-funkciók által meghívott függvényeket hozhat létre.

Visual Studio használata, ha a tevékenység trigger van konfigurálva, használja a [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET attribútum. 

Fejlesztési használata az Azure Portalon, a tevékenység eseményindító határozza meg a következő JSON-objektum a `bindings` tömbje *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` a tevékenység nevét a rendszer. Ez az érték, amely az orchestrator függvények használatával a tevékenység függvény meghívása. Ez a tulajdonság nem kötelező. Ha nincs megadva, a függvény neve szolgál.

A trigger kötés belsőleg az alapértelmezett tárfiókot a függvényalkalmazás üzenetsorok kérdezi le. Ez a várólista egy belső implementálási részlete a bővítményt, ezért azt nem explicit módon történik a kötés tulajdonságai.

### <a name="trigger-behavior"></a>Az eseményindító viselkedés

Íme néhány a tevékenység eseményindító kapcsolatos megjegyzések:

* **Threading** – ellentétben az orchestration eseményindító tevékenység eseményindítók nem rendelkezik körül threading korlátozások vagy i/o. Normál funkciók például kezelhető.
* **Poison üzenetkezelés** -tevékenység eseményindítók nem ártalmas üzenetek támogatottak.
* **Üzenet látható-e** -tevékenység eseményindító üzenetek el távolítva a sorból, és egy konfigurálható időtartamának láthatatlan marad. Ezek az üzenetek látható-e automatikusan megújul, mindaddig, amíg a függvényalkalmazás fut, és kifogástalan állapotban.
* **Visszatérési értékek** -értékek JSON-szerializált, és megőrzi a az Azure Table storage a vezénylési előzménytábla adja vissza.

> [!WARNING]
> A tárolási háttéralkalmazások létre tevékenységfüggvényeket egy implementálási részlete és felhasználói kódban nem működhet együtt ezeket entitások közvetlenül.

### <a name="trigger-usage"></a>Eseményindító-használat

A bemeneti és kiírja, csakúgy, mint az orchestration eseményindító a tevékenység az eseményindító támogatja a kötés. Néhány dolog tudni a bemeneti és kimeneti kezelése:

* **bemenetek** -tevékenységfüggvényeket natív módon használhatják [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) paraméter típusaként. Azt is megteheti tevékenység függvényének deklarálható szerializálható JSON paraméter típussal. Ha használ `DurableActivityContext`, meghívhatja [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) beolvasásához és deszerializálni a tevékenység függvény bemeneti.
* **outputs** -tevékenységfüggvényeket támogatja a kimeneti értékeket, valamint bemenetei között. A függvény visszatérési értéke a kimeneti értéket használja, és JSON-szerializálható kell lennie. Ha olyan függvényt ad vissza `Task` vagy `void`, amely egy `null` értékét menti a rendszer a kimenetként.
* **metaadatok** -kötést hozhasson létre tevékenységfüggvényeket egy `string instanceId` paramétert a szülő vezénylési példány Azonosítójának lekéréséhez.

### <a name="trigger-sample"></a>Eseményindító-minta

Az alábbiakban látható egy példa egy egyszerű "Hello World" tevékenység függvény néz:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

```javascript
module.exports = function(context) {
    context.done(null, `Hello ${context.bindings.name}!`);
};
```

Az alapértelmezett paraméter típusa a `ActivityTriggerAttribute` kötés `DurableActivityContext`. Azonban tevékenység eseményindítók is közvetlenül a JSON-serializeable kötelező támogatási típusokat, (beleértve az egyszerű típusok), ugyanannak a függvénynek sikerült egyszerűbb, mint a következőképpen:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (csak függvények v2)

```javascript
module.exports = function(context, name) {
    context.done(null, `Hello ${name}!`);
};
```

### <a name="passing-multiple-parameters"></a>Több paraméterek átadása 

Nem alkalmas több paraméterek átadása egy tevékenység függvényt közvetlenül. Az ajánlás ebben az esetben az objektumok egy tömbjét adja át, vagy használja, [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) objektumokat.

A következő mintát használja az új funkciók [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) szolgáltatással együtt [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

## <a name="orchestration-client"></a>Vezénylési ügyfél

A vezénylési ügyfél kötés lehetővé teszi az orchestrator-függvényekkel kezelheti funkciók írását. Például akkor műveleteket hajthat végre vezénylési példányok a következő módon:
* Indítsa el őket.
* A lekérdezés azok állapotát.
* Állítsa le azokat.
* Események küldése őket, miközben futnak.

Ha a Visual Studio használata esetén kell kötni a vezénylési ügyfél használatával a [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET attribútum.

Programozási nyelvek használata (pl. *.csx* fájlok) fejlesztéshez, az orchestration eseményindító meghatározása szerint a következő JSON-objektum a `bindings` tömbje *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub` – Amikor több függvényalkalmazás osztani ugyanazt a tárfiókot, de kell elkülöníteni egymástól esetekben használatos. Ha nincs megadva, az alapértelmezett érték a `host.json` szolgál. Ezt az értéket meg kell egyeznie a cél az orchestrator functions által használt érték.
* `connectionName` -A egy tárfiók kapcsolati sztringje tartalmazó alkalmazásbeállítás neve. A tárfiók, ez a kapcsolati karakterlánc által képviselt ugyanaz a cél az orchestrator függvények által használt kell lennie. Ha nincs megadva, az alapértelmezett tárfiók kapcsolati sztringje a függvényalkalmazás szolgál.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki ezeket a tulajdonságokat, és az alapértelmezett viselkedés támaszkodnak.

### <a name="client-usage"></a>Ügyfelek általi használattal

A C#-függvények, általában kötött `DurableOrchestrationClient`, amely teljes hozzáférést biztosít az összes ügyfél API-k Durable Functions által támogatott. API-k, az ügyfél-objektumon a következők:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Azt is megteheti, hogy kötést hozhasson létre `IAsyncCollector<T>` ahol `T` van [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) vagy `JObject`.

Tekintse meg a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API-dokumentáció további részleteket az ezeket a műveleteket.

### <a name="client-sample-visual-studio-development"></a>Eszközügyfél minta (Visual Studio fejlesztési)

Íme egy példa üzenetsor által aktivált függvényt, amely elindítja a "HelloWorld" vezénylési.

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

### <a name="client-sample-not-visual-studio"></a>Eszközügyfél minta (nem a Visual Studio)

Ha nem használ a Visual Studio-fejlesztéshez, hozhat létre a következő *function.json* fájlt. Ez a példa bemutatja, hogyan üzenetsor által aktivált függvény, amely használ a tartós vezénylési ügyfél kötésének konfigurálása:

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
      "direction": "out"
    }
  ],
  "disabled": false
} 
```

Az alábbiakban nyelvét a minták azt elindítani az új orchestrator-funkció példányokat.

#### <a name="c-sample"></a>C#-minta

A következő minta bemutatja, hogyan használhatja az új funkció-példány egy C#-szkriptfüggvény kezdő kötés tartós vezénylési ügyfél:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript-minta

A következő minta bemutatja, hogyan használhatja az új funkció-példány indítása a JavaScript-függvény kötése tartós vezénylési ügyfél:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

További részletek a példányok indítása található [példány felügyeleti](durable-functions-instance-management.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ellenőrzőpont és visszajátszás viselkedések ismertetése](durable-functions-checkpointing-and-replay.md)

