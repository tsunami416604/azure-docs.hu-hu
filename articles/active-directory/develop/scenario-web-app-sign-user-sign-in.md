---
title: Írjon egy webalkalmazást, amely bejelentkezik a felhasználókba - Microsoft identity platform | Azure
description: További információ a felhasználókat be- és kijelentkező webalkalmazások létrehozásáról
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 317ca55adb9f680dc93343a185395abad08889da
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881315"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>A felhasználókba bejelentkező webalkalmazás: Bejelentkezés és kijelentkezés

Megtudhatja, hogyan adhat hozzá bejelentkezést a felhasználókban bejelentkező webalkalmazás kódjához. Ezután tanuld meg, hogyan hagyd, hogy kijelentkezzenek.

## <a name="sign-in"></a>Bejelentkezés

A bejelentkezés két részből áll:

- A HTML-lapon található bejelentkezési gomb
- A bejelentkezési művelet a vezérlő ben lévő kód mögött

### <a name="sign-in-button"></a>Bejelentkezési gomb

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core-ban a bejelentkezési `Views\Shared\_LoginPartial.cshtml`gomb elérhetőa. Csak akkor jelenik meg, ha nincs hitelesített fiók. Ez azt, hogy akkor jelenik meg, ha a felhasználó még nem jelentkezett be, vagy kijelentkezett.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Az MVC ASP.NET a kijelentkezési `Views\Shared\_LoginPartial.cshtml`gomb a alkalmazásban látható. Csak akkor jelenik meg, ha van hitelesített fiók. Ez azt, hogy akkor jelenik meg, ha a felhasználó korábban bejelentkezett.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

