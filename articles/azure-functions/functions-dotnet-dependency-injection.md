---
title: Függőséginjektálás használata a .NET Azure Functionsben
description: Ismerje meg, hogyan használhatja a függőségi befecskendezést a szolgáltatások .NET-függvényekbe való regisztrálásához és használatához
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678443"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Függőséginjektálás használata a .NET Azure Functionsben

A Azure Functions támogatja a függőségi injektálás (DI) szoftver kialakítási mintáját, amely az osztályok és a függőségek közötti [vezérlés (NOB)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elérését szolgáló módszer.

- A függőség injekciója Azure Functions a .NET Core függőségi injekciós funkciókra épül. A [.net Core-függőségek injektálásának](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) ismerete ajánlott. A függőségek felülbírálása és a konfigurációs értékek beolvasása a Azure Functions a használati tervben eltéréseket mutat.

- A függőségi befecskendezés támogatása Azure Functions 2. x-vel kezdődik.

## <a name="prerequisites"></a>Előfeltételek

A függőségi befecskendezés használata előtt telepítenie kell a következő NuGet-csomagokat:

- [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- A [Microsoft. net. SDK. functions csomag](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 vagy újabb verziója

## <a name="register-services"></a>Szolgáltatások regisztrálása

A szolgáltatások regisztrálásához hozzon létre egy metódust a `IFunctionsHostBuilder` példányok konfigurálásához és az összetevők hozzáadásához.  A Azure Functions gazdagép létrehoz egy példányt, `IFunctionsHostBuilder` és közvetlenül a metódusba továbbítja azokat.

A metódus regisztrálásához adja hozzá azt `FunctionsStartup` a Assembly attribútumot, amely megadja az indításkor használt típus nevét.

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

- *Az indítási osztály csak a beállítás és a regisztráció céljára szolgál.* Ne használja az indításkor regisztrált szolgáltatásokat az indítási folyamat során. Például ne próbáljon naplózni egy olyan üzenetet, amely az indítás során regisztrálva van. A regisztrációs folyamat ezen pontja túl korai ahhoz, hogy a szolgáltatások elérhetők legyenek. A `Configure` metódus futtatása után a functions Runtime továbbra is regisztrálja a további függőségeket, ami befolyásolhatja a szolgáltatások működését.

- *A függőségi injektálási tároló csak explicit módon regisztrált típusokat*tartalmaz. A `Configure` metódusban csak az injekciós típusként elérhető szolgáltatások vannak beállítva. Ennek eredményeképpen a functions-specifikus típusok, `BindingContext` például `ExecutionContext` nem érhetők el a telepítés során, vagy injektálható típusokként.

## <a name="use-injected-dependencies"></a>Beinjektált függőségek használata

A konstruktor-injektálás a függőségek elérhetővé tételéhez használható a függvényben. A konstruktor befecskendezésének használata megköveteli, hogy ne használjon statikus osztályokat.

Az alábbi minta azt mutatja be, `IMyService` hogyan `HttpClient` történik a és a függőségek beadása egy http-triggerrel elindított függvénybe. Ez a példa a [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) csomagot használja az indításhoz való regisztráláshoz `HttpClient` .

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
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
> - Ne adja hozzá `AddApplicationInsightsTelemetry()` a szolgáltatások gyűjteményhez, mert regisztrálja azokat a szolgáltatásokat, amelyek ütköznek a környezet által nyújtott szolgáltatásokkal.
> - Ne regisztrálja saját `TelemetryConfiguration` magát, `TelemetryClient` vagy ha a beépített Application Insights funkciót használja. Ha konfigurálnia kell a saját `TelemetryClient` példányát, hozzon létre egyet a `TelemetryConfiguration` befecskendezés használatával, ahogyan az [Azure functions figyelése](./functions-monitoring.md#version-2x-and-later-2)című részen látható.

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> és ILoggerFactory

A gazdagép a ( `ILogger<T>` z `ILoggerFactory` ) és a szolgáltatásokat a konstruktorokban fogja beadni.  Alapértelmezés szerint azonban ezeket az új naplózási szűrőket a rendszer kiszűri a függvények naplóiból.  A `host.json` fájl módosításával további szűrőket és kategóriákat is be kell állítani.  Az alábbi példa bemutatja, hogyan adhat `ILogger<HttpTrigger>` hozzá egy olyan naplókat, amelyeket a gazdagép tesz elérhetővé.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

És egy `host.json` olyan fájl, amely hozzáadja a napló szűrőt.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>A függvény által biztosított szolgáltatások

A Function Host számos szolgáltatást regisztrál. A következő szolgáltatások az alkalmazástól való függőségként is biztonságosak:

|Szolgáltatástípus|Élettartama|Leírás|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Futásidejű konfiguráció|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|A gazdagép-példány AZONOSÍTÓjának biztosításáért felelős|

Ha vannak olyan szolgáltatások, amelyeknek függőségi viszonyra van szüksége, [hozzon létre egy problémát, és javasolja őket a githubon](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Gazdagép-szolgáltatások felülbírálása

A gazdagép által nyújtott szolgáltatások felülbírálása jelenleg nem támogatott.  Ha vannak olyan szolgáltatások, amelyeket felül szeretne bírálni, [hozzon létre egy problémát, és javasolja őket a githubon](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Beállítások és beállítások használata

Az [Alkalmazásbeállítások](./functions-how-to-use-azure-function-app-settings.md#settings) által meghatározott értékek egy `IConfiguration` példányban érhetők el, amely lehetővé teszi az alkalmazás-beállítási értékek olvasását az indítási osztályban.

A `IConfiguration` példány értékeit egyéni típusba is kinyerheti. Ha az Alkalmazásbeállítások értékeit egyéni típusra másolja, a szolgáltatás egyszerűen tesztelhető, így ezek az értékek injektálható. A konfigurációs példányba beolvasott beállításoknak egyszerű kulcs/érték pároknak kell lenniük.

Vegye figyelembe a következő osztályt, amely egy konzisztens nevű tulajdonságot tartalmaz egy alkalmazás-beállítással:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Valamint egy `local.settings.json` olyan fájl, amely az egyéni beállítást a következőképpen strukturálhatja:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

A `Startup.Configure` metódusból kinyerheti a `IConfiguration` példány értékeit az egyéni típusba a következő kód használatával:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Meghívja `Bind` azokat az értékeket, amelyek a konfigurációból az egyéni példányba egyező tulajdonságokat tartalmaznak. A beállítások példány mostantól elérhető a NOB-tárolóban egy függvénybe való behelyezéshez.

A beállítások objektumot az általános `IOptions` felület egy példánya fecskendezi a függvénybe. A `Value` tulajdonság használatával érheti el a konfigurációban található értékeket.

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

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

- [A Function-alkalmazás figyelése](functions-monitoring.md)
- [Ajánlott eljárások a functions szolgáltatáshoz](functions-best-practices.md)
