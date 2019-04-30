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
origin.date: 12/11/2018
ms.date: 03/25/2019
ms.author: v-junlch
ms.openlocfilehash: 69cf91f1448e36353f83de7a271abb3b53858bb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648465"
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

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace VSSample
{
    public static class HttpStart
    {
        [FunctionName("HttpStart")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
            [OrchestrationClient] DurableOrchestrationClientBase starter,
            string functionName,
            ILogger log)
        {
            // Function input comes from the request content.
            dynamic eventData = await req.Content.ReadAsAsync<object>();
            string instanceId = await starter.StartNewAsync(functionName, eventData);

            log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

            return starter.CreateCheckStatusResponse(req, instanceId);
        }
    }
}
```

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

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Net.Http.Headers;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    using Moq;
    using Xunit;

    public class HttpStartTests
    {
        [Fact]
        public async Task HttpStart_returns_retryafter_header()
        {
            // Define constants
            const string functionName = "SampleFunction";
            const string instanceId = "7E467BDB-213F-407A-B86A-1954053D3C24";

            // Mock TraceWriter
            var loggerMock = new Mock<ILogger>();

            // Mock DurableOrchestrationClientBase
            var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();

            // Mock StartNewAsync method
            durableOrchestrationClientBaseMock.
                Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
                ReturnsAsync(instanceId);

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

            // Validate that output is not null
            Assert.NotNull(result.Headers.RetryAfter);

            // Validate output's Retry-After header value
            Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
        }
    }
}
```

## <a name="unit-testing-orchestrator-functions"></a>Az orchestrator funkciók egységtesztelés

Az orchestrator funkciók még egy érdekes dolgot tesztelést, mivel általában sokkal több üzleti logikai egység.

Ebben a szakaszban a egység tesztek kimenetét fogja ellenőrizni a `E1_HelloSequence` az Orchestrator-funkció:

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;

namespace VSSample
{
    public static class HelloSequence
    {
        [FunctionName("E1_HelloSequence")]
        public static async Task<List<string>> Run(
            [OrchestrationTrigger] DurableOrchestrationContextBase context)
        {
            var outputs = new List<string>();

            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello_DirectInput", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("E1_SayHello")]
        public static string SayHello([ActivityTrigger] DurableActivityContextBase context)
        {
            string name = context.GetInput<string>();
            return $"Hello {name}!";
        }

        [FunctionName("E1_SayHello_DirectInput")]
        public static string SayHelloDirectInput([ActivityTrigger] string name)
        {
            return $"Hello {name}!";
        }
    }
 }
```

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

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using System.Threading.Tasks;
    using Microsoft.Azure.WebJobs;
    using Moq;
    using Xunit;

    public class HelloSequenceTests
    {
        [Fact]
        public async Task Run_returns_multiple_greetings()
        {
            var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
            durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello_DirectInput", "London")).ReturnsAsync("Hello London!");

            var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);

            Assert.Equal(3, result.Count);
            Assert.Equal("Hello Tokyo!", result[0]);
            Assert.Equal("Hello Seattle!", result[1]);
            Assert.Equal("Hello London!", result[2]);
        }
    }
}
```

## <a name="unit-testing-activity-functions"></a>Egység tesztelési tevékenységfüggvényeket

Tevékenységfüggvényeket ugyanúgy, mint a nem tartós függvények tesztelése egység is lehet.

Ebben a szakaszban a egységtesztet viselkedését fogja ellenőrizni a `E1_SayHello` tevékenység függvény:

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs;

namespace VSSample
{
    public static class HelloSequence
    {
        [FunctionName("E1_HelloSequence")]
        public static async Task<List<string>> Run(
            [OrchestrationTrigger] DurableOrchestrationContextBase context)
        {
            var outputs = new List<string>();

            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
            outputs.Add(await context.CallActivityAsync<string>("E1_SayHello_DirectInput", "London"));

            // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
            return outputs;
        }

        [FunctionName("E1_SayHello")]
        public static string SayHello([ActivityTrigger] DurableActivityContextBase context)
        {
            string name = context.GetInput<string>();
            return $"Hello {name}!";
        }

        [FunctionName("E1_SayHello_DirectInput")]
        public static string SayHelloDirectInput([ActivityTrigger] string name)
        {
            return $"Hello {name}!";
        }
    }
 }
```

És az egységteszteket ellenőrzi, hogy a kimenet formátumát. Az egységteszteket használhatja a paramétertípusok közvetlenül vagy mock [DurableActivityContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContextBase.html) osztály:

```csharp
// Copyright (c) .NET Foundation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

namespace VSSample.Tests
{
    using Microsoft.Azure.WebJobs;
    using Xunit;
    using Moq;

    public class HelloSequenceActivityTests
    {
        [Fact]
        public void SayHello_returns_greeting()
        {
            var durableActivityContextMock = new Mock<DurableActivityContextBase>();
            durableActivityContextMock.Setup(x => x.GetInput<string>()).Returns("John");
            var result = HelloSequence.SayHello(durableActivityContextMock.Object);
            Assert.Equal("Hello John!", result);
        }

        [Fact]
        public void SayHello_returns_greeting_direct_input()
        {
            var result = HelloSequence.SayHelloDirectInput("John");
            Assert.Equal("Hello John!", result);
        }
    }
}
```
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók a xUnit](https://xunit.github.io/docs/getting-started-dotnet-core)
> 
> [További tudnivalók a moq](https://github.com/Moq/moq4/wiki/Quickstart)

<!-- Update_Description: wording update -->