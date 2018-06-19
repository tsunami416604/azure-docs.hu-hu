---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 98f3372b48b004c0067a085bbf130bc4e5144437
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "36205586"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Be- és kijelentkezési kérések kezelésére vezérlő hozzáadása

Ez a lépés bemutatja, hogyan hozzon létre egy új tartományvezérlő teszi közzé a bejelentkezési és kijelentkezési metódusokat.

1.  Kattintson a jobb gombbal a `Controllers` mappára, és válassza `Add` > `Controller`
2.  Válassza a(z) `MVC (.NET version) Controller – Empty` lehetőséget.
3.  Kattintson a *hozzáadása*
4.  Nevezze el `HomeController` kattintson *hozzáadása*
5.  Adja hozzá *OWIN* osztály mutató hivatkozásokat:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. A tartományvezérlő egy hitelesítési kérdés kód lekérhetik a két módszer bejelentkezési kezeléséhez az alábbi és kijelentkezési hozzáadása:
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Az alkalmazás kezdőlapját jelentkezzen be a bejelentkezési gomb felhasználókat létrehozása

A Visual Studio vegye fel a Bejelentkezés gombra, és megjeleníti a felhasználói adatok hitelesítés után új nézet létrehozása:

1.  Kattintson a jobb gombbal a `Views\Home` mappára, és válassza `Add View`
2.  Nevezze el a következőképpen: `Index`.
3.  A következő HTML, amely tartalmazza a Bejelentkezés gombra, adja hozzá a fájlhoz:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign-In with Microsoft Guide</title>
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
> Ezen a lapon egy fekete háttérrel SVG-formátumban ad hozzá a Bejelentkezés gombra:<br/>![Jelentkezzen be Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> Több bejelentkezési gomb, előbb lépjen a [ezen a lapon](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "irányelvek Branding").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Felhasználói jogcímek megjelenítendő vezérlő hozzáadása
Ebben a vezérlőben a használatát mutatja be a `[Authorize]` attribútum egy tartományvezérlőre védelméhez. Ez az attribútum korlátozza a hozzáférést a vezérlő csak a hitelesített felhasználók lehetővé. Az alábbi kódot alkalmazza az attribútum részeként a bejelentkezés felhasználói jogcímeket lekérése megjelenítéséhez.

1.  Kattintson a jobb gombbal a `Controllers` mappába: `Add` > `Controller`
2.  Válassza a(z) `MVC {version} Controller – Empty` lehetőséget.
3.  Kattintson a *hozzáadása*
4.  Nevezze el `ClaimsController`
5.  Cserélje le a vezérlő osztályhoz tartozó kódot - Ez a gyorsjavítás a `[Authorize]` attribútumot az osztályra:

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
> Használata miatt a `[Authorize]` csak akkor hajtható végre attribútum, a tartományvezérlő minden módszert, ha a felhasználó hitelesítése. Ha a felhasználó nincs hitelesítve, és megpróbál hozzáférni a tartományvezérlő, OWIN egy hitelesítési kérdés kezdeményezése, és hitelesíteni a felhasználót. A fenti kódot ellenőrzi, hogy az adott felhasználói attribútumok a felhasználói azonosítót jogkivonatban foglalt jogcímek listája. Ezek az attribútumok közé tartozik a felhasználó teljes neve és a felhasználónév, valamint a globális felhasználói azonosító tulajdonos. Is tartalmaz, a *Bérlőazonosító*, amely jelenti, hogy a szervezet a felhasználói Azonosítóját. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>A felhasználói jogcímek megjelenítendő nézet létrehozása

A Visual Studio a felhasználói jogcímek megjelenő weblap új nézet létrehozása:

1.  Kattintson a jobb gombbal a `Views\Claims` mappa és: `Add View`
2.  Nevezze el a következőképpen: `Index`.
3.  Adja hozzá a fájlhoz a következő HTML-KÓDBAN:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign-In with Microsoft Sample</title>
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
