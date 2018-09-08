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
ms.date: 07/04/2018
ms.author: azfuncdf
ms.openlocfilehash: 24889765369a2af7d87ddb0fdc8f7c9f91ac66fd
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091391"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Függvény típusa és a tartós függvények (az Azure Functions) szolgáltatások áttekintése

Durable Functions az Azure állapot-nyilvántartó hangolja össze a függvény végrehajtási.  Tartós függvények a különböző Azure Functions áll megoldás.  Ezek a függvények mindegyike egy vezénylési részeként különböző szerepkörök játszhatja le.  A következő dokumentum a különböző típusú részt vesz egy tartós függvény vezénylési funkciók áttekintése.  Gyakori minták a functions összekapcsolása is tartalmaz.  

![Durable functions típusai][1]  

## <a name="types-of-functions"></a>Függvények típusai

### <a name="activity-functions"></a>Tevékenységfüggvényeket

Tevékenységfüggvényeket olyan tartós vezénylési alapvető munkaegysége.  Tevékenységfüggvényeket funkcióit és a folyamat alatt vezényelt feladatait.  Például előfordulhat, hogy létrehoz egy tartós függvényt, amely egy megrendelés feldolgozásához – ellenőrizze a készlet díja szerint számítjuk fel az ügyfél, és hozzon létre egy szállításáról.  Ezeket a feladatokat mindegyike lenne egy tevékenység függvényt.  Tevékenységfüggvényeket korlátozások nem szerepel bennük teheti munka típusától.  Az Azure Functions támogatja, bármilyen nyelven írhatók.  A tartós feladat keretrendszer garantálja, hogy minden tevékenység hívott függvény egy vezénylési során legalább egyszer hajtható végre.

Egy tevékenység függvényt kell elindítható a egy [tevékenység eseményindító](durable-functions-bindings.md#activity-triggers).  Ez a függvény kap egy [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) paraméterként. Az eseményindító átadni a függvény bemenetei között szereplő bármely más objektumot is kötni.  A tevékenység függvényt is térjen vissza az orchestrator értékeket adhat vissza.  Ha a küldő vagy sok érték visszaadása egy tevékenység-függvényt, [rekordokat tartalmazó vagy tömbök](durable-functions-bindings.md#passing-multiple-parameters).  Tevékenységfüggvényeket csak indítható el egy vezénylési-példányból.  Bár egy kódrészletet egy tevékenységgel és egy másik függvény (például egy HTTP által aktivált függvény) között megosztható, minden egyes függvény legfeljebb egy trigger.

További információt és példákat találhat a [Durable Functions kötés cikk](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Az orchestrator-funkciók

Az orchestrator függvények olyan tartós függvények legfontosabb céljait segítik.  Az orchestrator-funkciók a módja és a végrehajtás sorrendje leírása.  Az orchestrator-funkciók leírása a vezénylési kód (C# vagy JavaScript), ahogyan a [durable functions áttekintése](durable-functions-overview.md).  Egy vezénylési számos különböző típusú műveletek, például rendelkezhet [Tevékenységfüggvényeket](#activity-functions), [alárendelt vezénylések](#sub-orchestrations), [külső események Várakozás](#external-events), és [ időzítők](#durable-timers).  

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

Egy példa egy C#-ban több függvényalkalmazás vezénylési lejjebb találja.  Egy tevékenység külső vezénylési indul el. Egy másik tevékenység majd betölti és visszaállíthatja az állapotot.  Az orchestrator várakozik az állapot kész a folytatás előtt.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Olvassa Durable Functions – dokumentáció](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Telepítse a Durable Functions bővítmény és a minták](durable-functions-install.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png
