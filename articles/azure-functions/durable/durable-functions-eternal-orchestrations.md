---
title: Örök vezénylések a tartós függvényekben - Azure
description: Ismerje meg, hogyan valósíthatja meg az örök vezénylések használatával a Durable Functions bővítmény az Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478814"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Örök vezénylések a tartós függvényekben (Azure Functions)

*Az örök vezénylések* olyan orchestrator függvények, amelyek soha nem érnek véget. Ezek akkor hasznosak, ha [tartós függvényeket](durable-functions-overview.md) szeretne használni az összesítőkhöz és minden olyan forgatókönyvhöz, amely végtelen ciklust igényel.

## <a name="orchestration-history"></a>Vezénylési előzmények

Ahogy azt a [vezénylési előzmények](durable-functions-orchestrations.md#orchestration-history) témakörben, a tartós feladat keretrendszer nyomon követi az egyes függvényvezénylési előzmények. Ez az előzmények folyamatosan növekszik, amíg az orchestrator függvény továbbra is ütemezi az új munka. Ha az orchestrator függvény végtelen ciklusba kerül, és folyamatosan ütemezi a munkát, ez az előzmény kritikusan nagydá válhat, és jelentős teljesítményproblémákat okozhat. Az *örök vezénylési* koncepció célja az volt, hogy enyhítse az ilyen típusú problémákat a végtelen hurkokat igénylő alkalmazások esetében.

## <a name="resetting-and-restarting"></a>Alaphelyzetbe állítás és újraindítás

Végtelen ciklusok használata helyett az orchestrator függvények `ContinueAsNew` alaphelyzetbe állítják állapotukat a `continueAsNew` [vezénylési eseményindító kötés](durable-functions-bindings.md#orchestration-trigger)(.NET) vagy (JavaScript) metódusának meghívásával. Ez a módszer egyetlen JSON-szerializálható paramétert vesz igénybe, amely a következő orchestrator függvénygenerálás új bemenete lesz.

A `ContinueAsNew` rendszer megküldésekor a példány várólistára helyezi magát, mielőtt kilépne. Az üzenet újraindítja a példányt az új bemeneti értékkel. Ugyanaz a példányazonosító megmarad, de az orchestrator függvény előzményei hatékonyan csonkulnak.

> [!NOTE]
> A tartós feladatkeretrendszer ugyanazt a példányazonosítót tartja fenn, de belsőleg létrehoz `ContinueAsNew`egy új *végrehajtási azonosítót* az orchestrator függvényhez, amelyet a rendszer alaphelyzetbe állít. Ez a végrehajtási azonosító általában nem érhető el külsőleg, de hasznos lehet tudni, ha vezénylési végrehajtás hibakeresés.

## <a name="periodic-work-example"></a>Példa időszakos munkára

Az örök vezénylések egyik felhasználási esete olyan kód, amelynek határozatlan ideig időszakos munkát kell végeznie.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> Az előző C# példa a Durable Functions 2.x. A tartós függvények 1.x `DurableOrchestrationContext` esetén `IDurableOrchestrationContext`a helyett a ot kell használnia. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

---

A különbség ebben a példában, és egy időzítő által aktivált függvény, hogy a törlési eseményindító idők itt nem ütemezés alapján. Például egy CRON ütemezés, amely óránként hajt végre egy függvényt, 1:00, 2:00, 3:00 stb. Ebben a példában azonban, ha a tisztítás 30 percet vesz igénybe, akkor 1:00, 2:30, 4:00 stb.

## <a name="starting-an-eternal-orchestration"></a>Örök vezénylés indítása

Használja `StartNewAsync` a (.NET) `startNew` vagy a (JavaScript) metódust egy örök vezénylési, ugyanúgy, mint bármely más vezénylési függvény.  

> [!NOTE]
> Ha biztosítania kell, hogy egy singleton örök vezénylési fut, fontos, hogy ugyanazt a példányt `id` a vezénylés indításakor. További információt a [Példánykezelés című témakörben talál.](durable-functions-instance-management.md)

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Az előző kód a Durable Functions 2.x. A Durable Functions 1.x `OrchestrationClient` esetén az `DurableClient` attribútum helyett attribútumot kell `DurableOrchestrationClient` használni, és `IDurableOrchestrationClient`a paramétertípusát kell használnia a helyett. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Kilépés az örök vezénylési

Ha egy orchestrator függvényt végül be kell fejeznie, `ContinueAsNew` akkor mindössze annyit kell tennie, hogy *nem* hívja meg, és hagyja, hogy a függvény kilép.

Ha egy orchestrator függvény végtelen ciklusban van, és `TerminateAsync` le kell `terminate` állítani, használja a [vezénylési ügyfélkötés](durable-functions-bindings.md#orchestration-client) (.NET) vagy (JavaScript) metódusát a vezénylési ügyfélkötés leállításához. További információt a [Példánykezelés című témakörben talál.](durable-functions-instance-management.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan valósítható meg a singleton vezénylések](durable-functions-singletons.md)
