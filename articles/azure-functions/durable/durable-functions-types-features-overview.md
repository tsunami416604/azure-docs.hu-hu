---
title: Függvény típusa és az Azure Functions-Durable Functions bővítmény szolgáltatásai
description: Ismerje meg a functions és az Azure Functions-Durable Functions vezénylési a függvény-függvény kommunikációt támogató szerepkörök típusú.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 76b6f013333113d5a24b744bc962d36b1c0e21b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455734"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions-típusok és a szolgáltatások (az Azure Functions)

Durable Functions bővítmény [Azure Functions](../functions-overview.md). Durable Functions állapotalapú vezénylés, a függvény végrehajtási is használhat. Tartós függvények egy megoldás, amely különböző, az Azure functions áll. Funkciók egy tartós függvény vezénylési lejátszhatja különböző szerepköröket. 

Ez a cikk áttekintést, a functions-Durable Functions vezénylési használható típusú. A cikk gyakori minták segítségével csatlakoztathatják a functionst tartalmazza. Ismerje meg, hogyan Durable Functions nyújt segítséget az alkalmazás fejlesztői problémák megoldásában.

![Durable functions típusú bemutató kép][1]  

## <a name="types-of-durable-functions"></a>Durable functions típusai

Háromféle tartós függvényt is használhatja az Azure Functions: tevékenység, az orchestrator és az ügyfél.

### <a name="activity-functions"></a>Tevékenységfüggvényeket

Tevékenységfüggvényeket egy tartós függvény vezénylési alapvető munkaegysége. Tevékenység funkciók a functions és a feladatokat, amelyek Vezényel az a folyamat során. Például előfordulhat, hogy hozzon létre egy tartós függvényt egy megrendelés feldolgozásához. A feladatok között ellenőrzi a készletet, az ügyfél díjszabási, majd hozza létre a szállítás. Minden tevékenység egy tevékenység függvény lenne. 

