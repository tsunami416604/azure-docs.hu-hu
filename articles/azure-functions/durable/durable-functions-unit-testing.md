---
title: Az Azure Durable Functions egység tesztelése
description: További információ a tartós funkciók egységteszteléséről.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 86733f8b5b80799bad3e52c643ed27465dfc7641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74231225"
---
# <a name="durable-functions-unit-testing"></a>Tartós funkciók egység tesztelése

Az egységtesztelés fontos része a modern szoftverfejlesztési gyakorlatnak. Az egységtesztek ellenőrzik az üzleti logika viselkedését, és védelmet nyújtanak a jövőben iszomtól mentes törési változások bevezetésétől. A tartós funkciók könnyen növekedhetnek a komplexitásban, így az egységtesztek bevezetése segít elkerülni a változások megtörését. A következő szakaszok bemutatják, hogyan egyesítheti a három függvénytípust – vezénylési ügyfél, orchestrator és tevékenységfüggvények.

> [!NOTE]
> Ez a cikk útmutatást nyújt a durable functions 1.x-et célzó tartós függvények alkalmazásainak egységteszteléséhez. Még nem lett frissítve, hogy figyelembe vegye a Durable Functions 2.x-ben bevezetett változásokat. A verziók közötti különbségekről a [Durable Functions verziók ról](durable-functions-versions.md) szóló cikkben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

Az ebben a cikkben szereplő példák a következő fogalmak és keretek ismeretét igénylik:

* Egységtesztelés

* Tartós függvények

* [xUnit](https://xunit.github.io/) - Tesztelési keretrendszer

* [moq](https://github.com/moq/moq4) - Gúnyos keret

## <a name="base-classes-for-mocking"></a>Alaposztályok a gúnyolódáshoz

A gúnyolódást a Durable Functions 1.x három absztrakt osztálya támogatja:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Ezek az osztályok a `DurableOrchestrationClient`, `DurableOrchestrationContext`alaposztályai, és `DurableActivityContext` amelyek meghatározzák az Vezénylési ügyfél, az Orchestrator és a Tevékenység metódusokat. A mocks beállítja az alaposztály metódusok várható viselkedését, így az egységteszt ellenőrizheti az üzleti logikát. Az alkalmazási ügyfél és az orchestrator üzleti logikájának teszteléséhez kétlépéses munkafolyamat áll:

1. Vezénylési ügyfél- és orchestrator függvényaláírások definiálásakor a konkrét implementáció helyett használja az alaposztályokat.
2. Az egységtesztekben kigúnyolja az alaposztályok viselkedését, és ellenőrzi az üzleti logikát.

További részleteket a következő bekezdésekben a vezénylési ügyfélkötést és az orchestrator eseményindító kötést használó függvények tesztelésére.

## <a name="unit-testing-trigger-functions"></a>Egységtesztelési eseményindító funkciói

Ebben a szakaszban az egységteszt ellenőrzi a következő HTTP-eseményindító függvény logikáját az új vezénylések indításához.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Az egységtesztelési feladat a válasz `Retry-After` hasznos adatában megadott fejléc értékének ellenőrzése lesz. Így az egység teszt `DurableOrchestrationClientBase` gúnyolódni néhány módszer biztosítása kiszámítható viselkedés.

Először is, egy álat `DurableOrchestrationClientBase`az alap osztály van szükség, . A modell lehet egy új `DurableOrchestrationClientBase`osztály, amely végrehajtja . Azonban egy gúnyos keretrendszer, mint a [moq](https://github.com/moq/moq4) leegyszerűsíti a folyamatot:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Ezután `StartNewAsync` a módszert kigúnyolják, hogy egy jól ismert példányazonosítót adjanak vissza.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

Következő `CreateCheckStatusResponse` gúnyolódik, hogy mindig vissza egy üres HTTP 200 választ.

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

`ILogger`is kigúnyolják:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Most `Run` a módszer az egység teszt:

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
        loggerMock.Object);
 ```

 Az utolsó lépés a kimenet és a várt érték összehasonlítása:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Az összes lépés egyesítése után az egységteszt a következő kóddal rendelkezik:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Egységtesztelés orchestrator funkciók

Orchestrator funkciók még érdekesebb egység tesztelés, mivel általában sokkal több üzleti logika.

Ebben a szakaszban az egységtesztek `E1_HelloSequence` ellenőrzik az Orchestrator függvény kimenetét:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Az egységteszt kódja egy mock létrehozásával kezdődik:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Ezután a tevékenység módszer hívások lesz gúnyolódni:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Ezután az egység `HelloSequence.Run` teszt hívja módszer:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

És végül a kimenet érvényesíthető:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Az összes lépés egyesítése után az egységteszt a következő kóddal rendelkezik:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Egységvizsgálati tevékenység funkciói

A tevékenységfüggvények ugyanúgy tesztelhetők egységben, mint a nem tartós funkciók.

Ebben a szakaszban az egységteszt `E1_SayHello` ellenőrzi a Tevékenység funkció működését:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

És az egység tesztek ellenőrzik a kimenet formátumát. Az egységtesztek közvetlenül vagy a `DurableActivityContextBase` modellosztályban használhatják a paramétertípusokat:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az xUnit-ról](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [További információ a moq-ról](https://github.com/Moq/moq4/wiki/Quickstart)
