---
title: Funkcióláncolás a tartós függvényekben - Azure
description: Ismerje meg, hogyan futtathat olyan tartós függvények mintát, amelyek függvénysorozatokat hajtanak végre.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562061"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Funkcióláncolás a tartós függvényekben – Hello szekvenciaminta

A függvényláncolás egy adott sorrendben futó függvénysorozat mintájára utal. Gyakran az egyik függvény kimenetét kell alkalmazni egy másik függvény bemenetére. Ez a cikk a Tartós függvények rövid útmutatójának[(C#](durable-functions-create-first-csharp.md) vagy [JavaScript)](quickstart-js-vscode.md)befejezésekor létrehozott láncolási sorozatot ismerteti. A tartós funkciókról a [Tartós függvények – áttekintés című témakörben olvashat bővebben.](durable-functions-overview.md)

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>A funkciók

Ez a cikk a mintaalkalmazás következő funkcióit ismerteti:

* `E1_HelloSequence`: Olyan [orchestrator függvény,](durable-functions-bindings.md#orchestration-trigger) amely egymás után többször is hív. `E1_SayHello` Ez tárolja a kimeneteket a `E1_SayHello` hívások és rögzíti az eredményeket.
* `E1_SayHello`: Olyan [tevékenységfüggvény,](durable-functions-bindings.md#activity-trigger) amely egy karakterláncot "Hello" karakterrel készít elő.
* `HttpStart`: Egy HTTP-aktivált függvény, amely elindítja az orchestrator egy példányát.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence orchestrator függvény

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Minden C# vezénylési `DurableOrchestrationContext`függvénynek típusú `Microsoft.Azure.WebJobs.Extensions.DurableTask` paraméterrel kell rendelkeznie, amely a szerelvényben létezik. Ez a környezeti objektum lehetővé teszi, hogy `CallActivityAsync` más *tevékenységfüggvényeket* hívjon meg, és adja át a bemeneti paramétereket a metódusával.

A kód `E1_SayHello` háromszor hív egymás után különböző paraméterértékekkel. Az egyes hívatok visszatérési értéke hozzáadódik a `outputs` függvény végén visszaadott listához.

# <a name="javascript"></a>[Javascript](#tab/javascript)

> [!NOTE]
> A JavaScript durable functions csak a Functions 2.0 futásidejűekhez érhetők el.

#### <a name="functionjson"></a>function.json

Ha a Visual Studio Code vagy az Azure Portal fejlesztéshez, itt a *function.json* fájl tartalmát az orchestrator függvény. A legtöbb orchestrator *function.json* fájlok at majdnem pontosan így néz ki.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

A lényeg a `orchestrationTrigger` kötés típusa. Minden orchestrator függvénynek ezt az eseményindító típust kell használnia.

> [!WARNING]
> Az orchestrator-függvények "nincs I/O" szabályának betartására, ne használjon bemeneti vagy kimeneti kötéseket az `orchestrationTrigger` eseményindító-kötés használatakor.  Ha más bemeneti vagy kimeneti kötésekre van szükség, `activityTrigger` azokat az orchestrator által megnevezett függvények környezetében kell használni. További információkért tekintse meg az [orchestrator függvénykód megkötések](durable-functions-code-constraints.md) cikket.

#### <a name="indexjs"></a>index.js

Itt van a funkció:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Minden JavaScript vezénylési függvénynek tartalmaznia kell a [ `durable-functions` modult.](https://www.npmjs.com/package/durable-functions) Ez egy könyvtár, amely lehetővé teszi, hogy írjon tartós funkciók JavaScript. Három jelentős különbség van egy vezénylési függvény és más JavaScript-függvények között:

1. A funkció egy [generátor funkció.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript). .
2. A függvény a `durable-functions` modul metódusának `orchestrator` hívásába van `df`csomagolva (itt).
3. A függvénynek szinkronnak kell lennie. Mivel az "orchestrator" metódus kezeli a "context.done" metódus hívását, a függvénynek egyszerűen "return"-nek kell lennie.

Az `context` objektum `df` egy tartós vezénylési környezetobjektumot tartalmaz, `callActivity` amely lehetővé teszi más *tevékenységfüggvények* hívását és bemeneti paraméterek átadását a metódushasználatával. A kód `E1_SayHello` háromszor egymás után, különböző `yield` paraméterértékekkel, a végrehajtás jelzésére a dinkodisa tevékenység függvényhívásait jelzi. Az egyes hívak visszatérési `outputs` értéke hozzáadódik a tömbhöz, amely a függvény végén kerül visszaadásra.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello tevékenység függvénye

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

A tevékenységek `ActivityTrigger` az attribútumot használják. A megadott `IDurableActivityContext` a tevékenységhez kapcsolódó műveletek végrehajtásához, `GetInput<T>`például a bemeneti érték eléréséhez a használatával.

`E1_SayHello` Végrehajtása egy viszonylag triviális string formázási művelet.

A kötés `IDurableActivityContext`helyett közvetlenül kötődhet a tevékenységfüggvénybe átadott típushoz. Példa:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.json

A `E1_SayHello` *tevékenységfüggvény function.json* fájlja hasonló `E1_HelloSequence` a függvényéhez, `activityTrigger` azzal a `orchestrationTrigger` különbséggel, hogy kötéstípus helyett kötéstípust használ.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> A vezénylési függvény `activityTrigger` által hívott függvénynek a kötést kell használnia.

`E1_SayHello` Végrehajtása egy viszonylag triviális string formázási művelet.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

A JavaScript vezénylési függvényektől eltérően egy tevékenységfüggvénynek nincs szüksége speciális beállításokra. Az orchestrator függvény által átadott bemenet `context.bindings` az objektumon található `activityTrigger` a kötés neve `context.bindings.name`alatt - ebben az esetben . A kötésnév az exportált függvény paramétereként állítható be, és közvetlenül elérhető, ami a mintakód nak is felel meg.

---

### <a name="httpstart-client-function"></a>HttpStart ügyfélfüggvény

Az orchestrator függvény egy ügyfélfüggvény használatával elindíthatja az orchestrator függvény példányát. A `HttpStart` HTTP által aktivált függvényt fogja `E1_HelloSequence`használni a példányainak indításához.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Az orchestrators együttműködéséhez a függvénynek tartalmaznia kell egy bemeneti kötést. `DurableClient` Az ügyfél segítségével vezénylési. Segíthet egy HTTP-választ is visszaadni, amely URL-címeket tartalmaz az új vezénylés állapotának ellenőrzéséhez.

# <a name="javascript"></a>[Javascript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Az orchestrators együttműködéséhez a függvénynek tartalmaznia kell egy bemeneti kötést. `durableClient`

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Objektum `df.getClient` beszerzésére `DurableOrchestrationClient` használható. Az ügyfél segítségével vezénylési. Segíthet egy HTTP-választ is visszaadni, amely URL-címeket tartalmaz az új vezénylés állapotának ellenőrzéséhez.

---

## <a name="run-the-sample"></a>Minta futtatása

A `E1_HelloSequence` vezénylés végrehajtásához küldje `HttpStart` el a következő HTTP POST-kérelmet a függvénynek.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> Az előző HTTP-kódrészlet feltételezi, hogy `host.json` van egy bejegyzés `api/` a fájlban, amely eltávolítja az alapértelmezett előtagot az összes HTTP trigger függvény URL-címéről. Ennek a konfigurációnak a jelölését `host.json` a mintákban lévő fájlban találja.

Ha például a mintát egy "myfunctionapp" nevű függvényalkalmazásban futtatja, cserélje le a(z) "{host}" helyett a "myfunctionapp.azurewebsites.net".

Az eredmény egy HTTP 202 válasz, mint ez (vágott rövidség):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Ezen a ponton a vezénylési várólistára kerül, és azonnal elindul. A fejlécben `Location` lévő URL-cím a végrehajtás állapotának ellenőrzésére használható.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Az eredmény a vezénylés állapota. Ez fut, és gyorsan befejeződik, így látod, hogy a *befejezett* állapotban a válasz, hogy néz ki, mint ez (díszítve a rövidség):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Mint látható, a `runtimeStatus` példány *befejeződött,* `output` és a tartalmazza a JSON-szerializált eredménye az orchestrator függvény végrehajtása.

> [!NOTE]
> Hasonló kezdőlogikát valósíthat meg más `queueTrigger` `eventHubTrigger`eseményindítótípusokhoz is, például a , vagy `timerTrigger`a hoz.

Tekintse meg a függvény végrehajtási naplók. A `E1_HelloSequence` függvény a [vezénylési megbízhatósági](durable-functions-orchestrations.md#reliability) témakörben leírt visszajátszási viselkedés miatt többször indult el és fejeződött be. Másrészt, már csak három kivégzések, `E1_SayHello` mivel ezek a függvény kivégzések nem kap visszajátszott.

## <a name="next-steps"></a>További lépések

Ez a minta egy egyszerű függvényláncolási vezénylési. A következő minta bemutatja, hogyan valósítható meg a fan-out / fan-in minta.

> [!div class="nextstepaction"]
> [Futtassa a fan-out/fan-in minta](durable-functions-cloud-backup.md)
