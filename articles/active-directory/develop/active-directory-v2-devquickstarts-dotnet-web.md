---
title: "Az Azure AD v2.0 .NET web app bejelentkezési első lépések |} Microsoft Docs"
description: "Hogyan hozhat létre egy .NET MVC webalkalmazás, mely aláírja a felhasználók be mindkét személyes Microsoft-Account és a munkahelyi vagy iskolai fiókját."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mtillman
editor: 
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 44691f7c06aede764c3bf0dcc99848a4f22ce08d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Bejelentkezés hozzáadása egy .NET MVC webalkalmazás
A v2.0-végponttal támogatja a személyes Microsoft-fiókot is a webes alkalmazásokhoz való hitelesítés és a munkahelyi vagy iskolai fiókok gyorsan is hozzáadhat.  Az ASP.NET web apps Ez elvégezhető a .NET-keretrendszer 4.5 része a Microsoft OWIN köztes használatával.

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
>
>

 Itt azt fogja létrehozni egy webes alkalmazás OWIN beléptetni a felhasználót, a felhasználói információkat jelenítsen meg, és jelentkezzen ki az alkalmazásból a felhasználó által.

## <a name="download"></a>Letöltés
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Követéséhez is [töltse le az alkalmazás vázát egy .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) vagy klónozza a vázat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

A kész alkalmazásról, valamint az oktatóanyag végén valósul meg.

