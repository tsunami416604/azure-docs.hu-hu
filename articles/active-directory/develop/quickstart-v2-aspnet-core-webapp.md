---
title: Az Azure AD v2.0 ASP.NET Core webes alkalmazás – rövid útmutató |} A Microsoft Docs
description: Az ASP.NET Core-webalkalmazás OpenID Connect használatával a Microsoft bejelentkezési megvalósítása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fed62dfc3f7e7dc974fb709261e363f26ce97c51
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200990"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Gyors útmutató: Bejelentkezés Microsoft-hozzáadása az ASP.NET Core-webalkalmazás

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ebben a rövid útmutatóban megtudhatja, hogyan bejelentkezhet az ASP.NET Core-webalkalmazás a személyes fiókok (hotmail.com, outlook.com, mások) és munkahelyi és iskolai fiókok minden olyan Azure Active Directory (Azure AD)-példányból.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működését mutatja](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
>
> 1. Nyissa meg a [az Azure portal - alkalmazásregisztrációk (előzetes verzió)](https://aka.ms/aspnetcore2-1-aad-quickstart-v2).
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Regisztrálja az alkalmazást, és manuálisan adja hozzá az alkalmazás regisztrációs adatok a megoldáshoz, kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást, majd válassza az **Alkalmazásregisztrációk (előzetes verzió)** > **Új regisztráció** lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>    - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AspNetCore-Quickstart`).
>    - A **válasz URL-cím**, adjon hozzá `https://localhost:44321/`, és válassza ki **regisztrálása**.
> 1. Válassza ki a **hitelesítési** menüben, majd adja hozzá a következő információkat:
>    - A **válasz URL-cím**, adjon hozzá `https://localhost:44321/signin-oidc`, és válassza ki **regisztrálása**.
>    - Az a **speciális beállítások** szakaszában **kijelentkezési URL-címe** való `https://localhost:44321/signout-oidc`.
>    - A **típusú Implicit engedélyezés**, ellenőrizze **azonosító-jogkivonatokat**.
>    - Kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> A kódminta működjön ez a rövid útmutató, a válasz URL-címek, hozzá kell `https://localhost:44321/` és `https://localhost:44321/signin-oidc`, adja hozzá a kijelentkezési URL-cím `https://localhost:44321/signout-oidc`, és a kérelem azonosító-jogkivonatokat állít ki, az engedélyezési végponton.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-aspnet-core-project"></a>2. lépés: Az ASP.NET Core-projekt letöltése

- [A Visual Studio 2017-megoldás letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt konfigurálása

1. Bontsa ki például a zip-fájlt egy helyi mappába a gyökérmappa - belül **C:\Azure-Samples**
1. Ha a Visual Studio 2017-et használ, nyissa meg a megoldást a Visual Studióban (nem kötelező).
1. Szerkessze a **appsettings.json** fájlt. Keresés `ClientId` , és cserélje le `Enter_the_Application_Id_here` az a **Alkalmazásazonosítót (ügyfél)** értékét az imént regisztrált alkalmazást. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div renderon="docs"]
> Az elemek magyarázata:
> - `Enter_the_Application_Id_here` -a a **Alkalmazásazonosítót (ügyfél)** az alkalmazás az Azure Portalon regisztrálta. Annak **Alkalmazásazonosítót (ügyfél)** az alkalmazás **áttekintése** lapot.
> - `Enter_the_Tenant_Info_Here` -a következő lehetőségek egyike:
>   - Ha az alkalmazás támogatja a **fiókok csak a szervezeti könyvtárban található**, cserélje le ezt az értéket a **Bérlőazonosító** vagy **bérlőnevet** (például contoso.microsoft.com)
>   - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>   - Ha az alkalmazás **minden Microsoft-fiókfelhasználót** támogat, ezt az értéket a `common` értékre cserélje le
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

## <a name="more-information"></a>További információ

Ez a szakasz a felhasználók bejelentkeztetéséhez szükséges kód áttekintését tartalmazza. Ez lehet hasznos, ha szeretné megismerni a kód működését, fő argumentumot, és ha azt szeretné, bejelentkezés hozzáadása egy meglévő ASP.NET Core-alkalmazást.

### <a name="startup-class"></a>Indítási osztály

*Microsoft.AspNetCore.Authentication* közbenső egy indítási osztályt, a üzemeltetési folyamat inicializálja esetén végrehajtott használja:

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Azure AD v2.0

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

A metódus `AddAuthentication` konfigurálja a szolgáltatást a cookie-alapú hitelesítést, amely a használt böngésző forgatókönyvek, valamint állítsa be a kihívás az OpenID Connect hozzáadásához. 

A sor tartalmazó `.AddAzureAd` az Azure AD-hitelesítés hozzáadása az alkalmazáshoz. Jelentkezzen be az Azure AD v2.0-végpont használatával, majd van konfigurálva.

> |Ahol  |  |
> |---------|---------|
> | ClientID  | Az alkalmazás (ügyfél) Alkalmazásazonosítója regisztrálva az Azure Portalon. |
> | szolgáltató | Az STS végpont a felhasználó hitelesítéséhez. Általában ez a <https://login.microsoftonline.com/{tenant}/v2.0> nyilvános felhő, ahol {tenant}, a bérlő vagy a bérlő Azonosítóját, vagy *közös* a közös végpontot (több-bérlős alkalmazásokhoz használt) mutató hivatkozás |
> | TokenValidationParameters | A jogkivonatok érvényesítéséhez használatos paraméterek listája. Ebben az esetben `ValidateIssuer` értékre van állítva `false` jelzi, hogy fogadja a semmilyen személyes vagy munkahelyi vagy iskolai fiókkal történő bejelentkezések. |

### <a name="protect-a-controller-or-a-controllers-method"></a>Egy vezérlő vagy egy vezérlő metódusának védelme

Egy tartományvezérlő vagy a tartományvezérlő módszerek használatával védheti a `[Authorize]` attribútum. Ez az attribútum korlátozza a hozzáférést a vezérlő vagy a módszerek azáltal, hogy csak hitelesített felhasználók, ami azt jelenti, hogy a hitelesítési kérdés eléréséhez a vezérlő, ha a felhasználó hitelesítése nem indítható.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

Tekintse meg a GitHub-adattárat az ASP.NET Core gyorsútmutatóban való hitelesítés hozzáadása egy új ASP.NET Core webes alkalmazás további információk:

> [!div class="nextstepaction"]
> [Az ASP.NET Core Web App mintakódot](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

