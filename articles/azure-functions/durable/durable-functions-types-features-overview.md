---
title: Függvény-típusok és Durable Functions – Azure szolgáltatások áttekintése
description: Ismerje meg, milyen funkciók és szerepkörök, amelyek lehetővé teszik egy tartós függvény vezénylési részeként függvény függvény kommunikációhoz.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbfee92343bfecfbe8395f95775ae1f107b99299
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037276"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Függvény típusa és a tartós függvények (az Azure Functions) szolgáltatások áttekintése

Durable Functions állapot-nyilvántartó hangolja össze a függvény végrehajtási. Tartós függvények a különböző Azure Functions áll megoldás. Ezek a függvények mindegyike egy vezénylési részeként különböző szerepkörök játszhatja le. A következő dokumentum a különböző típusú részt vesz egy tartós függvény vezénylési funkciók áttekintése. Gyakori minták a functions összekapcsolása is tartalmaz.  Vágjon bele most, hogy a tartós függvény létrehozása [ C# ](durable-functions-create-first-csharp.md) vagy [JavaScript](quickstart-js-vscode.md).

![Durable functions típusai][1]  

## <a name="types-of-functions"></a>Függvények típusai

### <a name="activity-functions"></a>Tevékenységfüggvényeket

Tevékenységfüggvényeket olyan tartós vezénylési alapvető munkaegysége.  Tevékenységfüggvényeket funkcióit és a folyamat alatt vezényelt feladatait.  Például előfordulhat, hogy létrehoz egy tartós függvényt, amely egy megrendelés feldolgozásához – ellenőrizze a készlet díja szerint számítjuk fel az ügyfél, és hozzon létre egy szállításáról.  Ezeket a feladatokat mindegyike lenne egy tevékenység függvényt.  Tevékenységfüggvényeket korlátozások nem szerepel bennük teheti munka típusától.  Csak írható bármely [Durable Functions által támogatott nyelvi](durable-functions-overview.md#language-support). A tartós feladat keretrendszer garantálja, hogy minden tevékenység hívott függvény legalább egyszer során végrehajtandó folyamattevékenységek vezénylése.

Egy tevékenység függvényt kell elindítható a egy [tevékenység eseményindító](durable-functions-bindings.md#activity-triggers).  .NET-es függvényeket kap egy [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) paraméterként. Az eseményindító átadni a függvény bemenetei között szereplő bármely más objektumot is kötni. A JavaScript, bemenet-n keresztül elérhető a `<activity trigger binding name>` tulajdonsága a [ `context.bindings` objektum](../functions-reference-node.md#bindings).

A tevékenység függvényt is térjen vissza az orchestrator értékeket adhat vissza.  Ha a küldő vagy sok érték visszaadása egy tevékenység-függvényt, [rekordokat tartalmazó vagy tömbök](durable-functions-bindings.md#passing-multiple-parameters).  Tevékenységfüggvényeket csak indítható el egy vezénylési-példányból.  Bár egy kódrészletet egy tevékenységgel és egy másik függvény (például egy HTTP által aktivált függvény) között megosztható, minden egyes függvény legfeljebb egy trigger.

További információt és példákat találhat a [Durable Functions kötés cikk](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Az orchestrator-funkciók

Az orchestrator függvények olyan tartós függvények legfontosabb céljait segítik.  Az orchestrator-funkciók a módja és a végrehajtás sorrendje leírása.  Az orchestrator-funkciók leírása a kódban a vezénylési (C# vagy JavaScript), ahogyan a [Durable Functions-minták és technikai kulcsfogalmak](durable-functions-concepts.md).  Egy vezénylési számos különböző típusú műveletek, például rendelkezhet [Tevékenységfüggvényeket](#activity-functions), [alárendelt vezénylések](#sub-orchestrations), [külső események Várakozás](#external-events), és [ időzítők](#durable-timers).  

Az orchestrator függvényének kell elindítható a egy [vezénylési eseményindító](durable-functions-bindings.md#orchestration-triggers).

Az orchestrator indítja el egy [orchestrator ügyfél](#client-functions) amely sikerült magát indítható bármilyen forrásból (HTTP, üzenetsorok, az eseményfolyamokról).  Folyamattevékenységek vezénylése minden példánya egy példány azonosítóval rendelkezik, amely képes lesz automatikusan létrehozott (ajánlott) vagy a felhasználó által létrehozott.  Ez az azonosító az alábbiakra használhatók [felügyelhető](durable-functions-instance-management.md) , a vezénylési.

További információt és példákat találhat a [Durable Functions kötés cikk](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Ügyfél-funkciók

Ügyfél függvények találhatók aktivált, amely egy vezénylési új példányát hozza létre.  Azok a belépési pont egy tartós vezénylési példányának létrehozásához.  Ügyfél-funkciók is bármely (HTTP, üzenetsorok, esemény-adatfolyamok, stb.) eseményindító által aktivált és az alkalmazás által támogatott bármilyen nyelven fejlesztett.  Ügyfél-függvények, az eseményindító mellett egy [vezénylési ügyfél](durable-functions-bindings.md#orchestration-client) kötést, amely lehetővé teszi, hogy hozhat létre és kezelhet tartós vezénylések.  A legalapvetőbb például egy ügyfél függvényt, egy HTTP által aktivált függvényt, amely elindítja egy orchestrator-függvényt, és választ küld jelölőnégyzet állapotának, [az alábbi példában látható](durable-functions-http-api.md#http-api-url-discovery).

További információt és példákat találhat a [Durable Functions kötés cikk](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkciók és minták

### <a name="sub-orchestrations"></a>Alvezénylések

Mellett tevékenységfüggvényeket hívja meg, az orchestrator-funkciók meghívhatja az orchestrator-függvényekkel. Ha például hozhat létre egy nagyobb vezénylési orchestrator funkciók kívül egy könyvtár. Vagy egy orchestrator-függvényt több példánya is futtatható egyszerre.

További információt és példákat találhat a [alárendelt vezénylési cikk](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Tartós időzítők

[Durable Functions](durable-functions-overview.md) biztosít *tartós időzítők* orchestrator funkciók megvalósításához az késleltetések vagy időtúllépések, az aszinkron műveletek beállításához használható. Tartós időzítők célszerű használni az orchestrator funkcióit `Thread.Sleep` és `Task.Delay` (C#), vagy `setTimeout()` és `setInterval()` (JavaScript).

További információk és példák a tartós időzítők megtalálható a [tartós időzítők cikk](durable-functions-timers.md)

### <a name="external-events"></a>Külső események

Az orchestrator funkciók várhat a külső eseményeket közvetlenül egy vezénylési példányt frissíteni. Ez a szolgáltatás tartós függvények gyakran hasznos emberi beavatkozás vagy más külső visszahívások kezelése.

További információt és példákat találhat a [külső események cikk](durable-functions-external-events.md).

### <a name="error-handling"></a>Hibakezelés

Tartós függvény vezénylések kódban vannak megvalósítva, és a programozási nyelv hibakezelési funkciói.  Ez azt jelenti, például "try/catch" fog működni a vezénylési minták.  Durable functions néhány beépített újrapróbálkozási szabályzatok is kapható.  Művelet is késleltetés, és ismételje meg a tevékenység automatikusan kivételek.  Újrapróbálkozás átmeneti kivételek kezelésére anélkül, hogy le kellene mondaniuk az orchestration teszi lehetővé.

További információt és példákat találhat a [hibakezelési cikk](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Több függvényalkalmazás kommunikáció

Tartós vezénylési általában egy adott függvény alkalmazás a környezeten belül él, amíg nincsenek minták ahhoz, hogy koordinálja a vezénylések számos függvényalkalmazások között.  Annak ellenére, hogy az alkalmazások közötti kommunikációt, hogy valamelyike HTTP-n keresztül, a tartós keretrendszer használatával minden egyes tevékenységhez azt jelenti, hogy továbbra is fenntarthat egy tartós folyamat két alkalmazás között.

Egy több függvényalkalmazás szolgáltatásvezénylés példái C# és a JavaScript lent találhatók.  Egy tevékenység külső vezénylési indul el. Egy másik tevékenység majd betölti és visszaállíthatja az állapotot.  Az orchestrator várakozik az állapot kész a folytatás előtt.

#### <a name="c"></a>C#

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
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

    // Call a remote orchestration
    const statusUrl = yield context.df.callActivity("StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true) {
        const isComplete = yield context.df.callActivity("CheckIsComplete", statusUrl);
        if (isComplete) {
            break;
        }

        const waitTime = moment(context.df.currentUtcDateTime).add(1, "m").toDate();
        yield context.df.createTimer(waitTime);
    }

    // B is done. Now go do more work...
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

> [!div class="nextstepaction"]
> [Olvassa Durable Functions – dokumentáció](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
