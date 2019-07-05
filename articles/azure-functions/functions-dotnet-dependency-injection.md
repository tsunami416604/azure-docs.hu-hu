---
title: Függőségi beszúrást használata a .NET Azure Functions szolgáltatásban
description: Megtudhatja, hogyan használja a függőségi beszúrást regisztrálásához és a szolgáltatások használata a .NET-es függvényeket
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: az Azure functions, függvények, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, cshoe
ms.openlocfilehash: 781bcdc158cb362b7c46e1ba9771b6a92ebc56a8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479617"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Függőségi beszúrást használata a .NET Azure Functions szolgáltatásban

Az Azure Functions támogatja a függőségi injektálási (DI) szoftver tervezési mintát, amely a technika eléréséhez [vezérlő invertálásának (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) osztályok és a függőségek között.

Az Azure Functions az ASP.NET Core függőségi beszúrást funkciók épül. Szolgáltatások élettartam és tervezési mintáinak ismerete [ASP.NET Core függőségi beszúrást](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) DI szolgáltatásainak használata az Azure Functions előtt javasolt a alkalmazás.

A függőségi beszúrást megkezdi az Azure Functions támogatása 2.x.

## <a name="prerequisites"></a>Előfeltételek

Függőségi beszúrást használata előtt telepítenie kell a következő NuGet-csomagokat:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions csomag](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 verzió vagy újabb

- Nem kötelező: [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) csak indításkor HttpClient regisztrálásához szükséges

## <a name="register-services"></a>Szolgáltatások regisztrálása

Szolgáltatások regisztrálása, konfigurálásához, és adjon hozzá összetevőket metódus segítségével létrehozhat egy `IFunctionsHostBuilder` példány.  Az Azure Functions állomás létrehoz egy példányt a `IFunctionsHostBuilder` és továbbadja azt közvetlenül a metódus.

A módszer regisztrálásához adja hozzá a `FunctionsStartup` szerelvény attribútum, amely meghatározza a típusnév indítása során használt. Kód is hivatkozik egy előzetes verzióját [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) nugeten.

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

## <a name="use-injected-dependencies"></a>Injektált-függőségek használatára

ASP.NET Core konstruktor injektálási használja a függőségek a függvény számára elérhetővé tenni. A következő minta bemutatja, hogyan a `IMyService` és `HttpClient` függőségek a rendszer kártevő program férkőzik be egy HTTP-eseményindítóval aktivált függvényt.

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
            _client = httpClientFactory.CreateClient();;
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

Konstruktor injektálási használatát, az azt jelenti, hogy ne használjon statikus funkciók Ha előnyeit függőségi beszúrást szeretné.

## <a name="service-lifetimes"></a>Szolgáltatás-élettartam

Az Azure Functions-alkalmazások, adja meg, az azonos szolgáltatás élettartamának [ASP.NET függőségi beszúrást](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): átmeneti, hatókörrel rendelkező, és egypéldányos.

A functions-alkalmazás egy hatókörrel rendelkező szolgáltatás élettartamának megegyezik a függvény végrehajtási idejének magyarázata. Hatókörön belüli szolgáltatások végrehajtásonkénti egyszer létrehozni. A végrehajtás során, hogy a szolgáltatás újabb kérelmek újból felhasználhatja a meglévő szolgáltatáspéldány. A singleton szolgáltatás élettartama megegyezik a gazdagép élettartama, és a függvénykivételek azon a példányon keresztül újrahasznosított.

Egyszeres élettartama szolgáltatások ajánlott kapcsolatok és az ügyfeleknek, például `SqlConnection`, `CloudBlobClient`, vagy `HttpClient` példányok.

Megtekintése vagy letöltése egy [különböző élettartam-minta](https://aka.ms/functions/di-sample) a Githubon.

## <a name="logging-services"></a>Naplózási szolgáltatások

Ha saját naplózási szolgáltatói van szüksége, ajánlott módja regisztrálni egy `ILoggerProvider` példány. Az Application Insights automatikusan hozzáadja az Azure Functions.

> [!WARNING]
> Ne adjon hozzá `AddApplicationInsightsTelemetry()` regisztrál, mert a szolgáltatások gyűjtemény adott ütközés services a környezet által biztosított szolgáltatásokkal.

## <a name="function-app-provided-services"></a>Függvény a megadott app servicesben

A függvény gazdagép számos szolgáltatás regisztrálja. A következő szolgáltatások biztonságosak az alkalmazás függőségei:

|Service Type|Élettartam|Leírás|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Egypéldányos|Futásidejű konfigurációja|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Egypéldányos|Biztosítja a gazdagép-példány azonosítója|

Ha azt szeretné, hogy más szolgáltatások [probléma létrehozása, és javaslatot őket a Githubon](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Szolgáltatások működtetéséhez felülbírálása

Felülbírálja a gazdagép által nyújtott szolgáltatások jelenleg nem támogatott.  Ha szeretné felülbírálni, szolgáltatást [probléma létrehozása, és javaslatot őket a Githubon](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

- [A függvényalkalmazás figyelése](functions-monitoring.md)
- [Functions – ajánlott eljárások](functions-best-practices.md)
