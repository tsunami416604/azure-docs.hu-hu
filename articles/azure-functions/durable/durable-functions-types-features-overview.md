---
title: A függvények típusai és funkciói a Azure Functions Durable Functions bővítményében
description: Ismerje meg, hogy milyen típusú függvények és szerepkörök működnek a Durable Functions Azure Functions-ben való összehangolása során.
services: functions
author: jeffhollan
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 0d3087c768a02bb5c647fc0d10db3aa4274804f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097746"
---
# <a name="durable-functions-types-and-features-azure-functions"></a>Durable Functions típusok és szolgáltatások (Azure Functions)

Durable Functions a [Azure functions](../functions-overview.md)kiterjesztése. A függvények végrehajtásának állapot-nyilvántartó előkészítéséhez Durable Functions is használhatja. A tartós függvény olyan megoldás, amely különböző Azure-függvényekből áll. A függvények a különböző szerepköröket egy tartós függvény-előkészítés során is lejátszhatják. 

Ez a cikk áttekintést nyújt a Durable Functions-előkészítésben használható függvények típusairól. A cikk a függvények összekapcsolásához használható általános mintákat tartalmaz. Ismerje meg, hogyan segítheti a Durable Functions az alkalmazások fejlesztési kihívásainak megoldásában.

![Egy olyan rendszerkép, amely a tartós függvények típusait jeleníti meg][1]  

## <a name="types-of-durable-functions"></a>A tartós függvények típusai

A Azure Functions-ben négy tartós függvényt használhat: tevékenység, Orchestrator, entitás és ügyfél.

### <a name="activity-functions"></a>Activity függvények

A tevékenységi funkciók az alapszintű Munkaegységek tartós függvények előkészítésében. A Activity functions a folyamat során feldolgozható függvények és feladatok. Létrehozhat például egy tartós függvényt egy megrendelés feldolgozásához. A feladatok közé tartozik a leltár ellenőrzése, az ügyfél töltése és a szállítás létrehozása. Minden feladat tevékenységi függvény lenne. 