## <a name="register-an-app"></a>Alkalmazás regisztrálása
Hozzon létre egy új alkalmazást [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy kövesse az alábbi [részletes lépéseket](active-directory-v2-app-registration.md).  Győződjön meg arról, hogy:

* Másolja le a **alkalmazásazonosító** be az alkalmazáshoz hozzárendelt szüksége lehet rájuk hamarosan.
* Adja hozzá a **webes** platform az alkalmazásra vonatkozóan.
* Adja meg a megfelelő **átirányítási URI-**. Az Azure ad Szolgáltatásba, ahol hitelesítési válaszok legyenek irányítva – ehhez az oktatóanyaghoz az alapértelmezett érték jelzi az átirányítási uri `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Telepítse és konfigurálja az OWIN hitelesítést
Itt konfigurálását végezzük el az OWIN közbenső szoftvert az OpenID Connect hitelesítési protokoll használatára.  OWIN be- és kijelentkezési kérések kiállítása, a felhasználói munkamenet kezelésére, és többek között a felhasználó adatai használható.

1. Első lépésként nyissa meg a `web.config` fájlt a projekt gyökérkönyvtárában, és adja meg az alkalmazás konfigurációs értékeit a `<appSettings>` szakasz.

  * A `ida:ClientId` van a **alkalmazásazonosító** az alkalmazáshoz a regisztrációs portálon rendelt.
  * A `ida:RedirectUri` van a **átirányítási Uri-** a portálon megadott.

2. Ezt követően az OWIN köztes NuGet-csomagok hozzáadása a projekthez, a Csomagkezelő konzol használatával.

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. Adja hozzá a "OWIN indítási osztály" a projekt neve `Startup.cs` jobb kattintson a projektre--> **Hozzáadás** --> **új elem** --> "OWIN" keresése.  Az OWIN közbenső szoftver meghívja a `Configuration(...)` metódust az alkalmazás indulásakor.
4. Módosítsa az osztálydeklaráció való `public partial class Startup` -azt korábban már megvalósított Ez az osztály tartozik, egy másik fájlban.  Az a `Configuration(...)` metódus hívása ConfigureAuth(...) hitelesítés a webalkalmazás beállítása legyen  

        ```C#
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. Nyissa meg a fájlt `App_Start\Startup.Auth.cs` és megvalósítását a `ConfigureAuth(...)` metódust.  Megadja a paraméterek `OpenIdConnectAuthenticationOptions` koordináták az alkalmazás az Azure AD kommunikálni fog szolgálni.  Biztosítani kell a Cookie-hitelesítés beállítása – az OpenID Connect köztes alatt a magában foglalja az cookie-kat használ.

        ```C#
        public void ConfigureAuth(IAppBuilder app)
                     {
                             app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);
        
                             app.UseCookieAuthentication(new CookieAuthenticationOptions());
        
                             app.UseOpenIdConnectAuthentication(
                                     new OpenIdConnectAuthenticationOptions
                                     {
                                             // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                                             // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                             // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.
        
                                             ClientId = clientId,
                                             Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
                                             RedirectUri = redirectUri,
                                             Scope = "openid email profile",
                                             ResponseType = "id_token",
                                             PostLogoutRedirectUri = redirectUri,
                                             TokenValidationParameters = new TokenValidationParameters
                                             {
                                                     ValidateIssuer = false,
                                             },
                                             Notifications = new OpenIdConnectAuthenticationNotifications
                                             {
                                                     AuthenticationFailed = OnAuthenticationFailed,
                                             }
                                     });
                     }
        ```

## <a name="send-authentication-requests"></a>Hitelesítési kérések küldése
Az alkalmazás megfelelően van konfigurálva az OpenID Connect hitelesítési protokoll használatával a v2.0-végponttal való kommunikációhoz.  OWIN rendelkezik végrehajtott összes hitelesítési üzenetek létrehozásával, ellenőrzése az Azure ad-jogkivonatok és karbantartása a felhasználói munkamenet ugly részletes megvagyunk.  Most már kell biztosítani a felhasználóknak a be-és kijelentkezés úgy.

- Használhatja a tartományvezérlőket, hogy a felhasználó bejelentkezik egy bizonyos lap elérése előtt kötelező címkék engedélyezik.  Nyissa meg `Controllers\HomeController.cs`, és adja hozzá a `[Authorize]` címke a jogi tudnivalók megjelenítése Névjegy vezérlőhöz.
        
        ```C#
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- OWIN segítségével közvetlenül kiadni a kód a érkező hitelesítési kéréseket.  Nyissa meg `Controllers\AccountController.cs`.  A SignIn() és SignOut() műveletek adja a challenge OpenID Connect és kijelentkezési kérések ki.

        ```C#
        public void SignIn()
        {
            // Send an OpenID Connect sign-in request.
            if (!Request.IsAuthenticated)
            {
                HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }
        
        // BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
        public void SignOut()
        {
            // Send an OpenID Connect sign-out request.
            HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            Response.Redirect("/");
        }
        ```

- Most, nyissa meg a `Views\Shared\_LoginPartial.cshtml`.  Ez az amelyen megjelenítése a felhasználónak az alkalmazás be- és kijelentkezési hivatkozások, és nyomtassa ki a nézetben a felhasználó nevét.

        ```HTML
        @if (Request.IsAuthenticated)
        {
            <text>
                <ul class="nav navbar-nav navbar-right">
                    <li class="navbar-text">
        
                        @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@
        
                        Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
                    </li>
                    <li>
                        @Html.ActionLink("Sign out", "SignOut", "Account")
                    </li>
                </ul>
            </text>
        }
        else
        {
            <ul class="nav navbar-nav navbar-right">
                <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }
        ```

## <a name="display-user-information"></a>Felhasználói adatok megjelenítése
Az OpenID Connect felhasználók hitelesítésekor a v2.0-végpontra egy id_token az alkalmazást, a jogcímeket vagy helyességi feltételek a felhasználóról tartalmaz adja vissza.  Ezeket a jogcímeket segítségével szabja személyre az alkalmazást:

- Nyissa meg az `Controllers\HomeController.cs` fájlt.  A felhasználói jogcímek, a vezérlők keresztül érheti el a `ClaimsPrincipal.Current` rendszerbiztonsági objektumot.

        ```C#
        [Authorize]
        public ActionResult About()
        {
            ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;
        
            // The object ID claim will only be emitted for work or school accounts at this time.
            Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
            ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;
        
            // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
            ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;
        
            // The subject or nameidentifier claim can be used to uniquely identify the user
            ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        
            return View();
        }
        ```

## <a name="run"></a>Futtassa a következőt:
Végül felépítéséhez és az alkalmazás futtatása!   Jelentkezzen be személyes Microsoft-Account vagy a munkahelyi vagy iskolai fiókkal, és figyelje meg, hogyan jelenik meg a felső navigációs sáv a felhasználó identitását.  Most már rendelkezik egy webalkalmazást, amely képes hitelesíteni a személyes és munkahelyi vagy iskolai fiókkal rendelkező felhasználók iparági szabványos protokollok használatával biztonságossá.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [is letöltheti a .zip Itt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), vagy a Githubból is klónozhatja:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Következő lépések
Ön most már továbbléphet az összetettebb témákra.  Előfordulhat, hogy ki szeretné próbálni:

[A Web API-t biztonságossá a a v2.0-végpontra >>](active-directory-devquickstarts-webapi-dotnet.md)

További forrásokért tekintse meg:

* [A v2.0 – útmutató fejlesztőknek >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "azure-active-directory" címke >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Biztonsági frissítések termékeinkhez
Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről. Látogasson el [erre a lapra](https://technet.microsoft.com/security/dd252948), és fizessen elő a biztonsági tanácsadói riasztásokra.
