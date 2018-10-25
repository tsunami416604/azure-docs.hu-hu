---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: ac8e678d8ca006fc5b0ab52f007590ac4e334bf7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988543"
---
## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz bemutatja a lépéseket, telepítése és konfigurálása a hitelesítési folyamat keresztül OWIN közbenső szoftvert az OpenID Connect hitelesítést használó ASP.NET-projekt.

> Töltse le ezt a mintát a Visual Studio-projekt ehelyett inkább? [Töltse le a projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) , és ugorjon a [konfigurációs lépés](#register-your-application) konfigurálása a kódminta végrehajtása előtt.

### <a name="create-your-aspnet-project"></a>Az ASP.NET-projekt létrehozása

1. A Visual Studióban: `File` > `New` > `Project`
2. A *Visual C# \Web*válassza `ASP.NET Web Application (.NET Framework)`.
3. Adjon nevet az alkalmazásnak, és kattintson a *OK*
4. Válassza ki `Empty` , és adja hozzá a jelölőnégyzet bejelölésével `MVC` referenciák

## <a name="add-authentication-components"></a>Adja hozzá a hitelesítés összetevői

1. A Visual Studióban: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Az *OWIN közbenső szoftver NuGet-csomagokat* hozzáadásához írja az alábbiakat a Package Manager Console (Csomagkezelő konzol) ablakba:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Ezeket a kódtárakat
> A fenti kódtárak engedélyezik az egyszeri bejelentkezést (SSO) az OpenID Connecttel, cookie-alapú hitelesítés használatával. Ha befejeződött a hitelesítés, és a rendszer elküldte a felhasználóhoz tartozó jogkivonatot az alkalmazásnak, az OWIN közbenső szoftver létrehoz egy munkameneti cookie-t. A böngésző majd használja a cookie-k további kérések így a felhasználóknak nem kell újra a jelszót, és nincs további ellenőrzés van szükség.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>A hitelesítési folyamat konfigurálásához
Az alábbi lépések segítségével hozzon létre egy OWIN indítási osztályt OpenID Connect-hitelesítés konfigurálása közbenső szoftvert. Ez az osztály a lesz automatikusan végrehajtva, amikor az IIS-folyamat elindul.

> [!TIP]
> Ha a projekt gyökérmappája nem tartalmazza a `Startup.cs` fájlt:
> 1. Kattintson a jobb gombbal a projektmappa gyökérkönyvtárába a: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Adja neki a `Startup.cs` nevet
>
>> Győződjön meg arról, hogy a kiválasztott osztály egy OWIN indítási osztály, és nem egy szabványos C#-osztály. Ehhez ellenőrizze, hogy látja-e a következőt a névtér fölött: `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`.

1. Adjon hozzá *OWIN* és *Microsoft.IdentityModel* való hivatkozások `Startup.cs`:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Indítási osztályt cserélje le az alábbi kódot:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>További információ
> Az *OpenIDConnectAuthenticationOptions* alatt megadott paraméterek koordinátaként szolgálnak az alkalmazás számára az Azure AD-vel való kommunikációhoz. Az OpenID Connect közbenső szoftvert használ cookie-kat a háttérben, mert azt is be kell állítania hitelesítési cookie-k a fent látható kóddal. A *ValidateIssuer* értéke arra utasítja az OpenIdConnectet, hogy ne korlátozza a hozzáférést egy adott szervezetre.
<!--end-collapse-->
