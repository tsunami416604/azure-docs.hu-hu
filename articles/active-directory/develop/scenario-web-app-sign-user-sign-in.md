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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09b39cb9db2450b7d200ec725396141f72f1b2f1
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310029"
---
# <a name="web-app-that-signs-in-users---sign-in-and-sign-out"></a>Felhasználók számára bejelentkező webes alkalmazás – bejelentkezés és kijelentkezés

Megtudhatja, hogyan adhat hozzá bejelentkezést a webalkalmazáshoz, amely bejelentkezik a felhasználók számára, és hogyan engedheti ki a regisztrációt

## <a name="sign-in"></a>Bejelentkezés

A bejelentkezés két részből áll:

- a HTML-lap bejelentkezési gombja
- a bejelentkezés művelet a vezérlő mögötti kódban

### <a name="sign-in-button"></a>Bejelentkezés gomb

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.net Core a bejelentkezési gomb elérhető a-ben `Views\Shared\_LoginPartial.cshtml` , és csak akkor jelenik meg, ha nincs hitelesített fiók (azaz ha a felhasználó még nincs bejelentkezve vagy kijelentkezett).

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.net MVC-ben a kijelentkezési gomb `Views\Shared\_LoginPartial.cshtml` megjelenik, és csak akkor jelenik meg, ha van egy hitelesített fiók (vagyis amikor a felhasználó korábban bejelentkezett).

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

# <a name="javatabjava"></a>[Java](#tab/java)

