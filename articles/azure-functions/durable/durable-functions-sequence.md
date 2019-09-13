---
title: Függvények láncolása Durable Functionsban – Azure
description: Megtudhatja, hogyan futtathat egy olyan Durable Functions mintát, amely függvények sorozatát hajtja végre.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ee5b18ddc734335ddac2a7d3352de0e4388f445d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933259"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Függvények láncolása Durable Functions-Hello Sequence minta

A függvények láncolása egy adott sorrendben végrehajtott függvények sorrendjének a mintáját jelöli. Egy függvény kimenetét gyakran egy másik függvény bemenetére kell alkalmazni. Ez a cikk a Durable Functions rövid útmutató ([C#](durable-functions-create-first-csharp.md) vagy [JavaScript](quickstart-js-vscode.md)) befejezése után létrehozott láncolási sorozatot ismerteti. További információ a Durable Functionsről: [Durable functions áttekintése](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E1_HelloSequence`: Egy Orchestrator függvény, amely `E1_SayHello` többször is meghívja a sorozatot. Tárolja a `E1_SayHello` hívások kimeneteit, és rögzíti az eredményeket.
* `E1_SayHello`: Egy tevékenység-függvény, amely paraméterként megadott egy "Hello" karakterláncot.

A következő szakaszokban ismertetjük a C# parancsfájlok és a JavaScriptek konfigurációját és kódját. A Visual Studio-fejlesztés kódja a cikk végén látható.

> [!NOTE]
> A JavaScript-Durable Functions csak a 2. x futtatókörnyezethez érhető el.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>function. JSON fájl

Ha a Visual Studio Code-ot vagy a Azure Portalt használja a fejlesztéshez, itt látható a *function. JSON* fájl tartalma a Orchestrator függvényhez. A legtöbb Orchestrator *függvény. a JSON* -fájlok majdnem ugyanúgy néznek ki, mint ez.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

A lényeg `orchestrationTrigger` a kötés típusa. Az összes Orchestrator függvénynek ezt az trigger-típust kell használnia.

> [!WARNING]
> Az Orchestrator függvények "nincs I/O" szabályának betartásához ne használjon semmilyen bemeneti vagy kimeneti kötést az `orchestrationTrigger` trigger kötésének használatakor.  Ha más bemeneti vagy kimeneti kötésekre van szükség, azokat a `activityTrigger` függvények kontextusában kell használni, amelyeket a Orchestrator hívnak.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C#parancsfájl (Visual Studio Code és Azure Portal mintakód)

Itt látható a forráskód:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Minden C# előkészítési függvénynek rendelkeznie kell egy típusú `DurableOrchestrationContext`paraméterrel, amely létezik a `Microsoft.Azure.WebJobs.Extensions.DurableTask` szerelvényben. Ha parancsfájlt használ C# , a szerelvény hivatkozhat a `#r` jelölés használatával. Ez a környezeti objektum lehetővé teszi más *tevékenységi* függvények meghívását és a bemeneti paraméterek átadását a saját [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metódusának használatával.

A kód háromszor `E1_SayHello` meghívja a különböző paraméterek értékeit. Az egyes hívások visszatérési értéke hozzáadódik a `outputs` listához, amelyet a függvény végén adnak vissza.

### <a name="javascript"></a>Javascript

Itt látható a forráskód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Minden JavaScript-előkészítési függvénynek tartalmaznia kell a [ `durable-functions` modult](https://www.npmjs.com/package/durable-functions). Ez egy olyan könyvtár, amely lehetővé teszi Durable Functions a JavaScriptben való írását. Három jelentős különbség van egy összehangoló függvény és más JavaScript-függvények között:

1. A függvény egy [Generator függvény.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. A függvény a `durable-functions` `orchestrator` modul metódusának (itt `df`) hívásával van becsomagolva.
3. A függvénynek szinkronnak kell lennie. Mivel a "Orchestrator" metódus kezeli a "Context. Done" hívását, a függvénynek egyszerűen "Return" értéknek kell lennie.

Az `context` objektum tartalmaz egy `df` objektumot, amely lehetővé teszi más *tevékenységi* függvények meghívását és `callActivity` a bemeneti paraméterek átadását a metódusának használatával. A kód háromszor `E1_SayHello` meghívja a különböző paraméterek értékeit `yield` , ezzel jelezve, hogy a végrehajtásnak meg kell várnia a visszaadott aszinkron tevékenység függvényének hívását. Az egyes hívások visszatérési értéke hozzáadódik a `outputs` listához, amelyet a függvény végén adnak vissza.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>function. JSON fájl

A *function. JSON* fájl a Activity függvényhez `E1_SayHello` hasonló, azzal a `E1_HelloSequence` kivétellel, hogy `orchestrationTrigger` kötési típus helyett `activityTrigger` kötési típust használ.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> A hanghívási függvény által hívott függvénynek a `activityTrigger` kötést kell használnia.

A megvalósítása `E1_SayHello` viszonylag triviális karakterlánc-formázási művelet.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Ez a függvény egy [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html)típusú paramétert tartalmaz, amelyet a Orchestrator függvény [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_)által a által átadott bemenet beszerzéséhez használ.

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

A JavaScript-figyelő függvényektől eltérően a tevékenység-függvények nem igényelnek speciális beállítást. A Orchestrator függvény által átadott bemenet a `context.bindings` `activityTrigger` kötés neve alatt található objektumon szerepel `context.bindings.name`– ebben az esetben. A kötési név beállítható az exportált függvény paramétereként, és közvetlenül is elérhető, ami a mintakód.

## <a name="run-the-sample"></a>Minta futtatása

Az `E1_HelloSequence` előkészítés végrehajtásához küldje el a következő http post-kérelmet.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Az előző http-kódrészlet feltételezi, hogy van egy bejegyzés `host.json` a fájlban, amely eltávolítja `api/` az alapértelmezett előtagot a http-trigger összes funkciójának URL-címéről. A konfigurációhoz `host.json` tartozó jelölést a mintákban található fájlban találja.

Ha például egy "myfunctionapp" nevű Function alkalmazásban futtatja a mintát, cserélje le a "{host}" kifejezést "myfunctionapp.azurewebsites.net" értékre.

Az eredmény egy HTTP 202-válasz, amely a következőhöz hasonlóan van (rövidítve):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Ezen a ponton a rendszer várólistára helyezi az előkészítést, és azonnal elindul. A `Location` fejlécben található URL-cím segítségével ellenőrizhető a végrehajtás állapota.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Az eredmény az előkészítés állapota. A rendszer gyorsan futtatja és befejezi, így a *befejezett* állapotú, a következőhöz hasonló választ kell látnia (rövidítve):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Amint láthatja, a példány `runtimeStatus` `output` *befejeződik* , és a tartalmazza a Orchestrator függvény végrehajtásának JSON-szerializált eredményét.

> [!NOTE]
> A Orchestrator függvényt elindító HTTP POST-végpont a "HttpStart" nevű HTTP-trigger függvényként lett implementálva a minta alkalmazásban. Hasonló indító logikát alkalmazhat más típusú triggerekhez, `queueTrigger` `eventHubTrigger`például, vagy `timerTrigger`.

Tekintse meg a függvény-végrehajtási naplókat. A függvény többször indult el és fejeződött be, mert a folyamat megbízhatósága című témakörben leírt újrajátszás viselkedése történt. [](durable-functions-orchestrations.md#reliability) `E1_HelloSequence` Másfelől azonban csak három végrehajtás `E1_SayHello` történt, mivel ezek a függvények végrehajtása nem kerül újra lejátszásra.

## <a name="visual-studio-sample-code"></a>Visual Studio-mintakód

Íme egy Visual Studio-projekt egyetlen C# fájlja:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>További lépések

Ez a minta egy egyszerű függvény-láncolási előkészítést mutat be. A következő minta bemutatja, hogyan valósítható meg a ventilátor-out/Fan-in minta.

> [!div class="nextstepaction"]
> [A fan-out/Fan-in minta futtatása](durable-functions-cloud-backup.md)
