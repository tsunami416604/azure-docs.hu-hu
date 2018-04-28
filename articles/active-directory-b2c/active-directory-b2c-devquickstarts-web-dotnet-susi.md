---
title: Hitelesítés, regisztráció, jelszó-átállítási ASP.NET Azure Active Directory B2C
description: Hogyan hozhat létre, a sign-Close-Up/sign-a webalkalmazás, a profil szerkesztése és a jelszó alaphelyzetbe állítása az Azure Active Directory B2C használatával.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/17/2017
ms.author: davidmu
ms.openlocfilehash: 3f601fbbbb9aab0f942b26da9ad1c8c0a0dc9b16
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>Egy ASP.NET-webalkalmazás létrehozása az Azure Active Directory B2C regisztráció, bejelentkezés, profil szerkesztése és a jelszó alaphelyzetbe állítása

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Az Azure AD B2C szervezetiidentitás-szolgáltatások hozzáadása a webalkalmazáshoz
> * A webes alkalmazás regisztrálása az Azure AD B2C-címtárban
> * Egy felhasználó sign-Close-Up/sign-in, a profil szerkesztése és a jelszó-visszaállítási házirend a webalkalmazás létrehozása

## <a name="prerequisites"></a>Előfeltételek

- A B2C-bérlő csatlakoztatni kell egy Azure-fiókra. Létrehozhat egy ingyenes Azure-fiók [Itt](https://azure.microsoft.com/en-us/).
- Szüksége [Microsoft Visual Studio](https://www.visualstudio.com/) vagy megtekintéséhez és módosításához a mintakódot hasonló programok.

## <a name="create-an-azure-ad-b2c-directory"></a>Azure AD B2C címtár létrehozása

Ahhoz, hogy használni tudja az Azure AD B2C-t, előbb létre kell hoznia egy címtárat vagy bérlőt. A könyvtár egy olyan tároló, a felhasználók, alkalmazások, csoportok és több. Ha egy már nem rendelkezik, ez az útmutató a folytatás előtt hozzon létre egy B2C-címtárat.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Az Azure-előfizetéshez a B2C-bérlő csatlakoztatni kell. Kiválasztása után **létrehozása**, jelölje be a **kapcsolat egy meglévő Azure AD B2C bérlő számára az Azure-előfizetésem** lehetőséget, majd a a **Azure AD B2C bérlő** legördülő listán, válassza ki a a bérlői szeretne hozzárendelni.

## <a name="create-and-register-an-application"></a>Hozzon létre, és egy alkalmazás regisztrálása

Ezt követően kell létrehozni és regisztrálni az alkalmazást a B2C-címtárban. Ez az Azure AD B2C-vel történő alkalmazása biztonságos kommunikációhoz szükséges információkat biztosít. 

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Amikor elkészült, hogy az API-k és a natív alkalmazás az alkalmazás beállításaiban.

## <a name="create-policies-on-your-b2c-tenant"></a>A B2C-bérlő a házirendek létrehozása

Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Ebben a kódmintában három identitásélményt tartalmaz: **előfizetési & bejelentkezési**, **profil szerkesztése**, és **jelszó-változtatási**.  Mindkettőhöz létre kell hoznia egy szabályzatot a [szabályzatok áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md) leírtak szerint. -Házirendet ne feledje, válassza ki a megjelenítendő név attribútum vagy jogcímet, és másolja le a későbbi használatra a házirend nevét.

### <a name="add-your-identity-providers"></a>Az identitás-szolgáltatóktól hozzáadása

Válassza ki a beállítások **identitás-szolgáltatóktól** , és válassza a felhasználónév előfizetési vagy e-mailt létrehozni.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Regisztráció és bejelentkezés házirend létrehozása

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Profilszerkesztési szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Új jelszó kérésére vonatkozó szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

A házirendek létrehozása után készen áll az alkalmazás elkészítésére.

## <a name="download-the-sample-code"></a>A mintakód letöltése

Az oktatóanyag kódjának kezelése a [GitHubon](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) történik. A minta klónozásához futtassa a következőt:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Miután letöltötte a mintakódot, nyissa meg a Visual Studio .sln fájlt a kezdéshez. A megoldásfájl két projektet tartalmaz: `TaskWebApp` és `TaskService`. `TaskWebApp` a rendszer az MVC webalkalmazás, amely a felhasználó kommunikál. A `TaskService` az alkalmazás webes API háttérszolgáltatása, amely tárolja a felhasználók feladatlistáit. Ez a cikk csak a `TaskWebApp` alkalmazást ismerteti. Megtudhatja, hogyan hozhat létre `TaskService` Azure AD B2C segítségével, lásd: [a .NET webes api-oktatóanyag](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>A bérlői és a házirendek kód frissítése

A minta úgy van konfigurálva, hogy a bemutató bérlőnk házirendjeit és ügyfél-azonosítóját használja. Csatlakoztassa a saját bérlőt, nyissa meg a szükséges `web.config` a a `TaskWebApp` projektre, és cserélje le a következő értékeket:

* Az `ida:Tenant` helyett szerepeljen a bérlő neve
* Az `ida:ClientId` helyett szerepeljen a webapp alkalmazásazonosítója
* Az `ida:ClientSecret` helyett szerepeljen a webapp titkos kulcsa
* Az `ida:SignUpSignInPolicyId` helyett szerepeljen a „regisztrálási vagy bejelentkezési” házirend neve
* Az `ida:EditProfilePolicyId` helyett szerepeljen a „profil szerkesztése” házirend neve
* Az `ida:ResetPasswordPolicyId` helyett szerepeljen a „Jelszó alaphelyzetbe állítása” házirend neve

## <a name="launch-the-app"></a>Indítsa el az alkalmazást
A Visual studióban, indítsa el az alkalmazást. A feladatlista lapon keresse meg és jegyezze fel, az URL-címe: https://login.microsoftonline.com/ *YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName*& client_id =*YourclientID*.....

Iratkozzon fel az alkalmazást az e-mail cím vagy a felhasználó neve. Jelentkezzen ki, majd jelentkezzen be újra, és szerkesztheti a profilját, vagy a jelszó alaphelyzetbe állítása. Kijelentkezés és bejelentkezés másik felhasználóként. 

## <a name="add-social-idps"></a>Közösségi IDPs hozzáadása

Jelenleg az alkalmazás támogatja a csak a regisztráció és bejelentkezés felhasználói **helyi fiókok**; fiókok a B2C-címtárban tárolja, amely a felhasználónév és jelszó használata. Az Azure AD B2C segítségével támogathatja más **identitás-szolgáltatóktól** (IDPs) bármely, a kód módosítása nélkül.

Közösségi IDPs hozzáadása az alkalmazáshoz, először ezek a cikkek részletes utasításait követve. Minden egyes IDP, amelyeket támogatni kíván meg kell, hogy a rendszer az alkalmazás regisztrálása és szerezzen be egy ügyfél-azonosítót.

* [Az IDP Facebook beállítása](active-directory-b2c-setup-fb-app.md)
* [Az IDP Google beállítása](active-directory-b2c-setup-goog-app.md)
* [Az IDP Amazon beállítása](active-directory-b2c-setup-amzn-app.md)
* [Az IDP LinkedIn beállítása](active-directory-b2c-setup-li-app.md)

Miután hozzáadta az identitás-szolgáltatóktól B2C-címtárban, módosítsa a három szabályzat tartalmazza az új IDPs mindegyikének leírtak szerint a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md). A házirend mentése után futtassa újra az alkalmazást.  A bejelentkezési hozzáadott új IDPs kell megjelennie, és minden a személyazonosság-előfizetési beállítások észlel.

A házirendek kísérletezhet, és figyelje meg a mintaalkalmazás hatással. Vegye fel vagy távolítsa el a IDPs, kezelheti az alkalmazás jogcímét, vagy módosítsa a regisztrációs attribútumokat. Kísérletet, amíg meg nem látja hogyan házirendeket, a hitelesítési kérelmek és OWIN alkalmazássá.

## <a name="sample-code-walkthrough"></a>A minta kód forgatókönyv
Az alábbi szakaszok bemutatják, hogyan példakód alkalmazás van konfigurálva. Akkor lehet, hogy ezzel útmutatóként a jövőbeli fejlesztés.

### <a name="add-authentication-support"></a>Vegye fel a hitelesítéshez

Most már konfigurálhatja az alkalmazás az Azure AD B2C használatához. Az alkalmazást úgy, hogy az OpenID Connect hitelesítési kérelmeket küld az Azure AD B2C kommunikál. A kérelmek előírják a felhasználói élmény, a házirend hajtható végre szeretné az alkalmazást. A Microsoft OWIN könyvtár segítségével ezeket a kérelmeket küldeni, házirendek hajtható végre, kezelheti a felhasználói munkamenetek és még sok más.

#### <a name="install-owin"></a>Az OWIN telepítése

Első lépésként hozzá az OWIN köztes NuGet-csomagok a projekthez a Visual Studio Csomagkezelő konzol használatával.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

#### <a name="add-an-owin-startup-class"></a>OWIN indítási osztály hozzáadása

Adjon hozzá egy OWIN indítási osztályt a `Startup.cs` nevű API-hoz.  Kattintson a jobb gombbal a projektre, válassza az **Add** (Hozzáadás), majd a **New Item** (Új elem) lehetőséget, és keresse meg az OWIN elemet. Az OWIN közbenső szoftver meghívja a `Configuration(…)` metódust az alkalmazás indulásakor.

A mintánkban módosítottuk az osztálydeklarációt `public partial class Startup` értékre, és implementáltuk az `App_Start\Startup.Auth.cs` fájlban lévő osztály másik részét. A `Configuration` metóduson belül hozzáadtunk egy hívást a `ConfigureAuth` metódusra, amely a `Startup.Auth.cs` fájlban van definiálva. A módosítás után a `Startup.cs` a következőképpen néz ki:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

#### <a name="configure-the-authentication-middleware"></a>A hitelesítési köztes konfigurálása

Nyissa meg a fájlt `App_Start\Startup.Auth.cs` és megvalósítását a `ConfigureAuth(...)` metódust. Megadja a paraméterek `OpenIdConnectAuthenticationOptions` kommunikálni az Azure AD B2C-koordináták az alkalmazások kiszolgálására. Ha nem adja meg a paraméterek, az alapértelmezett értéket fogja használni. Például, hogy ne adja meg a `ResponseType` minta, ezért a alapértelmezett érték `code id_token` minden kimenő kérelem Azure AD B2C használni fogják.

Szükség cookie-hitelesítés beállítása. Az OpenID Connect köztes cookie-kat használ a felhasználói munkameneteket, többek között.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"openid profile offline_access {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

A `OpenIdConnectAuthenticationOptions` újabb meghatároztuk visszahívási funkciók az OpenID Connect köztes által kapott az adott értesítésekhez. Ezek közül a viselkedésmódok meghatározása egy `OpenIdConnectAuthenticationNotifications` objektumra, és tárolja azokat a `Notifications` változó. A minta a három különböző visszahívások attól függően, hogy az esemény azt meg.

### <a name="using-different-policies"></a>Eltérő házirendek használatával

A `RedirectToIdentityProvider` értesítési aktiválódik, amikor egy kérelem Azure AD B2C. A visszahívási függvény `OnRedirectToIdentityProvider`, rendszer ellenőrzi a kimenő hívásban, ha azt szeretné, hogy egy másik házirend. Ahhoz, hogy ne a jelszó alaphelyzetbe állítása, vagy szerkesztheti a profilját, például a jelszó-visszaállítási házirend az alapértelmezett "Regisztráció vagy bejelentkezés" házirend helyett használja a megfelelő házirendet kell.

A mintában, amikor a felhasználó szeretné a jelszó alaphelyzetbe állítása, vagy szerkesztheti a profilját, azt a házirend hozzáadása azt előnyben részesítik a OWIN környezetben való használatát. Amely a következő módon hajtható végre:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

A visszahívási függvény is létrehozható és `OnRedirectToIdentityProvider` a következő tevékenységek végrehajtásával:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

### <a name="handling-authorization-codes"></a>Hitelesítési kódok kezelése

A `AuthorizationCodeReceived` értesítési lesz kiváltva, ha az engedélyezési kód érkezik. Az OpenID Connect köztes nem támogatja a hozzáférési jogkivonatok cserélő kódokat. Manuálisan továbbíthatja a jogkivonat a visszahívási függvény a kódját. További információkért tekintse meg a [dokumentáció](active-directory-b2c-devquickstarts-web-api-dotnet.md) , amely ismerteti, hogyan.

### <a name="handling-errors"></a>Hibák kezelése

A `AuthenticationFailed` értesítési lesz kiváltva, ha a hitelesítés sikertelen. A visszahívási metódus képes kezelni a hibákat, igény szerint. Azonban hozzá kell adnia egy ellenőrizze a hiba kódja `AADB2C90118`. A "Regisztráció vagy bejelentkezés" házirend végrehajtása közben a felhasználó rendelkezik-e választhat egy **elfelejtette a jelszavát?** hivatkozásra. Ebben az esetben az Azure AD B2C elküldi az alkalmazás adott hiba mely azt jelzi, hogy az alkalmazás egy kérelmet, használja helyette: a jelszó-visszaállítási házirend győződjön.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

### <a name="send-authentication-requests-to-azure-ad"></a>Az Azure AD hitelesítési kérések küldése

Az alkalmazás megfelelően konfigurálva van az Azure AD B2C az OpenID Connect hitelesítési protokoll használatával kommunikálnak. OWIN kezeli a hitelesítési üzenetek létrehozásával, az ellenőrzése az Azure AD B2C származó jogkivonatokat és a felhasználói munkamenet megtartásával részleteit. Most már minden felhasználói folyamat kezdeményezéséhez.

Amikor a felhasználó megadja **Sign up/bejelentkezési**, **profilszerkesztés**, vagy **jelszó-átállítási** web app alkalmazásban a társított művelet indították el a `Controllers\AccountController.cs`:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

OWIN segítségével jelentkezzen ki a felhasználó az alkalmazásból. A `Controllers\AccountController.cs` vezetünk be:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Explicit módon hívja meg a házirend, akkor is használhatnak a `[Authorize]` a tartományvezérlőket-címke, amely végrehajtja a házirend, ha a felhasználó nem jelentkezett be. Nyissa meg `Controllers\HomeController.cs` , és adja hozzá a `[Authorize]` címkén belül, hogy a jogcímek vezérlő.  Az utolsó házirend állították be, mikor OWIN választja ki a `[Authorize]` címke talált.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Felhasználói adatok megjelenítése

Amikor a felhasználók hitelesítésére OpenID Connect használatával, ha az Azure AD B2C azonosítója jogkivonatot ad vissza, amely tartalmazza az alkalmazás **jogcímek**. Ezek a helyességi feltételek a felhasználóról. Az alkalmazás személyre szabása jogcímek használata.

Nyissa meg az `Controllers\HomeController.cs` fájlt. Felhasználói jogcímeket, a vezérlők keresztül érheti el a `ClaimsPrincipal.Current` rendszerbiztonsági objektumot.

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Bármilyen jogcímet, amely az alkalmazás fogad ugyanúgy érheti el.  Az alkalmazás kap minden jogcím listája érhető el, a a **jogcímek** lap.
