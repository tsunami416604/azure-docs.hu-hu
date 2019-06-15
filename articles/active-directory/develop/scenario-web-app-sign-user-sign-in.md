---
title: Webes alkalmazás, amely képes bejelentkeztetni a felhasználókat (bejelentkezés) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, hogy bejelentkezik felhasználók (bejelentkezés)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fb7fbba7ec48da580d2a630ae51aa20b3307848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074620"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Webalkalmazás-alkalmazást, amely képes bejelentkeztetni a felhasználókat – bejelentkezés

Ismerje meg, jelentkezzen be a kódot a webalkalmazás számára, hogy jelentkezik be felhasználók hozzáadása.

## <a name="sign-in"></a>Bejelentkezés

A kódot, amelyet az előző cikkben [alkalmazás kód konfigurációját](scenario-web-app-sign-user-app-configuration.md) minden, meg kell valósítania kijelentkezést. Miután a felhasználó bejelentkezett az alkalmazásba, valószínűleg érdemes teszi lehetővé számukra, jelentkezzen ki. Az ASP.NET core kijelentkezési kezeli az Ön számára.

## <a name="what-sign-out-involves"></a>Magában foglalja a mi jelentkezzen ki

A webalkalmazás kijelentkezés tárgya több, mint a bejelentkezett fiók vonatkozó információk eltávolítását a webalkalmazás állapotát.
A webalkalmazás kell is átirányítja a felhasználót a Microsoft identity platform v2.0 `logout` végpontot, jelentkezzen ki. Ha a webes alkalmazás átirányítja a felhasználót a felhasználót, hogy a `logout` végpont, ez a végpont törli a felhasználói munkamenetet a böngészőből. Ha az alkalmazás nem lépjen a `logout` végpont, a felhasználó lenne hitelesítse magát újra az alkalmazásba anélkül, hogy megadja a bejelentkezési adatait, mert lenne egy érvényes egyszeri bejelentkezési munkamenetet a Microsoft Identity platform v2.0-végpont.

További tudnivalókért tekintse meg a [kijelentkezési kérés küldése](v2-protocols-oidc.md#send-a-sign-out-request) szakasz a [Microsoft Identity platform v2.0 és az OpenID Connect protokoll](v2-protocols-oidc.md) fogalmi dokumentáció.

## <a name="application-registration"></a>Alkalmazásregisztráció

Az alkalmazás regisztrációja során fogja korábban regisztrált egy **kijelentkezési URI közzététele**. A jelen oktatóanyagban regisztrált `https://localhost:44321/signout-oidc` a a **kijelentkezési URL-címe** mezőjében a **speciális beállítások** című rész a **hitelesítési** lap. További részletekért lásd:, [ a webApp alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>Az ASP.NET Core-kódot

### <a name="signout-button"></a>Kijelentkezés gombra

A Kijelentkezés gombra érhető el a `Views\Shared\_LoginPartial.cshtml` , és csak akkor jelenik meg egy hitelesített fiók (amely akkor, ha a felhasználó korábban bejelentkezett) esetén.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()` a művelet a `AccountController`

Nyomja le az **jelentkezzen ki** a webes alkalmazások eseményindítói gombjára a `SignOut` művelet a `Account` vezérlő. Az ASP.NET core-sablonokat, a korábbi verzióiban a `Account` vezérlő beágyazott a webalkalmazással együtt, de ez már nem így, mert már része a magát az ASP.NET Core-keretrendszernek. 

A kód a `AccountController` érhető el a tárházból az ASP.NET core, a [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). A fiókok felügyelete:

- Csoportok OpenID egy átirányítási URI-t `/Account/SignedOut` úgy, hogy a vezérlő vissza, ha az Azure ad-ben végzett el a kijelentkezési nevezzük.
- Hívások `Signout()`, amely lehetővé teszi az OpenIdConnect közbenső szoftvert, forduljon a Microsoft identity platform `logout` végpont amely:

  - Törli a munkamenetcookie-t a böngészőből, és
  - Hívások végül visszahívja a **kijelentkezési URL-címe**, amely) alapértelmezés szerint jeleníti meg az aláírt nézet lap meg [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) ASP.NET Core részeként is biztosított.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>A hívást elfogja a `logout` végpont

Az ASP.NET Core OpenIdConnect közbenső szoftver lehetővé teszi, hogy az alkalmazást, hogy a Microsoft identity platform hívása intercept `logout` végpontot azáltal, hogy nevű OpenIdConnect esemény `OnRedirectToIdentityProviderForSignOut`. A webalkalmazás használja, a fiók kiválasztása párbeszédpanelen lehet kijelentkezni. Ha a felhasználó számára megjelenő elkerülése érdekében. A hozzáférés teheti a `AddAzureAdV2Authentication` , a `Microsoft.Identity.Web` újrafelhasználható könyvtár. Lásd: [StartupHelpers.cs L58-L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

```CSharp
public static IServiceCollection AddAzureAdV2Authentication(this IServiceCollection services,
                                                            IConfiguration configuration)
{
    ...
    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
        ...
        options.Authority = options.Authority + "/v2.0/";
        ...
        // Attempt to avoid displaying the select account dialog when signing out
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            var user = context.HttpContext.User;
            context.ProtocolMessage.LoginHint = user.GetLoginHint();
            context.ProtocolMessage.DomainHint = user.GetDomainHint();
            await Task.FromResult(0);
        };
    }
}
```

## <a name="aspnet-code"></a>ASP.NET Code

Az ASP.NET a Kijelentkezés a SignOut() metódus egy tartományvezérlőn (például AccountController) akkor aktiválódik. Ez a módszer nem része az ASP.NET keretrendszer (ellentétesen, mi történik, az ASP.NET Core), és nem használ, az aszinkron feladatok, és ezért azt:

- az OpenId kijelentkezési challenge küld
- Kiüríti a gyorsítótárat
- szeretné az oldal átirányítja a felhasználót

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 MsalAppBuilder.ClearUserTokenCache();
 Response.Redirect("/");
}
```

## <a name="protocol"></a>Protocol

Ha nem szeretné használni az ASP.NET Core vagy az ASP.NET, tekintse meg a protokoll dokumentációja, az elérhető [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-web-app-sign-user-production.md)
