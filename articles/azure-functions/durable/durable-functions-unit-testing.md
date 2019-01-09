---
title: Az Azure Durable Functions egységtesztelés
description: Ismerje meg, hogyan egységhez Durable Functions tesztelése.
services: functions
author: kadimitr
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: kadimitr
ms.openlocfilehash: 38db54889c1fff0406f4e21cea80e16b6fdc25d1
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105508"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions egységtesztelés

Egységtesztelés modern szoftverfejlesztési gyakorlatban fontos része. Egységtesztek ellenőrizze az üzleti logika viselkedés, és megvédheti a maradna kompatibilitástörő változásokat bevezetése a későbbiekben. Durable Functions is könnyen egyre összetettebbé válnak hogy introducing egységteszteket parancsban történt használhatatlanná tévő elkerülése érdekében. Az alábbi szakaszok azt ismertetik, hogyan egység három függvény típusai - Vezénylési ügyfél, az Orchestrator és a tevékenység teszteljen funkciók.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő példák a következő fogalmak és keretrendszereket ismerete szükséges:

* Egységtesztelés

* Tartós függvények

* [xUnit](https://xunit.github.io/) -tesztelési keretrendszer

* [moq](https://github.com/moq/moq4) -keretrendszer szimulálása

## <a name="base-classes-for-mocking"></a>A szimulálás alaposztályok

A szimulálás támogatott három az absztrakt osztályok a Durable Functions-n keresztül:

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

* [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html)

Ezeket az osztályokat is az alaposztályok [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html), [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html), és [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) definiáló Vezénylési ügyfél , Az orchestrator és a tevékenység. A mocks alaposztály módszerek várt működése állítja be, a test jednotky ellenőrizheti az üzleti logikát. Vezénylési ügyfél és az Orchestrator az üzleti logika egységtesztelés kétlépéses munkafolyamatot van:

1. Vezénylési ügyfél és a vezénylő aláírások definiálásakor használja a az alaposztályok helyett a tényleges megvalósítás.
2. Az egységteszteket utánzása az alaposztályok viselkedését, és ellenőrizze az üzleti logikát.

További részleteket talál a következő bekezdések tesztelési funkciók, amelyek a vezénylési ügyfél kötést és az orchestrator-trigger kötést.

## <a name="unit-testing-trigger-functions"></a>Egység tesztelési eseményindító függvények

Ebben a szakaszban a egységtesztet ellenőrzi a következő HTTP által aktivált függvény indítása új vezénylések logikáját.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Az egység vizsgálati feladat lesz értékének ellenőrzése a `Retry-After` a válasz hasznos adatban megadott fejlécet. Így a egységtesztet fog utánzása néhány [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) módszerek kiszámítható viselkedés biztosításához.

Először egy helyettem az alaposztály szükség, [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). A helyettem lehet egy új osztályt, amely megvalósítja [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html). Például egy utánzási keretrendszer használata azonban [moq](https://github.com/moq/moq4) leegyszerűsíti a folyamatot:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Ezután `StartNewAsync` metódus az utánzott vissza egy jól ismert példányok.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Tovább `CreateCheckStatusResponse` utánzott mindig, lépjen vissza az üres 200-as HTTP-választ.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`TraceWriter` akkor is utánzott:

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

Most már a `Run` metódus meghívása a egységtesztet:

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

 Az utolsó lépés, hogy összehasonlítja a várt értékkel:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Után minden lépést ötvöző a egységtesztet fog rendelkezni a következő kódot:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Az orchestrator funkciók egységtesztelés

Az orchestrator funkciók még egy érdekes dolgot tesztelést, mivel általában sokkal több üzleti logikai egység.

Ebben a szakaszban a egység tesztek kimenetét fogja ellenőrizni a `E1_HelloSequence` az Orchestrator-funkció:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

A teszt kódját egy helyettem létrehozásával kezdődik:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Ezután a tevékenység metódust hívja fog mocked:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Ezután meghívja a egységtesztet `HelloSequence.Run` módszer:

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

Után minden lépést ötvöző a egységtesztet fog rendelkezni a következő kódot:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Egység tesztelési tevékenységfüggvényeket

Tevékenységfüggvényeket ugyanúgy, mint a nem tartós függvények tesztelése egység is lehet.

Ebben a szakaszban a egységtesztet viselkedését fogja ellenőrizni a `E1_SayHello` tevékenység függvény:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

És az egységteszteket ellenőrzi, hogy a kimenet formátumát. Az egységteszteket használhatja a paramétertípusok közvetlenül vagy mock [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) osztály:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók a xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)

> [További tudnivalók a moq](https://github.com/Moq/moq4/wiki/Quickstart)
