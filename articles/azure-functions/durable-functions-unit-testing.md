---
title: "Az Azure tartós funkciók egységek tesztelése"
description: "Megtudhatja, hogyan egységhez tartós funkciók tesztelése."
services: functions
author: kadimitr
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2018
ms.author: kadimitr
ms.openlocfilehash: a8d2a2281dcaf6e5e308ad4a2aafb167f9ba9121
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="durable-functions-unit-testing"></a>Tartós funkciók egységek tesztelése

Egységek tesztelése modern software development eljárások fontos része. Egység tesztek ellenőrizze az üzleti logika viselkedését, és megvédheti a észrevétlenül jelentős változásokat a jövőben bemutatása. Könnyen növelhető összetettsége tartós funkciók, így introducing egység tesztek segítségével továbbra is működni fognak módosításokat. A következő szakaszok azt ismertetik, hogyan egység tesztelje a három függvény típusok - Vezénylési ügyfél, az Orchestrator és a tevékenység funkciók. 

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő példák a következő fogalmakat és keretrendszerek kapcsolódó ismeretek szükségesek: 

* Egységek tesztelése

* Tartós függvények 

* [xUnit](https://xunit.github.io/) -tesztelési keretrendszer

* [moq](https://github.com/moq/moq4) -keretrendszer Mocking


## <a name="base-classes-for-mocking"></a>Alaposztályok mocking 

Mocking tartós funkciókkal két absztrakt osztályok keresztül támogatja:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

Ezeket az osztályokat is az alaposztályok [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) és [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) definiáló Vezénylési ügyfél és az Orchestrator módszerek. A mocks alaposztály módszerek várt működése állítja be, az egységteszt ellenőrizheti az üzleti logikát. A Vezénylési ügyfél és az Orchestrator tesztelése az üzleti logikai egység kétlépéses munkafolyamat van:

1. Használata helyett a megvalósítását az alaposztályok, Orchestration ügyfél és az Orchestrator által aláírások meghatározásakor.
2. Egység-tesztek mock az alaposztályok viselkedését, és ellenőrizze az üzleti logika. 

További részletekért keresse az alábbi bekezdésekben tesztelési függvények, amelyek az orchestration ügyfél kötés és az orchestrator indítás kötés.

## <a name="unit-testing-trigger-functions"></a>Egység tesztelési eseményindító funkciók

Ebben a szakaszban az egységteszt a kapacitásprofillal szemben érvényesíti a programot a következő HTTP eseményindító függvény olyan új álló üzenettípusok összehangolását.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Az egység teszt feladat lesz értékének ellenőrzése a `Retry-After` a válasz tartalomban megadott fejléc. Így az egységteszt fog mock néhány [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) módszerek előre jelezhető viselkedés biztosításához. 

Először egy mock az alaposztály szükség, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). A mock lehet egy új osztályt, amely megvalósítja az [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Azonban a például a mocking keretrendszer használatával [moq](https://github.com/moq/moq4) leegyszerűsíti a folyamatot:    

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Majd `StartNewAsync` módszer van mocked vissza egy jól ismert példány.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Következő `CreateCheckStatusResponse` mocked mindig a visszatérési értéke 200-as HTTP-válasz üres.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
        });
```

`TraceWriter` is mocked:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Most már a `Run` metódus lehívásra kerül az egység vizsgálat:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object, 
        functionName,
        traceWriterMock.Object);
 ``` 

 Az utolsó lépése, hogy összehasonlítja a kimenet a várt érték:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Az egységteszt kell után kombinálásával az összes lépést, a következő kódot: 

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Az orchestrator funkciók tesztelés egység

Az orchestrator funkciók még ennél is érdekesebb megoldást tesztelését, mivel általában sokkal több üzleti logikai egységéhez. Az Orchestrator függvények jelenleg csak a C# valósítható.

Ebben a szakaszban a egység tesztek kimenetét fogja ellenőrizni a `E1_HelloSequence` Orchestrator-funkció:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

A teszt kódját egy mock létrehozásának indul:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Ezután a tevékenység metódushívások fog mocked:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Ezután az egységteszt fel fogja hívni `HelloSequence.Run` módszert:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

És végül a kimeneti érvényesíti:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Az egységteszt kell után kombinálásával az összes lépést, a következő kódot:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Egység tesztelési tevékenység funkciók

Tevékenység funkciók nem tartós funkciók ugyanúgy tesztelt egység is lehet. Tevékenység függvénynek nincs mocking osztály alaposztályát. Ezért a egység tesztek használja a paraméter közvetlenül.

Ebben a szakaszban az egységteszt viselkedését ellenőrzi a `E1_SayHello` tevékenység függvény:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

És az egységteszt ellenőrzi, hogy a kimeneti formátum:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók xUnit](http://xunit.github.io/docs/getting-started-dotnet-core)

> [További tudnivalók moq](https://github.com/Moq/moq4/wiki/Quickstart)