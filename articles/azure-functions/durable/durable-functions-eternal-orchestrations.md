---
title: Örök Összehangolók a Durable Functionsban – Azure
description: Megtudhatja, hogyan valósítható meg az örök Összehangolók a Azure Functions Durable Functions-bővítményének használatával.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 99f57f2e0b34f2e596ff9cf1a872650228ef0acd
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614855"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Örök összeszerelések Durable Functionsban (Azure Functions)

Az *örök* Összehangolók olyan Orchestrator függvények, amelyek soha nem fejeződik be. Akkor hasznosak, ha a [Durable Functionst](durable-functions-overview.md) szeretné használni a gyűjtők számára, és olyan forgatókönyveket, amelyek végtelen hurkot igényelnek.

## <a name="orchestration-history"></a>Előkészítési előzmények

Ahogy az a előkészítési [Előzmények](durable-functions-orchestrations.md#orchestration-history) témakörben is szerepel, a tartós feladatok keretrendszere nyomon követi az egyes függvények összehangolása előzményeit. Az előzmények folyamatosan növekednek, amíg a Orchestrator függvény továbbra is új munkát ütemezhet. Ha a Orchestrator függvény végtelen ciklusba kerül, és folyamatosan dolgozik, az előzmények kritikusan nagy mértékben növekednek, és jelentős teljesítménnyel kapcsolatos problémákat okozhatnak. Az *örök* előkészítési koncepció célja, hogy enyhítse az ilyen típusú problémákat a végtelen hurkokat igénylő alkalmazások esetében.

## <a name="resetting-and-restarting"></a>Alaphelyzetbe állítás és újraindítás

A végtelen hurkok használata helyett a Orchestrator functions visszaállítja az állapotát úgy, hogy meghívja a koordinációs [trigger kötésének](durable-functions-bindings.md#orchestration-trigger)`ContinueAsNew` (.net) vagy `continueAsNew` (JavaScript) metódusát. Ez a metódus egy JSON-szerializálható paramétert használ, amely a következő Orchestrator-függvény új bemenete lesz.

`ContinueAsNew` hívásakor a példány enqueues egy üzenetet, mielőtt kilép. Az üzenet újraindítja a példányt az új bemeneti értékkel. A rendszer megőrzi a példány AZONOSÍTÓját, de a Orchestrator függvény előzményeit a rendszer gyakorlatilag csonkolja.

> [!NOTE]
> Az állandó feladathoz tartozó keretrendszer ugyanazokat az AZONOSÍTÓkat tartja fenn, de belsőleg létrehoz egy új *végrehajtási azonosítót* a Orchestrator függvény számára, amelyet a `ContinueAsNew`alaphelyzetbe állít. Ez a végrehajtási azonosító általában nem külsőleg van kitéve, de hasznos lehet tudni, hogy mikor kell a hibakeresést végrehajtani.

## <a name="periodic-work-example"></a>Ismétlődő munka példája

Az örök feldolgozók esetében az egyik használati eset olyan kód, amelynek határozatlan idejű munkát kell végeznie.

### <a name="c"></a>C#

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
> Az előző C# példa a Durable functions 2. x. Durable Functions 1. x esetén a `IDurableOrchestrationContext`helyett `DurableOrchestrationContext`t kell használnia. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

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

A példa és az időzítő által aktivált függvény közötti különbség az, hogy a törlési kiváltó idő itt nem menetrend alapján történik. Például egy olyan CRON-ütemterv, amely óránként hajt végre egy függvényt, a 1:00-es, 2:00-es, 3:00-as és más-más időpontban hajtja végre, és az átfedésben lévő problémákba ütközik. Ebben a példában azonban, ha a tisztítás 30 percet vesz igénybe, akkor a rendszer a 1:00, 2:30, 4:00 stb. időpontban ütemezi, és az átfedés nem lehetséges.

## <a name="starting-an-eternal-orchestration"></a>Örök összehangolás indítása

A `StartNewAsync` (.NET) vagy a `startNew` (JavaScript) metódussal elindíthat egy örök előkészítést, ugyanúgy, mint bármely más előkészítési funkciót.  

> [!NOTE]
> Ha meg kell győződnie arról, hogy egy egypéldányos örök előkészítést kell futtatnia, fontos, hogy ugyanazt a példányt `id` az előkészítés indításakor. További információ: [példányok kezelése](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Az előző kód Durable Functions 2. x. Durable Functions 1. x esetén a `DurableClient` attribútum helyett `OrchestrationClient` attribútumot kell használnia, és a `DurableOrchestrationClient` paraméter típusát kell használnia `IDurableOrchestrationClient`helyett. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

## <a name="exit-from-an-eternal-orchestration"></a>Kilépés az örök összeszerelésből

Ha egy Orchestrator függvénynek végül végre kell hajtania a műveletet, akkor mindössze annyit kell tennie, hogy *nem* hívja meg a `ContinueAsNew`t, és hagyja ki a funkciót.

Ha egy Orchestrator-függvény végtelen ciklusban van, és le kell állítani, a beállítás leállításához használja a koordinációs [ügyfél](durable-functions-bindings.md#orchestration-client) `TerminateAsync` (.net) vagy `terminate` (JavaScript) metódusát. További információ: [példányok kezelése](durable-functions-instance-management.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan valósítható meg az Egypéldányos előkészítés](durable-functions-singletons.md)
