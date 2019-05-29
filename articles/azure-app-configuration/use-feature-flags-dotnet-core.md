---
title: A szolgáltatás jelzők egy .NET Core-alkalmazást az oktatóanyag |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan funkció jelzők megvalósítása a .NET Core-alkalmazások
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
ms.openlocfilehash: b0e48a0db63eded9e9c4921d33b03af39656ce0d
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299264"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Oktatóanyag: Funkciójelölők használata .NET Core-alkalmazásokban

A .NET Core szolgáltatás kezelési kódtárakat bármilyen-támogatást nyújt a szolgáltatás jelzők megvalósítása a .NET- vagy ASP.NET Core alkalmazás. Lehetővé teszik, hogy a szolgáltatás megőrzendő tartalomként jelöli meg a programkód több deklaratív, hogy az összes nem rendelkezik a `if` utasítás a őket manuálisan. Szolgáltatás jelző életciklusának (például frissítési és gyorsítótára államok jelző, a kérelem-hívás közben nem módosítható kell egy jelző állapot garancia) bennük a háttérben. Emellett az ASP.NET Core library biztosít többek között az MVC-vezérlő műveletek, nézetek, útvonalak és közbenső szoftver-a-beépített Integrációk.

A [funkció jelzők hozzáadása ASP.NET Core-alkalmazás](./quickstart-feature-flag-aspnet-core.md) a rövid útmutató bemutatja a szolgáltatás jelzők hozzáadása egy ASP.NET Core-alkalmazást a többféleképpen. Ez az oktatóanyag azt ismerteti, ezeket a további részleteket. Tekintse meg a [ASP.NET Core szolgáltatás management dokumentációja](https://go.microsoft.com/fwlink/?linkid=2091410) teljes.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Adja hozzá a szolgáltatás jelzők funkció rendelkezésre állásának szabályozhatja az alkalmazás legfontosabb részeit.
> * Alkalmazáskonfiguráció integrálható, ha a szolgáltatás jelölők kezelése segítségével.

## <a name="setup"></a>Telepítés

A .NET Core-szolgáltatás kezelő `IFeatureManager` funkció jelzők olvas be a keretrendszer natív konfigurációs rendszer. Ennek eredményeképpen meghatározhatja az alkalmazás funkció jelzők segítségével bármely konfigurációs forrás, amely támogatja a .NET Core, beleértve a helyi *appsettings.json* fájlban vagy környezeti változót. A szolgáltatás manager a .NET Core függőségi beszúrást támaszkodik. A szolgáltatás management szolgáltatásokat standard konvenciókat is regisztrálhat.

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

A szolgáltatás-kezelő szolgáltatás jelzők kikeresi alapértelmezés szerint a a .NET Core-konfigurációs adatokat a "FeatureManagement" című szakaszát. Az alábbi példa utasítja, hogy inkább a "MyFeatureFlags" nevű másik szakaszban olvasni.

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

Ha a szolgáltatás jelzők szűrőket használ, kell egy további tár tartalmazza, és regisztrálja. Az alábbi példa bemutatja, hogyan használható egy beépített funkciót nevű szűrőt **PercentageFilter "** .

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

Hatékonyan működjön, kell tartani a szolgáltatás jelzők mimo aplikaci, és külön-külön kezelhető. Ha így tesz, bármikor módosíthatja a jelző állapotok, és ezek a módosítások hatállyal az alkalmazásban. Alkalmazáskonfiguráció egy központi helyen rendszerezéséhez és az összes a szolgáltatás szabályozásával megőrzendő tartalomként jelöli meg egy dedikált portál felhasználói felületén keresztül, és kézbesíti a jelzők az alkalmazást közvetlenül a .NET Core-ügyfél kódtárakat biztosít. Az alkalmazások konfigurálása az ASP.NET Core-alkalmazás csatlakoztatása a legegyszerűbben a konfigurációszolgáltató `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Adja hozzá a következő kódban a NuGet-csomagot is használhatja a *Program.cs* fájlt:

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

A szolgáltatás jelző értéket vár az idő előrehaladtával változik. A szolgáltatás manager alapértelmezés szerint frissíti a szolgáltatás jelző értéke 30 másodpercenként. Használhat egy másik lekérdezési időközét az `options.UseFeatureFlags()` fenti hívás.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>A szolgáltatás jelző deklarace

Minden funkció jelölője két részből áll: egy nevet és a egy vagy több szűrő, amely segítségével kiértékelheti a funkció állapota listáját *a* (azt jelenti, amikor annak értéke `True`). A szűrő meghatározza a használati eset, amelynek levélszolgáltatásnak kell bekapcsolni. Ha a szolgáltatás azt a jelzőt több szűrőt, a lista pontja ahhoz, amíg egy szűrőt határozza meg, hogy a funkciót engedélyezni kell. Ezen a ponton a funkció jelölője minősül *a* és a többi szűrő eredményt a rendszer kihagyja. Ha nincs szűrő azt jelzi, hogy a funkciót engedélyezni kell, a szolgáltatás jelző van *ki*.

A szolgáltatás manager által támogatott *appsettings.json* funkció jelzők konfigurációs forrásaként. Az alábbi példa bemutatja, hogyan állíthatja be a szolgáltatás jelzők json-fájlban.

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
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

Szabályok szerint a `FeatureManagement` jelző eszközfunkció-beállítások szakasz a json-dokumentum szolgál. A fenti példában a meghatározott szűrőket jeleníti meg a szolgáltatás három jelölőt a *EnabledFor* tulajdonság:

* **FeatureA** van *a*.
* **FeatureB** van *ki*.
* **FeatureC** nevű szűrőt adja *százalékos* együtt egy *paraméterek* tulajdonság. *Százalékos* konfigurálható szűrő egy példát, és meghatározza a valószínűség 50 %-a a **FeatureC** kell jelző *a*.

## <a name="referencing"></a>Hivatkozó

Bár nem kötelező, a szolgáltatás jelzők definiálható mint `enum` változókat, hogy azok könnyen lehet hivatkozni a kódban.

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>A szolgáltatás jelző ellenőrzése

A szolgáltatás felügyeleti alapvető mintája, hogy először ellenőrizze, ha egy szolgáltatás a jelzővel állítható be *a* , és hajtsa végre a karakterláncon belül lévő műveleteket, ha ebben az esetben.

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Függőséginjektálás

Az ASP.NET Core MVC, a szolgáltatás-kezelő `IFeatureManager` függőségi beszúrást keresztül érhetők el.

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

## <a name="controller-action"></a>Vezérlő művelet

Az MVC-vezérlők egy `Feature` attribútum vezérlőelemhez használható, teljes vezérlőosztály vagy egy bizonyos művelet engedélyezve van-e. A következő `HomeController` tartományvezérlő szükséges *FeatureA* kell *a* mielőtt bármilyen műveletet tartalmaz hajtható végre.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

A következő `Index` fenti művelethez szükséges *FeatureA* kell *a* ahhoz, hogy futtatható.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Ha egy MVC-vezérlő vagy a művelet le van tiltva, mert a szolgáltatás szabályozásával való jelző *ki*, egy regisztrált `IDisabledFeatureHandler` nevezzük. Az alapértelmezett `IDisabledFeatureHandler` 404-es állapotkódot ad vissza, választörzs nélkül az ügyfélnek.

## <a name="view"></a>Nézet

Az MVC-nézetekben egy `<feature>` címke segítségével a tartalmak egy funkció jelző engedélyezve van-e vagy sem jelennek meg.

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>MVC-szűrő

MVC-szűrők beállíthatja úgy, hogy a rendszer aktiválja a funkciót azt a jelzőt állapota alapján. A következő hozzáadása az MVC-szűrő nevű `SomeMvcFilter`. Ez a szűrő aktiválódik az MVC folyamat csak akkor, ha belül *FeatureA* engedélyezve van.

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

## <a name="route"></a>Útválasztás

Útvonalak funkció jelzők alapján dinamikusan is kommunikálhatnak. A következő hozzáad egy útvonalat, amely beállítja a `Beta` , az alapértelmezett vezérlő csak akkor, ha *FeatureA* engedélyezve van.

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Közbenső szoftver

A szolgáltatás jelzők használható alkalmazás elágazások és közbenső feltételesen hozzáadása. A következő beszúrásokat egy közbenső összetevő a kérelemben szereplő folyamat csak akkor, ha *FeatureA* engedélyezve van.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Ez felépíti ki több általános teszi, hogy egy funkció jelölője alapján a teljes alkalmazás ágban.

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan funkció jelzők megvalósításához az ASP.NET Core-alkalmazást a felügyelniük a `Microsoft.FeatureManagement` kódtárakat. Az alábbi cikkekben talál további információt felügyeleti támogatásáról az ASP.NET Core és az alkalmazás konfigurációját.

* [ASP.NET Core funkció jelölője mintakód](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Microsoft.FeatureManagement dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Funkciójelölők kezelése](./manage-feature-flags.md)
