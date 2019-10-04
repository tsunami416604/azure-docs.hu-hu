---
title: Örök Összehangolók a Durable Functionsban – Azure
description: Megtudhatja, hogyan valósítható meg az örök Összehangolók a Azure Functions Durable Functions-bővítményének használatával.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: dbe51eddcf748843fd90cc533063fd25e7c282fd
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933369"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Örök összeszerelések Durable Functionsban (Azure Functions)

Az *örök* Összehangolók olyan Orchestrator függvények, amelyek soha nem fejeződik be. Akkor hasznosak, ha a Durable Functionst [](durable-functions-overview.md) szeretné használni a gyűjtők számára, és olyan forgatókönyveket, amelyek végtelen hurkot igényelnek.

## <a name="orchestration-history"></a>Előkészítési előzmények

Ahogy az a előkészítési [Előzmények](durable-functions-orchestrations.md#orchestration-history) témakörben is szerepel, a tartós feladatok keretrendszere nyomon követi az egyes függvények összehangolása előzményeit. Az előzmények folyamatosan növekednek, amíg a Orchestrator függvény továbbra is új munkát ütemezhet. Ha a Orchestrator függvény végtelen ciklusba kerül, és folyamatosan dolgozik, az előzmények kritikusan nagy mértékben növekednek, és jelentős teljesítménnyel kapcsolatos problémákat okozhatnak. Az *örök* előkészítési koncepció célja, hogy enyhítse az ilyen típusú problémákat a végtelen hurkokat igénylő alkalmazások esetében.

## <a name="resetting-and-restarting"></a>Alaphelyzetbe állítás és újraindítás

A végtelen hurkok használata helyett a Orchestrator functions a [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metódus meghívásával állítja vissza az állapotukat. Ez a metódus egy JSON-szerializálható paramétert használ, amely a következő Orchestrator-függvény új bemenete lesz.

Ha `ContinueAsNew` a hívása megtörténik, a példány enqueues egy üzenetet, mielőtt kilép. Az üzenet újraindítja a példányt az új bemeneti értékkel. A rendszer megőrzi a példány AZONOSÍTÓját, de a Orchestrator függvény előzményeit a rendszer gyakorlatilag csonkolja.

> [!NOTE]
> Az állandó feladathoz tartozó keretrendszer ugyanazt a példányt tárolja, de belsőleg létrehoz egy új *végrehajtási azonosítót* a Orchestrator függvény számára, amely `ContinueAsNew`alaphelyzetbe áll. Ez a végrehajtási azonosító általában nem külsőleg van kitéve, de hasznos lehet tudni, hogy mikor kell a hibakeresést végrehajtani.

## <a name="periodic-work-example"></a>Ismétlődő munka példája

Az örök feldolgozók esetében az egyik használati eset olyan kód, amelynek határozatlan idejű munkát kell végeznie.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

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
Az [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metódus használatával elindíthat egy örök előkészítést. Ez nem más, mint bármely más összehangoló függvény elindítása.  

> [!NOTE]
> Ha meg kell győződnie arról, hogy egy egypéldányos örök előkészítés fut, fontos, hogy ugyanazt a `id` példányt őrizze meg az előkészítés megkezdése során. További információ: [példányok kezelése](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [OrchestrationClient] DurableOrchestrationClientBase client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop"), instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Kilépés az örök összeszerelésből

Ha egy Orchestrator függvénynek végül végre kell hajtania a műveletet, akkor mindössze annyit kell `ContinueAsNew` tennie, hogy *nem* hívja meg a függvényt, és hagyja ki a funkciót.

Ha egy Orchestrator-függvény végtelen ciklusban van, és le kell állítani, a [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metódus használatával állítsa le. További információ: [példányok kezelése](durable-functions-instance-management.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan valósítható meg az Egypéldányos előkészítés](durable-functions-singletons.md)