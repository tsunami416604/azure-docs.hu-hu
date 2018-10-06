---
title: Az Azure AD v2.0 ASP.NET Core webes alkalmazás – rövid útmutató |} A Microsoft Docs
description: Az ASP.NET Core-webalkalmazás OpenID Connect használatával a Microsoft bejelentkezési megvalósítása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4ab3d0b74e8305d67af862020197c69b15221086
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830225"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Bejelentkezés Microsoft-hozzáadása az ASP.NET Core-webalkalmazás

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ez a rövid útmutató tartalmazza a kódminta azt mutatja be, hogyan ASP.NET Core-webalkalmazás személyes bejelentkezhet (hotmail.com, live.com, mások) és a munkahelyi és iskolai fiókok minden olyan Azure Active Directory-példányból.

![A rövid útmutató által létrehozott mintaalkalmazás működése](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Az alkalmazás regisztrálása és a rövid útmutató mintaalkalmazásának letöltése
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Az alkalmazás és a kódminta regisztrálása és konfigurálása
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> 
> 1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app).
> 1. Írja be az alkalmazás nevét. Ügyeljen arra, hogy az **Irányított beállítás** jelölőnégyzet ne legyen bejelölve, majd válassza a **Létrehozás** lehetőséget.
> 1. Kattintson a(z) `Add Platform`, majd a(z) `Web` elemre.
> 1. Ügyeljen arra, hogy az **Implicit forgalom engedélyezése** jelölőnégyzet *be legyen jelölve*.
> 1. Az **Átirányítási URL-címek** beállításnál írja be a következőt: `http://localhost:3110/`.
> 1. Görgessen le a lap aljára, és kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> Ahhoz, hogy a rövid útmutatóhoz tartozó kódminta működjön, hozzá kell adnia egy válasz URL-t a következő formában: `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-core-webapp/green-check.png) Az alkalmazása már konfigurálva van ezzel az attribútummal

#### <a name="step-2-download-your-aspnet-core-project"></a>2. lépés: Az ASP.NET Core-projekt letöltése

- [Az ASP.NET Core-projekt letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3. lépés: A projekt konfigurálása

1. Bontsa ki például a zip-fájlt egy helyi mappába közelebb gyökérmappájába - **C:\Azure-Samples**
1. Ha a Visual Studio 2017-et használ, nyissa meg a projektet a Visual Studióban (nem kötelező)
1. Szerkesztés **appsettings.json** , és cserélje le az értéket `ClientId` együtt az imént regisztrált alkalmazás alkalmazásazonosító:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Ha az alkalmazás egy *egybérlős alkalmazás* (célzó fiókok csak az aktuális könyvtárban található), a a **appsettings.json** fájlt és keresse meg az értékét `TenantId` , és cserélje le a `common`együtt a **Bérlőazonosító** vagy **bérlőnevet** (például contoso.microsoft.com). A bérlőnevet az **Áttekintés** lapon találja meg.

## <a name="more-information"></a>További információ

Ez a szakasz a felhasználók bejelentkeztetéséhez szükséges kód áttekintését tartalmazza. Ez lehet hasznos, ha szeretné megismerni a kód működését, fő argumentumot, és ha azt szeretné, bejelentkezés hozzáadása egy meglévő ASP.NET Core-alkalmazást.

### <a name="startup-class"></a>Indítási osztály

*Microsoft.AspNetCore.Authentication* közbenső egy indítási osztályt, a üzemeltetési folyamat inicializálja esetén végrehajtott használja:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

A metódus `AddAuthentication` konfigurálja a szolgáltatást, adja hozzá a Cookie-alapú hitelesítés – a böngésző forgatókönyvek esetén használt, valamint a OpenIdConnect a kihívás beállítása. 

A sor tartalmazó `.AddAzureAd` ad hozzá az alkalmazás az Azure Active Directory-hitelesítéssel.

Amellett, hogy a fájl **AzureAdAuthenticationBuilderExtensions.cs** metódust ad az Azure ad hitelesítési folyamatot. Ez a bővítmény metódus az Azure AD-hitelesítés szükséges attribútumok konfigurálása. A `Configure` metódus az `IConfigureNamedOptions` felület a következőket tartalmazza:

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Ahol  |  |
> |---------|---------|
> |ClientID     |Az alkalmazásazonosító az alkalmazás regisztrálva az Azure Portalon|
> |szolgáltató | Az STS végpont fo felhasználó hitelesítéséhez. Általában https://login.microsoftonline.com/{tenant}/v2.0 a nyilvános felhőknél, ahol a {tenant} a bérlő neve, a bérlő azonosítója, vagy *common* a közös végpontra való hivatkozáskor (több-bérlős alkalmazások esetében)|
> |UseTokenLifetime |Azt jelzi, hogy a hitelesítési cookie-t meg kell egyeznie a hitelesítési jogkivonat, amely|
> |RequireHttpsMetadata     |A metaadat-cím vagy a szolgáltató igényel a HTTPS. Javasoljuk, hogy módosítsa az értéket `True`|
> |TokenValidationParameters     | A jogkivonatok érvényesítéséhez használatos paraméterek listája. Ebben az esetben `ValidateIssuer` értékre van állítva `false` jelzi, hogy fogadja a semmilyen személyes vagy munkahelyi vagy iskolai fiókkal történő bejelentkezések|

### <a name="initiate-an-authentication-challenge"></a>Hitelesítő kérdés kezdeményezése

Kényszerítheti a felhasználót, hogy jelentkezzen be egy hitelesítési kérdésre, a vezérlő a fentiekhez hasonlóan kér a **AccountController.cs**:

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> Egy hitelesítési kérdésre, a fenti módszerrel kérése nem kötelező, és commonsly használatos, ha azt szeretné, hogy egy nézetet, hogy mind a hitelesített, és a nem hitelesített felhasználók számára érhető el. Vezérlő a következő szakaszban leírt módon védheti meg.

### <a name="protect-a-controller-or-a-controllers-method"></a>Egy vezérlő vagy egy vezérlő metódusának védelme

A vezérlő vagy a tartományvezérlő methodss használatával védheti a `[Authorize]` attribútum. Ez az attribútum korlátozza a hozzáférést a vezérlő vagy a módszerek azáltal, hogy csak hitelesített felhasználók – ami azt jelenti, hogy a hitelesítési kérdés eléréséhez a vezérlő, ha a felhasználó hitelesítése nem indítható.

## <a name="next-steps"></a>További lépések

További információk – hitelesítés hozzáadása egy új ASP.NET Core webes alkalmazásba való ebben az ASP.NET Core a rövid útmutatóban, tekintse meg a GitHub-adattárat:

> [!div class="nextstepaction"]
> [Az ASP.NET Core Web App mintakódot](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]