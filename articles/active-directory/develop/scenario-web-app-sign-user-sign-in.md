---
title: Felhasználói bejelentkezést bejelentkező webalkalmazás írása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre vagy ki felhasználókat használó webalkalmazásokat
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
ms.openlocfilehash: df02c7d2ace6c58d86f4044607eca386f1790e1d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734314"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Felhasználók számára bejelentkező webes alkalmazás: bejelentkezés és kijelentkezés

Megtudhatja, hogyan veheti fel a bejelentkezést a webalkalmazáshoz a felhasználókba bejelentkező programkódba. Ezután megtudhatja, hogyan lehet kijelentkezni.

## <a name="sign-in"></a>Bejelentkezés

A bejelentkezés két részből áll:

- A HTML-lap bejelentkezési gombja
- A bejelentkezési művelet a vezérlő mögötti kódban

### <a name="sign-in-button"></a>Bejelentkezés gomb

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core a Microsoft Identity platform alkalmazásai esetében a **Bejelentkezés** gomb `Views\Shared\_LoginPartial.cshtml` (MVC-alkalmazás esetén) vagy `Pages\Shared\_LoginPartial.cshtm` (Razor-alkalmazás esetén) elérhető. Csak akkor jelenik meg, ha a felhasználó nincs hitelesítve. Ez azt jelzi, hogy akkor jelenik meg, ha a felhasználó még nincs bejelentkezve vagy kijelentkezett. Ellenkező esetben a **kijelentkezés** gomb akkor jelenik meg, ha a felhasználó már be van jelentkezve. Vegye figyelembe, hogy a fiók vezérlője a **Microsoft. Identity. Web. UI** NuGet csomagban van definiálva a **MicrosoftIdentity** nevű területen.

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET MVC-ben a kijelentkezési gomb elérhető a `Views\Shared\_LoginPartial.cshtml`-ben. Ez csak akkor jelenik meg, ha hitelesített fiók van. Ez azt jelzi, hogy a felhasználó korábban már bejelentkezett.

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

A Java gyors útmutatóban a bejelentkezési gomb a [Main/Resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) fájlban található.

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

