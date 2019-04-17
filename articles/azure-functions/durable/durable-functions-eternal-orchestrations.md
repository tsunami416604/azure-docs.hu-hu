---
title: Durable Functions – az Azure a külső vezénylések
description: Ismerje meg, hogy külső vezénylések megvalósítása az Azure Functions a Durable Functions bővítmény használatával.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: c4adffd457338ffebfd1c9c7727023f82088dc57
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607738"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions (az Azure Functions) a külső vezénylések

*Külső vezénylések* az orchestrator-függvény, amely soha nem végződhet. Ezek akkor hasznos, ha a használni kívánt [Durable Functions](durable-functions-overview.md) összesítők adatait és a egy végtelen ciklust igénylő forgatókönyvek.

## <a name="orchestration-history"></a>Vezénylési előzmények

A [ellenőrzőpont és visszajátszás](durable-functions-checkpointing-and-replay.md), tartós feladat keretében nyomon követi, hogy az egyes függvény vezénylési előzményeit. Az előzményekben folyamatosan, amíg az orchestrator-funkció ütemezéséhez új munkahelyi folyamatosan nő. Ha az orchestrator függvény végtelen ciklust lépnek, és folyamatosan ütemezi a munkát, az előzmények sikerült növelheti a kritikus fontosságú, nagy és jelentős teljesítménybeli problémákat okozhat. A *külső vezénylési* fogalom úgy lett kialakítva, az ilyen típusú problémák végtelen hurkok igénylő alkalmazások esetében csökkentése érdekében.

## <a name="resetting-and-restarting"></a>Alaphelyzetbe állítása és újraindítása

Végtelen hurkok helyett az orchestrator funkciók állapotuk alaphelyzetbe meghívásával a [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metódust. Ez a módszer egy egyetlen szerializálható JSON paramétert, amely az új beviteli válik az orchestrator függvény a következő generációs vesz igénybe.

Amikor `ContinueAsNew` nevezzük, a példány enqueues magát, mielőtt kilép, egy üzenetet. Az üzenet az új bemeneti érték a példány újraindul. Az azonos Példányazonosító marad, de az orchestrator-függvény előzmények hatékonyan csonkítja.

> [!NOTE]
> A tartós feladat keretrendszer megtartja ugyanazt a Példányazonosító, de belsőleg létrehoz egy új *végrehajtási Azonosítóhoz* az orchestrator-függvény, amely lekérdezi állítsa alaphelyzetbe a `ContinueAsNew`. A végrehajtási Azonosítóhoz általában nem lesz közzétéve kívülről, de hasznos tudnivalók az orchestration végrehajtási történő hibakeresése során.

## <a name="periodic-work-example"></a>Rendszeres munkahelyi példa

Egy külső vezénylések funkcióban kódot, amely a rendszeres munkához határozatlan időre van szüksége.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup);

    context.df.continueAsNew(undefined);
});
```

Ebben a példában és a egy időzítő által aktivált függvény közötti különbség itt törlési eseményindító többször nem a ütemezés szerint. Például egy CRON-ütemezését, amely végrehajtja a függvényt óránként hajtja végre, 1:00, 2:00, 3:00-kor stb., és sikerült potenciálisan felmerülő átfedés hibák. Ebben a példában azonban a tisztítás 30 percet vesz igénybe. Ha ezután azt lesz ütemezve, 1:00, 2:30, 4:00, stb., és nem okoz átfedés.

## <a name="exit-from-an-eternal-orchestration"></a>Kilépés a külső vezénylések

Ha egy orchestrator-funkció végül befejezéséhez szükséges, akkor ehhez mindössze *nem* hívja `ContinueAsNew` , és lépjen ki a függvényt.

Ha egy orchestrator-függvényt egy végtelen ciklust, és igényeinek megfelelően le kell állítani, akkor a [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metódus leállításához. További információkért lásd: [példány felügyeleti](durable-functions-instance-management.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan valósíthat meg egyszeres vezénylések](durable-functions-singletons.md)