A Java gyorsindításban a bejelentkezési gomb a [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) fájlban található.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="python"></a>[Python](#tab/python)

A Python gyorsindítás, nincs bejelentkezési gomb. A kód-mögött automatikusan kéri a felhasználót a bejelentkezést, amikor eléri a webalkalmazás gyökerét. Lásd [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`az adatkezelő fellépése

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET a **webalkalmazás bejelentkezési** gombjának kiválasztásával `SignIn` aktiválhatja `AccountController` a vezérlőműveletet. A ASP.NET alapsablonok korábbi verzióiban a `Account` vezérlő be volt ágyazva a webalkalmazásba. Ez már nem így van, mert a vezérlő most már része a ASP.NET Core keretrendszer.

A kód `AccountController` a [ASP.NET](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)Core repository AccountController.cs. A fiókvezérlő kihívást jelent a felhasználó átirányítása a Microsoft identity platform végpont. A részleteket a ASP.NET Core részeként megadott [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) metódus tartalmazza.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET a kijelentkezés `SignOut()` a metódusból indul ki egy vezérlőn (például [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Ez a módszer nem része a ASP.NET keretrendszer (ellentétben azzal, ami történik ASP.NET Core). OpenID bejelentkezési kihívást küld, miután egy átirányítási URI-t javasolt.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Java-ban a kijelentkezést a Microsoft identity platform `logout` végpontjának `post_logout_redirect_uri` közvetlen hívásával és az érték megadásával kezeli a rendszer. További részletek: [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="python"></a>[Python](#tab/python)

Más platformoktól eltérően az MSAL Python gondoskodik afelhasználó bejelentkezési oldaláról történő bejelentkezésről. Lásd [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

A `_build_msal_app()` metódus az [app.py#L81-L88-ban](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) a következőképpen van definiálva:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Miután a felhasználó bejelentkezett az alkalmazásba, engedélyeznie kell számukra a kijelentkezést.

## <a name="sign-out"></a>Kijelentkezés

A webalkalmazásból való kijelentkezés több, mint a bejelentkezett fiókkal kapcsolatos információk eltávolítása a webalkalmazás állapotából.
A webalkalmazásnak át kell irányítania a `logout` felhasználót a Microsoft identity platform végpontjára a kijelentkezéshez. 

Amikor a webalkalmazás átirányítja `logout` a felhasználót a végpontra, ez a végpont törli a felhasználó munkamenetét a böngészőből. Ha az alkalmazás nem ment `logout` a végpontra, a felhasználó újra hitelesíti magát az alkalmazásba anélkül, hogy újra megadnák a hitelesítő adataikat. Ennek az az oka, hogy lesz egy érvényes egyszeri bejelentkezési munkamenet a Microsoft identity platform végpont.

További információ: [A Kijelentkezési kérelem küldése](v2-protocols-oidc.md#send-a-sign-out-request) a Microsoft [identity platformon és az OpenID Connect protokoll](v2-protocols-oidc.md) dokumentációjában található.

### <a name="application-registration"></a>Alkalmazásregisztráció

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Az alkalmazás regisztrációja során regisztrál egy kijelentkezés utáni URI-t. A mi bemutató, `https://localhost:44321/signout-oidc` regisztrált a **Kijelentkezés URL-cím** mezőjében a **Speciális beállítások** szakasz a **hitelesítés** oldalon. További információt [a WebApp alkalmazás regisztrálása című témakörben talál.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Az alkalmazás regisztrációja során regisztrál egy kijelentkezés utáni URI-t. A mi bemutató, `https://localhost:44308/Account/EndSession` regisztrált a **Kijelentkezés URL-cím** mezőjében a **Speciális beállítások** szakasz a **hitelesítés** oldalon. További információt [a WebApp alkalmazás regisztrálása című témakörben talál.](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="java"></a>[Java](#tab/java)

Az alkalmazás regisztrációja során regisztrál egy kijelentkezés utáni URI-t. A mi bemutató, `http://localhost:8080/msal4jsample/sign_out` regisztrált a **Kijelentkezés URL-cím** mezőjében a **Speciális beállítások** szakasz a **hitelesítés** oldalon.

# <a name="python"></a>[Python](#tab/python)

Az alkalmazás regisztrációja során nem kell regisztrálnia egy további kijelentkezési URL-címet. Az alkalmazás visszalesz hívva a fő URL-címére.

---

### <a name="sign-out-button"></a>Kijelentkezés gomb

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core alkalmazásban a kijelentkezési gomb a alkalmazásban `Views\Shared\_LoginPartial.cshtml`látható. Csak akkor jelenik meg, ha van hitelesített fiók. Ez azt, hogy akkor jelenik meg, ha a felhasználó korábban bejelentkezett.

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

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Az MVC ASP.NET a kijelentkezési `Views\Shared\_LoginPartial.cshtml`gomb a alkalmazásban látható. Csak akkor jelenik meg, ha van hitelesített fiók. Ez azt, hogy akkor jelenik meg, ha a felhasználó korábban bejelentkezett.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

# <a name="java"></a>[Java](#tab/java)

Java-gyorsútmutatónkban a kijelentkezés gomb a main/resources/templates/auth_page.html fájlban található.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

A Python gyorsindítás, a kijelentkezési gomb található a [templates/index.html#L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) fájlban.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut`az adatkezelő fellépése

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET a webes alkalmazás **Kijelentkezés** gombjának kiválasztásával aktiválhatja a `SignOut` `AccountController` vezérlőműveletet. A ASP.NET Core sablonok korábbi verzióiban a `Account` vezérlő be volt ágyazva a webalkalmazásba. Ez már nem így van, mert a vezérlő most már része a ASP.NET Core keretrendszer.

A kód `AccountController` a AccountController.cs-ben található ASP.NET [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)központi adattárból érhető el. A fiókellenőrzés:

- OpenID átirányítási URI-t állít `/Account/SignedOut` be, hogy a vezérlő visszahívható, amikor az Azure AD befejezte a kijelentkezést.
- A `Signout()`hívások , amelyek lehetővé teszik, hogy `logout` az OpenID Connect köztes szoftver kapcsolatba lépjen a Microsoft identity platform végpontjával. A végpont akkor:

  - Törli a munkamenet-cookie-t a böngészőből.
  - Visszahívja a kijelentkezés URL-címét. Alapértelmezés szerint a kijelentkezési URL-cím a [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)kijelentkezett megtekintési lapot jeleníti meg. Ez az oldal a ASP.NET Core részeként is biztosított.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET a kijelentkezés `SignOut()` a metódusból indul ki egy vezérlőn (például [AccountController.cs#L25-L31).](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31) Ez a módszer nem része a ASP.NET keret, ellentétben azzal, ami történik ASP.NET Core. Ez:

- OpenID kijelentkezési kihívást küld.
- Törli a gyorsítótárat.
- Átirányítja a kívánt oldalra.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

Java-ban a kijelentkezést a Microsoft identity platform `logout` végpontjának `post_logout_redirect_uri` közvetlen hívásával és az érték megadásával kezeli a rendszer. További részletek: [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="python"></a>[Python](#tab/python)

A felhasználót kijelentkező kód az [app.py#L46-L52.The](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48)code that sign out the user is in app.py#L46-L52 .

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>A végpont hívásának `logout` lehallgatása

A kijelentkezés utáni URI lehetővé teszi, hogy az alkalmazások részt vegyenek a globális kijelentkezésben.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core OpenID Connect köztes szoftver lehetővé teszi `logout` az alkalmazás számára, hogy elfogja `OnRedirectToIdentityProviderForSignOut`a Hívást a Microsoft identitásplatform végpontjára egy OpenID Connect esemény megadásával. Az eseményre való feliratkozás (a tokengyorsítótár törléséhez) a [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)című témakörben található példa.

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET delegálhatja a köztes szoftvert a kijelentkezés végrehajtásához, és törli a munkamenet-cookie-t:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

A Java gyorsindítás, a kijelentkezés utáni átirányítási URI csak megjeleníti az index.html lapot.

# <a name="python"></a>[Python](#tab/python)

A Python gyorsindítás, a kijelentkezés utáni átirányítási URI csak megjeleníti az index.html lapot.

---

## <a name="protocol"></a>Protocol (Protokoll)

Ha többet szeretne megtudni a kijelentkezésről, olvassa el az [Open ID Connect](./v2-protocols-oidc.md)című dokumentumát.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Átállás éles üzemre](scenario-web-app-sign-user-production.md)
