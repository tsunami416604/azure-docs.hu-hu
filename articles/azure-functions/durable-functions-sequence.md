---
title: Működik a tartós funkciókkal - Azure-láncolás
description: Megtudhatja, hogyan futtasson egy tartós funkciók mintát, amely a funkciók egy műveletsorozatot hajt végre.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: e53b38bf336816ca670fad3ab70a43e5cc8b3437
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>A tartós funkciók - Hello feladatütemezési minta láncolás működik

A minta a funkciók sorozatát végrehajtási meghatározott sorrendben függvény láncolás hivatkozik. A kimenet egy függvény gyakran kell másik függvény a bemeneti alkalmazható. Ez a cikk ismerteti egy használó mintaalkalmazás [tartós funkciók](durable-functions-overview.md) láncolás függvény végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse a tartós funkciók](durable-functions-install.md).

## <a name="the-functions"></a>A Funkciók

Ez a cikk ismerteti a mintaalkalmazást a következő funkciókat:

* `E1_HelloSequence`: Az orchestrator függvény, amely behívja `E1_SayHello` többször sorrendje egy sorozatban. A kimeneteinek tárol a `E1_SayHello` hívja, és a eredményeit rögzíti.
* `E1_SayHello`: Egy tevékenység függvény, amely lefoglalja a karakterlánc a "Hello"szövegrészt.

Az alábbi szakaszok ismertetik a konfiguráció és a C# a parancsfájlkezelést, és a JavaScript használt kódot. A Visual Studio fejlesztési kód a cikk végén meg.

> [!NOTE]
> Tartós funkciók érhető el a JavaScript csak a v2 funkciók futtatókörnyezetben.

## <a name="e1hellosequence"></a>E1_HelloSequence
### <a name="functionjson-file"></a>Function.JSON fájl

Ha Visual Studio Code vagy az Azure-portálon fejlesztési, itt van tartalma a *function.json* az orchestrator függvény fájlt. A legtöbb orchestrator *function.json* fájlok szinte teljesen így jelennek meg.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Fontos, a `orchestrationTrigger` kötéstípus. Minden orchestrator-funkciók az indítási típust kell használnia.

> [!WARNING]
> Elfogadják a "nincs i/o" szabály orchestrator funkciók, ne használja a bemeneti vagy kimeneti kötések használatakor a `orchestrationTrigger` indítás kötés.  Ha más bemeneti vagy kimeneti kötések van szükség, azok helyette használandó környezetében `activityTrigger` függvények, amelyek az orchestrator által meghívott.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C# parancsfájl (Visual Studio Code és az Azure portál mintakód) 

A forrás kód itt látható:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Minden C# vezénylési funkciók típusú paramétert kell `DurableOrchestrationContext`, amely megtalálható a `Microsoft.Azure.WebJobs.Extensions.DurableTask` szerelvény. C#-parancsfájlok használata, ha a szerelvény is használatával lehet hivatkozni a `#r` jelöléssel. A környezeti objektumot lehetővé teszi, hogy másik hívás *tevékenység* funkciók és pass bemeneti paraméterek használata a [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metódust.

A kód hívások `E1_SayHello` háromszor sorrendben különböző paraméterértékekkel. Minden hívás visszatérési értékének hozzáadódik a `outputs` lista, amelyet a függvény végén adja vissza.

### <a name="javascript"></a>Javascript

A forrás kód itt látható:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Minden JavaScript vezénylési funkciók tartalmaznia kell a `durable-functions` modul. Ez az a következőkből fordítja le a vezénylési függvény műveletek tartós meg végrehajtási protokoll folyamaton a nyelvek esetén a JavaScript-függvénytárat. Az orchestration függvény és más JavaScript-funkcióként között három jelentős különbség van:

