---
title: Durable Functions - Azure-láncolás függvény
description: Ismerje meg, hogyan futtathat egy Durable Functions-minta, amely végrehajtja a funkciók sorozatát.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: azfuncdf
ms.openlocfilehash: c84977dacddcf9ccca7fde735ad4acb8a1523fa9
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378703"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Durable Functions - Hello feladatütemezési minta a láncolási függvény

Függvény-láncolás a minta egy adott sorrendben hajtsa végre a funkciók sorozatát jelenti. A kimenet egy függvény gyakran kell egy másik függvény a bemeneti alkalmazható. Ez a cikk azt ismerteti, használó minta [Durable Functions](durable-functions-overview.md) láncolási függvény végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* [Durable Functions telepítése](durable-functions-install.md).

## <a name="the-functions"></a>Az funkciók

Ez a cikk ismerteti a mintaalkalmazást a következő funkciókat:

* `E1_HelloSequence`: Egy orchestrator-függvény, amely meghívja a `E1_SayHello` többször sorrendben. A kimenő adatait tárolja a `E1_SayHello` hívások, valamint rögzíti az eredményeket.
* `E1_SayHello`: Egy tevékenység-függvény, amely lefoglalja a "Hello" karakterlánc.

Az alábbi szakaszok ismertetik a konfiguráció és a C# a parancsfájlkezelést, és a JavaScript által használt kódot. A Visual Studio fejlesztési kód a cikk végén található meg.

> [!NOTE]
> Tartós függvények a v2 Functions futtatókörnyezete csak a JavaScript érhető el.

## <a name="e1hellosequence"></a>E1_HelloSequence
### <a name="functionjson-file"></a>Function.JSON fájlt

Ha Visual Studio Code vagy az Azure portal-fejlesztéshez, Íme a tartalmát a *function.json* az orchestrator függvény fájlt. A legtöbb orchestrator *function.json* fájlokat, így szinte teljesen jelennek meg.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

A lényeg az, a `orchestrationTrigger` typ vazby. Orchestrator összes függvényt kell használnia a trigger típusa.

> [!WARNING]
> Betartja a orchestrator funkciók a "nincs i/o" szabályt, nem használja minden olyan bemeneti vagy kimeneti kötés használatakor a `orchestrationTrigger` kötés aktiválásához.  Ha más bemeneti vagy kimeneti kötések van szükség, kell helyette használni őket kontextusában `activityTrigger` függvények, amelyek az orchestrator által meghívott.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#-szkript (Visual Studio Code és az Azure portal minta kód) 

A következő kódot:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

C# vezénylés a függvények rendelkeznie kell egy paraméter típusa `DurableOrchestrationContext`, amely megtalálható a `Microsoft.Azure.WebJobs.Extensions.DurableTask` sestavení. Ha a C#-szkript használata esetén a szerelvény lehet rá hivatkozni használatával a `#r` jelöléssel. A környezeti objektumon teszi lehetővé más hívás *tevékenység* függvényeket, és adja át a bemeneti paramétereket használja a [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metódus.

A kód meghívja `E1_SayHello` háromszor sorrendben exportálásánál különböző paraméterértékekkel. A visszaadott érték az egyes hívások adnak hozzá a `outputs` listát, amely a függvény végén adja vissza.

### <a name="javascript"></a>Javascript

A következő kódot:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

JavaScript a vezénylési függvények tartalmaznia kell a `durable-functions` modul. Ez az egy JavaScript-függvénytárat, amely lefordítja a vezénylési függvény műveletek a tartós végrehajtás protokoll folyamaton kívüli nyelv be. Van egy vezénylési függvényt és egyéb JavaScript-függvények közötti három jelentős különbség:

