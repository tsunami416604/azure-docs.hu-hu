---
title: Azure Durable Functions-egység tesztelése
description: Ismerje meg, hogyan lehet a test Durable Functions.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: glenga
ms.openlocfilehash: 95c6afcb2f7e864da4b9b43235326a17bed785fa
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614531"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions egység tesztelése

Az egység tesztelése a modern szoftverfejlesztési eljárások fontos részét képezi. Az egység-tesztek ellenőrzik az üzleti logikát és a védelemtől való megfelelést, és a jövőben nem figyelt feltörési változásokat jelentenek. A Durable Functions könnyedén növelheti a bonyolultságot, így az egységek tesztelésének bevezetésével elkerülhető a változások megszakítása. A következő szakaszokban bemutatjuk, hogyan kell tesztelni a három függvényt – a-összehangoló ügyfelet, a Orchestrator és a tevékenységi funkciókat.

> [!NOTE]
> Ez a cikk útmutatást nyújt az Durable Functions alkalmazások Durable Functions 1. x célra való célzásához. Még nem frissült a Durable Functions 2. x verzióban bevezetett változások miatt. A verziók közötti különbségekről a [Durable functions verziók](durable-functions-versions.md) című cikkben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő példák a következő fogalmakat és keretrendszerek ismeretét igénylik:

* Egységtesztelés

* Tartós függvények

* [xUnit](https://xunit.github.io/) – tesztelési keretrendszer

* [MOQ](https://github.com/moq/moq4) -modellezési keretrendszer

## <a name="base-classes-for-mocking"></a>A modellezés alaposztályai

A kigúnyolás a Durable Functions 1. x három absztrakt osztályán keresztül támogatott:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Ezek az osztályok `DurableOrchestrationClient`, `DurableOrchestrationContext`és `DurableActivityContext` alaposztályai, amelyek a Orchestrator és a tevékenységi módszereket határozzák meg. A kigúnyolja az alaposztály-metódusok várt viselkedését állítja be, így az egység tesztelése ellenőrizheti az üzleti logikát. Az egység kétlépéses munkafolyamata az üzleti logikát teszteli az összehangoló ügyfélben és a Orchestrator:

1. Az alaposztályok használata a konkrét implementáció helyett az előkészítési ügyfél és a Orchestrator függvény aláírásának meghatározásakor.
2. A Unit-tesztek során az alaposztályok viselkedését és az üzleti logikát kell ellenőrizni.

A következő bekezdésekben talál további részleteket az előkészítési ügyfél kötését és a Orchestrator trigger kötést használó függvények teszteléséhez.

## <a name="unit-testing-trigger-functions"></a>Unit Testing trigger functions

Ebben a szakaszban az egység tesztelése ellenőrzi a következő HTTP-trigger függvény logikáját az új előkészítések elindításához.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

Az egység teszt feladata a válasz adattartalomban megadott `Retry-After` fejléc értékének ellenőrzése. Így az egység tesztelése kigúnyol néhány `DurableOrchestrationClientBase` módszert a kiszámítható működés biztosítása érdekében.

Először is szükség van az alaposztály mintájának kiírására, `DurableOrchestrationClientBase`. A modell lehet egy új osztály, amely megvalósítja `DurableOrchestrationClientBase`. Azonban a [MOQ](https://github.com/moq/moq4) , például a következő modellezési keretrendszer használatával egyszerűsíti a folyamatot:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Ezután `StartNewAsync` metódust egy jól ismert példány AZONOSÍTÓjának visszaadására.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

A következő `CreateCheckStatusResponse` a rendszer kigúnyolja, hogy mindig üres HTTP 200 választ ad vissza.

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

a `ILogger` a következőket is kigúnyolja:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

Most a `Run` metódust kell meghívni az egység tesztből:

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

 Az utolsó lépés a kimenet összehasonlítása a várt értékkel:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Az összes lépés egyesítése után az egység tesztelése a következő kódot fogja tartalmazni:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Unit Testing Orchestrator functions

A Orchestrator függvények még érdekesebbek az egység tesztelése során, mivel általában sokkal több üzleti logikával rendelkeznek.

Ebben a szakaszban az egység tesztek ellenőrzik a `E1_HelloSequence` Orchestrator függvény kimenetét:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Az egység tesztelési kódja a modell létrehozásával kezdődik:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Ezt követően a tevékenység metódusának hívásait a rendszer kigúnyolja:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Ezután az egység tesztelése `HelloSequence.Run` metódust hív meg:

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Végül a rendszer érvényesíti a kimenetet:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Az összes lépés egyesítése után az egység tesztelése a következő kódot fogja tartalmazni:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Unit Testing Activity functions

A Activity functions olyan egységként is tesztelhető, mint a nem tartós függvények.

Ebben a szakaszban az egység tesztelése ellenőrzi a `E1_SayHello` Activity függvény viselkedését:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Az egység tesztek pedig ellenőrzik a kimenet formátumát. Az egység tesztek közvetlenül vagy a `DurableActivityContextBase` osztályhoz tartozó paraméterek használatát vehetik igénybe:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [További információ a MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