A tevékenységi funkciók nem korlátozódnak a bennük elvégezhető feladatok típusára. Egy tevékenység-függvényt bármilyen nyelven írhat, [amelyet Durable functions támogat](durable-functions-overview.md#language-support). A tartós feladati keretrendszer garantálja, hogy minden ún. tevékenységi függvény legalább egyszer végre lesz hajtva egy előkészítés során.

Tevékenység- [trigger](durable-functions-bindings.md#activity-triggers) használatával aktiválhat egy Activity függvényt. A .NET függvények paraméterként kapnak egy [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) . Azt is megteheti, hogy az triggert bármely más objektumhoz köti, hogy beadja a függvény bemeneteit. A JavaScriptben az `<activity trigger binding name>` [ `context.bindings` objektum](../functions-reference-node.md#bindings)tulajdonságán keresztül férhet hozzá egy bemenethez.

A Activity függvény értékeket is visszaadhat a Orchestrator. Ha egy tevékenység-függvényből nagy mennyiségű értéket küld vagy ad vissza, [rekordok vagy tömböket](durable-functions-bindings.md#passing-multiple-parameters)is használhat. Egy tevékenység-függvény csak egy előkészítési példányból aktiválható. Bár a tevékenység függvény és egy másik függvény (például egy HTTP-triggert használó függvény) megoszthat egy kódot, minden függvénynek csak egy triggere lehet.

További információért és Példákért lásd: [Activity functions](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Orchestrator függvények

A Orchestrator függvények leírják a műveletek végrehajtását, valamint a műveletek végrehajtásának sorrendjét. A Orchestrator függvények a kódban (C# vagy JavaScriptben) való összeszerelést írják le, ahogy azt a [Durable functions minták és műszaki fogalmak](durable-functions-concepts.md)mutatják be. Az előkészítés számos különböző típusú műveletet tartalmazhat, többek között a [tevékenységek](#activity-functions)funkcióit [](#sub-orchestrations), az alfolyamatokat, [a külső eseményekre való várakozást és az](#external-events) [időzítőket](#durable-timers). A Orchestrator függvények is kommunikálhatnak az [Entity functions](#entity-functions)szolgáltatással.

A Orchestrator függvényt egy előkészítési triggernek [](durable-functions-bindings.md#orchestration-triggers)kell kiváltani.

A Orchestrator egy [Orchestrator-ügyfél](#client-functions)indítja el. A Orchestrator bármely forrásból (HTTP, üzenetsor, esemény-adatfolyam) aktiválhatja. Egy előkészítés minden példánya rendelkezik egy példány-azonosítóval. A példány azonosítója lehet automatikusan generálható (ajánlott) vagy felhasználó által generált. A példány azonosítóját használhatja az előkészítési [példányok kezelésére](durable-functions-instance-management.md) .

További információért és Példákért lásd: előkészítési [Eseményindítók](durable-functions-bindings.md#orchestration-triggers).

###  <a name="entity-functions"></a>Entity functions (előzetes verzió)

Az Entity functions olyan műveleteket határoz meg, amelyek olyan kis méretű állapotok olvasására és frissítésére szolgálnak, amelyek *tartós entitások*. A Orchestrator függvényekhez hasonlóan az Entity functions is egy speciális trigger típussal, az *entitások triggerével*működik. A Orchestrator függvényektől eltérően az Entity functions nem rendelkezik konkrét kód megkötésekkel. Az Entity functions emellett explicit módon kezeli az állapotot, nem pedig implicit módon jelképezi az állapotot a vezérlési folyamaton keresztül.

> [!NOTE]
> Az Entity functions és a kapcsolódó funkciók csak Durable Functions 2,0-es és újabb verziókban érhetők el.

Az Entity functions szolgáltatással kapcsolatos további információkért tekintse meg az [Entity functions](durable-functions-preview.md#entity-functions) előzetes verziójának dokumentációját.

### <a name="client-functions"></a>Ügyfél-függvények

Az ügyfél-függvények olyan működést váltottak ki, amely az előkészítési és entitási példányok létrehozására és kezelésére szolgál. Ezek gyakorlatilag a belépési pont a Durable Functions való interakcióhoz. Bármelyik forrásból (HTTP, üzenetsor, esemény-adatfolyam stb.) aktiválhatja az ügyfél függvényét. Az ügyfél-függvény a hangszerelési [ügyfél kötését](durable-functions-bindings.md#orchestration-client) használja tartós Összehangolók és entitások létrehozásához és kezeléséhez.

Az ügyfél függvényének legalapvetőbb példája egy HTTP-triggert használó függvény, amely elindítja a Orchestrator függvényt, majd visszaadja az állapot-ellenőrzési választ. Példaként tekintse meg a [http API URL-címének felderítése](durable-functions-http-api.md#http-api-url-discovery)című témakört.

További információért és Példákért lásd: előkészítési [ügyfél](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Funkciók és minták

A következő szakaszok a Durable Functions típusok funkcióit és mintáit ismertetik.

### <a name="sub-orchestrations"></a>Alvezénylések

A Orchestrator függvények hívhatják a tevékenységek funkcióit, de más Orchestrator-függvényeket is meghívhatnak. Létrehozhat például egy nagyobb, a Orchestrator-függvények könyvtárainak összeszerelését. A Orchestrator függvény több példányát is futtathatja párhuzamosan.

További információért és Példákért lásd: alfolyamatok. [](durable-functions-sub-orchestrations.md)

### <a name="durable-timers"></a>Tartós időzítők

[](durable-functions-overview.md) A Durable functions *tartós időzítőket* biztosít, amelyek a Orchestrator függvényekben a késések megvalósításához, illetve az aszinkron műveletek időtúllépésének beállításához használhatók. Használjon tartós időzítőket a és `Thread.Sleep` `Task.Delay` a (C#) vagy `setTimeout()` `setInterval()` a (JavaScript) helyett a Orchestrator függvényekben.

További információért és Példákért lásd: [tartós időzítők](durable-functions-timers.md).

### <a name="external-events"></a>Külső események

A Orchestrator függvények megvárhatják, hogy külső események frissítsenek egy előkészítési példányt. Ez a Durable Functions funkció gyakran hasznos az emberi interakciók vagy más külső visszahívások kezeléséhez.

További információért és példákért tekintse meg a [külső eseményeket](durable-functions-external-events.md).

### <a name="error-handling"></a>Hibakezelés

Durable Functions-összeszerelések megvalósításához használjon kódot. Használhatja a programozási nyelv hiba-kezelési funkcióit. Mintázatok `try` , például / `catch` a munkához. 

A Durable Functions beépített újrapróbálkozási szabályzatokkal is rendelkezik. Egy művelet automatikusan késleltetheti és újrapróbálkozhat a tevékenységekkel, ha kivétel történik. Az újrapróbálkozások segítségével az átmeneti kivételeket az előkészítés megszakítása nélkül kezelheti.

További információért és Példákért lásd: [hibakezelés](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Többfunkciós alkalmazások közötti kommunikáció

Bár a tartós előkészítés egyetlen Function-alkalmazás kontextusában fut, mintákat használhat a különböző functions-alkalmazások összehangolása érdekében. Az alkalmazások közötti kommunikáció HTTP-n keresztül fordulhat elő, de az egyes tevékenységek tartós keretrendszere használatával továbbra is tartós folyamatokat tarthat fenn két alkalmazás között.

Az alábbi példák az alkalmazások és a C# JavaScript közötti többfunkciós előkészítést szemléltetik. Az egyes példákban az egyik tevékenység elindítja a külső kialakítást. Egy másik tevékenység beolvassa és visszaadja az állapotot. A Orchestrator megvárja, amíg az állapot folytatódni fog `Complete` .

Íme néhány példa az alkalmazások többfunkciós összevetésére:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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

Első lépésként hozza létre első tartós funkcióját a [C#](durable-functions-create-first-csharp.md) vagy a [JavaScript](quickstart-js-vscode.md)használatával.

> [!div class="nextstepaction"]
> [További információ a Durable Functionsról](durable-functions-bindings.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
