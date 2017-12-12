---
title: Az Azure Active Directory B2C |} Microsoft Docs
description: "Hogyan kell a .NET-webalkalmazás létrehozása, és hívja meg a webes api Azure Active Directory B2C és az OAuth 2.0 hozzáférési jogkivonatok használatával."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: mtillman
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.openlocfilehash: 42b2b698493408f11ee23f06f99d9ba22860746a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Az Azure AD B2C: .NET webes API-hívás .NET-webalkalmazásból

Az Azure AD B2C segítségével hatékony identitáskezelési funkciókat adhat hozzá a webalkalmazások és webes API-k. A cikkből megtudhatja, hogyan kérjen hozzáférési jogkivonatok és ellenőrizze a .NET "Feladatlista" webes alkalmazás a .NET webes API-t.

Ez a cikk nem foglalkozik megvalósítható bejelentkezési, regisztrációs és profilkezelési Azure AD B2C. A cikk foglalkozik hívó Web API-kat a felhasználó már hitelesítése után. Ha még nem tette meg, a következőket:

* Első lépések egy [.NET-webalkalmazás](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
* Első lépések egy [.NET webes API-t](active-directory-b2c-devquickstarts-api-dotnet.md)

## <a name="prerequisite"></a>Előfeltétel

Hozható létre olyan webes alkalmazás, amely behívja a webes API-t kell:

1. [Az Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md).
2. [Regisztrálja a webes api](active-directory-b2c-app-registration.md#register-a-web-api).
3. [A webes alkalmazás regisztrálása](active-directory-b2c-app-registration.md#register-a-web-app).
4. [Házirendek beállítása](active-directory-b2c-reference-policies.md).
5. [Engedélyeket kap a webes alkalmazás használja a web api](active-directory-b2c-access-tokens.md#publishing-permissions).

> [!IMPORTANT]
> Az ügyfélalkalmazásnak és a webes API-nak ugyanazt az Azure AD B2C könyvtárat kell használnia.
>

## <a name="download-the-code"></a>A kód letöltése

Az oktatóanyag kódjának kezelése a [GitHubon](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) történik. A minta klónozásához futtassa a következőt:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Miután letöltötte a mintakódot, nyissa meg a Visual Studio .sln fájlt a kezdéshez. A megoldásfájl két projektet tartalmaz: `TaskWebApp` és `TaskService`. `TaskWebApp`a felhasználó kommunikál MVC webalkalmazás van. A `TaskService` az alkalmazás webes API háttérszolgáltatása, amely tárolja a felhasználók feladatlistáit. Ez a cikk nem tér ki az épület a `TaskWebApp` webes alkalmazás vagy a `TaskService` webes API-t. Megtudhatja, hogyan hozhat létre az Azure AD B2C segítségével .NET web app, tekintse meg a [.NET web app oktatóanyag](active-directory-b2c-devquickstarts-web-dotnet-susi.md). Megtudhatja, hogyan hozhat létre a .NET webes API-t az Azure AD B2C használatával biztonságossá téve, tekintse meg a [.NET webes API-k oktatóanyag](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Az Azure AD B2C konfiguráció frissítése

A minta úgy van konfigurálva, hogy a bemutató bérlőnk házirendjeit és ügyfél-azonosítóját használja. Ha azt szeretné, használhatja a saját bérlőt:

1. Nyissa meg a `web.config` fájlt a `TaskService` projektben, és a következőképpen cserélje le az értékeket:

    * Az `ida:Tenant` helyett szerepeljen a bérlő neve
    * `ida:ClientId`a webes api-alkalmazás azonosítójú
    * Az `ida:SignUpSignInPolicyId` helyett szerepeljen a „regisztrálási vagy bejelentkezési” házirend neve

2. Nyissa meg a `web.config` fájlt a `TaskWebApp` projektben, és a következőképpen cserélje le az értékeket:

    * Az `ida:Tenant` helyett szerepeljen a bérlő neve
    * Az `ida:ClientId` helyett szerepeljen a webapp alkalmazásazonosítója
    * Az `ida:ClientSecret` helyett szerepeljen a webapp titkos kulcsa
    * Az `ida:SignUpSignInPolicyId` helyett szerepeljen a „regisztrálási vagy bejelentkezési” házirend neve
    * Az `ida:EditProfilePolicyId` helyett szerepeljen a „profil szerkesztése” házirend neve
    * Az `ida:ResetPasswordPolicyId` helyett szerepeljen a „Jelszó alaphelyzetbe állítása” házirend neve



## <a name="requesting-and-saving-an-access-token"></a>A kért és mentése egy hozzáférési jogkivonatot:

### <a name="specify-the-permissions"></a>Az engedélyek megadásához

Annak érdekében, hogy a webes API-hívás, szeretné hitelesíteni a felhasználót (a sign-Close-Up/bejelentkezési házirend használatával) és [olyan hozzáférési jogkivonatot kap](active-directory-b2c-access-tokens.md) az Azure AD B2C. Ahhoz, hogy olyan hozzáférési jogkivonatot kap, először meg kell azt szeretné, hogy a hozzáférési jogkivonat megadását az engedélyeket. Az engedélyek vannak megadva a `scope` paraméter, amikor a kérést a `/authorize` végpont. Ahhoz például, hogy egy hozzáférési jogkivonat App ID URI-azonosítója az erőforrás alkalmazás "olvasási" engedéllyel rendelkező `https://contoso.onmicrosoft.com/tasks`, a hatókör lenne `https://contoso.onmicrosoft.com/tasks/read`.

A hatókör a mintában szereplő, nyissa meg a fájlt `App_Start\Startup.Auth.cs` , és adja meg a `Scope` OpenIdConnectAuthenticationOptions változóját.

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>Exchange-hozzáférési tokent az engedélyezési kódot

Egy felhasználót a regisztráció és a bejelentkezési élmény befejezése után az alkalmazás kap egy engedélyezési kód az Azure AD B2C. Az OWIN OpenID Connect köztes fogja tárolni a kódot, de nem cserélnek azt a hozzáférési tokent. Használhatja a [MSAL könyvtár](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) az exchange végrehajtásához. A mintában szereplő konfigurált a lekérdezésértesítési visszahívást az OpenID Connect köztes be, amikor egy engedélyezési kód érkezik. A visszahívási MSAL használjuk a jogkivonat kód csere, és menteni a jogkivonatot a gyorsítótárba.

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

Ez a szakasz ismerteti, amelyek használata során kapott jogkivonat sign-Close-Up/bejelentkezés az Azure AD B2C a webes API-k eléréséhez.

### <a name="retrieve-the-saved-token-in-the-controllers"></a>A mentett lexikális elem szerepel a vezérlők beolvasása

A `TasksController` felelős kommunikál a webes API-t és HTTP-kérelmek küldéséhez az API használatával olvasását, létrehozását és törölheti a feladatokat. Az API-t az Azure AD B2C által védett, mert kell először kérjen le a jogkivonatot az előző lépésben mentett.

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

### <a name="read-tasks-from-the-web-api"></a>Feladatok olvasni a webes API-k

Ha megkapta a jogkivonatot, csatolhat azt a HTTP `GET` kérheti a `Authorization` biztonságosan hívására fejléc `TaskService`:

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

### <a name="create-and-delete-tasks-on-the-web-api"></a>Hozzon létre vagy töröljön a webes API-k feladatai

Ugyanezt a mintát követik küldésekor `POST` és `DELETE` kéréseket a webes API-hoz, MSAL segítségével kéri le a hozzáférési jogkivonatot a gyorsítótárból.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Végezetül hozza létre, és mindkét az alkalmazások futtatása. Regisztráció és bejelentkezés, és hozzon létre feladatokat a bejelentkezett felhasználó. Kijelentkezés és bejelentkezés másik felhasználóként. Hozzon létre feladatokat ennek a felhasználónak. Figyelje meg, hogyan feladatok felhasználónként tárolja a API, mivel az API kinyeri a felhasználó identitását a jogkivonatból kap. Próbálja meg is játszik és a hatókörök. Távolítsa el a "write", és próbálja meg újból felvenni a feladatok engedéllyel. Ne feledje Kijelentkezés a hatókör minden módosításakor.

