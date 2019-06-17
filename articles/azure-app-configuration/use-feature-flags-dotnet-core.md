---
title: A szolgáltatás jelzők egy .NET Core-alkalmazást az oktatóanyag |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan funkció jelzők bevezetése a .NET Core alkalmazásokat.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 577cb55ce381976a6d623b272b920d0d1bf2eeb9
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143999"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Oktatóanyag: A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás

A .NET Core szolgáltatás kezelési kódtárakat bármilyen támogatást nyújt a szolgáltatás jelzők megvalósítása az egy .NET- vagy ASP.NET Core-alkalmazás. Ezek a könyvtárak engedélyezése funkció jelzők deklaratív hozzáadását a kódhoz, hogy az összes nem kell a `if` utasítás a őket manuálisan.

A szolgáltatás felügyeleti könyvtárak funkció jelző életciklusának a háttérben is kezelheti. Például a könyvtárak frissítése és a gyorsítótár jelző állapotok vagy garantálja a kérelem-hívás közben nem módosítható kell egy jelző állapot. Az ASP.NET Core-kódtár emellett-a-beépített Integrációk, beleértve az MVC-vezérlő műveletek, nézetek, útvonalak és közbenső biztosít.

A [funkció jelzők hozzáadása ASP.NET Core-alkalmazás rövid](./quickstart-feature-flag-aspnet-core.md) funkció jelzők. beépülő ASP.NET Core alkalmazás különböző módszert mutat be. Ez az oktatóanyag ezen módszerek részletesebben ismerteti. Egy teljes körű referenciáért lásd: a [ASP.NET Core szolgáltatás management dokumentációja](https://go.microsoft.com/fwlink/?linkid=2091410).

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Adja hozzá a szolgáltatás jelzők funkció rendelkezésre állásának szabályozhatja az alkalmazás legfontosabb részeit.
> * Alkalmazáskonfiguráció integrálható, ha a szolgáltatás jelölők kezelése használ.

## <a name="set-up-feature-management"></a>A szolgáltatás felügyeletének beállítása

A .NET Core-szolgáltatás kezelő `IFeatureManager` funkció jelzők olvas be a keretrendszer natív konfigurációs rendszer. Ennek eredményeképpen bármely konfigurációs forrás, amely támogatja a .NET Core, beleértve a helyi használatával meghatározhatja az alkalmazás által a szolgáltatás jelzők *appsettings.json* fájlban vagy környezeti változót. `IFeatureManager` .NET Core függőségi beszúrást támaszkodik. A szolgáltatás felügyeleti szolgáltatások standard szintű konvenciók használatával regisztrálhatja:

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

Alapértelmezés szerint a szolgáltatás-kezelő szolgáltatás jelzőket a lekéri a `"FeatureManagement"` szakaszában a .NET Core-konfigurációs adatokat. Az alábbi példa arra utasítja a szolgáltatás-kezelő nevű másik szakaszban olvasható `"MyFeatureFlags"` helyette:

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

Ha a szolgáltatás jelzők szűrőket használ, kell egy további tár tartalmazza, és regisztrálja. Az alábbi példa bemutatja, hogyan használható egy beépített funkciót nevű szűrőt `PercentageFilter`:

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

Azt javasoljuk, hogy funkció jelzők az alkalmazáson kívül tartani, és külön-külön kezelhető. Ha így tesz, bármikor módosíthatja a jelző állapotok, és ezek a módosítások érvénybe léptetéséhez azonnal az alkalmazásban. Alkalmazáskonfiguráció biztosít egy központi helyen rendszerezéséhez és a egy dedikált portál felhasználói felületén keresztül az összes funkció jelző szabályozása. Alkalmazáskonfiguráció is kézbesíti a jelzők az alkalmazást közvetlenül a .NET Core-ügyfél kódtárakat.

Az alkalmazások konfigurálása az ASP.NET Core-alkalmazás csatlakoztatása a legegyszerűbben a konfigurációszolgáltató `Microsoft.Extensions.Configuration.AzureAppConfiguration`. A NuGet-csomagot használ, adja hozzá a következő kódot a *Program.cs* fájlt:

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

A szolgáltatás jelző értéket vár az idő előrehaladtával változik. Alapértelmezés szerint a szolgáltatás-kezelő 30 másodpercenként frissíti a szolgáltatás jelző értékét. A következő kód bemutatja, hogyan módosíthatja lekérdezési időközét az 5 másodperc a `options.UseFeatureFlags()` hívása:

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(300);
           });
});
```

## <a name="feature-flag-declaration"></a>A szolgáltatás jelző deklarace

Minden funkció jelölője két részből áll: egy nevet és a egy vagy több szűrő, amely segítségével kiértékelheti a funkció állapota listáját *a* (azt jelenti, amikor annak értéke `True`). Egy szűrőt a használati esetek határozza meg, amikor egy szolgáltatást be kell kapcsolni.

Amikor a szolgáltatás azt a jelzőt több szűrő, a lista pontja ahhoz, amíg egy szűrőt meghatározza, hogy a funkciót engedélyezni kell. Ezen a ponton a funkció jelölője van *a*, és a többi szűrő eredményt a rendszer kihagyja. Nincs szűrő azt jelzi, hogy a funkciót engedélyezni kell, ha van-e a funkció jelölője *ki*.

A szolgáltatás manager által támogatott *appsettings.json* funkció jelzők konfigurációs forrásaként. Az alábbi példa bemutatja, hogyan állíthatja be a szolgáltatás jelzők JSON-fájlban:

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

Szabályok szerint a `FeatureManagement` jelző eszközfunkció-beállítások szakasz a JSON-dokumentum szolgál. Az előző példában a meghatározott szűrőket jeleníti meg a szolgáltatás három jelölőt a `EnabledFor` tulajdonság:

* `FeatureA` van *a*.
* `FeatureB` van *ki*.
* `FeatureC` Meghatározza egy szűrőt nevű `Percentage` együtt egy `Parameters` tulajdonság. `Percentage` konfigurálható szűrő van. Ebben a példában `Percentage` 50 %-os valószínűségét adja meg a `FeatureC` kell jelző *a*.

## <a name="feature-flag-references"></a>A szolgáltatás jelző referenciák

Így könnyen hivatkozhat funkció jelzőket a code-ban, be kell állítania az őket `enum` változókat:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>A szolgáltatás jelző ellenőrzések

A szolgáltatás felügyeleti alapvető mintája, hogy először ellenőrizze, ha egy szolgáltatás a jelzővel állítható be *a*. Ha tehát a szolgáltatás-kezelő majd futtatja a műveleteket, amely tartalmazza a szolgáltatás. Példa:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Függőséginjektálás

Az ASP.NET Core mvc-ben, a szolgáltatás-kezelő érhető `IFeatureManager` függőségi beszúrást keresztül:

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

MVC-vezérlők, használja a `Feature` vezérlő attribútumot teljes vezérlőosztály vagy egy bizonyos művelet engedélyezve van-e. A következő `HomeController` tartományvezérlő szükséges `FeatureA` kell *a* mielőtt bármilyen műveletet tartalmazza a vezérlőosztály hajtható végre:

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A következő `Index` művelethez szükséges `FeatureA` kell *a* ahhoz, hogy futtatható:

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Ha egy MVC-vezérlő vagy a művelet le van tiltva, mert a szolgáltatás szabályozásával való jelző *ki*, egy regisztrált `IDisabledFeaturesHandler` felület nevezzük. Az alapértelmezett `IDisabledFeaturesHandler` felület 404-es állapotkódot ad vissza az ügyfélnek, választörzs nélkül.

## <a name="mvc-views"></a>MVC-nézetek

Az MVC-nézetekben használható egy `<feature>` tartalom megjelenítése címke alapján hogy engedélyezve van-e a szolgáltatás azt a jelzőt:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-szűrők

MVC-szűrőket állíthatja, hogy az aktivált egy funkció jelölője állapota alapján. A következő kódot egy MVC-szűrőt, nevű ad `SomeMvcFilter`. Ez a szűrő aktiválódik az MVC folyamat csak akkor, ha belül `FeatureA` engedélyezve van.

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

## <a name="routes"></a>Útvonalak

A szolgáltatás jelzők segítségével dinamikusan elérhetővé az útvonalak. Az alábbi kód hozzáad egy útvonalat, amely beállítja a `Beta` , az alapértelmezett vezérlő csak akkor, ha `FeatureA` engedélyezve van:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Közbenső szoftver

A szolgáltatás jelzők segítségével feltételesen adja hozzá az alkalmazás-ágak és köztes szoftverek. A következő kód beszúrása egy közbenső összetevő a kérelemben szereplő folyamat csak akkor, ha `FeatureA` engedélyezve van:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Ez a kód épít ki további általános teszi, hogy egy funkció jelölője alapján a teljes alkalmazás ág:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan funkció jelzők megvalósításához az ASP.NET Core-alkalmazás használatával az `Microsoft.FeatureManagement` kódtárakat. ASP.NET Core és az alkalmazás konfigurációját a szolgáltatás felügyeleti támogatásával kapcsolatos további információkért lásd a következőket:

* [ASP.NET Core funkció jelölője mintakód](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Funkciójelölők kezelése](./manage-feature-flags.md)
