---
title: Hitelesítés, előfizetési, jelszó-visszaállítás, az Azure Active Directory B2C |} A Microsoft Docs
description: Hogyan hozhat létre egy webalkalmazást, amely rendelkezik regisztrálási-regisztrálási vagy bejelentkezési profil szerkesztése és a jelszó alaphelyzetbe állítása, Azure Active Directory B2C használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 838717aa8f426ea54f4736453aac8c6fcf9a0099
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344497"
---
# <a name="create-an-aspnet-web-app-with-azure-active-directory-b2c-sign-up-sign-in-profile-edit-and-password-reset"></a>ASP.NET-webalkalmazás létrehozása az Azure Active Directory B2C regisztrációs, bejelentkezési,-profilszerkesztést és jelszó alaphelyzetbe állítása

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Azure AD B2C identitáskezelési funkciókat hozzáadása a webalkalmazáshoz
> * A webes alkalmazás regisztrálása az Azure AD B2C-címtárban
> * Hozzon létre egy regisztrálási-regisztrálási vagy bejelentkezési felhasználói, -profilszerkesztést és a webalkalmazás a jelszó-visszaállítási házirend

## <a name="prerequisites"></a>Előfeltételek

- A B2C-bérlő kapcsolódnia kell egy Azure-fiókra. Létrehozhat egy ingyenes Azure-fiókkal [Itt](https://azure.microsoft.com/).
- Szükséges [Microsoft Visual Studio](https://www.visualstudio.com/) vagy más hasonló program megtekintése és módosítása a mintakódot.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C bérlő létrehozása

Használatához Azure AD B2C-vel, létre kell hoznia egy bérlőt. A bérlő egy olyan tároló, a minden felhasználó, alkalmazások, csoportok és így tovább. Ha Ön nem rendelkezik ilyennel, hozzon létre egy B2C-bérlő ebben az útmutatóban a folytatás előtt.

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

> [!NOTE]
> 
> Az Azure-előfizetéshez az Azure AD B2C-bérlő csatlakozni kell. Kiválasztása után **létrehozás**, jelölje be a **hivatkozásra egy meglévő Azure AD B2C-bérlőt szeretne aktiválni az Azure-előfizetésem** lehetőséget, majd a **Azure AD B2C-bérlő** legördülő menüben válassza a a bérlő társítani szeretné.

## <a name="create-and-register-an-application"></a>Hozzon létre, és a egy alkalmazás regisztrálása

Ezután meg kell létrehozása és az alkalmazás regisztrálása az Azure AD B2C-bérlőben. Ez biztosítja, hogy adatokat, amelyek az Azure AD B2C-vel való az alkalmazás biztonságos kommunikációhoz. 

Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t. Érdemes most a korábban létrehozott bérlő használatával.

[!INCLUDE [active-directory-b2c-register-web-api](../../includes/active-directory-b2c-register-web-api.md)]

Amikor elkészült, egy API-t és a egy natív alkalmazást kap az alkalmazás beállításaiban.

## <a name="create-policies-on-your-b2c-tenant"></a>Szabályzatok a B2C bérlő létrehozása

Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Ez a kódminta három identitásélményt tartalmaz: **regisztrációs és bejelentkezési**, **profil szerkesztése**, és **új jelszó kérésére vonatkozó**.  Mindkettőhöz létre kell hoznia egy szabályzatot a [szabályzatok áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md) leírtak szerint. Az egyes szabályzatoknál mindenképpen jelölje ki a megjelenítendő név attribútum vagy a jogcím, és másolja le a házirend nevét későbbi használatra.

### <a name="add-your-identity-providers"></a>Az identitásszolgáltató hozzáadása

Válassza a beállítások **Identitásszolgáltatók** , és válassza a regisztráció felhasználónév vagy a regisztráció E-mail-címmel.

### <a name="create-a-sign-up-and-sign-in-policy"></a>Regisztrációs és bejelentkezési szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

### <a name="create-a-profile-editing-policy"></a>Profilszerkesztési szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

### <a name="create-a-password-reset-policy"></a>Új jelszó kérésére vonatkozó szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

Miután létrehozta a szabályzatokat, készen áll az alkalmazás elkészítésére.

## <a name="download-the-sample-code"></a>A mintakód letöltése

Az oktatóanyag kódjának kezelése a [GitHubon](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) történik. A minta klónozásához futtassa a következőt:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Miután letöltötte a mintakódot, nyissa meg a Visual Studio .sln fájlt a kezdéshez. A megoldásfájl két projektet tartalmaz: `TaskWebApp` és `TaskService`. `TaskWebApp` a rendszer az MVC-webalkalmazás, amellyel a felhasználó kommunikál. A `TaskService` az alkalmazás webes API háttérszolgáltatása, amely tárolja a felhasználók feladatlistáit. Ez a cikk csak a `TaskWebApp` alkalmazást ismerteti. Megtudhatja, hogyan hozhat létre `TaskService` Azure AD B2C-t használja, lásd: [a .NET webes api-oktatóanyag](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="update-code-to-use-your-tenant-and-policies"></a>A bérlő és a szabályzatok használata a kód frissítése

A minta úgy van konfigurálva, hogy a bemutató bérlőnk házirendjeit és ügyfél-azonosítóját használja. Szeretne csatlakozni, a saját bérlőn, meg kell nyitnia `web.config` a a `TaskWebApp` projektre, és cserélje le a következő értékeket:

* Az `ida:Tenant` helyett szerepeljen a bérlő neve
* Az `ida:ClientId` helyett szerepeljen a webapp alkalmazásazonosítója
* Az `ida:ClientSecret` helyett szerepeljen a webapp titkos kulcsa
* Az `ida:SignUpSignInPolicyId` helyett szerepeljen a „regisztrálási vagy bejelentkezési” házirend neve
* Az `ida:EditProfilePolicyId` helyett szerepeljen a „profil szerkesztése” házirend neve
* Az `ida:ResetPasswordPolicyId` helyett szerepeljen a „Jelszó alaphelyzetbe állítása” házirend neve

## <a name="launch-the-app"></a>Indítsa el az alkalmazást
A Visual studióban nyissa meg az alkalmazást. Lépjen a feladatlista lapra, és figyelje meg az URL-cím: https://*YourTenantName*.b2clogin.com/*YourTenantName*/oauth2/v2.0/authorize?p=*YourSignUpPolicyName* & client_id =*YourclientID*...

Jelentkezzen az alkalmazás az e-mail-cím vagy a felhasználó neve. Jelentkezzen ki, majd jelentkezzen be újra a profil szerkesztése vagy alaphelyzetbe állíthatja a jelszót. Jelentkezzen ki, és jelentkezzen be másik felhasználóként. 

## <a name="add-social-idps"></a>Közösségi identitásszolgáltató hozzáadása

Jelenleg az alkalmazás támogatja a csak a regisztráció és bejelentkezés felhasználói **helyi fiókok**; a B2C-címtárban tárolt fiókok, egy felhasználónevet és jelszót használja. Azure AD B2C használatával hozzáadhat más támogatási **Identitásszolgáltatók** (IDP) bármely, a kód módosítása nélkül.

A közösségi identitásszolgáltató hozzáadása az alkalmazáshoz, először a részletes utasításait végrehajtva ezek a cikkek. Minden egyes Identitásszolgáltatót szeretne biztosítani meg kell regisztrálni egy alkalmazást, hogy a rendszer, és szerezzen be egy ügyfél-azonosítót.

* [Facebook-Identitásszolgáltató beállítása](active-directory-b2c-setup-fb-app.md)
* [Google-Identitásszolgáltató beállítása](active-directory-b2c-setup-goog-app.md)
* [Amazon-Identitásszolgáltató beállítása](active-directory-b2c-setup-amzn-app.md)
* [LinkedIn-Identitásszolgáltató beállítása](active-directory-b2c-setup-li-app.md)

Miután az identitás-szolgáltatóktól ad B2C-címtárban, szerkessze tartalmazza az új identitásszolgáltató használatát, a három szabályzat minden egyes leírtak szerint a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md). Miután mentette a szabályzatokat, futtassa újra az alkalmazást.  Az új identitásszolgáltató bejelentkezési hozzáadott kell megjelennie, és minden, az identitás-előfizetési beállítások élményhez.

Kísérletezhet a szabályzatokat, és vizsgálja meg a mintaalkalmazást kifejtett hatást. Adja hozzá, vagy távolítsa el az identitásszolgáltató használatát, alkalmazás jogcímét, vagy módosítsa a regisztrációs attribútumokat. Kísérletet, amíg nem láthatja, hogy milyen szabályzatok, a hitelesítési kérések és OWIN alkalmazássá.

## <a name="sample-code-walkthrough"></a>Kód mintaútmutató
A következő szakaszok bemutatják, hogyan van konfigurálva a mintakódot. Használhat ez segítségképp jövőbeli alkalmazás fejlesztése.

### <a name="add-authentication-support"></a>Hitelesítési támogatás hozzáadása

Most konfigurálhatja az alkalmazás Azure AD B2C segítségével. Az alkalmazás az Azure AD B2C-vel kommunikál, OpenID Connect hitelesítési kérések küldésével. A kérések végrehajtásához adja meg a szabályzatot szeretné az alkalmazás felhasználói élményét szabályozzák. A Microsoft OWIN könyvtár segítségével ezeket a kérelmeket küldeni, hajtsa végre a házirendek, a felhasználói munkamenetek kezelhet stb.

#### <a name="install-owin"></a>Az OWIN telepítése

Első lépésként adja hozzá az OWIN közbenső NuGet-csomagok a projekt a Visual Studio Csomagkezelői konzol használatával.

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

#### <a name="configure-the-authentication-middleware"></a>A közbenső hitelesítési szoftver konfigurálása

Nyissa meg a fájlt `App_Start\Startup.Auth.cs` és megvalósítása a `ConfigureAuth(...)` metódust. A paraméterek meg `OpenIdConnectAuthenticationOptions` állnak az Azure AD B2C-vel folytatott kommunikációhoz az alkalmazás koordinátáit. Ha nem ad meg bizonyos paraméterek, az alapértelmezett értéket fogja használni. Például, hogy ne adjon meg a `ResponseType` a mintában tehát az alapértelmezett érték `code id_token` az egyes kimenő kérelmekre az Azure AD B2C-vel használni fogják.

Is kell a cookie-hitelesítés beállítása. Az OpenID Connect közbenső szoftvert használ cookie-kat a felhasználói munkameneteket, többek között.

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

A `OpenIdConnectAuthenticationOptions` újabb meghatározzuk egy készletét az adott értesítésekhez, az OpenID Connect közbenső szoftver által kapott visszahívási függvényekben. Ezen viselkedés meghatározása egy `OpenIdConnectAuthenticationNotifications` objektumra, és eltárolni a `Notifications` változó. A mintánkban meghatározzuk az esemény függően három különböző visszahívásokat.

### <a name="using-different-policies"></a>Különböző szabályzatok használatával

A `RedirectToIdentityProvider` értesítés aktiválódik, amikor kérelem érkezik, az Azure AD B2C-t. A visszahívási függvény `OnRedirectToIdentityProvider`, ellenőrizzük a kimenő hívásban szeretnénk egy másik szabályzat használja. Annak érdekében, hogy ne a jelszó alaphelyzetbe állítása, és a egy profil szerkesztése, például a jelszó-visszaállítási házirend az alapértelmezett "Regisztrálási vagy bejelentkezési" házirend helyett használja a megfelelő házirendet kell.

A mintánkban amikor a felhasználó alaphelyzetbe állíthatja a jelszót, vagy módosítsa a profilt kíván hozzáadunk a szabályzatot, hogy inkább az OWIN-környezetben való használata. Amely a következő módon teheti meg:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

A visszahívási függvény valósíthat meg, és `OnRedirectToIdentityProvider` az alábbiak szerint:

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

A `AuthorizationCodeReceived` értesítés akkor aktiválódik, ha a hozzáférési kód érkezik. Az OpenID Connect közbenső szoftver nem támogatja a cserélő kódokat a hozzáférési tokenekhez. A kód meg a tokent egy visszahívási függvény a cserél manuálisan is. További információkért tekintse meg a [dokumentáció](active-directory-b2c-devquickstarts-web-api-dotnet.md) , amely bemutatja, hogyan.

### <a name="handling-errors"></a>Hibák kezelése

A `AuthenticationFailed` értesítés akkor aktiválódik, ha a hitelesítés sikertelen. A visszahívási metódus képes kezelni a hibákat, igény szerint. Azonban hozzá kell adnia egy hibakód keresése `AADB2C90118`. A "Regisztrálási vagy bejelentkezési" szabályzatot a végrehajtása során a felhasználó rendelkezik-e ki egy **elfelejtette a jelszavát?** hivatkozásra. Ebben az esetben az Azure AD B2C-t küld az alkalmazás adott hiba kód jelzi, hogy az alkalmazás egy kérelmet, a jelszó-visszaállítási házirend használata esetén inkább győződjön meg.

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

Az alkalmazás most már megfelelően van konfigurálva, az Azure AD B2C az OpenID Connect hitelesítési protokoll használatával kommunikálnak. OWIN kezeli az Azure AD B2C érkező jogkivonatok érvényességének elvégezte a hitelesítési üzenetek, valamint a felhasználói munkamenetek fenntartását. Most már minden egyes felhasználói folyamat elindítására.

Amikor egy felhasználó kijelöl **Sign up/bejelentkezés**, **profil szerkesztése**, vagy **jelszó alaphelyzetbe állítása** a web app alkalmazásban a társított művelet meghívása a `Controllers\AccountController.cs`:

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

OWIN használhatja ki a felhasználót az alkalmazásból is. A `Controllers\AccountController.cs` van:

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

Explicit módon meghívása egy szabályzatot, túl használhatja egy `[Authorize]` címke, a vezérlők, amely végrehajtja a házirend, ha a felhasználó nem jelentkezett be. Nyissa meg `Controllers\HomeController.cs` , és adja hozzá a `[Authorize]` a jogcímek vezérlő címkét.  Az utolsó szabályzat konfigurálva mikor OWIN kiválasztja a `[Authorize]` címke elérte.

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

### <a name="display-user-information"></a>Felhasználói információ megjelenítése

Amikor a felhasználók az OpenID Connect hitelesítést, Azure AD B2C visszaadja-e, amely tartalmazza az alkalmazás-azonosító jogkivonat **jogcímek**. Ezek a helyességi feltételek a felhasználóról. Jogcímek segítségével személyre szabhatja az alkalmazást.

Nyissa meg az `Controllers\HomeController.cs` fájlt. Felhasználói jogcímek keresztül elérhető a tartományvezérlőkön keresztül a `ClaimsPrincipal.Current` rendszerbiztonsági objektumot.

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

Bármilyen jogcímet, amely az alkalmazás fogad a ugyanúgy érheti el.  Az alkalmazás megkapja az összes a jogcímek listája a rendelkezésére áll a **jogcímek** lapot.
