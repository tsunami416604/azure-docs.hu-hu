---
title: Függőséginjektálás használata a .NET Azure Functionsben
description: A függőségi injektálás használata a szolgáltatások regisztrálásához és használatához a .NET függvényekben
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678443"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Függőséginjektálás használata a .NET Azure Functionsben

Az Azure Functions támogatja a függőségi injektálási (DI) szoftvertervezési mintát, amely az osztályok és függőségeik közötti [Inverzió (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) elérésének technikája.

- Függőségi injektálás az Azure Functions ben épül a .NET core függőségi injektálás funkciók. A [.NET Core függőségi injektálás](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) ismerete ajánlott. Különbségek vannak abban, hogy hogyan bírálja felül a függőségeket, és hogyan olvassa be a konfigurációs értékeket az Azure Functions a felhasználási terv.

- A függőségi injektálás támogatása az Azure Functions 2.x-szel kezdődik.

## <a name="prerequisites"></a>Előfeltételek

A függőségi injektálás használata előtt telepítenie kell a következő NuGet csomagokat:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions csomag](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28-as vagy újabb verzió

## <a name="register-services"></a>Szolgáltatások regisztrálása

Szolgáltatások regisztrálásához hozzon létre egy módszert egy `IFunctionsHostBuilder` példány konfigurálására és összetevők hozzáadására.  Az Azure Functions gazdagép `IFunctionsHostBuilder` létrehoz egy példányt, és továbbítja azt közvetlenül a metódusba.

A metódus regisztrálásához `FunctionsStartup` adja hozzá az indításkor használt típusnevet megadó összeállítási attribútumot.

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

### <a name="caveats"></a>Ellenmondások

A futásidejű folyamatok előtt és után futó regisztrációs lépések sorozata fut. Ezért tartsa szem előtt a következő elemeket:

- *Az indítási osztály csak beállításra és regisztrációra szolgál.* Kerülje az indításkor regisztrált szolgáltatások használatát az indítási folyamat során. Például ne próbáljon meg naplózni egy üzenetet egy olyan naplózóban, amely et az indítás során regisztrálnak. A regisztrációs folyamat ezen pontja túl korai ahhoz, hogy a szolgáltatások elérhetők legyenek. A `Configure` metódus futtatása után a Functions futásidejű továbbra is regisztrálja a további függőségeket, amelyek befolyásolhatják a szolgáltatások működését.

- *A függőségi injektálási tároló csak kifejezetten regisztrált típusokat tartalmaz.* Az egyetlen elérhető szolgáltatás injekciós típusként `Configure` az, ami a módszerben be van állítva. Ennek eredményeképpen a funkciók-specifikus `ExecutionContext` típusok, például `BindingContext` a telepítés során vagy injekciós típusként nem érhetők el.

## <a name="use-injected-dependencies"></a>Injektált függőségek használata

Konstruktor injekció segítségével, hogy a függőségek elérhetővé egy funkciót. A konstruktor befecskendezés etekintetben megköveteli, hogy ne használjon statikus osztályokat.

Az alábbi minta `IMyService` bemutatja, hogyan injektálják a függőségeket és `HttpClient` a függőségeket egy HTTP-aktivált függvénybe. Ez a példa a [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) `HttpClient` csomagot használja, amely indításkor regisztráláshoz szükséges.

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

## <a name="service-lifetimes"></a>A szolgáltatás élettartama

Az Azure Functions-alkalmazások ugyanazokat a szolgáltatásélettartamokat biztosítják, mint [ASP.NET függőségi injektálás.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) A Functions alkalmazások esetében a különböző szolgáltatásélettartamok a következőképpen viselkednek:

- **Átmeneti**: A szolgáltatás minden egyes kérésére átmeneti szolgáltatások jönnek létre.
- **Hatókörrel:** A hatókörrel tartozó szolgáltatás élettartama megegyezik a függvény végrehajtási élettartama. A hatókörrel létrehozott szolgáltatások végrehajtásonként egyszer jönnek létre. A szolgáltatás későbbi kérései a végrehajtás során újrafelhasználják a meglévő szolgáltatáspéldányt.
- **Singleton:** A singleton szolgáltatás élettartama megegyezik a gazdagép élettartama, és újra fel kell használni a függvény-végrehajtások az adott példányon. Singleton élettartam-szolgáltatások ajánlott kapcsolatok és `SqlConnection` az `HttpClient` ügyfelek, például vagy példányok.

Tekintse meg vagy töltse le [a githubon a különböző szolgáltatásélettartamok mintáját.](https://aka.ms/functions/di-sample)

## <a name="logging-services"></a>Naplózási szolgáltatások

Ha saját naplózási szolgáltatóra van szüksége, `ILoggerProvider` regisztráljon egy egyéni típust példányként. Az Application Insights automatikusan hozzáadódik az Azure Functions hez.

> [!WARNING]
> - Ne adja `AddApplicationInsightsTelemetry()` hozzá a szolgáltatásgyűjteményhez, mert regisztrálja azokat a szolgáltatásokat, amelyek ütköznek a környezet által nyújtott szolgáltatásokkal.
> - Ne regisztrálja `TelemetryConfiguration` a `TelemetryClient` saját, vagy ha a beépített Application Insights funkciót használ. Ha saját `TelemetryClient` példányt kell konfigurálnia, `TelemetryConfiguration` hozzon létre egyet az injektált módon, ahogy az [az Azure Functions figyelőjében](./functions-monitoring.md#version-2x-and-later-2)látható.

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> és ILoggerFactory

A gazdagép `ILogger<T>` `ILoggerFactory` befecskendezi és szolgáltatásokat nyújt a konstruktorokba.  Alapértelmezés szerint azonban ezek az új naplózási szűrők kilesznek szűrve a függvénynaplókból.  A további szűrők `host.json` és kategóriák beállításához módosítania kell a fájlt.  A következő minta `ILogger<HttpTrigger>` bemutatja egy a gazdagép által elérhetővé tett naplók hozzáadását.

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

És `host.json` egy fájl, amely hozzáadja a naplószűrőt.

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

## <a name="function-app-provided-services"></a>A függvényalkalmazás által nyújtott szolgáltatások

A függvényállomás számos szolgáltatást regisztrál. A következő szolgáltatások biztonságosan figyelembe függőségaz alkalmazásban:

|Szolgáltatástípus|Életre|Leírás|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Futásidejű konfiguráció|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|A gazdapéldány azonosítójának megadásáért felelős|

Ha vannak más szolgáltatások is, amelyektől függőséget szeretne igénybe venni, [hozzon létre egy problémát, és javasoljon azokat a GitHubon.](https://github.com/azure/azure-functions-host)

### <a name="overriding-host-services"></a>A gazdaszolgáltatások felülbírálása

A gazdagép által nyújtott felülbírálási szolgáltatások jelenleg nem támogatottak.  Ha vannak olyan szolgáltatások, amelyeket felül szeretne bírni, [hozzon létre egy problémát, és javasoljon azokat a GitHubon.](https://github.com/azure/azure-functions-host)

## <a name="working-with-options-and-settings"></a>A beállítások és beállítások használata

Az [alkalmazásbeállításokban](./functions-how-to-use-azure-function-app-settings.md#settings) definiált értékek `IConfiguration` egy példányban érhetők el, amely lehetővé teszi az alkalmazásbeállítási értékek olvasását az indítási osztályban.

A `IConfiguration` példány értékeit egyéni típusba bonthatja ki. Az alkalmazásbeállítási értékek egyéni típusba másolása megkönnyíti a szolgáltatások tesztelését azáltal, hogy ezeket az értékeket injektálóvá teszi. A konfigurációs példányba beolvasott beállításoknak egyszerű kulcs-/értékpároknak kell lenniük.

Vegye figyelembe a következő osztályt, amely egy alkalmazásbeállítással megegyező nevű tulajdonságot tartalmaz:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

És `local.settings.json` egy fájl, amely az egyéni beállítást a következőképpen strukturálhatja:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

A `Startup.Configure` metóduson belül a következő `IConfiguration` kód használatával kinyerheti a példány értékeit az egyéni típusba:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

A `Bind` hívás olyan értékeket másol, amelyek a konfigurációból egyező tulajdonságnevekkel rendelkeznek az egyéni példányba. A beállítások példánya már elérhető az IoC-tárolóban egy függvény befecskendezéséhez.

A beállítások objektuma az általános `IOptions` felület példányaként kerül a függvénybe. A `Value` tulajdonság segítségével érheti el a konfigurációban található értékeket.

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

A beállításokkal kapcsolatos további részletekért tekintse meg [ASP.NET A ASP.NET Core beállítási mintáját.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

> [!WARNING]
> Ne próbáljon értékeket olvasni olyan fájlokból, mint *a local.settings.json* vagy *az appsettings.{ environment}.json* a felhasználási tervben. Az eseményindító kapcsolatokhoz kapcsolódó fájlokból beolvasott értékek nem érhetők el, mivel az alkalmazás méretezi, mert a gazdainfrastruktúra nem fér hozzá a konfigurációs adatokhoz.

## <a name="next-steps"></a>További lépések

További információkért lásd a következőket:

- [A függvényalkalmazás figyelése](functions-monitoring.md)
- [A függvények gyakorlati tanácsai](functions-best-practices.md)