Tevékenységfüggvényeket teheti őket a munka típusától nincsenek korlátozva. Bármely írhat egy tevékenység függvény [nyelv, amely támogatja a Durable Functions](durable-functions-overview.md#language-support). A tartós feladat keretrendszer garantálja, hogy minden tevékenység hívott függvény egy vezénylési során legalább egyszer hajtható végre.

Használja az [tevékenység eseményindító](durable-functions-bindings.md#activity-triggers) aktiválhat egy tevékenység függvényt. .NET-es függvényeket kap egy [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) paraméterként. Az eseményindító átadni a függvény bemenetei között szereplő bármely más objektumot is kötni. A JavaScript, egy bemeneti keresztül hozzáférhet a `<activity trigger binding name>` tulajdonsága a [ `context.bindings` objektum](../functions-reference-node.md#bindings).

A tevékenység függvényt is az orchestrator értékeket adhat vissza. Ha küld vagy értékek nagy számú visszaadása egy tevékenység-függvényt, [rekordokat tartalmazó vagy tömbök](durable-functions-bindings.md#passing-multiple-parameters). Egy tevékenység függvény csak egy vezénylési példányból is indíthat. -Tevékenységgel és egy másik függvény (például egy HTTP-eseményindítóval aktivált függvényt) előfordulhat, hogy oszt meg egy kódrészletet, bár minden egyes függvény csak egy eseményindító is rendelkezik.

További információ és példák: [tevékenységfüggvényeket](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Az orchestrator-funkciók

Az orchestrator-funkciók a műveletek végrehajtásának módját és a sorrend, amelyben végrehajtás ismertetik. Az orchestrator-funkciók leírása a kódban a vezénylési (C# vagy JavaScript), ahogyan [Durable Functions-minták és technikai kulcsfogalmak](durable-functions-concepts.md). Egy vezénylési műveleteket, beleértve számos különböző típusú rendelkezhet [tevékenységfüggvényeket](#activity-functions), [alárendelt vezénylések](#sub-orchestrations), [külső események Várakozás](#external-events), és [időzítők](#durable-timers). 

Az orchestrator függvényének kell elindítható a egy [vezénylési eseményindító](durable-functions-bindings.md#orchestration-triggers).

Az orchestrator indítja el egy [orchestrator ügyfél](#client-functions). Az orchestrator bármilyen forrásból (HTTP, várólista, esemény-adatfolyam) is indíthat. Egy vezénylési minden példánya egy példány azonosítóval rendelkezik. A példány azonosítóját (ajánlott) automatikusan létrehozott vagy felhasználó által létrehozott. Használhatja a példány azonosító [felügyelhető](durable-functions-instance-management.md) , a vezénylési.

További információ és példák: [Vezénylési eseményindítók](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Ügyfél-funkciók

Ügyfél függvények találhatók aktivált, amely egy vezénylési új példányokat hoz létre. Ügyfél funkciók a belépési pont egy Durable Functions vezénylési példányának létrehozásához. Bármilyen forrásból (HTTP, várólista, esemény-adatfolyam) függvény ügyfél is indíthat. Bármilyen nyelven, az alkalmazás által támogatott ügyfél függvény írhat. 

Ügyfél-funkciók is rendelkezik egy [vezénylési ügyfél](durable-functions-bindings.md#orchestration-client) kötést. Egy ügyfél függvényt használhatja a kötés létrehozásához és kezeléséhez a tartós vezénylések vezénylési ügyfél. 

A legalapvetőbb például egy ügyfél függvényt, egy HTTP-eseményindítóval aktivált függvényt, amely elindítja egy orchestrator-függvényt, és majd a jelölőnégyzet állapotának választ adja vissza. Egy vonatkozó példáért lásd: [HTTP API URL-cím-felderítési](durable-functions-http-api.md#http-api-url-discovery).

További információ és példák: [Vezénylési ügyfél](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkciók és minták

A következő szakaszok ismertetik a funkciók és a tartós függvények típusú.

### <a name="sub-orchestrations"></a>Alvezénylések

Orchestrator funkciók tevékenységfüggvényeket meghívhatja, de ezek is meghívhat az orchestrator-függvényekkel. Ha például hozhat létre egy nagyobb vezénylési orchestrator funkciók kívül egy könyvtár. Másik lehetőségként egy orchestrator-függvényt több példánya is futtatható egyszerre.

További információ és példák: [alárendelt vezénylések](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Tartós időzítők

[Durable Functions](durable-functions-overview.md) biztosít *tartós időzítők* felhasználható az orchestrator funkciók megvalósításához az késleltetések vagy időtúllépések, az aszinkron műveletek beállításához. Tartós időzítők használata az orchestrator funkcióit `Thread.Sleep` és `Task.Delay` (C#) vagy `setTimeout()` és `setInterval()` (JavaScript).

További információ és példák: [tartós időzítők](durable-functions-timers.md).

### <a name="external-events"></a>Külső események

Az orchestrator funkciók várhat a külső eseményeket közvetlenül egy vezénylési példányt frissíteni. Ez a szolgáltatás tartós függvények gyakran hasznos emberi beavatkozás vagy más külső visszahívások kezelése.

További információ és példák: [külső események](durable-functions-external-events.md).

### <a name="error-handling"></a>Hibakezelés

Durable Functions vezénylések megvalósítása a kód használatával. A programozási nyelv hibakezelési funkcióját is használhatja. Minták, mint `try` / `catch` a vezénylési működik. 

Durable Functions beépített újrapróbálkozási szabályzatok is kapható. Művelet késleltetése és a tevékenységek automatikusan újra, ha kivétel lép fel. Használhatja az újrapróbálkozások elhagyása nélkül modernizálhat a vezénylési átmeneti kivételek kezelésére.

További információ és példák: [hibakezelés](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Több függvényalkalmazás kommunikáció

Bár egy tartós vezénylési egyetlen függvényalkalmazás környezetében fut, használhatja a minták vezénylések koordinálására számos függvényalkalmazások között. Alkalmazások közötti kommunikáció HTTP-n keresztül is felléphet, de a tartós keretrendszer használatával minden egyes tevékenységhez azt jelenti, hogy továbbra is fenntarthat egy tartós folyamat két alkalmazás között.

Az alábbi példák bemutatják, több függvényalkalmazás szolgáltatásvezénylés C# és a JavaScript. Minden példánál egy tevékenység külső vezénylési indítja el. Egy másik tevékenység lekéri és állapotát adja vissza. Az orchestrator megvárja, amíg az állapot `Complete` folytatás előtt.

Íme néhány példa a több függvényalkalmazás vezénylési:

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration.
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now, go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    // Do some work...

    // Call a remote orchestration.
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete.
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now, go do more work...
});
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, orchestratorName) {
    const options = {
        method: "POST",
        uri: `https://appB.azurewebsites.net/orchestrations/${orchestratorName}`,
        body: ""
    };

    const statusUrl = await request(options);
    return statusUrl;
};
```

```javascript
const request = require("request-promise-native");

module.exports = async function(context, statusUrl) {
    const options = {
        method: "GET",
        uri: statusUrl,
        resolveWithFullResponse: true,
    };

    const response = await request(options);
    // 200 = Complete, 202 = Running
    return response.statusCode === 200;
};
```

## <a name="next-steps"></a>További lépések

Első lépésként hozzon létre az első tartós függvény a [ C# ](durable-functions-create-first-csharp.md) vagy [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Tudjon meg többet a tartós függvények](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
