---
title: "Ismerkedés az Azure AD .NET webalkalmazás |} Microsoft Docs"
description: "A .NET MVC webes alkalmazás, amely az Azure AD-bejelentkezés létrehozása."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 3c1e558c9d41e385f80939203a3457b74e30973b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>ASP.NET webalkalmazás be- és kijelentkezési az Azure ad szolgáltatással
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Be- és kijelentkezési egyetlen azáltal csak néhány sornyi kódot, Azure Active Directory (Azure AD) egyszerűen meg a webalkalmazás Identitáskezelés kihelyező. ASP.NET webes alkalmazások mindkét felhasználók regisztrálhat a Microsoft általi implementációja Open Web Interface .NET (OWIN) köztes használatával. Közösségi szerkesztésű OWIN köztes a .NET-keretrendszer 4.5 része. Ez a cikk bemutatja, hogyan használható az OWIN:

* A felhasználók beléptetése a webalkalmazások Azure AD használatával az identitás-szolgáltatóként.
* Bizonyos felhasználói információk jelennek meg.
* A felhasználók az alkalmazások kívül.

## <a name="before-you-get-started"></a>A kezdés előtt
* Töltse le a [app vázat](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) vagy töltse le a [elkészült mintát](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).
* Szükség az Azure AD-bérlő, amelyben az alkalmazás regisztrálásához. Ha még nem telepítette az Azure AD-bérlő [beszerzéséről egy](active-directory-howto-tenant.md).

Ha készen áll, hajtsa végre a következő négy részből áll.

## <a name="step-1-register-the-new-app-with-azure-ad"></a>1. lépés: Az új alkalmazás regisztrálása az Azure ad szolgáltatással
Az alkalmazás beállítása a felhasználók hitelesítésére, először regisztrálja az Ön bérelt szolgáltatásának a következő módon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiók nevére. Az a **Directory** listára, válassza ki az Active Directory-bérlőt, ahol az alkalmazást regisztrálni kívánt.
3. Kattintson a **több szolgáltatások** a bal oldali ablaktáblán, és válassza a **Azure Active Directory**.
4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.
5. Kövesse az utasításokat, hozzon létre egy új **webalkalmazás és/vagy WebAPI**.
  * **Név** az alkalmazásnak, hogy a felhasználók ismerteti.
  * **Bejelentkezési URL-cím** az alkalmazás alap URL-címe. A vázat alapértelmezett URL-címe: https://localhost:44320 /.
6. A regisztráció befejezését követően az Azure AD rendeli hozzá az alkalmazás egy egyedi azonosítót. Kimásolhatja az értéket az alkalmazás oldalon a következő szakaszokban lévő használatára.
7. Az a **beállítások** -> **tulajdonságok** az alkalmazás lapján frissítse a App ID URI. A **App ID URI** az alkalmazás egyedi azonosítója. Az elnevezési konvenció: `https://<tenant-domain>/<app-name>` (például `https://contoso.onmicrosoft.com/my-first-aad-app`).

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>2. lépés: Az alkalmazás használatának beállítása a OWIN hitelesítési folyamatot
Ebben a lépésben az OWIN közbenső szoftvert az OpenID Connect hitelesítési protokoll használatára konfigurálja. OWIN segítségével be- és kijelentkezési kérések kiállítása, kezelni a felhasználói munkameneteket, felhasználói adatok beolvasása és így tovább.