1. A függvény egy [generátor függvény.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. A függvény hívása van csomagolva az `durable-functions` modul (Itt `df`).
3. A függvény meghívásával ér véget `return`, nem `context.done`.

A `context` az objektum tartalmaz egy `df` objektum teszi lehetővé más hívás *tevékenység* függvényeket, és adja át a bemeneti paramétereket használja a `callActivityAsync` metódus. A kód meghívja `E1_SayHello` háromszor sorrendben exportálásánál különböző paraméterértékekkel használatával `yield` jelzi a végrehajtás a tevékenység aszinkron függvényhívások vissza kell várnia kell. A visszaadott érték az egyes hívások adnak hozzá a `outputs` listát, amely a függvény végén adja vissza.

## <a name="e1sayhello"></a>E1_SayHello
### <a name="functionjson-file"></a>Function.JSON fájlt

A *function.json* fájl a tevékenység függvény `E1_SayHello` a hasonló `E1_HelloSequence` azzal a különbséggel, hogy használja egy `activityTrigger` kötéstípus helyett egy `orchestrationTrigger` typ vazby.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Egy vezénylési függvény által meghívott függvényeket kell használnia a `activityTrigger` kötést.

Végrehajtásának `E1_SayHello` formázási művelet viszonylag triviális karakterlánc.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Ez a függvény típusú paraméterrel rendelkezik [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), amellyel az orchestrator-függvény hívása által átadott bemeneteként [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

JavaScript-vezénylési függvény eltérően tevékenység JavaScript-függvény kell semmilyen speciális beállítást. A bemeneti az orchestrator függvény által átadott található a `context.bindings` objektum neve alatt található a `activitytrigger` kötés – ebben az esetben `context.bindings.name`. A kötés neve lehet állítja be az exportált függvény egy paramétert és érhető el közvetlenül, amely a mintakód leírása.

## <a name="run-the-sample"></a>Minta futtatása

Végrehajtásához a `E1_HelloSequence` vezénylés, a következő HTTP POST kérés küldése.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Az előző HTTP a kódtöredék azt feltételezi, hogy a bejegyzés a `host.json` fájlt, amely eltávolítja az alapértelmezett `api/` előtag a HTTP eseményindító függvény URL-címekhez. Ezt a konfigurációt a jelölőnyelvi megtalálja a `host.json` a mintákban található fájl.

Például ha a minta egy függvényalkalmazáshoz "myfunctionapp" nevű rendszert használ, cserélje le "{host}" "myfunctionapp.azurewebsites.net".

Az eredmény egy HTTP-202 választ (az áttekinthetőség vágott) ehhez hasonló lesz:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Ezen a ponton a vezénylési várólistára van, és azonnal futni kezd. Az URL-címet a `Location` fejlécet is használható a végrehajtási állapotának ellenőrzéséhez.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Ez a vezénylési állapotát. Fut, és gyorsan, befejeződik, így látható, a *befejezve* állapot, a válasz a következőhöz hasonló (az áttekinthetőség vágott):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Amint láthatja, a `runtimeStatus` , de a példány *befejezve* és a `output` orchestrator függvény végrehajtása JSON-szerializált eredményét tartalmazza.

> [!NOTE]
> A HTTP POST-végpontot, amely az orchestrator függvény lépések implementálva van a mintaalkalmazást, mint a HTTP-trigger "HttpStart" nevű függvény. Megvalósíthat például minden olyan eseményindító esetében hasonló kezdő szintű logikai `queueTrigger`, `eventHubTrigger`, vagy `timerTrigger`.

Tekintse meg a függvény végrehajtási naplókat. A `E1_HelloSequence` függvény elindult, és többször befejeződött leírt visszajátszását hiba miatt a [áttekintése](durable-functions-overview.md). Másrészről, volt, csak három végrehajtások `E1_SayHello` ezeket függvénykivételek visszajátszani nem lekérése óta.

## <a name="visual-studio-sample-code"></a>A Visual Studio-mintakód

Az orchestration-fájlként egyetlen C# Visual Studio-projektet a következő:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>További lépések

Ez a minta egy egyszerű függvény-láncolás vezénylési bemutatta. A következő minta bemutatja a fan-kimenő/fan-a minta megvalósítása. 

> [!div class="nextstepaction"]
> [Fan-kimenő/fan-a minta futtatása](durable-functions-cloud-backup.md)
