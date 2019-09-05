---
title: Függőségi befecskendezés használata a .NET-Azure Functions
description: Ismerje meg, hogyan használhatja a függőségi befecskendezést a szolgáltatások .NET-függvényekbe való regisztrálásához és használatához
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functions, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: e31f3dc166177ce36289b97d85d90a9582c9cae5
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375984"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Függőségi befecskendezés használata a .NET-Azure Functions

A Azure Functions támogatja a függőségi injektálás (DI) szoftver kialakítási mintáját, amely az osztályok és a függőségek közötti [vezérlés (NOB)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elérését szolgáló módszer.

A Azure Functions a ASP.NET Core függőségek befecskendezési funkcióinak tetejére épül. A [ASP.net Core függőségi injekció](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) szolgáltatásainak, élettartamának és kialakítási mintáinak ismerete a Azure functions alkalmazásban található di-funkciók használata előtt ajánlott.

A függőségi befecskendezés támogatása Azure Functions 2. x-vel kezdődik.

## <a name="prerequisites"></a>Előfeltételek

A függőségi befecskendezés használata előtt telepítenie kell a következő NuGet-csomagokat:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- A [Microsoft. net. SDK. functions csomag](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 vagy újabb verziója

- Nem kötelező: A [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) csak indításkor szükséges a HttpClient regisztrálásához

## <a name="register-services"></a>Szolgáltatások regisztrálása

A szolgáltatások regisztrálásához létrehozhat egy metódust a `IFunctionsHostBuilder` példányok konfigurálásához és az összetevők hozzáadásához.  A Azure functions gazdagép létrehoz egy példányt, `IFunctionsHostBuilder` és közvetlenül a metódusba továbbítja azokat.

A metódus regisztrálásához adja hozzá `FunctionsStartup` azt a Assembly attribútumot, amely megadja az indításkor használt típus nevét. A kód emellett a [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) előzetes kiadására hivatkozik a Nuget.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Beinjektált függőségek használata

ASP.NET Core a konstruktor injekcióját használja, hogy a függőségek elérhetők legyenek a függvény számára. Az alábbi minta azt mutatja be, `IMyService` hogyan `HttpClient` történik a és a függőségek beadása egy http-triggerrel elindított függvénybe. 

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

A konstruktor-injektálás használata azt jelenti, hogy nem használhat statikus függvényeket, ha szeretné kihasználni a függőségi injekciót. A Cosmos-ügyfél [ezt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/CodeSamples/AzureFunctions/AzureFunctionsCosmosClient.cs)ajánlja.

## <a name="service-lifetimes"></a>Szolgáltatás élettartama

Azure Functions alkalmazások ugyanazt a szolgáltatási élettartamot biztosítják, mint a [ASP.net függőségi befecskendezés](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): átmeneti, hatókörű és egyedi.

A functions alkalmazásban a hatókörön belüli szolgáltatás élettartama megegyezik a függvény végrehajtási élettartamával. A hatókörrel rendelkező szolgáltatások végrehajtáskor egyszer jönnek létre. A szolgáltatás későbbi kérelmei a végrehajtás során újra felhasználják a meglévő szolgáltatást. Az egyszeres szolgáltatás élettartama megegyezik a gazdagép élettartamával, és az adott példányon végrehajtott függvények végrehajtása során újra felhasználja őket.

Az egyedi élettartamú szolgáltatások a kapcsolatok és az ügyfelek, például `SqlConnection` `CloudBlobClient`, vagy `HttpClient` példányok esetén ajánlottak.

A GitHubon megtekintheti és letöltheti a [különböző szolgáltatási élettartamokat tartalmazó mintát](https://aka.ms/functions/di-sample) .

## <a name="logging-services"></a>Naplózási szolgáltatások

Ha saját naplózási szolgáltatóra van szüksége, az ajánlott módszer egy `ILoggerProvider` példány regisztrálása. A Application Insights Azure Functions automatikusan hozzáadja.

> [!WARNING]
> Ne adja hozzá `AddApplicationInsightsTelemetry()` a szolgáltatások gyűjteményhez, mert regisztrálja azokat a szolgáltatásokat, amelyek ütköznek a környezet által nyújtott szolgáltatásokkal.

## <a name="function-app-provided-services"></a>A függvény által biztosított szolgáltatások

A Function Host számos szolgáltatást regisztrál. A következő szolgáltatások az alkalmazástól való függőségként is biztonságosak:

|Service Type|Élettartama|Leírás|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Futásidejű konfiguráció|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|A gazdagép-példány AZONOSÍTÓjának biztosításáért felelős|

Ha vannak olyan szolgáltatások, amelyeknek függőségi viszonyra van szüksége, [hozzon létre egy problémát, és javasolja őket a githubon](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Gazdagép-szolgáltatások felülbírálása

A gazdagép által nyújtott szolgáltatások felülbírálása jelenleg nem támogatott.  Ha vannak olyan szolgáltatások, amelyeket felül szeretne bírálni, [hozzon létre egy problémát, és javasolja őket a githubon](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

- [A Function-alkalmazás figyelése](functions-monitoring.md)
- [Ajánlott eljárások a functions szolgáltatáshoz](functions-best-practices.md)
