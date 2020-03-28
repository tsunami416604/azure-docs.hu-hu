---
title: Oktatóanyag a .NET Core alkalmazás funkciójelzőinek használatához | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan valósíthatja meg a funkciójelzőket a .NET Core alkalmazásokban.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 3182961f928a9befc5a55fb6d58e22c74ba81089
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79473438"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Oktatóanyag: Szolgáltatásjelzők használata ASP.NET Core alkalmazásban

A .NET Core Feature Management kódtárak idiomatic támogatást nyújtanak a .NET vagy ASP.NET Core alkalmazások szolgáltatásjelzőinek megvalósításához. Ezek a tárak lehetővé teszik, hogy deklaratív módon add `if` funkció jelzők a kódot, így nem kell írni az összes utasítást nekik manuálisan.

A Szolgáltatáskezelő kódtárak is kezelheti a funkciók jelzőéletciklusát a színfalak mögött. Például a könyvtárak frissítik és gyorsítótárazási jelző állapotai, vagy garantálja a zászló állapot nem módosítható kérés hívás közben. Emellett a ASP.NET Core könyvtár beépített integrációt is kínál, beleértve az MVC vezérlőműveleteket, nézeteket, útvonalakat és köztes szoftvereket.

A [Funkciójelzők hozzáadása egy ASP.NET Core alkalmazáshoz rövid útmutató](./quickstart-feature-flag-aspnet-core.md) számos módot mutat be a szolgáltatásjelzők hozzáadására egy ASP.NET Core alkalmazásban. Ez az oktatóanyag részletesebben ismerteti ezeket a módszereket. A teljes körű hivatkozást a [ASP.NET Core szolgáltatásfelügyeleti dokumentációjában](https://go.microsoft.com/fwlink/?linkid=2091410)találja.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A szolgáltatás elérhetőségének szabályozásához adja hozzá a szolgáltatásjelzőket az alkalmazás kulcsfontosságú részeihez.
> * Integrálja az alkalmazáskonfigurációval, amikor a funkciójelzők kezelésére használja.

## <a name="set-up-feature-management"></a>Szolgáltatáskezelés beállítása

Adjon hozzá hivatkozást a `Microsoft.FeatureManagement` NuGet csomaghoz a .NET Core szolgáltatáskezelő használatához.
    
A . `IFeatureManager` Ennek eredményeképpen az alkalmazás szolgáltatásjelzőit a .NET Core által támogatott konfigurációs forrás használatával határozhatja meg, beleértve a helyi *appsettings.json* fájlt vagy a környezeti változókat. `IFeatureManager`a .NET Core függőségi injektálásra támaszkodik. A szolgáltatásfelügyeleti szolgáltatásokat szabványos konvenciók használatával regisztrálhatja:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Alapértelmezés szerint a szolgáltatáskezelő lekéri `"FeatureManagement"` a szolgáltatásjelzőket a .NET Core konfigurációs adatok szakaszából. A következő példa arra utasítja a szolgáltatáskezelőt, hogy olvasson egy másik, ehelyett hívott `"MyFeatureFlags"` szakaszból:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Ha szűrőket használ a szolgáltatásjelzőkben, egy további könyvtárat kell megadnia, és regisztrálnia kell azt. A következő példa bemutatja, hogyan kell `PercentageFilter`használni a beépített szolgáltatásszűrő nevű:

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Azt javasoljuk, hogy tartsa a szolgáltatásjelzők az alkalmazáson kívül, és kezelje őket külön-külön. Ezzel bármikor módosíthatja a jelzőállapotokat, és azonnal életbe léptetheti ezeket a módosításokat az alkalmazásban. Az Alkalmazáskonfiguráció központosított helyet biztosít az összes funkciójelző rendszerezéséhez és vezérléséhez egy dedikált portál felhasználói felületén keresztül. Az Alkalmazáskonfiguráció a jelzőket közvetlenül a .NET Core ügyfélkódtárakon keresztül is eljuttatja az alkalmazáshoz.

A ASP.NET Core alkalmazás alkalmazáskonfigurációhoz való csatlakoztatásának `Microsoft.Azure.AppConfiguration.AspNetCore`legegyszerűbb módja a konfigurációs szolgáltatón keresztül történik. A NuGet csomag használatához kövesse az alábbi lépéseket.

1. Nyissa *meg Program.cs* fájlt, és adja hozzá a következő kódot.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. *Nyissa meg Startup.cs,* és frissítse a `Configure` módszert egy köztes szoftver hozzáadásához, hogy a szolgáltatásjelző értékei ismétlődő időközönként frissülhessenek, miközben a ASP.NET Core webalkalmazás továbbra is fogadja a kéréseket.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

A funkciójelző konklisen-értékei várhatóan idővel változnak. Alapértelmezés szerint a szolgáltatásjelző értékei 30 másodpercig gyorsítótárazva vannak, így a köztes szoftver kérésének fogadásakor végrehajtott frissítési művelet nem frissíti az értéket, amíg a gyorsítótárazott érték le nem jár. A következő kód bemutatja, hogyan módosíthatja a gyorsítótár `options.UseFeatureFlags()` lejárati idejét vagy a lekérdezési időközt 5 percre a hívásban.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Szolgáltatásjelző deklarációja

Minden jellemzőjelzőnek két része van: egy név ből és egy vagy több szűrőből álló listából, amely `True`annak megállapítására szolgál, hogy egy szolgáltatás állapota be van-e *kapcsolva* (azaz ha az értéke). A szűrő azt a használati esetet határozza meg, hogy mikor kell egy szolgáltatást bekapcsolni.

Ha egy szolgáltatásjelző több szűrővel rendelkezik, a szűrőlista sorrendben halad át, amíg az egyik szűrő meg nem határozza, hogy engedélyezni kell a szolgáltatást. Ezen a ponton a szolgáltatásjelző be van *kapcsolva,* és a fennmaradó szűrőeredmények kimaradnak. Ha nincs szűrő, akkor a szolgáltatást engedélyezni kell, akkor a szolgáltatásjelző ki van *kapcsolva.*

A szolgáltatáskezelő támogatja *az appsettings.json-t* a szolgáltatásjelzők konfigurációs forrásaként. A következő példa bemutatja, hogyan állíthat be funkciójelzőket egy JSON-fájlban:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

A JSON-dokumentum szakasza `FeatureManagement` konvenció szerint a szolgáltatásjelző-beállításokhoz használatos. Az előző példában három jellemzőjelző látható `EnabledFor` a tulajdonságban definiált szűrőkkel:

* `FeatureA`be van *kapcsolva.*
* `FeatureB`ki van *kapcsolva.*
* `FeatureC`tulajdonsággal elnevezett `Percentage` szűrőt `Parameters` ad meg. `Percentage`egy konfigurálható szűrő. Ebben a `Percentage` példában 50 százalékos valószínűséget `FeatureC` ad meg a *jelzőnek.*

## <a name="feature-flag-references"></a>Jellemzőjelző-hivatkozások

Annak lehetővé teszi, hogy könnyen hivatkozhat a `enum` kódban lévő jellemzőjelzőkre, meg kell határoznia őket változóként:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Jellemzőjelző-ellenőrzések

A szolgáltatáskezelés alapvető mintája az, hogy először ellenőrizze, hogy egy jellemzőjelző be *van-e*állítva . Ha igen, a szolgáltatáskezelő ezután futtatja a funkció ban található műveleteket. Példa:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Függőséginjektálás

A core MVC ASP.NET függőségi `IFeatureManager` injektálással érheti el a szolgáltatáskezelőt:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Vezérlő műveletek

Az MVC-vezérlőkben az `FeatureGate` attribútum segítségével szabályozhatja, hogy egy teljes vezérlőosztály vagy egy adott művelet engedélyezve van-e. A `HomeController` következő `FeatureA` vezérlőnek be kell *kapcsolva* lennie, mielőtt a vezérlőosztály által tartalmazott műveletek végrehajthatók lennének:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A `Index` következő `FeatureA` műveletnek be kell *kapcsolva* a futtatáshoz:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Ha egy MVC-vezérlő vagy művelet le van tiltva, mert a vezérlő funkciójelző ki van *kapcsolva,* a rendszer regisztrált `IDisabledFeaturesHandler` felületet hív meg. Az `IDisabledFeaturesHandler` alapértelmezett felület egy 404-es állapotkódot ad vissza az ügyfélnek választörzs nélkül.

## <a name="mvc-views"></a>MVC nézetek

MVC nézetekben `<feature>` a tartalom megjelenítéséhez címke használható attól függően, hogy engedélyezve van-e egy szolgáltatásjelző:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Az alternatív tartalom megjelenítéséhez, ha `negate` a követelmények nem teljesülnek, az attribútum használható.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

A `<feature>` szolgáltatáscímke akkor is használható a tartalom megjelenítésére, ha a lista bármely vagy összes szolgáltatása engedélyezve van.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC szűrők

Az MVC-szűrőket beállíthatja úgy, hogy a szolgáltatásjelző állapota alapján aktiválódjanak. A következő kód hozzáad `SomeMvcFilter`egy MVC szűrőt, amelynek neve . Ez a szűrő csak akkor aktiválódik `FeatureA` az MVC-folyamaton belül, ha engedélyezve van. Ez a képesség `IAsyncActionFilter`a. 

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="middleware"></a>Middleware

A szolgáltatásjelzők segítségével feltételesen is hozzáadhat alkalmazáságakat és köztes szoftvereket. A következő kód csak akkor szúr be köztes szoftverösszetevőt a kérelemfolyamatba, ha `FeatureA` engedélyezve van:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Ez a kód a teljes alkalmazás szolgáltatásjelző alapján történő elágazásának általánosabb képességéből épül fel:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan valósíthatja meg a `Microsoft.FeatureManagement` szolgáltatásjelzőket a ASP.NET Core alkalmazásban a könyvtárak használatával. A ASP.NET Core és az Alkalmazáskonfiguráció szolgáltatáskezelési támogatásáról az alábbi forrásokban talál további információt:

* [ASP.NET Core szolgáltatásjelző mintakódja](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement dokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Funkciójelölők kezelése](./manage-feature-flags.md)