A Java gyors útmutatóban a kijelentkezés gomb a [Main/Resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) fájlban található.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python rövid útmutatójában nincs bejelentkezési gomb. A rendszer automatikusan kéri a felhasználót, hogy a webalkalmazás gyökerének elérésekor a kód mögé jelentkezzen be. Lásd: [app. a # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18)

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="login-action-of-the-controller"></a>`login()`a vezérlő művelete

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.net-ben a webalkalmazásban a **Bejelentkezés** gomb megnyomásával elindítja `SignIn` a műveletet `AccountController` a vezérlőn. A ASP.net Core-sablonok korábbi verzióiban a `Account` vezérlőt a webalkalmazásba ágyazták be, de már nem ez a helyzet, mert már része a ASP.net Core-keretrendszernek.

A kód `AccountController` a ASP.net Core adattárból érhető el a [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)-ből. A fiók-vezérlés a Microsoft Identity platform végpontjának átirányításával vitatja meg a felhasználót. További részletekért tekintse meg a ASP.NET Core részeként megadott [bejelentkezési](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) metódust.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.net-ben a kijelentkezés a `SignOut()` vezérlő metódusa alapján történik (például [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Ez a metódus nem része a ASP.NET-keretrendszernek (ellentétben a ASP.NET Core). Ez

- OpenId bejelentkezési kihívás küldése az átirányítási URI-nak való javaslata után

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-ban a kijelentkezés a Microsoft Identity platform kijelentkezési végpontjának közvetlen meghívásával és a post_logout_redirect_uri biztosításával kezelhető. További részletek: [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

A többi platformtól eltérően a MSAL. A Python gondoskodik arról, hogy a felhasználó bejelentkezzen a bejelentkezési oldalról. Lásd: [app. a # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28)

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use empty list [] to just sign in,
                           # here we choose to also collect end user consent upfront
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

az [app. a # L81-L88 alkalmazásban](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) definiált _build_msal_app () metódust a következőképpen kell megadni:

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
    if accounts:  # So all account(s) belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Miután a felhasználó bejelentkezett az alkalmazásba, valószínűleg engedélyezni szeretné a kijelentkezést.

## <a name="sign-out"></a>Kijelentkezés

A webalkalmazásból való kijelentkezés több, mint a webalkalmazás állapotában lévő bejelentkezett fiók adatainak eltávolítása.
A webalkalmazásnak is át kell irányítani a felhasználót a Microsoft `logout` Identity platform-végpontra a kijelentkezéshez. Ha a webalkalmazás átirányítja a felhasználót a `logout` végpontra, ez a végpont törli a felhasználó munkamenetét a böngészőből. Ha az alkalmazás nem a `logout` végpontra került, a felhasználó újra hitelesíteni fogja magát az alkalmazásba anélkül, hogy újra be kellene írnia a hitelesítő adatokat, mert érvényes egyszeri bejelentkezéssel kell rendelkezniük a Microsoft Identity platform végpontjának használatával.

További információért lásd a [kijelentkezési kérelem küldése](v2-protocols-oidc.md#send-a-sign-out-request) szakaszt a [Microsoft Identity platform és az OpenID Connect protokoll](v2-protocols-oidc.md) fogalmi dokumentációjában.

### <a name="application-registration"></a>Alkalmazás regisztrálása

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Az alkalmazás regisztrálása során regisztrálva lesz egy **utólagos kijelentkezési URI**. Az oktatóanyagban a **hitelesítés** lap `https://localhost:44321/signout-oidc` **Speciális beállítások** szakaszának **kijelentkezési URL-címe** mezőjében regisztrálta a regisztrációt. Részletekért lásd: [a webApp alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Az alkalmazás regisztrálása során regisztrálva lesz egy **utólagos kijelentkezési URI**. Az oktatóanyagban a **hitelesítés** lap `https://localhost:44308/Account/EndSession` **Speciális beállítások** szakaszának **kijelentkezési URL-címe** mezőjében regisztrálta a regisztrációt. Részletekért lásd: [a webApp alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)

# <a name="javatabjava"></a>[Java](#tab/java)

Az alkalmazás regisztrálása során regisztrálnia kell egy **utólagos kijelentkezési URI**-t. Az oktatóanyagban a **hitelesítés** lap `http://localhost:8080/msal4jsample/` **Speciális beállítások** szakaszának **kijelentkezési URL-címe** mezőjében regisztrálta a regisztrációt.

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alkalmazás regisztrálása során nem kell regisztrálnia egy további kijelentkezési URL-címet. A rendszer visszahívja az alkalmazást a fő URL-címére

---

### <a name="sign-out-button"></a>Kijelentkezés gomb

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.net Core a kijelentkezési gomb `Views\Shared\_LoginPartial.cshtml` a és csak akkor jelenik meg, ha van egy hitelesített fiók (azaz amikor a felhasználó korábban bejelentkezett).

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

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.net MVC-ben a kijelentkezési gomb `Views\Shared\_LoginPartial.cshtml` megjelenik, és csak akkor jelenik meg, ha van egy hitelesített fiók (vagyis amikor a felhasználó korábban bejelentkezett).

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python rövid útmutatójában a kijelentkezés gomb a [templates/index. html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) fájlban található.

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

### <a name="signout-action-of-the-controller"></a>`Signout()`a vezérlő művelete

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.net a webalkalmazásban a **kijelentkezés** gomb megnyomásával elindítja `SignOut` a műveletet `AccountController` a vezérlőn. A ASP.net Core-sablonok korábbi verzióiban a `Account` vezérlőt a webalkalmazásba ágyazták be, de már nem ez a helyzet, mert már része a ASP.net Core-keretrendszernek.

A kód `AccountController` a ASP.net Core adattárból érhető el a [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)-ból. A fiókok felügyelete:

- Beállítja az OpenID-átirányítási `/Account/SignedOut` URI-t úgy, hogy a vezérlőt vissza lehessen hívni, amikor az Azure ad befejezte a kijelentkezést
- Meghívások `Signout()`, amelyek lehetővé teszi, hogy a OpenIdConnect köztes kapcsolat a Microsoft Identity platform `logout` végpontján keresztül lépjen kapcsolatba:

  - Törli a munkamenet-cookie-t a böngészőből, és
  - Végül a **kijelentkezési URL-címet**hívja vissza, amely alapértelmezés szerint megjeleníti a kijelentkezett nézetet a [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) ASP.net Core részeként is.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.net-ben a kijelentkezés a `SignOut()` vezérlő metódusa alapján történik (például [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Ez a metódus nem része a ASP.NET-keretrendszernek (ellentétben a ASP.NET Core). Ez

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
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-ban a kijelentkezés a Microsoft Identity platform kijelentkezési végpontjának közvetlen meghívásával és a post_logout_redirect_uri biztosításával kezelhető. További részletek: [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

A felhasználót kijelentkezési kód az [app. L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L46-L52) 

```Python
@app.route("/logout")
def logout():
    session["user"] = None  # Log out from this app from its session
    # session.clear()  # If you prefer, this would nuke the user's token cache too
    return redirect(  # Also need to logout from Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>A `logout` végpontra irányuló hívás elfogása

A kilépés utáni URI lehetővé teszi, hogy az alkalmazások részt vegyenek a globális kijelentkezésben.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.net Core OpenIdConnect middleware lehetővé teszi, hogy az alkalmazás elhallgassa a hívást a `logout` Microsoft Identity platform végpontján egy nevű `OnRedirectToIdentityProviderForSignOut`OpenIdConnect-esemény biztosításával. Tekintse meg a [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) című példát, amely bemutatja, hogyan fizethet elő erre az eseményre (a jogkivonat-gyorsítótár törléséhez)

```CSharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET delegálja a köztes kapcsolatot a kijelentkezés végrehajtásához, törölje a munkamenet cookie-jait:

```CSharp
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

# <a name="javatabjava"></a>[Java](#tab/java)

A Java gyors útmutatóban a kijelentkezési átirányítás URI-ja csak az index. html lapot jeleníti meg. 

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python rövid útmutatójában a kijelentkezési átirányítás URI-ja csak az index. html lapot jeleníti meg.

---

## <a name="protocol"></a>Protocol

Ha többet szeretne megtudni a kijelentkezésről, olvassa el a protokoll dokumentációját, amely elérhető az [Open ID csatlakozás](./v2-protocols-oidc.md)lehetőségnél.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áthelyezés éles környezetbe](scenario-web-app-sign-user-production.md)
