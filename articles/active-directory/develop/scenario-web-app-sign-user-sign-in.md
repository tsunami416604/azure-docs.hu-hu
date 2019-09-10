---
title: Felhasználók számára bejelentkező webes alkalmazás (bejelentkezés) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely bejelentkezik a felhasználók számára (bejelentkezés)
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
ms.openlocfilehash: 9766b530b4d795d0f35f097de20155cdd17687ca
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812395"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Felhasználók bejelentkezési webalkalmazása – bejelentkezés

Megtudhatja, hogyan adhat hozzá bejelentkezési kódot a webalkalmazáshoz, amely bejelentkezik a felhasználók számára.

## <a name="sign-in"></a>Bejelentkezés

Az előző cikk [programkód-konfigurációjában](scenario-web-app-sign-user-app-configuration.md) áttekintett kód csak a bejelentkezés megvalósításához szükséges.
Miután a felhasználó bejelentkezett az alkalmazásba, valószínűleg engedélyezni szeretné a kijelentkezést. A ASP.NET Core kezeli a kijelentkezést.

## <a name="what-sign-out-involves"></a>A kijelentkezési folyamat

A webalkalmazásból való kijelentkezés több, mint a webalkalmazás állapotában lévő bejelentkezett fiók adatainak eltávolítása.
A webalkalmazásnak is át kell irányítani a felhasználót a Microsoft `logout` Identity platform-végpontra a kijelentkezéshez. Ha a webalkalmazás átirányítja a felhasználót a `logout` végpontra, ez a végpont törli a felhasználó munkamenetét a böngészőből. Ha az alkalmazás nem a `logout` végpontra került, a felhasználó újra hitelesíteni fogja magát az alkalmazásba anélkül, hogy újra be kellene írnia a hitelesítő adatokat, mert érvényes egyszeri bejelentkezéssel kell rendelkezniük a Microsoft Identity platform végpontjának használatával.

További információért lásd a [kijelentkezési kérelem küldése](v2-protocols-oidc.md#send-a-sign-out-request) szakaszt a [Microsoft Identity platform és az OpenID Connect protokoll](v2-protocols-oidc.md) fogalmi dokumentációjában.

## <a name="application-registration"></a>Alkalmazás regisztrálása

Az alkalmazás regisztrálása során regisztrálva lesz egy **utólagos kijelentkezési URI**. Az oktatóanyagban a **hitelesítés** lap `https://localhost:44321/signout-oidc` **Speciális beállítások** szakaszának **kijelentkezési URL-címe** mezőjében regisztrálta a regisztrációt. Részletekért lásd: [a webApp alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

## <a name="aspnet-core-code"></a>ASP.NET Core kód

### <a name="signout-button"></a>Kijelentkezés gomb

A kijelentkezés gomb ki van téve `Views\Shared\_LoginPartial.cshtml` a szolgáltatásban, és csak akkor jelenik meg, ha van egy hitelesített fiók (ha a felhasználó korábban bejelentkezett).

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

### <a name="signout-action-of-the-accountcontroller"></a>`Signout()`a művelet`AccountController`

Ha megnyomja a **kijelentkezés** gombot a webalkalmazásban, `SignOut` elindítja `Account` a műveletet a vezérlőn. A ASP.net Core-sablonok korábbi verzióiban a `Account` vezérlőt a webalkalmazásba ágyazták be, de ez már nem jelent meg, mert már része a ASP.net Core-keretrendszernek. 

A kód `AccountController` a ASP.net Core adattárból érhető el a [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)-ból. A fiókok felügyelete:

- Beállítja az OpenID-átirányítási `/Account/SignedOut` URI-t úgy, hogy a vezérlőt vissza lehessen hívni, amikor az Azure ad elvégezte a kijelentkezést
- Meghívások `Signout()`, amelyek lehetővé teszi, hogy a OpenIdConnect köztes kapcsolat a Microsoft Identity platform `logout` végpontján keresztül lépjen kapcsolatba:

  - Törli a munkamenet-cookie-t a böngészőből, és
  - A hívások végül a **kijelentkezési URL-t**kérik vissza, amely alapértelmezés szerint megjeleníti a kijelentkezett nézet [SignedOut. html fájlt](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) , amely a ASP.net Core részeként is elérhető.

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>A `logout` végpontra irányuló hívás elfogása

A ASP.net Core OpenIdConnect middleware lehetővé teszi, hogy az alkalmazás elhallgassa a hívást a `logout` Microsoft Identity platform végpontján egy nevű `OnRedirectToIdentityProviderForSignOut`OpenIdConnect-esemény biztosításával. A webalkalmazás használatával megkísérli elkerülni a fiók kiválasztása párbeszédpanelt, hogy a rendszer kijelentkezéskor megjelenítse a felhasználót. Ez a feltartóztatás az újrafelhasználható `AddAzureAdV2Authentication` függvénytárban `Microsoft.Identity.Web` történik. Lásd: [StartupHelpers.cs L58 – L66](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L58-L66)

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

A ASP.NET-ben a kijelentkezés a kijelentkezés () metódusból aktiválódik a vezérlőn (például AccountController). Ez a metódus nem része a ASP.NET-keretrendszernek (ellentétben a ASP.NET Core), és nem használja az aszinkron használatát, ezért:

- OpenId-kijelentkezési kihívás küldése
- a gyorsítótár kiürítése
- átirányítja a kívánt oldalra

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

Ha nem szeretne ASP.NET Core vagy ASP.NET használni, tekintse meg a protokoll dokumentációját, amely elérhető az [Open ID csatlakozás](./v2-protocols-oidc.md)lehetőségnél.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áthelyezés éles környezetbe](scenario-web-app-sign-user-production.md)
