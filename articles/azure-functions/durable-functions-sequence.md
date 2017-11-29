---
title: "Működik a tartós funkciókkal - Azure-láncolás"
description: "Megtudhatja, hogyan futtasson egy tartós funkciók mintát, amely a funkciók egy műveletsorozatot hajt végre."
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
ms.openlocfilehash: 0973f83ae839597f3b499814a4a04a8a640a1fb6
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>A tartós funkciók - Hello feladatütemezési minta láncolás működik

A minta a funkciók sorozatát végrehajtási meghatározott sorrendben függvény láncolás hivatkozik. A kimenet egy függvény gyakran kell másik függvény a bemeneti alkalmazható. Ez a cikk ismerteti egy használó mintaalkalmazás [tartós funkciók](durable-functions-overview.md) láncolás függvény végrehajtásához.

## <a name="prerequisites"></a>Előfeltételek

* Kövesse az utasításokat a [tartós funkciók telepítése](durable-functions-install.md) a minta beállítása.

## <a name="the-functions"></a>A Funkciók

Ez a cikk ismerteti a mintaalkalmazást a következő funkciókat:

* `E1_HelloSequence`: Az orchestrator függvény, amely behívja `E1_SayHello` többször sorrendje egy sorozatban. A kimeneteinek tárol a `E1_SayHello` hívja, és a eredményeit rögzíti.
* `E1_SayHello`: Egy tevékenység függvény, amely lefoglalja a karakterlánc a "Hello"szövegrészt.

Az alábbi szakaszok ismertetik a konfiguráció és a kódot, amely az Azure portál fejlesztésére szolgálnak. A Visual Studio fejlesztési kód a cikk végén meg.
 
## <a name="functionjson-file"></a>Function.JSON fájl

Fejlesztés az Azure-portálon használja, ha ez tartalmát a *function.json* az orchestrator függvény fájlt. A legtöbb orchestrator *function.json* fájlok szinte teljesen így jelennek meg.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Fontos, a `orchestrationTrigger` kötéstípus. Minden orchestrator-funkciók az indítási típust kell használnia.

> [!WARNING]
> Elfogadják a "nincs i/o" szabály orchestrator funkciók, ne használja a bemeneti vagy kimeneti kötések használatakor a `orchestrationTrigger` indítás kötés.  Ha más bemeneti vagy kimeneti kötések van szükség, azok helyette használandó környezetében `activityTrigger` függvények, amelyek az orchestrator által meghívott.

## <a name="c-script"></a>C#-szkript

A forrás kód itt látható:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Minden C# vezénylési funkciók típusú paramétert kell `DurableOrchestrationContext`, amely megtalálható a `Microsoft.Azure.WebJobs.Extensions.DurableTask` szerelvény. C#-parancsfájlok használata, ha a szerelvény is használatával lehet hivatkozni a `#r` jelöléssel. A környezeti objektumot lehetővé teszi, hogy másik hívás *tevékenység* funkciók és pass bemeneti paraméterek használata a [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) metódust.

A kód hívások `E1_SayHello` háromszor sorrendben különböző paraméterértékekkel. Minden hívás visszatérési értékének hozzáadódik a `outputs` lista, amelyet a függvény végén adja vissza.

A *function.json* fájl a tevékenység függvény `E1_SayHello` hasonló az `E1_HelloSequence` azzal a különbséggel, hogy használja az `activityTrigger` kötéstípus ahelyett, hogy egy `orchestrationTrigger` kötéstípus.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Az orchestration függvény által meghívott függvényeket kell használnia a `activityTrigger` kötés.

Végrehajtásának `E1_SayHello` egy viszonylag trivial művelet formázási karakterlánc.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Ez a függvény típusú paraméterrel rendelkezik [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), az orchestrator függvény által átadott bemeneti eléréséhez használt [ `CallActivityAsync<T>` ](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

## <a name="run-the-sample"></a>A minta futtatásához

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

## <a name="next-steps"></a>Következő lépések

Ez a példa azt mutatják, egy egyszerű függvény-láncolás vezénylési. A következő példa bemutatja, hogyan fan-kimenő/fan-a minta végrehajtásához. 

> [!div class="nextstepaction"]
> [A Fan-kimenő/fan-a minta futtatásához](durable-functions-cloud-backup.md)
