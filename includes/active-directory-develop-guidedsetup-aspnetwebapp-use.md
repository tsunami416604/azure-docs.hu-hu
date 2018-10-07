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
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 167fccd8e0546bc8f5ac1b24489cae68cc14191f
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843235"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Bejelentkezési és kijelentkezési kérések kezelésére vezérlő hozzáadása

Ez a lépés bemutatja, hogyan hozhat létre egy új vezérlőt elérhetővé a bejelentkezési és kijelentkezési metódusokat.

1.  Kattintson a jobb gombbal a `Controllers` mappát, és válassza ki `Add` > `Controller`
2.  Válassza a(z) `MVC (.NET version) Controller – Empty` lehetőséget.
3.  Kattintson a *hozzáadása*
4.  Nevezze el `HomeController` kattintson *hozzáadása*
5.  Adjon hozzá *OWIN* az osztályra hivatkozik:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Adja hozzá a két módszer kezeléséhez jelentkezzen be az alábbi és kijelentkezési a tartományvezérlőre, amely kezdeményezi egy hitelesítési ellenőrző kód használatával:
    
    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Hozzon létre a felhasználók keresztül egy bejelentkezés gombot az alkalmazás kezdőlapja

A Visual Studióban hozzon létre egy új nézetet a bejelentkezés gomb hozzáadásához és a felhasználói adatok hitelesítés utáni megjelenítéséhez:

1.  Kattintson a jobb gombbal a `Views\Home` mappát, és válassza ki `Add View`
2.  Nevezze el a következőképpen: `Index`.
3.  Adja hozzá a fájlhoz a következő, bejelentkezés gombot tartalmazó HTML-kódot:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>További információ
> Ezen a lapon fekete háttérrel SVG-formátumban ad hozzá egy bejelentkezés gombot:<br/>![Bejelentkezés Microsoft-fiókkal](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> További bejelentkezési gombok, nyissa meg a [ezt oldal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "védjegyzési útmutató").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Felhasználói jogcímek megjeleníthető vezérlő hozzáadása
Ez a vezérlő bemutatja, hogy hogyan védheti meg a vezérlőt az `[Authorize]` attribútummal. Ez az attribútum oly módon korlátozza a hozzáférést, hogy csak a hitelesített felhasználókat engedi hozzáférni a vezérlőhöz. Az alábbi kódot használ az attribútum megjelenítendő felhasználói jogcímeket, amelyek lekérni a bejelentkezés során.

1.  Kattintson a jobb gombbal a `Controllers` mappa: `Add` > `Controller`
2.  Válassza a(z) `MVC {version} Controller – Empty` lehetőséget.
3.  Kattintson a *hozzáadása*
4.  Adja neki a `ClaimsController` nevet
5.  Cserélje le a kódot a vezérlő osztályhoz az alábbi - kód Ez hozzáadja a `[Authorize]` attribútumot az osztályra:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>További információ
> Az `[Authorize]` attribútum használata miatt a vezérlő metódusait csak akkor lehet végrehajtani, ha a felhasználó hitelesítve van. Ha a felhasználó nincs hitelesítve, és megpróbál hozzáférni a vezérlő, OWIN kezdeményezzen egy hitelesítési kérdés, és hitelesíti a felhasználót. A fenti kód megvizsgálja az adott felhasználói attribútumok a felhasználói azonosító jogkivonat szerepel a jogcímek listája. Ilyen attribútum például a felhasználó teljes neve és felhasználóneve, valamint a globális felhasználóazonosító tárgya. Emellett tartalmazza a *bérlőazonosítót* is, amely a felhasználó szervezetének azonosítóját jelöli. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>A felhasználói jogcímek megjeleníthető nézet létrehozása

A Visual Studióban hozzon létre egy új nézetet a felhasználói jogcímek weboldalon való megjelenítéséhez:

1.  Kattintson a jobb gombbal a `Views\Claims` mappát és: `Add View`
2.  Nevezze el a következőképpen: `Index`.
3.  Adja hozzá a következő HTML-kódot a fájlhoz:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```