1. Első lépésként hozzá az OWIN köztes NuGet-csomagok a projekthez a Csomagkezelő konzol használatával.

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. Egy OWIN indítási osztály hozzáadása a projekthez nevű `Startup.cs`, kattintson jobb gombbal a projektre, válassza ki **Hozzáadás**, jelölje be **új elem**, majd keresse meg a **OWIN**. Az OWIN köztes meghívja a **Configuration(...)**  módszer az alkalmazás indításakor.
3. Módosítsa az osztálydeklaráció való `public partial class Startup`. Azt korábban már megvalósított Ez az osztály tartozik, egy másik fájlban. Az a **Configuration(...)**  metódus hívása legyen **ConfgureAuth(...)**  az alkalmazás hitelesítés beállítása.  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. Nyissa meg a App_Start\Startup.Auth.cs fájlt, és megvalósításához a **ConfigureAuth(...)**  metódust. Megadja a paraméterek *OpenIDConnectAuthenticationOptions* kommunikálni az Azure AD alkalmazás-koordináták szolgálnak. Azt is be kell állítania cookie-hitelesítés, mert az OpenID Connect köztes cookie-kat használ a háttérben.

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
                 Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = context =>
                        {
                            context.HandleResponse();
                            context.Response.Redirect("/Error?message=" + context.Exception.Message);
                            return Task.FromResult(0);
                        }
                    }
             });
     }
     ```

5. Nyissa meg a web.config fájlt a projekt gyökérkönyvtárában, és írja be a konfigurációs értékeket az a `<appSettings>` szakasz.
  * `ida:ClientId`: A GUID-azonosító az Azure-portálról másolt "1. lépés: az új alkalmazás regisztrálása az Azure ad szolgáltatással."
  * `ida:Tenant`: Az Azure AD-bérlő (például contoso.onmicrosoft.com) a neve.
  * `ida:PostLogoutRedirectUri`: A kijelző, amely közli az Azure AD, ha a rendszer átirányítja az a felhasználó a kijelentkezési kérés sikeres végrehajtása után.

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>3. lépés: Használja az Azure AD-be- és kijelentkezési kérések kiállítása az OWIN
Az alkalmazás megfelelően konfigurálva van az Azure AD az OpenID Connect hitelesítési protokoll használatával kommunikálnak. OWIN kezelt összes hitelesítési üzenetek létrehozásával, ellenőrzése az Azure ad-jogkivonatok és karbantartása a felhasználói munkamenetek részleteit. Most már kell biztosítani a felhasználóknak a be-és kijelentkezés úgy.

1. Használhatja a tartományvezérlőket, a felhasználóknak a bejelentkezéshez, mielőtt hozzáférnek az egyes lapok címkék engedélyezéséhez. Ehhez nyissa meg a Controllers\HomeController.cs, és adja hozzá a `[Authorize]` címkén belül, hogy a jogi tudnivalók megjelenítése Névjegy művelet.

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. OWIN segítségével közvetlenül kiadni a kód a érkező hitelesítési kéréseket. Ehhez nyissa meg a Controllers\AccountController.cs. Ezt követően a SignIn() és SignOut() műveletek adja ki az OpenID Connect kérdés és kijelentkezési kérések.

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Nyissa meg a Views\Shared\_LoginPartial.cshtml megjelenítése a felhasználónak az alkalmazás be- és kijelentkezési hivatkozások, és nyomtassa ki a nézetben a felhasználó nevét.

    ```HTML
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

## <a name="step-4-display-user-information"></a>4. lépés: Felhasználói információk megjelenítése
Amikor a felhasználók az OpenID Connect, az Azure AD egy id_token az alkalmazásra, amely tartalmazza a "jogcímek", vagy a felhasználóval kapcsolatos előfeltételek adja vissza. Ezeket a jogcímeket segítségével szabja személyre az alkalmazás a következő módon:

1. Nyissa meg a Controllers\HomeController.cs fájlt. A felhasználói jogcímek, a vezérlők keresztül érheti el a `ClaimsPrincipal.Current` rendszerbiztonsági objektumot.

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. Hozza létre, és futtassa az alkalmazást. Ha egy új felhasználó még nem hozott létre, hogy az Ön bérlőjében egy onmicrosoft.com tartománnyal, most az ehhez idő. Ezt a következőképpen teheti meg:

  a. Jelentkezzen be, hogy a felhasználó, és vegye figyelembe, hogyan jelennek meg a felső sávon a felhasználó identitását.

  b. Jelentkezzen ki, majd jelentkezzen be egy másik felhasználó az Ön bérelt szolgáltatásának.

  c. Különösen gondolkodhat már, ha regisztrálja, és futtassa az alkalmazást (a saját clientId) egy másik példánya, és egyszeri bejelentkezés működés közben bemutató.

## <a name="next-steps"></a>Következő lépések
Referenciáért lásd: [az elkészült mintát](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (a konfigurációs értékek nélkül).

Most már továbbléphet az összetettebb témákra. Például [egy webes API-t az Azure ad-vel biztonságos](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
