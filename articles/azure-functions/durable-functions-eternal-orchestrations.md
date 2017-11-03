---
title: "A tartós funkciók - Azure eternal álló üzenettípusok összehangolását"
description: "Útmutató az Azure Functions tartós funkciók bővítményével eternal álló üzenettípusok összehangolását végrehajtásához."
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
ms.openlocfilehash: 79fcbbf5f506858789a6bd1e6ad2e292c72b65a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>A tartós függvények (az Azure Functions) eternal álló üzenettípusok összehangolását

*Eternal álló üzenettípusok összehangolását* olyan orchestrator funkció, amely soha nem végződhet. Akkor hasznosak, ha a használni kívánt [tartós funkciók](durable-functions-overview.md) naplózási gyűjtők és végtelen hurkot igénylő forgatókönyvek.

## <a name="orchestration-history"></a>Vezénylési előzmények

A [ellenőrzőpontok létrehozása és a visszajátszás](durable-functions-checkpointing-and-replay.md), tartós feladat keretében nyomon követi, hogy az egyes függvény vezénylési előzményeit. Az előzmények növekedésének folyamatosan amennyiben az orchestrator-funkció továbbra is fennáll, új munkaelem ütemezése. Az orchestrator függvény végtelen hurkot állapotba kerül, és folyamatosan ütemezi a munkát, ha az előzmények sikerült nő, különösen nagyméretű és jelentős teljesítménybeli problémákat okozhat. A *eternal vezénylési* koncepció úgy lett kialakítva, az ilyen típusú végtelen hurkok igénylő alkalmazásokhoz problémák elhárítása érdekében.

## <a name="resetting-and-restarting"></a>Alaphelyzetbe állítása és újraindítása

Végtelen hurkok helyett az orchestrator funkciók az állapot visszaállítására meghívásával a [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metódust. Ez a módszer egy egyetlen szerializálhatók a JSON-paramétert fogad, válik az új bemeneti orchestrator függvény a következő generációs.

Amikor `ContinueAsNew` neve, a példány enqueues önmagához, mielőtt kilép egy üzenetet. Az üzenet az új bemeneti értékkel rendelkező példány újraindul. Az azonos Példányazonosítót maradnak, de az orchestrator függvény előzmények hatékonyan függvény egésszé csonkítja.

> [!NOTE]
> A tartós feladat keretrendszer tart fenn a azonos Példányazonosítót, de belső létrehoz egy új *végrehajtási Azonosítóhoz* lekérdezi állítsa alaphelyzetbe az orchestrator függvényhez `ContinueAsNew`. A végrehajtási Azonosítóhoz általában nem lesz közzétéve kívülről, de tudni a vezénylési végrehajtási elhárításakor hasznos lehet.

## <a name="periodic-work-example"></a>Rendszeres munka – példa

Egy használati eset eternal álló üzenettípusok összehangolását a kódot, amely rendszeres munkájuk határozatlan időre van szüksége.

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer<string>(nextCleanup);

    context.ContinueAsNew();
}
```

Ez a példa és egy időzítő-eseményindítóval aktivált függvény közötti különbség itt a karbantartási eseményindító alkalommal nem a ütemezés szerint. Például egy CRON-ütemezését, amely végrehajtja a függvény minden órában végrehajtja az 1:00, 2:00, 3:00 stb és potenciálisan futtathat ütközött átfedi egymást. Ebben a példában, azonban a karbantartás 30 perc, ha majd azt ütemezi, 1:00, 2:30, 4:00 és hasonló, és hozzon létre lehetősége nélkül.

## <a name="counter-example"></a>A számláló – példa

Íme egy egyszerűsített példa egy *számláló* függvény, amely figyeli a *növekmény* és *csökkentheti* eternally események.

```csharp
[FunctionName("SimpleCounter")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int counterState = context.GetInput<int>();

    string operation = await context.WaitForExternalEvent<string>("operation");

    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }
    
    context.ContinueAsNew(counterState);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Léphet ki egy eternal vezénylési

Ha az orchestrator függvény végül befejezéséhez szükséges, akkor csak annyit kell tennie *nem* hívás `ContinueAsNew` és a kilépéshez függvény segítségével.

Ha az orchestrator függvény végtelen hurkot és kell állítani, a a [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metódus leállításához. További információkért lásd: [Példánykezelés](durable-functions-instance-management.md).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan valósítja meg a singleton álló üzenettípusok összehangolását](durable-functions-singletons.md)

> [!div class="nextstepaction"]
> [Egy minta eternal vezénylési futtatása](durable-functions-counter.md)