A Python rövid útmutatójában nincs bejelentkezési gomb. A kód mögött automatikusan bekéri a felhasználót, hogy jelentkezzen be, amikor eléri a webalkalmazás gyökerét. Lásd: [app. a # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`a vezérlő művelete

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET-ben a webalkalmazás **bejelentkezési** gombjára kattintva elindítja a `SignIn` műveletet a `AccountController` vezérlőn. A ASP.NET Core sablonok korábbi verzióiban a `Account` vezérlőt a webalkalmazásba ágyazták be. Ez már nem így van, mert a vezérlő már része a **Microsoft. Identity. Web. UI** NuGet csomagnak. További részletekért lásd: [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

Ez a vezérlő a Azure AD B2C-alkalmazásokat is kezeli.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben a kijelentkezés a `SignOut()` vezérlő metódusa alapján történik (például [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Ez a metódus nem része a ASP.NET-keretrendszernek (ellentétben a ASP.NET Core). Az átirányítási URI-t javasolva egy OpenID bejelentkezési kihívást küld.

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

A Java-ban a kijelentkezést a Microsoft Identity platform `logout` végpontjának közvetlen meghívásával és `post_logout_redirect_uri` az érték megadásával kezeljük. Részletekért lásd: [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

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

A többi platformtól eltérően a MSAL Python gondoskodik arról, hogy a felhasználó bejelentkezzen a bejelentkezési oldalról. Lásd: [app. a # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

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

A `_build_msal_app()` metódus a következő módon van definiálva az [app. a # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) :

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

Miután a felhasználó bejelentkezett az alkalmazásba, engedélyeznie kell a kijelentkezést.

## <a name="sign-out"></a>Kijelentkezés

A webalkalmazásból való kijelentkezés több, mint a webalkalmazás állapotában lévő bejelentkezett fiók adatainak eltávolítása.
A webalkalmazásnak is át kell irányítani a felhasználót a Microsoft `logout` Identity platform-végpontra a kijelentkezéshez.

Ha a webalkalmazás átirányítja a felhasználót a `logout` végpontra, ez a végpont törli a felhasználó munkamenetét a böngészőből. Ha az alkalmazás nem a `logout` végpontra mutat, a felhasználó újra hitelesíteni fogja magát az alkalmazásban anélkül, hogy újra be kellene írnia a hitelesítő adatait. Ennek az az oka, hogy egy érvényes egyszeri bejelentkezési munkamenettel rendelkeznek a Microsoft Identity platform-végponttal.

További információért lásd a [kijelentkezési kérelem küldése](v2-protocols-oidc.md#send-a-sign-out-request) szakaszt a [Microsoft Identity platform és az OpenID Connect Protocol](v2-protocols-oidc.md) dokumentációjában.

### <a name="application-registration"></a>Alkalmazásregisztráció

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Az alkalmazás regisztrálása során regisztrálnia kell a kijelentkezés utáni URI-t. Az oktatóanyagban a **hitelesítés** lapon `https://localhost:44321/signout-oidc` , a **Speciális beállítások** szakasz **kijelentkezési URL-címe** mezőjében regisztrált. Részletekért lásd: [a webApp alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Az alkalmazás regisztrálása során regisztrálnia kell a kijelentkezés utáni URI-t. Az oktatóanyagban a **hitelesítés** lapon `https://localhost:44308/Account/EndSession` , a **Speciális beállítások** szakasz **kijelentkezési URL-címe** mezőjében regisztrált. Részletekért lásd: [a webApp alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="java"></a>[Java](#tab/java)

Az alkalmazás regisztrálása során regisztrálnia kell a kijelentkezés utáni URI-t. Az oktatóanyagban a **hitelesítés** lapon `http://localhost:8080/msal4jsample/sign_out` , a **Speciális beállítások** szakasz **kijelentkezési URL-címe** mezőjében regisztrált.

# <a name="python"></a>[Python](#tab/python)

Az alkalmazás regisztrálása során nem kell regisztrálnia egy további kijelentkezési URL-címet. A rendszer visszahívja az alkalmazást a fő URL-címére.

---

### <a name="sign-out-button"></a>Kijelentkezés gomb

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET-ben a webalkalmazás **kijelentkezés** gombjára kattintva elindítja a `SignOut` műveletet a `AccountController` vezérlőn (lásd alább).

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET MVC-ben a kijelentkezési gomb elérhető a `Views\Shared\_LoginPartial.cshtml`-ben. Ez csak akkor jelenik meg, ha hitelesített fiók van. Ez azt jelzi, hogy a felhasználó korábban már bejelentkezett.

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

A Java gyors útmutatóban a kijelentkezés gomb a main/resources/templates/auth_page.html fájlban található.

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

A Python rövid útmutatójában a kijelentkezés gomb a [templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) fájlban található.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut`a vezérlő művelete

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core sablonok korábbi verzióiban a `Account` vezérlőt a webalkalmazásba ágyazták be. Ez már nem így van, mert a vezérlő már része a **Microsoft. Identity. Web. UI** NuGet csomagnak. További részletekért lásd: [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

- Beállítja az OpenID-átirányítási `/Account/SignedOut` URI-t úgy, hogy a vezérlőt vissza lehessen hívni, amikor az Azure ad befejezte a kijelentkezést.
- Meghívások `Signout()`, amelyek lehetővé teszi, hogy az OpenID Connect middleware `logout` kapcsolatba lépjen a Microsoft Identity platform-végponttal. A végpont ekkor:

  - Törli a munkamenet-cookie-t a böngészőből.
  - A kijelentkezési URL-címet hívja vissza. Alapértelmezés szerint a kijelentkezési URL-cím a [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml)kijelentkezett nézet lapját jeleníti meg. Ez az oldal a MIcrosoft. Identity. Web részeként is elérhető.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben a kijelentkezés a `SignOut()` vezérlő metódusa alapján történik (például [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Ez a metódus nem része a ASP.NET keretrendszernek, ellentétben azzal, ami a ASP.NET Core történik. Ez

- OpenID-kijelentkezési kihívást küld.
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

A Java-ban a kijelentkezést a Microsoft Identity platform `logout` végpontjának közvetlen meghívásával és `post_logout_redirect_uri` az érték megadásával kezeljük. Részletekért lásd: [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

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

A felhasználót kijelentkezési kód az [app. L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>A `logout` végpontra irányuló hívás elfogása

A kijelentkezés utáni URI lehetővé teszi, hogy az alkalmazások részt vegyenek a globális kijelentkezésben.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core OpenID Connect middleware lehetővé teszi, hogy az alkalmazás feltartóztatja a Microsoft Identity `logout` platform végpontjának hívását egy nevű `OnRedirectToIdentityProviderForSignOut`OpenID Connect-esemény biztosításával. Ezt a Microsoft. Identity. Web automatikusan kezeli (amely törli a fiókokat abban az esetben, amikor a webalkalmazás meghívja a webes API-kat)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben delegálja a middleware-t, hogy végrehajtsa a kijelentkezést, törli a munkamenet cookie-jait:

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

A Java gyors útmutatóban a kijelentkezés utáni átirányítási URI csak az index. html lapot jeleníti meg.

# <a name="python"></a>[Python](#tab/python)

A Python rövid útmutatójában a kijelentkezés utáni átirányítási URI csak az index. html lapot jeleníti meg.

---

## <a name="protocol"></a>Protocol (Protokoll)

Ha többet szeretne megtudni a kijelentkezésről, olvassa el az [Open ID csatlakozással](./v2-protocols-oidc.md)elérhető protokoll dokumentációját.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Átállás éles üzemre](scenario-web-app-sign-user-production.md)
