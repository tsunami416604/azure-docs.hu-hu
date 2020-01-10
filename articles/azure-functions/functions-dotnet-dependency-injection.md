---
title: Függőségi befecskendezés használata a .NET-Azure Functions
description: Ismerje meg, hogyan használhatja a függőségi befecskendezést a szolgáltatások .NET-függvényekbe való regisztrálásához és használatához
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 4c6ccf9dce0fc119bd666871489a42a3ef734f81
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769200"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Függőségi befecskendezés használata a .NET-Azure Functions

A Azure Functions támogatja a függőségi injektálás (DI) szoftver kialakítási mintáját, amely az osztályok és a függőségek közötti [vezérlés (NOB)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elérését szolgáló módszer.

- A függőség injekciója Azure Functions a .NET Core függőségi injekciós funkciókra épül. A [.net Core-függőség injektálásának](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) ismerete ajánlott. A függőségek felülbírálása és a konfigurációs értékek beolvasása a használati terv Azure Functions alapján azonban eltérő.

- A függőségi befecskendezés támogatása Azure Functions 2. x-vel kezdődik.

## <a name="prerequisites"></a>Előfeltételek

A függőségi befecskendezés használata előtt telepítenie kell a következő NuGet-csomagokat:

- [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- A [Microsoft. net. SDK. functions csomag](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 vagy újabb verziója

## <a name="register-services"></a>Szolgáltatások regisztrálása

A szolgáltatások regisztrálásához hozzon létre egy metódust egy `IFunctionsHostBuilder`-példányhoz tartozó összetevők konfigurálásához és hozzáadásához.  A Azure Functions gazdagép létrehoz egy `IFunctionsHostBuilder` egy példányát, és közvetlenül a metódusba továbbítja azokat.

A metódus regisztrálásához adja hozzá a `FunctionsStartup` Assembly attribútumot, amely megadja az indításkor használt típus nevét.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Figyelmeztetések

A Futtatás előtt és után futtatott regisztrációs lépések sorozata az indítási osztályt dolgozza fel. Ezért ne feledje a következő elemeket:

- *Az indítási osztály csak a beállítás és a regisztráció céljára szolgál.* Ne használja az indításkor regisztrált szolgáltatásokat az indítási folyamat során. Például ne próbáljon naplózni egy olyan üzenetet, amely az indítás során regisztrálva van. A regisztrációs folyamat ezen pontja túl korai ahhoz, hogy a szolgáltatások elérhetők legyenek. A `Configure` metódus futtatása után a functions Runtime továbbra is regisztrál további függőségeket, ami befolyásolhatja a szolgáltatások működését.

- *A függőségi injektálási tároló csak explicit módon regisztrált típusokat*tartalmaz. A `Configure` metódusban csak az injekciós típusként elérhető szolgáltatások vannak beállítva. Ennek eredményeképpen a functions-specifikus típusok, például a `BindingContext` és az `ExecutionContext` nem érhetők el a telepítés során vagy injekciós típusokként.

## <a name="use-injected-dependencies"></a>Beinjektált függőségek használata

A konstruktor-injektálás a függőségek elérhetővé tételéhez használható a függvényben. A konstruktor befecskendezésének használata megköveteli, hogy ne használjon statikus osztályokat.

Az alábbi minta azt mutatja be, hogy a rendszer hogyan fecskendez be a `IMyService` és `HttpClient` függőségeket egy HTTP-triggert igénylő függvénybe. Ez a példa a [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) csomagot használja, amely a `HttpClient` indításkor való regisztrálásához szükséges.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

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

## <a name="service-lifetimes"></a>Szolgáltatás élettartama

Azure Functions alkalmazások ugyanazt a szolgáltatási élettartamot biztosítják, mint a [ASP.net függőségi injekció](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). A functions alkalmazás esetében a különböző szolgáltatási élettartamok a következőképpen viselkednek:

- **Átmeneti**: az átmeneti szolgáltatások a szolgáltatás minden egyes kérelme alapján jönnek létre.
- **Hatókörön**belüli: a hatókörön belüli szolgáltatás élettartama megfelel a függvény végrehajtási élettartamának. A hatókörrel rendelkező szolgáltatások végrehajtáskor egyszer jönnek létre. A szolgáltatás későbbi kérelmei a végrehajtás során újra felhasználják a meglévő szolgáltatást.
- **Egyszeres**: az egyszeres szolgáltatás élettartama megegyezik a gazdagép élettartamával, és az adott példányon végrehajtott függvények végrehajtása során újra felhasználja őket. Az egyedi élettartamú szolgáltatások a kapcsolatok és az ügyfelek számára ajánlottak, például `SqlConnection` vagy `HttpClient` példányok esetén.

A GitHubon megtekintheti és letöltheti a [különböző szolgáltatási élettartamokat tartalmazó mintát](https://aka.ms/functions/di-sample) .

## <a name="logging-services"></a>Naplózási szolgáltatások

Ha saját naplózási szolgáltatóra van szüksége, regisztráljon egy egyéni típust `ILoggerProvider` példányként. A Application Insights Azure Functions automatikusan hozzáadja.

> [!WARNING]
> - Ne vegyen fel `AddApplicationInsightsTelemetry()` a szolgáltatások gyűjteménybe, mert regisztrálja azokat a szolgáltatásokat, amelyek ütköznek a környezet által nyújtott szolgáltatásokkal.
> - Ha beépített Application Insights funkciót használ, ne regisztráljon saját `TelemetryConfiguration` vagy `TelemetryClient`.

## <a name="function-app-provided-services"></a>A függvény által biztosított szolgáltatások

A Function Host számos szolgáltatást regisztrál. A következő szolgáltatások az alkalmazástól való függőségként is biztonságosak:

|Szolgáltatás típusa|Élettartama|Leírás|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Futásidejű konfiguráció|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|A gazdagép-példány AZONOSÍTÓjának biztosításáért felelős|

Ha vannak olyan szolgáltatások, amelyeknek függőségi viszonyra van szüksége, [hozzon létre egy problémát, és javasolja őket a githubon](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Gazdagép-szolgáltatások felülbírálása

A gazdagép által nyújtott szolgáltatások felülbírálása jelenleg nem támogatott.  Ha vannak olyan szolgáltatások, amelyeket felül szeretne bírálni, [hozzon létre egy problémát, és javasolja őket a githubon](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Beállítások és beállítások használata

Az [Alkalmazásbeállítások](./functions-how-to-use-azure-function-app-settings.md#settings) által meghatározott értékek egy `IConfiguration` példányban érhetők el, amely lehetővé teszi az Alkalmazásbeállítások értékének olvasását az indítási osztályban.

Az `IConfiguration` példány értékeit egyéni típusba is kinyerheti. Ha az Alkalmazásbeállítások értékeit egyéni típusra másolja, a szolgáltatás egyszerűen tesztelhető, így ezek az értékek injektálható. A konfigurációs példányba beolvasott beállításoknak egyszerű kulcs/érték pároknak kell lenniük.

Vegye figyelembe a következő osztályt, amely egy konzisztens nevű tulajdonságot tartalmaz egy alkalmazás-beállítással.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

A `Startup.Configure` metódusból kinyerheti a `IConfiguration` példány értékeit az egyéni típusba a következő kód használatával:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

A rendszer meghívja a konfigurációban szereplő, az egyéni példányba tartozó tulajdonságokat tartalmazó `Bind`-példányok értékeit. A beállítások példány mostantól elérhető a NOB-tárolóban egy függvénybe való behelyezéshez.

A Options objektumot az általános `IOptions` felület példánya fecskendezi a függvénybe. A konfigurációban található értékek eléréséhez használja a `Value` tulajdonságot.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

A beállításokkal kapcsolatos további részletekért tekintse meg a [ASP.net Core beállítások mintáját](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) .

> [!WARNING]
> Kerülje az értékek olvasását olyan fájlokból, mint például a *Local. Settings. JSON* vagy a *appSettings. { Environment}. JSON* a használati tervben. Az elindító kapcsolatokhoz kapcsolódó fájlokból beolvasott értékek nem érhetők el az alkalmazás skálázása miatt, mert az üzemeltetési infrastruktúra nem fér hozzá a konfigurációs adatokhoz.

## <a name="next-steps"></a>Következő lépések

További információkért lásd a következőket:

- [A Function-alkalmazás figyelése](functions-monitoring.md)
- [Ajánlott eljárások a functions szolgáltatáshoz](functions-best-practices.md)
