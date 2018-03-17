---
title: "Állapot-nyilvántartó singletons tartós funkciókkal – Azure"
description: "Útmutató az Azure Functions a tartós funkciók bővítmény egypéldányos állapot-nyilvántartó valósít meg."
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
ms.openlocfilehash: 46cdd8523117e1100e7ce2a29ade9eb2dc0afe75
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Állapot-nyilvántartó singletons tartós funkciókkal - számláló minta

> [!NOTE]
> Ez a minta azt vannak újraírását. A kódrészleteket el lettek távolítva, és az új mintát új cikkek váltják fel őket.

Állapot-nyilvántartó singletons függvényeket a hosszan futó (vélhetően eternal) az orchestrator-állapot tárolása és meghívott és egyéb funkciók kellettek. Állapot-nyilvántartó singletons hasonlóak a [szereplő modell](https://en.wikipedia.org/wiki/Actor_model) elosztott számítástechnikai.

Nem a megfelelő "szereplő" megvalósítása során az orchestrator funkciók rendelkezik futásidejű azonos jellemzőkkel számos. Például azok állapotalapú, megbízható, egyszálas, hely-átlátható és globálisan címezhető. Ezek a jellemzőit, amelyek valós szereplő megvalósítások különösen akkor hasznos, de egy különálló keretrendszer szükségessége nélkül.

Ez a cikk bemutatja, hogyan futtathatók a *számláló* minta. A példa bemutatja egy egypéldányú objektum, amely támogatja a *növekmény* és *csökkentheti* műveletek, és ennek megfelelően frissíti a belső állapotot.

## <a name="prerequisites"></a>Előfeltételek

* Kövesse az utasításokat a [tartós funkciók telepítése](durable-functions-install.md) a minta beállítása.
* Ez a cikk feltételezi, hogy már lezajlott a [Hello feladatütemezési](durable-functions-sequence.md) minta forgatókönyv.

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

A számláló forgatókönyv, érdekes nehéz rendszeres állapotmentes függvények használatával végrehajtásához. Egyik fő kihívása rendelkezik kezel **egyidejűségi**. Műveletek, például *növekmény* és *csökkentheti* atomi van szüksége, vagy más lehetnek versenyhelyzetek igénylő műveletek felülírásához egymással.

Egy lehetőség használatával egy virtuális a teljesítményszámláló-adatok tárolásához, azonban ez költséges, kezelése **megbízhatóság** feladat lehet, mivel egy virtuális rendszeresen újra sikerült indítani. A szinkronizálás másik megoldásként használhat egy elosztott platform eszközök, például blob címbérleteket párhuzamossági kezeléséhez, de az azzal, hogy nagy mennyiségű **összetettsége**.

Tartós funkciók lehetővé teszi az ilyen forgatókönyv trivial megvalósítani, mert a vezénylési példányai vannak affinitás alapján egy virtuális és az orchestrator függvény végrehajtása van mindig egyszálas. Nem csak az, hogy, de ezek hosszan futó, állapotalapú, és reagálni külső események. Az alábbi mintakód bemutatja, hogyan valósítja meg a számláló, hosszan futó orchestrator függvényében.

## <a name="the-sample-function"></a>A sample függvény

Ez a cikk végigvezeti a **E3_Counter** mintaalkalmazás függvényt.



## <a name="the-counter-orchestration"></a>A számláló az orchestration

Az alábbi szakaszok ismertetik a kódot, amely a Visual Studio Code és az Azure portál fejlesztésére szolgál.

### <a name="c-script"></a>C# parancsfájl

A function.json fájlt:

<!-- [!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)] -->

A run.csx fájlt:

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)] -->

### <a name="precompiled-c"></a>Lefordított C# 

Az alábbi szakaszok ismertetik a Visual Studio fejlesztői használt kódot.

A kód, amely megvalósítja az orchestrator függvény itt látható:

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)] -->

### <a name="explanation-of-the-code"></a>A kód ismertetése

Az orchestrator függvény alapvetően a következőket teszi:

1. Figyeli a nevű külső esemény *művelet* használatával [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Lépésekben vagy csökkenti a `counterState` lokális változó attól függően, hogy a kért műveletet.
3. Az orchestrator használatával újraindul a [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metódus legújabb értékre állítja `counterState` az új bemenő adatként.
4. Továbbra is fennáll, végtelen, vagy amíg nem fut egy *end* üzenet jelenik meg.

Itt látható egy példa egy *eternal vezénylési* &mdash; Ez azt jelenti, hogy egy másikat, amely potenciálisan soha nem ér véget. Azt által küldött üzenetek reagáljon a [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metódus, amely az az orchestrator funkció hívása.

Az orchestrator függvény egy egyedi jellemzője, gyakorlatilag az előzményeket nem rendelkezik: a `ContinueAsNew` művelet visszaállítja az előzmények összes feldolgozott esemény után. Ez az előnyben részesített módja az orchestrator, amely egy tetszőleges élettartama végrehajtásához. Használja a `while` hurok okozhat az orchestrator függvény előzmények unbounded, feleslegesen magas memóriahasználatban eredményezve növelése sikertelen volt.

> [!NOTE]
> A `ContinueAsNew` metódusnak más példái mellett eternal álló üzenettípusok összehangolását. További információkért lásd: [Eternal álló üzenettípusok összehangolását](durable-functions-eternal-orchestrations.md).

## <a name="run-the-sample"></a>Minta futtatása

A vezénylési megkezdheti úgy, hogy a következő HTTP POST-kérelmet küld. Engedélyezi `counterState` nulla elindításához (az alapértelmezett érték `int`), a kérelemben nincs tartalom.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

A **E3_Counter** példány kezdődik, majd azonnal vár segítségével küldendő esemény `RaiseEventAsync` használatával vagy a **sendEventUrl** HTTP POST webhook 202 válasz hivatkozik. Érvényes `eventName` értékek a következők *incr*, *decr*, és *end*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

A függvény naplók az Azure Functions portálon bármikor megtekintheti a "incr" művelet eredményét.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

Hasonlóképpen, ha az orchestrator állapotának látja a `input` mező a frissített értéket (1) van beállítva.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

Folytatja a küldést új műveletek ehhez a példányhoz, és figyelje meg, hogy az állapot frissül a ennek megfelelően. Ha a példány leállítása, akkor ehhez küldése egy *end* műveletet.

> [!WARNING]
> Írásának időpontjában vannak ismert versenyhelyzetek meghívásakor `ContinueAsNew` egyidejűleg az üzenetek, például a külső események vagy megszakítási kérelem feldolgozása közben. Ezek versenyhelyzetek a legfrissebb információkért lásd: a [GitHub probléma](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="next-steps"></a>További lépések

Ez a minta bemutatta, hogyan legyen kezelve [külső események](durable-functions-external-events.md) és valósíthatnak meg [eternal álló üzenettípusok összehangolását](durable-functions-eternal-orchestrations.md) a [állapot-nyilvántartó singletons](durable-functions-singletons.md). A következő példa bemutatja, hogyan használja külső eseményeket és [tartós időzítők](durable-functions-timers.md) kezelésére emberi beavatkozást igényel.

> [!div class="nextstepaction"]
> [Futtathatja a emberi beavatkozást igényel](durable-functions-phone-verification.md)
