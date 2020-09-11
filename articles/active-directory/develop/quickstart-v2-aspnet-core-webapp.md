---
title: Bejelentkezés felvétele a Microsofttal a webalkalmazások ASP.NET Coreához | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan implementálhatja a Microsoft bejelentkezést egy ASP.NET Core webalkalmazásban az OpenID Connect használatával
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 7e8cea18999bec34eba6630a96dca089fd4725af
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90016320"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal ASP.NET Core webalkalmazásba

Ebben a rövid útmutatóban megtudhatja, hogyan jelentkezhet be ASP.NET Core webalkalmazás a személyes fiókokban (hotmail.com, outlook.com, Others) és munkahelyi és iskolai fiókokat bármely Azure Active Directory (Azure AD) példányból. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy ábrán.)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg a [Azure Portal-Alkalmazásregisztrációk](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és a megoldáshoz az alkalmazás regisztrációs adatainak manuális hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés**területen válassza a **Alkalmazásregisztrációk**, majd az **új regisztráció**lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `AspNetCore-Quickstart` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. Adjon meg egy **átirányítási URI** -t `https://localhost:44321/`
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. A **kezelés**területen válassza a **hitelesítés**lehetőséget.
> 1. Az **átirányítási URI**-k területen válassza az **URI hozzáadása**lehetőséget, majd írja be a `https://localhost:44321/signin-oidc`
> 1. Adja meg a **kijelentkezési URL-címét**`https://localhost:44321/signout-oidc`
> 1. Az **Implicit engedély** területen válassza az **Azonosítói jogkivonatok** elemet.
> 1. Kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, hozzá kell adnia a válasz URL-címeket a és a értékkel, `https://localhost:44321/` `https://localhost:44321/signin-oidc` hozzá kell adnia a kijelentkezési URL-címet, valamint az `https://localhost:44321/signout-oidc` engedélyezési végpont által kiállított azonosító jogkivonatokat.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-aspnet-core-project"></a>2. lépés: a ASP.NET Core-projekt letöltése

> [!div renderon="docs"]
> [A ASP.NET Core-megoldás letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Futtassa a projektet.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékével konfiguráltuk, és készen áll a futtatásra.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>3. lépés: a ASP.NET Core projekt konfigurálása
> 1. Bontsa ki a. zip-archívumot egy helyi mappába a meghajtó gyökerének közelében. Például a *C:\Azure-Samples*.
> 1. Nyissa meg a megoldást a Visual Studio 2019-ben.
> 1. Nyissa meg a *appsettings.js* fájlt, és módosítsa a következőket:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - A helyére írja `Enter_the_Application_Id_here` be a Azure Portalban regisztrált alkalmazás **alkalmazás (ügyfél) azonosítóját** . Az **alkalmazás (ügyfél) azonosítóját** az alkalmazás **Áttekintés** oldalán találja.
>    - Cserélje le az értékét `common` a következők egyikére:
>       - Ha az alkalmazás **csak ebben a szervezeti könyvtárban támogatja a fiókokat**, cserélje le ezt az értéket a **könyvtár (bérlő) azonosítójával** (GUID) vagy a **bérlő nevével** (például `contoso.onmicrosoft.com` ). A **címtár (bérlő) azonosítója** az alkalmazás **Áttekintés** lapján található.
>       - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>       - Ha az alkalmazás támogatja az **összes Microsoft-fiók felhasználót**, hagyja ezt az értéket `common`
>
> Ebben a rövid útmutatóban ne módosítsa a fájl *appsettings.js* más értékeit.
>
> #### <a name="step-4-build-and-run-the-application"></a>4. lépés: Az alkalmazás fordítása és futtatása
> 
> Az alkalmazás a Visual Studióban történő létrehozásához és futtatásához válassza a **hibakeresés menüt >** **hibakeresés indítása**vagy a kulcs megnyomásával `F5` .
> 
> A rendszer kéri a hitelesítő adatait, majd megkéri, hogy adja meg az alkalmazáshoz szükséges engedélyeket. Válassza az **elfogadás** lehetőséget a beleegyezés kérése lapon.
> 
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Az alkalmazás által az > felhasználótól kért engedélyeket megjelenítő belefoglalási párbeszédpanel":::
> 
> A kért engedélyek beleegyezése után az alkalmazás azt jeleníti meg, hogy sikeresen bejelentkezett a Azure Active Directory hitelesítő adataival.
> 
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Webböngésző, amely megjeleníti a futó webalkalmazást és a bejelentkezett felhasználót":::

## <a name="more-information"></a>További információ

Ez a szakasz áttekintést nyújt a felhasználók bejelentkezéséhez szükséges kódokról. Ez az Áttekintés hasznos lehet a kód működésének, a fő argumentumok, valamint a meglévő ASP.NET Core alkalmazásba való bejelentkezés hozzáadásának megismeréséhez.

### <a name="how-the-sample-works"></a>A minta működése
![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Indítási osztály

A *Microsoft. AspNetCore. Authentication* middleware olyan `Startup` osztályt használ, amely a üzemeltetési folyamat inicializálásakor fut:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

A `AddAuthentication()` metódus úgy konfigurálja a szolgáltatást, hogy cookie-alapú hitelesítést adjon hozzá, amely a böngészőbeli forgatókönyvekben használatos, és az OpenID connectre vonatkozó kihívás beállítására szolgál.

Az `.AddMicrosoftIdentityWebApp` alkalmazáshoz a Microsoft Identity platform hitelesítésének hozzáadását tartalmazó sor. Ezt követően a rendszer úgy konfigurálja, hogy a Microsoft Identity platform végpontjának használatával jelentkezzen be a `AzureAD` konfigurációs fájl *appsettings.js* szakaszában található információk alapján:

| *appsettings.jsa* kulcson | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | A Azure Portalban regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** .                                                                                       |
| `Instance`             | A hitelesítéshez használt biztonságijogkivonat-szolgáltatás (STS) végpontja. Ez az érték általában `https://login.microsoftonline.com/` Az Azure nyilvános felhőre utal. |
| `TenantId`             | A bérlő vagy a bérlői azonosító (GUID) neve, *vagy a felhasználók* munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal való bejelentkezéséhez.                             |

A `Configure()` metódus két fontos módszert tartalmaz, `app.UseCookiePolicy()` `app.UseAuthentication()` amelyek lehetővé teszik a megnevezett funkciókat.

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Egy vezérlő vagy egy vezérlő metódusának védelme

A vezérlő vagy vezérlő metódusait az attribútum használatával lehet védelemmel ellátni `[Authorize]` . Ez az attribútum csak a hitelesített felhasználók engedélyezésével korlátozza a vezérlőhöz vagy a metódusokhoz való hozzáférést, ami azt jelenti, hogy a hitelesítési kérdés elindítható a vezérlőhöz, ha a felhasználó nem hitelesítve van.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A ASP.NET Core oktatóanyagot tartalmazó GitHub-tárház útmutatást és további kódrészleteket tartalmaz, amelyek az alábbiakat mutatják be:

- Hitelesítés hozzáadása új ASP.NET Core webalkalmazáshoz
- A Microsoft Graph, más Microsoft API-k vagy a saját webes API-k hívása
- Engedélyezés hozzáadása
- Bejelentkezés a felhasználók számára az országos felhőkben vagy közösségi identitásokkal

> [!div class="nextstepaction"]
> [Webalkalmazás-oktatóanyagok ASP.NET Core a GitHubon](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
