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
ms.openlocfilehash: ba67acec778a48c084897095aa457e5637240a57
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227431"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Bejelentkezés Microsoft-hozzáadása az ASP.NET Core-webalkalmazás

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ez a rövid útmutató tartalmazza a kódminta azt mutatja be, hogyan ASP.NET Core-webalkalmazás személyes bejelentkezhet (hotmail.com, live.com, mások) és a munkahelyi és iskolai fiókok minden olyan Azure Active Directory-példányból.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működése](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Regisztrálja az alkalmazást, és töltse le a gyorsindítási alkalmazás
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Regisztráljon, és az alkalmazás és a kód a minta konfigurálásához
> #### <a name="step-1-register-your-application"></a>1. lépés: Az alkalmazás regisztrálása
> 
> 1. Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app).
> 1. Adja meg az alkalmazás nevét, ellenőrizze, hogy a beállítás **interaktív telepítés** nincs bejelölve, és kattintson a **létrehozás**.
> 1. Kattintson a `Add Platform`, majd `Web`.
> 1. Győződjön meg arról, hogy **Implicit folyamat engedélyezése** van *be van jelölve*.
> 1. A **átirányítási URL-címeket**, adja meg `https://localhost:3110/`.
> 1. Görgessen le a lap alján, és kattintson a **mentése**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> A kódminta működjön ez a rövid útmutató, a válasz URL-cím hozzáadása szüksége `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás végrehajtása nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már be van állítva](media/quickstart-v2-aspnet-core-webapp/green-check.png) az alkalmazás ezzel az attribútummal van konfigurálva.

#### <a name="step-2-download-your-aspnet-core-project"></a>2. lépés: Az ASP.NET Core-projekt letöltése

- [Az ASP.NET Core-projekt letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3. lépés: A projekt konfigurálása

1. Bontsa ki a zip-fájlt egy helyi mappába (például **C:\Azure-Samples**)
1. Ha a Visual Studio 2017-et használ, nyissa meg a projektet a Visual Studióban (nem kötelező)
1. Szerkesztés **appsettings.json** , és cserélje le az értéket `ClientId` együtt az imént regisztrált alkalmazás alkalmazásazonosító:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Ha az alkalmazás egy *egybérlős alkalmazás* (célzó fiókok csak az aktuális könyvtárban található), a a **appsettings.json** fájlt és keresse meg az értékét `TenantId` , és cserélje le a `common`együtt a **Bérlőazonosító** vagy **bérlőnevet** (például contoso.microsoft.com). Szerezheti be a bérlő neve az a **áttekintőlapján**.

## <a name="more-information"></a>További információ

Ez a szakasz áttekintést nyújt a kód a bejelentkezéshez szükséges felhasználók. Ez lehet hasznos, ha szeretné megismerni a kód működését, fő argumentumot, és ha azt szeretné, bejelentkezés hozzáadása egy meglévő ASP.NET Core-alkalmazást.

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
> |ClientId     |Az alkalmazásazonosító az alkalmazás regisztrálva az Azure Portalon|
> |szolgáltató | Az STS végpont fo felhasználó hitelesítéséhez. Általában https://login.microsoftonline.com/{tenant}/v2.0 nyilvános felhő, ahol {tenant} a bérlő, a bérlő azonosítóját, vagy *közös* a közös végpontot (több-bérlős alkalmazásokhoz használt) mutató hivatkozás|
> |UseTokenLifetime |Azt jelzi, hogy a hitelesítési cookie-t meg kell egyeznie a hitelesítési jogkivonat, amely|
> |RequireHttpsMetadata     |A metaadat-cím vagy a szolgáltató igényel a HTTPS. Javasoljuk, hogy módosítsa az értéket `True`|
> |TokenValidationParameters     | A jogkivonat érvényesítésére paraméterek listáját. Ebben az esetben `ValidateIssuer` értékre van állítva `false` jelzi, hogy fogadja a semmilyen személyes vagy munkahelyi vagy iskolai fiókkal történő bejelentkezések|

### <a name="initiate-an-authentication-challenge"></a>Kezdeményezzen egy hitelesítési kérdésre

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

### <a name="protect-a-controller-or-a-controllers-method"></a>Egy tartományvezérlő vagy egy tartományvezérlő metódus védelme

A vezérlő vagy a tartományvezérlő methodss használatával védheti a `[Authorize]` attribútum. Ez az attribútum korlátozza a hozzáférést a vezérlő vagy a módszerek azáltal, hogy csak hitelesített felhasználók – ami azt jelenti, hogy a hitelesítési kérdés eléréséhez a vezérlő, ha a felhasználó hitelesítése nem indítható.

## <a name="next-steps"></a>További lépések

További információk – hitelesítés hozzáadása egy új ASP.NET Core webes alkalmazásba való ebben az ASP.NET Core a rövid útmutatóban, tekintse meg a GitHub-adattárat:

> [!div class="nextstepaction"]
> [Az ASP.NET Core Web App mintakódot](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]