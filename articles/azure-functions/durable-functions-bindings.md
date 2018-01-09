---
title: "Kötések tartós függvények – Azure"
description: "Hogyan eseményindítók és kötések használható az Azure Functions a tartós Functons bővítmény."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 3be59e32de22e0939ee887fba1d20829f1ef22eb
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Kötések tartós függvények (az Azure Functions)

A [tartós funkciók](durable-functions-overview.md) bővítmény vezet be az orchestrator és a tevékenység függvény végrehajtása szabályozó két új eseményindító kötés. Egy kimeneti kötése, amely különbséglemezként funkcionál a tartós Functions futtatókörnyezete ügyfelet is okozna.

## <a name="orchestration-triggers"></a>Vezénylési eseményindítók

Az orchestration eseményindító lehetővé teszi a tartós orchestrator funkciók készíthet. Ehhez az eseményindítóhoz támogatja az új orchestrator-funkció példányai kezdő- és orchestrator függvény meglévő példányai váró "" feladat folytatása.

Az Azure Functions a Visual Studio eszközök használata esetén a vezénylési eseményindító segítségével lett konfigurálva a [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .NET attribútum.

Írásakor orchestrator funkciók (például az Azure portálon) programozási nyelven, a vezénylési eseményindító határozzák meg a következő JSON-objektum a `bindings` tömbje a *function.json* fájlt:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "version": "<Optional - version label of this orchestrator function>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`a vezénylési neve van. Ez az érték ügyfelek szeretne elindítani az új példányokat orchestrator függvény segítségével. Ez a tulajdonság nem kötelező. Ha nincs megadva, a függvény nevét használja.
* `version`a vezénylési verzió címkét van. Ügyfelek, indítsa el az orchestration egy új példányát tartalmaznia kell a megfelelő verzió címkét. Ez a tulajdonság nem kötelező. Ha nincs megadva, a üres karakterláncot kell használni. Versioning további információkért lásd: [Versioning](durable-functions-versioning.md).

> [!NOTE]
> A beállítás értéke a `orchestration` vagy `version` tulajdonságok jelenleg nem ajánlott.

Belső eseményindító kötés kérdezze le az alapértelmezett tárfiókot, a függvény alkalmazás várólistákból sorozata. Ezek a várólisták belső megvalósítás részletei bővítmény, ezért explicit módon nincsenek beállítva a kötési tulajdonságok.

### <a name="trigger-behavior"></a>Eseményindító viselkedése

Íme néhány a vezénylési eseményindító kapcsolatos megjegyzések:

* **Single-threading** -egy egyetlen dispatcher száltól használt összes orchestrator függvény végrehajtása egyetlen példányán. Ezért fontos győződjön meg arról, hogy az orchestrator funkciókódot hatékony, és nem hajtja végre az összes i/o. Fontos továbbá győződjön meg arról, hogy a szál nem kivéve, ha a tartós funkciók-specifikus feladat típusok várakozó aszinkron munka.
* **Poison-üzenetek kezelésének** -nem az elhalt üzenet támogatja a vezénylési eseményindítók.
* **Üzenet-láthatósági** -Vezénylési eseményindító üzenetek várólistából kivéve és konfigurálható idejére láthatatlan tartani. Látható-e az üzenetek automatikusan megújítják mindaddig, amíg a függvény alkalmazás fut, és megfelelő.
* **Visszatérési értékek** -visszatérési értékek JSON formátumúvá szerializált és maradnak meg a vezénylési előzménytábla Azure Table storage-ban. A visszatérési érték lehet lekérdezni a vezénylési ügyfél kötést, a későbbiekben olvashat.

> [!WARNING]
> Orchestrator funkciók soha nem kell használni a bemeneti vagy kimeneti kötések eltérő a vezénylési indítás kötés. Ezzel a tartós feladatkiterjesztés problémákat okozhat, mert ezeket a kötések nem veszi fel a single-threading és i/o-szabályok rendelkezik.

### <a name="trigger-usage"></a>Eseményindító kihasználtsága

A bemeneti és kimeneti az orchestration eseményindító kötése támogatja. A következőkben bemeneti és kimeneti kezelése:

* **bemenetek** -támogatás csak Vezénylési működik [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) paramétertípusaként. Közvetlenül a függvényaláíráshoz a bemeneti adatok deszerializálása nem támogatott. Kódot kell használnia a [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) metódus lehívása orchestrator függvény bemenet. A bemeneti JSON-szerializálható típusnak kell lennie.
* **kimeneti** -Vezénylési eseményindítók támogatja a kimeneti értékeit, valamint a bemeneti adatok. A függvény visszatérési értéke a kimeneti értéket hozzárendelni szolgál, és JSON-szerializálhatónak kell lennie. Ha egy olyan függvényt ad vissza `Task` vagy `void`, egy `null` értéket a rendszer menti a kimenetként.

> [!NOTE]
> Vezénylési eseményindítók csak támogatottak a C# most.

### <a name="trigger-sample"></a>Eseményindító minta

A következő egy példa hogyan nézhet ki a "Hello World" C# legegyszerűbb orchestrator függvény:

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

A legtöbb orchestrator függvény tevékenység funkciók, hívható meg, a "Hello, World" példa azt mutatja be, hogyan hívhatja meg egy tevékenység függvényben:

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = await context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

## <a name="activity-triggers"></a>Tevékenység eseményindítók

A tevékenység indítási függvények, amelyek az orchestrator-funkciók által meghívott szerzői teszi lehetővé.

Ha a Visual Studio használata esetén a tevékenység indítási segítségével lett konfigurálva a [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET attribútum. 

Fejlesztési használata az Azure-portálon, a tevékenység indítási határozzák meg a következő JSON-objektum a `bindings` tömbje *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "version": "<Optional - version label of this activity function>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`a tevékenység esetén. Ez az érték az orchestrator funkciók segítségével a tevékenység függvény meghívása. Ez a tulajdonság nem kötelező. Ha nincs megadva, a függvény nevét használja.
* `version`a tevékenység verzió címke van. Az orchestrator függvények meghívása tevékenységgel tartalmaznia kell a megfelelő verzió címke. Ez a tulajdonság nem kötelező. Ha nincs megadva, a üres karakterláncot kell használni. További információkért lásd: [Versioning](durable-functions-versioning.md).

> [!NOTE]
> A beállítás értéke a `activity` vagy `version` tulajdonságok jelenleg nem ajánlott.

Belső eseményindító kötés kérdezze le az alapértelmezett tárfiókot, a függvény alkalmazás üzenetsorból. A várólista egy belső végrehajtási részletei bővítmény, ezért a kötési tulajdonságok explicit módon nincs konfigurálva.

### <a name="trigger-behavior"></a>Eseményindító viselkedése

Az alábbiakban néhány tevékenység eseményindító megjegyzéseket:

* **Szálkezelési** -eltérően az orchestration eseményindító tevékenység eseményindítók nem rendelkezik körül szálkezelési korlátozások vagy i/o. Például reguláris funkciók kezelhető.
* **Poising üzenetének** -tevékenység eseményindítók nem az elhalt üzenet támogatja.
* **Üzenet-láthatósági** -tevékenység indítási üzenetek várólistából kivéve és konfigurálható idejére láthatatlan tartani. Látható-e az üzenetek automatikusan megújítják mindaddig, amíg a függvény alkalmazás fut, és megfelelő.
* **Visszatérési értékek** -visszatérési értékek JSON formátumúvá szerializált és maradnak meg a vezénylési előzménytábla Azure Table storage-ban.

> [!WARNING]
> A tároló háttér tevékenység függvények egy megvalósítási részletes, és a felhasználói kód nem működhet együtt a tárolási entitások közvetlenül.

### <a name="trigger-usage"></a>Eseményindító kihasználtsága

A bemeneti és kimeneti, csakúgy, mint a vezénylési eseményindító a tevékenység indítási kötése támogatja. A következőkben bemeneti és kimeneti kezelése:

* **bemenetek** -tevékenység funkciók natív módon használja [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) paramétertípusaként. Alternatív megoldásként egy tevékenység függvény deklarálható szerializálhatók a JSON paraméter típussal. Amikor `DurableActivityContext`, hívása [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) beolvasásához és a tevékenység függvény deszerializálni adjon meg.
* **kimeneti** -tevékenység funkciókat támogatja a kimeneti értékeit, valamint a bemeneti adatok. A függvény visszatérési értéke a kimeneti értéket hozzárendelni szolgál, és JSON-szerializálhatónak kell lennie. Ha egy olyan függvényt ad vissza `Task` vagy `void`, egy `null` értéket a rendszer menti a kimenetként.
* **metaadatok** -tevékenység funkciók köthető egy `string instanceId` paraméter használatával beolvassa az a szülő vezénylési Példányazonosítója.

> [!NOTE]
> Tevékenység eseményindítók jelenleg nem támogatottak a Node.js-funkciók.

### <a name="trigger-sample"></a>Eseményindító minta

A következő egy példa hogyan nézhet ki egy egyszerű "Hello World" C# tevékenység függvény:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Az alapértelmezett paraméter típusa a `ActivityTriggerAttribute` kötés `DurableActivityContext`. Azonban a tevékenység eseményindítók is közvetlenül a JSON-serializeable kötelező támogatási típusokat (beleértve a primitív típusok), ugyanezt a funkciót sikerült egyszerűsített, mint a következő:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

## <a name="orchestration-client"></a>Vezénylési ügyfél

Az orchestration ügyfél kötés lehetővé teszi az orchestrator functions interaktív funkciók írását. Például működhet a vezénylési példányok a következő módon:
* Indítsa el őket.
* Lekérdezési azok állapotát.
* Állítsa le azokat.
* Küldeni események őket most közben.

Ha a Visual Studio használata esetén köthető az orchestration ügyfél használatával a [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET attribútum.

Parancsprogramnyelv használata (pl. *.csx* fájlok) fejlesztési, a vezénylési eseményindító határozzák meg a következő JSON-objektum a `bindings` tömbje *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub`-Forgatókönyvekben, ahol több függvény alkalmazás megosztása ugyanazt a tárfiókot, de el legyen különítve egymástól kell használni. Ha nincs megadva, az alapértelmezett érték a `host.json` szolgál. Ezt az értéket meg kell egyeznie a cél az orchestrator függvény által használt érték.
* `connectionName`-A tárolási fiók kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ez a kapcsolati karakterlánc által képviselt tárfiók azonosnak kell lennie a cél az orchestrator-funkciók által alkalmazott. Ha nincs megadva, a függvény alkalmazás alapértelmezett tárolási fiók kapcsolati karakterlánca szolgál.

> [!NOTE]
> A legtöbb esetben azt javasoljuk, hogy hagyja ki ezeket a tulajdonságokat, és az alapértelmezett viselkedés támaszkodnak.

### <a name="client-usage"></a>Az ügyfelek általi használattal

A C# függvények, akkor általában kötni `DurableOrchestrationClient`, amely hozzáférést biztosít a tartós funkciók által támogatott API-k az összes ügyfél számára. Az ügyfél objektum API-kat a következők:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Másik lehetőségként kell kötni az `IAsyncCollector<T>` ahol `T` van [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) vagy `JObject`.

Tekintse meg a [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API dokumentációja további részleteket a ezeket a műveleteket.

### <a name="client-sample-visual-studio-development"></a>Ügyfél-mintát (a Visual Studio fejlesztői)

Íme egy példa várólista-eseményindítóval aktivált függvény, hogy a "HelloWorld" vezénylési elindul.

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

### <a name="client-sample-not-visual-studio"></a>Ügyfél-mintát (nem a Visual Studio)

Ha nem használja a Visual Studio fejlesztési, hozhat létre a következő *function.json* fájlt. A példa bemutatja, hogyan konfigurálhatja a várólista-eseményindítóval aktivált függvény, amely a kötés tartós vezénylési ügyfélprogramot használ:

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

Az alábbiakban nyelvspecifikus mintát, amely elindítani az új orchestrator-funkció példányokat.

#### <a name="c-sample"></a>C# minta

A következő példa bemutatja, hogyan használhatja a tartós vezénylési indítson egy új függvény példányt egy C# parancsfájl függvény kötési:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="nodejs-sample"></a>NODE.js-mintát

A következő példa bemutatja, hogyan használhatja a tartós orchestration-függvény új példányt a Node.js-függvény történő kötés:

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

További részleteket a példányok indítása található [példány felügyeleti](durable-functions-instance-management.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az ellenőrzőpontok létrehozása és a visszajátszás viselkedések](durable-functions-checkpointing-and-replay.md)

