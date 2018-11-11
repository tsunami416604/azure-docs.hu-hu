---
title: .NET webes API hívása egy .NET-webalkalmazás létrehozása az Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan kell egy .NET-webalkalmazás létrehozása és a egy webes API-t az Azure Active Directory B2C-vel és az OAuth 2.0 hozzáférési jogkivonatok használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7296954a17b21183eb8be2744b42289522cf7f57
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012496"
---
# <a name="call-a-net-web-api-from-a-net-web-app-in-azure-active-directory-b2c"></a>.NET webes API hívása egy .NET-webalkalmazás létrehozása az Azure Active Directory B2C-vel

Azure AD B2C használatával adhat hozzá hatékony identitáskezelési funkciókat a webalkalmazások és webes API-kat. Ez a cikk ismerteti, hogyan kérhetnek a hozzáférési jogkivonatok és a márka .NET "Feladatlista" webes alkalmazás a .NET webes API-t.

Ez a cikk nem tárgyalja a megvalósítása a bejelentkezési, regisztrációs és profilkezelési az Azure AD B2C-vel. A cikk foglalkozik hívó webes API-k után a felhasználó már hitelesítve van. Ha még nem tette, hogy az alábbi lépéseket kell tennie:

* Ismerkedés a [.NET-webalkalmazás](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Ismerkedés a [.NET webes API-hoz](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Előfeltétel

Hozhat létre egy webalkalmazást, amely meghív egy webes api-t kell tennie:

1. [Azure AD B2C-bérlő létrehozása](active-directory-b2c-get-started.md).
2. [Regisztráljon egy webes api](active-directory-b2c-app-registration.md).
3. [Webalkalmazás regisztrációja](active-directory-b2c-app-registration.md).
4. [Házirendek beállítása](active-directory-b2c-reference-policies.md).
5. [Engedélyeket a webes alkalmazás használatához a webes api](active-directory-b2c-access-tokens.md).

> [!IMPORTANT]
> Az ügyfélalkalmazásnak és a webes API-nak ugyanazt az Azure AD B2C könyvtárat kell használnia.
>

## <a name="download-the-code"></a>A kód letöltése

Az oktatóanyag kódjának kezelése a [GitHubon](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) történik. A minta klónozásához futtassa a következőt:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Miután letöltötte a mintakódot, nyissa meg a Visual Studio .sln fájlt a kezdéshez. A megoldásfájl két projektet tartalmaz: `TaskWebApp` és `TaskService`. `TaskWebApp` van egy MVC-webalkalmazást, amely a felhasználó kommunikál. A `TaskService` az alkalmazás webes API háttérszolgáltatása, amely tárolja a felhasználók feladatlistáit. Ez a cikk nem tárgyalja a building a `TaskWebApp` webalkalmazás vagy a `TaskService` webes API-t. A .NET-webalkalmazás létrehozása az Azure AD B2C használatával kapcsolatban lásd: a [.NET-alapú webappokról szóló oktatóanyagunkat](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Ismerje meg, hogyan hozhat létre a .NET webes API Azure AD B2C használatával biztonságossá téve, tekintse meg a [.NET webes API-k oktatóanyag](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Az Azure AD B2C konfiguráció frissítése

A minta úgy van konfigurálva, hogy a bemutató bérlőnk házirendjeit és ügyfél-azonosítóját használja. Ha szeretné, hogy a saját bérlő használatához:

1. Nyissa meg a `web.config` fájlt a `TaskService` projektben, és a következőképpen cserélje le az értékeket:

    * Az `ida:Tenant` helyett szerepeljen a bérlő neve
    * `ida:ClientId` az a webes api-alkalmazás azonosítója
    * Az `ida:SignUpSignInPolicyId` helyett szerepeljen a „regisztrálási vagy bejelentkezési” házirend neve

2. Nyissa meg a `web.config` fájlt a `TaskWebApp` projektben, és a következőképpen cserélje le az értékeket:

    * Az `ida:Tenant` helyett szerepeljen a bérlő neve
    * Az `ida:ClientId` helyett szerepeljen a webapp alkalmazásazonosítója
    * Az `ida:ClientSecret` helyett szerepeljen a webapp titkos kulcsa
    * Az `ida:SignUpSignInPolicyId` helyett szerepeljen a „regisztrálási vagy bejelentkezési” házirend neve
    * Az `ida:EditProfilePolicyId` helyett szerepeljen a „profil szerkesztése” házirend neve
    * Az `ida:ResetPasswordPolicyId` helyett szerepeljen a „Jelszó alaphelyzetbe állítása” házirend neve



## <a name="requesting-and-saving-an-access-token"></a>Kér, és a hozzáférési token mentése

### <a name="specify-the-permissions"></a>Az engedélyek megadása

A webes API-hívás indításához kell hitelesíteni a felhasználót (a regisztrálási-regisztrálási vagy bejelentkezési szabályzat használatával) és [egy hozzáférési jogkivonatot kap](active-directory-b2c-access-tokens.md) az Azure AD B2C-ből. Annak érdekében, hogy hozzáférési jogkivonatot kap, először adjon meg szeretné adni a hozzáférési jogkivonatot az engedélyeket. Az engedélyek vannak megadva a `scope` paraméter a kérelmet, győződjön meg arról, ha a `/authorize` végpont. Például a "olvasási" engedéllyel az erőforrás-alkalmazás, amely rendelkezik App ID URI-ját a hozzáférési jogkivonat beszerzése `https://contoso.onmicrosoft.com/tasks`, a hatókör lenne `https://contoso.onmicrosoft.com/tasks/read`.

Ebben a mintában a hatókört ad meg, nyissa meg a fájl `App_Start\Startup.Auth.cs` , és meghatározhatja a `Scope` OpenIdConnectAuthenticationOptions változóját.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (separated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Az engedélyezési kódot, a hozzáférési jogkivonat

Egy felhasználó a regisztrálási vagy bejelentkezési élmény befejezése után az alkalmazás megkapja az engedélyezési kódot az Azure AD B2C-ből. Az OWIN OpenID Connect közbenső szoftvert fogja tárolni a kódot, de a rendszer nem exchange-, egy hozzáférési jogkivonatot. Használhatja a [MSAL könyvtár](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) , hogy az exchange. A mintánkban konfiguráltuk a lekérdezésértesítési visszahívást, az OpenID Connect közbenső szoftvert, amikor egy engedélyezési kód érkezik. A visszahívás MSAL használjuk az exchange-jogkivonat a kódot, és mentse a token a gyorsítótárba.

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>A webes API meghívása

Ez a szakasz bemutatja, hogyan használható a token során kapott való-ra vagy jelentkezzen be az Azure AD B2C a webes API-k eléréséhez.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>A vezérlő a mentett jogkivonat lekérése

A `TasksController` felelős kommunikál a webes API-t és a HTTP-kérések küldését az API-t, olvassa el, hozzon létre és törölheti a feladatokat. Az API-t az Azure AD B2C által védett, mert kell először kérjen le a jogkivonatot az előző lépésben mentette.

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Olvassa el a feladatokat a webes API

Ha megkapta a jogkivonatot, csatlakoztathat, a HTTP `GET` kérheti a a `Authorization` biztonságos hívása fejléc `TaskService`:

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Hozzon létre vagy töröljön a webes API műveletek

Ugyanezt a mintát követi, amikor küld `POST` és `DELETE` kéréseket a webes API-t, az MSAL segítségével a hozzáférési jogkivonat lekérése a gyorsítótárból.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Végül hozhat létre, és mindkét az alkalmazások futtatására. Regisztráljon, és jelentkezzen be, és hozzon létre feladatokat a bejelentkezett felhasználó. Jelentkezzen ki, és jelentkezzen be másik felhasználóként. Hozzon létre feladatokat ennek a felhasználónak. Figyelje meg, hogy hogyan feladatok felhasználónként tárolja az API, mert az API kinyeri a felhasználó identitását a jogkivonat kap. Lejátszás hatókörökkel is kipróbálhatja. Távolítsa el a "write", és ismételje meg a feladat hozzáadása szükséges engedély. Csak ellenőrizze, hogy jelentkezzen ki a hatókört minden módosításakor.

