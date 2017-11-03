---
title: "Az Azure AD v2.0 .NET web app hívó API bevezetés |} Microsoft Docs"
description: ".NET MVC-webalkalmazás létrehozása a hívások web services személyes Microsoft-fiókok és a munkahelyi vagy iskolai fiókot a következő bejelentkezés alkalmával."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dc3162ae8e6ce622139125c2e78fa45d2e90d534
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="calling-a-web-api-from-a-net-web-app"></a>A webes API hívása a .NET-webalkalmazás
A v2.0-végponttal gyorsan hitelesítés hozzáadása a webalkalmazások és webes API-khoz személyes Microsoft-fiókot is támogatása és a munkahelyi vagy iskolai fiókok.  Itt azt fogja létrehozni egy MVC webalkalmazás, mely aláírja a felhasználók az OpenID Connect, használja a Microsoft OWIN köztes segítséget.  A webes alkalmazás OAuth 2.0 hozzáférési jogkivonatok lekérése egy webes api lehetővé teszi, hogy OAuth 2.0-s által védett létrehozása, olvasása, és törli a egy adott felhasználó "Feladatlista".

Ez az oktatóanyag összpontosítanak elsősorban MSAL segítségével szerezzen be, és a hozzáférési jogkivonatok egy webalkalmazást, teljes ismertetett [Itt](active-directory-v2-flows.md#web-apps).  Előfeltételként, érdemes lehet először megtudhatja, hogyan [hozzáadása alapszintű bejelentkezés a webes alkalmazás](active-directory-v2-devquickstarts-dotnet-web.md) vagy hogyan [megfelelően a webes API biztonságossá tétele](active-directory-v2-devquickstarts-dotnet-api.md).

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

## <a name="download-sample-code"></a>Mintakód letöltése
Az oktatóanyag kódjának [karbantartása a GitHubon történik](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Követéséhez is [töltse le az alkalmazás vázát egy .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) vagy klónozza a vázat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Alternatív megoldásként, [töltse le a kész alkalmazásról, mint egy .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) vagy a klónozáshoz a kész alkalmazásról:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Alkalmazás regisztrálása
Hozzon létre egy új alkalmazást [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy kövesse az alábbi [részletes lépéseket](active-directory-v2-app-registration.md).  Győződjön meg arról, hogy:

* Másolja le a **alkalmazásazonosító** be az alkalmazáshoz hozzárendelt szüksége lehet rájuk hamarosan.
* Hozzon létre egy **alkalmazás titkos kulcs** , a **jelszó** típusát, és másolja le az érték későbbi használatra
* Adja hozzá a **webes** platform az alkalmazásra vonatkozóan.
* Adja meg a megfelelő **átirányítási URI-**. Az Azure ad Szolgáltatásba, ahol hitelesítési válaszok legyenek irányítva – ehhez az oktatóanyaghoz az alapértelmezett érték jelzi az átirányítási uri `https://localhost:44326/`.

## <a name="install-owin"></a>Az OWIN telepítése
Adja hozzá az OWIN köztes NuGet-csomagok számára a `TodoList-WebApp` projektre a Csomagkezelő konzol használatával.  Az OWIN köztes be- és kijelentkezési kérések kiállítása, a felhasználói munkamenet kezelésére, és többek között a felhasználó adatai használható.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>A felhasználói bejelentkezés
Mostantól konfigurálhatja az OWIN közbenső szoftvert használja a [OpenID Connect hitelesítési protokoll](active-directory-v2-protocols.md).  

* Nyissa meg a `web.config` gyökérkönyvtárában található fájl a `TodoList-WebApp` projektre, és adja meg az alkalmazás konfigurációs értékeit a `<appSettings>` szakasz.
  * A `ida:ClientId` van a **alkalmazásazonosító** az alkalmazáshoz a regisztrációs portálon rendelt.
  * A `ida:ClientSecret` van a **alkalmazás titkos kulcs** a regisztrációs portálon létrehozott.
  * A `ida:RedirectUri` van a **átirányítási Uri-** a portálon megadott.
* Nyissa meg a `web.config` gyökérkönyvtárában található fájl a `TodoList-Service` projektre, és cserélje le a `ida:Audience` azonos **alkalmazásazonosító** a fentiek szerint.
* Nyissa meg a fájlt `App_Start\Startup.Auth.cs` , és adja hozzá `using` nyilatkozatait, a könyvtárak a fent.
* Ugyanebben a fájlban, valósítja meg a `ConfigureAuth(...)` metódust.  Megadja a paraméterek `OpenIDConnectAuthenticationOptions` koordináták az alkalmazás az Azure AD kommunikálni fog szolgálni.

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
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>A hozzáférési jogkivonatok lekérésére MSAL segítségével
Az a `AuthorizationCodeReceived` használandó szeretnénk értesítési, [OAuth 2.0 párhuzamosan az OpenID Connect](active-directory-v2-protocols.md) a hozzáférési tokent az tennivalók listája szolgáltatáshoz authorization_code beváltani.  MSAL teheti a folyamat egyszerűen meg:

* Először telepítse a MSAL előzetes verziója:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* Adja hozzá egy másik `using` nyilatkozatot, így a `App_Start\Startup.Auth.cs` MSAL fájlt.
* Ezután adja hozzá egy új módszer, a `OnAuthorizationCodeReceived` eseménykezelő.  A kezelő MSAL fogja használni a Tennivalók listája API olyan hozzáférési jogkivonatot szerezni, és a jogkivonat-ban tárolja MSAL tartozó jogkivonat gyorsítótára későbbi használatra:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* A webalkalmazásokban MSAL rendelkezik egy bővíthető jogkivonat gyorsítótára jogkivonatok tárolására használható.  Ez a minta valósítja meg a `NaiveSessionCache` gyorsítótár jogkivonatok http munkamenet tárhelyet használó.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>A webes API hívása
Most már időpontok a ténylegesen a 3. lépésében beszerzett access_token.  Nyissa meg a webes alkalmazást `Controllers\TodoListController.cs` fájlt, amely a CRUD-kérelmeket küld az tennivalók listája API-t.

* Segítségével MSAL újra ide access_tokens lehívni a MSAL gyorsítótárból.  Először adja hozzá a `using` MSAL nyilatkozata ehhez a fájlhoz.
  
    `using Microsoft.Identity.Client;`
* Az a `Index` művelet, használjon MSAL `AcquireTokenSilentAsync` metódus használatával kérje le egy access_token adatokat olvasni a feladatlista szolgáltatás használható:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* A minta hozzáadja az így létrejövő jogkivonatot a HTTP GET kérelemre, a `Authorization` fejlécből a feladatlista szolgáltatás használatával hitelesíteni a kérelmet.
* Ha a feladatlista-szolgáltatás egy `401 Unauthorized` választ, a MSAL access_tokens váltak érvénytelen valamilyen okból.  Ebben az esetben engedje el bármilyen access_tokens a MSAL gyorsítótárból és megjelenítése a felhasználó egy üzenetet, szükséges lehet, hogy jelentkezzen be újra, amely a token beszerzési folyamata újraindul.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* Hasonlóképpen ha MSAL nem lehet visszaadni egy access_token bármilyen okból, meg kell kérni a felhasználót, hogy jelentkezzen be újra.  Ez egyszerűen bármelyik elfogja az `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* A pontos azonos `AcquireTokenSilentAsync` tekintendő, amely a implementd a `Create` és `Delete` műveletek.  A webalkalmazásokban akkor is ezt a módszert MSAL access_tokens lekérdezni, amikor az alkalmazás kell.  MSAL beszerzése, gyorsítótárazás és jogkivonatok frissítése az Ön kezeli.

Végül felépítéséhez és az alkalmazás futtatása!  Jelentkezzen be Microsoft-Account vagy egy Azure AD-fiókot, és figyelje meg, hogyan jelenik meg a felső navigációs sáv a felhasználó identitását.  Adja hozzá, és töröljön néhány elemet a felhasználók feladatlistáit az OAuth 2.0 biztonságos működés közben az API-hívásokban megjelenítéséhez.  Most már rendelkezik egy webalkalmazás & webes API egyaránt az iparági szabványos protokollok, amely képes hitelesíteni a személyes és munkahelyi vagy iskolai fiókkal rendelkező felhasználók használatával biztonságossá.

Az elkészült mintát (a konfigurációs értékek nélkül) referenciaként [itt megadott](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Következő lépések
További forrásokért tekintse meg:

* [A v2.0 – útmutató fejlesztőknek >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "azure-active-directory" címke >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Biztonsági frissítések termékeinkhez
Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről. Látogasson el [erre a lapra](https://technet.microsoft.com/security/dd252948), és fizessen elő a biztonsági tanácsadói riasztásokra.

