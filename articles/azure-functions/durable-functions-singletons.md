---
title: Singletons a tartós funkciók elvégzésére – Azure
description: Hogyan singletons használható az Azure Functions a tartós Functons bővítményben.
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
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ea8b5db946d6b35ea4583d9170ec36e5f95e16cd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972551"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Egypéldányos orchestrators tartós funkciókkal (az Azure Functions)

A feladatok a háttérben vagy szereplő stílusú álló üzenettípusok összehangolását, gyakran kell győződjön meg arról, hogy csak egy példánya egy adott orchestrator egyszerre futtatja. Ezt megteheti [tartós funkciók](durable-functions-overview.md) egy adott hozzárendelésével példány azonosítója az orchestrator való létrehozásakor.

## <a name="singleton-example"></a>Egypéldányos – példa

Az alábbi C# példa bemutatja egy HTTP-eseményindító függvény által létrehozott egy egypéldányos háttérben történő feldolgozás vezénylési. A kód biztosítja, hogy csak egy példány létezik a megadott példány azonosítóját.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

Alapértelmezés szerint példány azonosítói véletlenszerűen létrehozott GUID. Azonban ebben az esetben a Példányazonosítót az URL-címről átadott útvonal adatai. A kód hívások [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) ellenőrizze, hogy a megadott Azonosítóval rendelkező példány már fut-e. Ha nem, példány létrehozásakor, hogy az azonosítóval.

Az orchestrator függvény végrehajtása részleteit ténylegesen nem számít. Ez lehet egy rendszeres orchestrator függvényt, amely indulásakor és befejezésekor, vagy egy végtelen futtató lehet (Ez azt jelenti, hogy egy [Eternal Vezénylési](durable-functions-eternal-orchestrations.md)). A legfontosabb az, hogy van-e legalább egyszer csak egy példány fut egyszerre.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan hívhatja meg alárendelt álló üzenettípusok összehangolását](durable-functions-sub-orchestrations.md)