1. A függvény egy [generátor függvény.](https://docs.microsoft.com/en-us/scripting/javascript/advanced/iterators-and-generators-javascript)
2. A funkció be van burkolva hívásakor a `durable-functions` modul (Itt `df`).
3. A függvény meghívásával karakterlánccal végződik-e `return`, nem `context.done`.

A `context` objektum tartalmaz egy `df` objektum lehetővé teszi, hogy másik hívás *tevékenység* funkciók és pass bemeneti paraméterek használata a `callActivityAsync` metódus. A kód hívások `E1_SayHello` háromszor sorrendben különböző paraméterértékekkel használatával `yield` annak jelzésére, hogy a végrehajtási megvárja-e a tevékenység aszinkron függvényhívások vissza kell adni. Minden hívás visszatérési értékének hozzáadódik a `outputs` lista, amelyet a függvény végén adja vissza.

## <a name="e1sayhello"></a>E1_SayHello
### <a name="functionjson-file"></a>Function.JSON fájl

A *function.json* fájl a tevékenység függvény `E1_SayHello` hasonló az `E1_HelloSequence` azzal a különbséggel, hogy használja az `activityTrigger` kötéstípus ahelyett, hogy egy `orchestrationTrigger` kötéstípus.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Az orchestration függvény által meghívott függvényeket kell használnia a `activityTrigger` kötés.

Végrehajtásának `E1_SayHello` egy viszonylag trivial művelet formázási karakterlánc.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Ez a függvény típusú paraméterrel rendelkezik [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), az orchestrator függvény által átadott bemeneti eléréséhez használt [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

Eltérően a JavaScript vezénylési JavaScript tevékenység függvényt kell semmilyen különleges beállítás. Az orchestrator függvény által átadott bemenetet található a `context.bindings` objektum neve alatt a `activitytrigger` kötés – ebben az esetben `context.bindings.name`. A kötés neve lehet beállítani az exportált függvény paramétereként és érhető el közvetlenül, amely példakód funkciója.

## <a name="run-the-sample"></a>Minta futtatása

Végrehajtásához a `E1_HelloSequence` vezénylési, a következő HTTP POST kérelem küldése.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

Például ha a minta egy "myfunctionapp" nevű funkció alkalmazást futtatja, cserélje le az "{állomás}" "myfunctionapp.azurewebsites.net".

Az eredménye egy HTTP 202 választ, ilyen (rövidített kivonatosan mutatja):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Ezen a ponton a vezénylési sorba van, és azonnal futtatni kezdődik. Az URL-címet a `Location` fejléc segítségével ellenőrizze a végrehajtási állapotát.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

A vezénylési állapotának eredménye. Fut, és gyorsan, elkészül, így azt a *befejezve* állapot választ adott a következőképpen néz ki (rövidített kivonatosan mutatja):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Ahogy látja, a `runtimeStatus` példány van *befejezve* és a `output` orchestrator függvény végrehajtása JSON-szerializált eredményét tartalmazza.

> [!NOTE]
> A HTTP POST-végpont, fut-e az orchestrator függvény meg van valósítva a mintaalkalmazást, mint a HTTP "HttpStart" nevű funkció. Eseményindító egyéb hasonló alapszintű logikát Megvalósíthat például `queueTrigger`, `eventHubTrigger`, vagy `timerTrigger`.

Tekintse meg a függvény végrehajtási naplót. A `E1_HelloSequence` függvény elindult, és több alkalommal leírt ismétlési hiba miatt befejeződött a [áttekintése](durable-functions-overview.md). Másrészről, történtek, csak három végrehajtások `E1_SayHello` mivel ezen függvény végrehajtások visszajátszani nem beolvasása.

## <a name="visual-studio-sample-code"></a>A Visual Studio mintakód

Íme egy egyetlen C# fájlban található, a Visual Studio-projekt vezénylési:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>További lépések

Ez a példa azt mutatják, egy egyszerű függvény-láncolás vezénylési. A következő példa bemutatja, hogyan fan-kimenő/fan-a minta végrehajtásához. 

> [!div class="nextstepaction"]
> [A Fan-kimenő/fan-a minta futtatásához](durable-functions-cloud-backup.md)
