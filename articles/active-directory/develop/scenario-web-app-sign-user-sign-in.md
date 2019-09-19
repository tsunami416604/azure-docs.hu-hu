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
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086455"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>Felhasználók bejelentkezési webalkalmazása – bejelentkezés

Megtudhatja, hogyan adhat hozzá bejelentkezési kódot a webalkalmazáshoz, amely bejelentkezik a felhasználók számára.

## <a name="sign-in"></a>Bejelentkezés

Az előző cikk [programkód-konfigurációjában](scenario-web-app-sign-user-app-configuration.md) áttekintett kód csak a bejelentkezés megvalósításához szükséges.
Miután a felhasználó bejelentkezett az alkalmazásba, valószínűleg engedélyezni szeretné a kijelentkezést. A ASP.NET Core kezeli a kijelentkezést.

## <a name="what-sign-out-involves"></a>A kijelentkezési folyamat

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

Az alkalmazás regisztrálása során nem kell regisztrálnia a kijelentkezési URL-címet. A minta nem valósít meg globális kijelentkezést

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

A Python rövid útmutatójában a kijelentkezési gomb a [templates/Display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20) fájlban található.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`Signout()`a vezérlő művelete

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.net a webalkalmazásban a **kijelentkezés** gomb megnyomásával elindítja `SignOut` a műveletet `AccountController` a vezérlőn. A ASP.net Core-sablonok korábbi verzióiban a `Account` vezérlőt a webalkalmazásba ágyazták be, de már nem ez a helyzet, mert már része a ASP.net Core-keretrendszernek.

A kód `AccountController` a ASP.net Core adattárból érhető el a [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs)-ból. A fiókok felügyelete:

- Beállítja az OpenID-átirányítási `/Account/SignedOut` URI-t úgy, hogy a vezérlőt vissza lehessen hívni, amikor az Azure ad elvégezte a kijelentkezést
- Meghívások `Signout()`, amelyek lehetővé teszi, hogy a OpenIdConnect köztes kapcsolat a Microsoft Identity platform `logout` végpontján keresztül lépjen kapcsolatba:

  - Törli a munkamenet-cookie-t a böngészőből, és
  - Végül a **kijelentkezési URL-címet**hívja vissza, amely alapértelmezés szerint megjeleníti a kijelentkezett nézetet a [SignedOut. html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) ASP.net Core részeként is.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.net-ben a kijelentkezés a `SignOut()` vezérlő metódusa alapján történik (például AccountController). Ez a metódus nem része a ASP.NET-keretrendszernek (ellentétben a ASP.NET Core). Nem használja `async`, és ezért:

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

A Java-ban a kijelentkezés a Microsoft Identity platform kijelentkezési végpontjának közvetlen meghívásával és a post_logout_redirect_uri biztosításával kezelhető.

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

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
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
