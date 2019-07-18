---
title: Az Azure AD szolgáltatással integrálható, .NET-alapú webes API létrehozása hitelesítéshez és engedélyezéshez | Microsoft Docs
description: Hogyan lehet az Azure AD szolgáltatással integrálható, .NET MVC webes API-t létrehozni hitelesítéshez és engedélyezéshez?
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5375d47c1b012a1c808a1115b7c902d99b05bf9d
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304722"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Gyors útmutató: Az Azure AD-vel integrált .NET-alapú webes API-k létrehozása hitelesítéshez és engedélyezéshez

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Ha olyan alkalmazást hoz létre, amely védett erőforrásokhoz biztosít hozzáférést, tudnia kell, hogyan előzze meg az erőforrásokhoz való jogosulatlan hozzáférést. Az Azure Active Directory (Azure AD) segítségével egyszerűen védetté tehet egy webes API-t OAuth 2.0 tulajdonosi hozzáférési jogkivonatok használatával, egy mindössze pársoros kód megírásával.

Az ASP.NET webalkalmazásokban ezt a védelmet a .NET keretrendszer 4.5-ös verziójában megtalálható, közösségi fejlesztésű OWIN közbenső szoftver Microsoft általi implementációjával érheti el. Itt az OWIN-nel egy „teendőlista” API-t fog létrehozni, amely:

* jelzi, mely API-k védettek;
* ellenőrzi, hogy a webes API-hívások érvényes hozzáférési jogkivonatot tartalmaznak-e.

Ennek a rövid útmutatónak a segítségével létre fog hozni egy teendőlista API-t, és megtanulja a következőket:

1. Alkalmazás regisztrálása az Azure AD-ben.
2. Az alkalmazás beállítása az OWIN hitelesítési folyamat használatára.
3. Ügyfélalkalmazás konfigurálása a webes API meghívására.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként végre kell hajtania az alábbi lépéseket:

* [Töltse le az alkalmazás vázát](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) vagy [a kész mintát](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Mindkettő Visual Studio 2013-megoldás.
* Rendelkeznie kell egy Azure AD-bérlővel, amelyben regisztrálhatja az alkalmazását. Ha még nem rendelkezik ilyennel, [így tehet szert rá](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>1\. lépés: Alkalmazás regisztrálása az Azure AD-ben

Hogy biztonságossá tegye alkalmazását, először létre kell hoznia egy alkalmazást a bérlőjében, és meg kell adnia az Azure AD-nek néhány kulcsfontosságú információt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure AD-bérlő kiválasztásához kattintson a fiókjára az oldal jobb felső részén, válassza a **Címtár váltása** elemet, majd válassza ki a megfelelő bérlőt.
    * Ezt a lépést kihagyhatja, ha fiókjában csak egy Azure AD-bérlővel rendelkezik, vagy ha már kiválasztotta a megfelelő Azure AD-bérlőt.

3. Válassza ki az **Azure Active Directory** elemet a bal oldali navigációs panelen.
4. Válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza az **új regisztráció**lehetőséget.
5. Amikor megjelenik az **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét. Például: "to Do List Service".
A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
6. Válassza ki a **webplatformot** az átirányítási **URI** szakaszban, és állítsa `https://localhost:44321/` be az értéket (az Azure ad által visszaadott jogkivonatokat).
7. Miután végzett, válassza a **Regisztrálás** lehetőséget. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét.
8. Válassza ki **az API közzététele** lehetőséget, majd kattintson **a hatókör hozzáadása**lehetőségre.
9. Fogadja el a javasolt alkalmazás-azonosító URI-t (API://{clientId}) a **Mentés és folytatás**lehetőség kiválasztásával.
10. Adja meg a következő paramétereket:
    1. A **hatókör neve**mezőbe írja be a "access_as_user" értéket.
    1. Győződjön meg arról, hogy **ki férhet**hozzá a **rendszergazdák és a felhasználók** beállításhoz?
    1. A **rendszergazda által megadott megjelenítendő név**mezőben adja meg a "hozzáférés TodoListService felhasználóként" kifejezést.
    1. A **rendszergazdai engedély leírása** típus "a TODOLISTSERVICE web API-t felhasználóként éri el".
    1. A **felhasználói engedély megjelenítendő neve** mezőbe írja be a "hozzáférés TodoListService felhasználóként" kifejezést.
    1. A **felhasználói beleegyezett Leírás** típusa "hozzáférés a TODOLISTSERVICE web API-hoz felhasználóként".
    1. Az **állapot**területen válassza az **engedélyezve**lehetőséget.
11. A konfiguráció mentéséhez válassza a **hatókör hozzáadása** elemet. Hagyja megnyitva a portált, mert rövidesen az ügyfélalkalmazást is regisztrálnia kell.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>2\. lépés: Az alkalmazás beállítása a OWIN hitelesítési folyamat használatára

A bejövő kérések és jogkivonatok ellenőrzése érdekében be kell állítania alkalmazását az Azure AD-vel való kommunikációhoz.

1. Először nyissa meg a megoldást, és adja hozzá az OWIN közbenső szoftver NuGet-csomagjait a TeendolistaSzolgaltatas projekthez a csomagkezelő konzol használatával.

    ```
    Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Adjon hozzá egy `Startup.cs` nevű OWIN indítási osztályt a TeendolistaSzolgaltatas projekthez.  Kattintson a jobb gombbal a projektre, válassza a **Hozzáadás > Új elem** lehetőséget, majd keresse meg az **OWIN** elemet. Az OWIN közbenső szoftver meghívja a `Configuration(…)` metódust az alkalmazás indulásakor.

3. Módosítsa az osztálydeklarációt a következőre: `public partial class Startup`. Egy másik fájlban már implementáltuk Önnek az osztály egy részét. A `Configuration(…)` metódusban hívja meg a `ConfigureAuth(…)` elemet a hitelesítés beállításához webes alkalmazása számára.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Nyissa meg az `App_Start\Startup.Auth.cs` fájlt, és hajtsa végre a `ConfigureAuth(…)` metódust. A `WindowsAzureActiveDirectoryBearerAuthenticationOptions` alatt megadott paraméterek koordinátákként fognak szolgálni alkalmazása számára az Azure AD-vel való kommunikációhoz. A használatához osztályokat kell használnia a `System.IdentityModel.Tokens` névtérben.

    ```csharp
    using Microsoft.IdentityModel.Tokens;
    ```

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                 Tenant = ConfigurationManager.AppSettings["ida:Tenant"],
                 TokenValidationParameters = new TokenValidationParameters
                 {
                    ValidAudience = ConfigurationManager.AppSettings["ida:Audience"]
                 }
            });
    }
    ```

5. Az `[Authorize]` attribútumok használatával JSON Web Token (JWT) formátumú tulajdonosi hitelesítéssel védheti vezérlőit és műveleteit. Jelölje meg a `Controllers\TodoListController.cs` osztályt egy engedélyezési címkével, amely az oldal elérése előtt arra kényszeríti a felhasználót, hogy jelentkezzen be.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Amikor egy jogosult hívó sikeresen meghívja a `TodoListController` API-k egyikét, a műveletnek szüksége lehet a hívó információihoz való hozzáférésre. Az OWIN hozzáférést biztosít a tulajdonosi jogkivonatban lévő jogcímekhez a `ClaimsPrincpal` objektumon keresztül.  

6. Általános követelmény a webes API-khoz a jogkivonatban jelen lévő „hatókörök” érvényesítése, amely biztosítja, hogy a felhasználó megadta a Teendőlista szolgáltatáshoz való hozzáféréshez szükséges engedélyeket.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Nyissa meg a TeendolistaSzolgaltatas projekt gyökerében található `web.config` fájlt, és adja meg konfigurációs értékeit az `<appSettings>` részben.
    * Az `ida:Tenant` az Azure AD-bérlő neve, például contoso.onmicrosoft.com.
    * Az `ida:Audience` az Azure Portalon megadott alkalmazásazonosító URI.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>3\. lépés: Ügyfélalkalmazás konfigurálása és a szolgáltatás futtatása

Mielőtt működésbe hozná a Teendőlista szolgáltatást, konfigurálnia kell a Teendőlista ügyfelet, hogy az jogkivonatokat kaphasson az Azure AD-ből, és hívásokat kezdeményezhessen a szolgáltatás felé.

1. Lépjen vissza az [Azure Portalra](https://portal.azure.com).
1. Hozzon létre egy új alkalmazás-regisztrációt az Azure AD-bérlőben.  Adjon meg egy **nevet** , amely leírja az alkalmazást a felhasználók `https://TodoListClient/` számára, írja be az átirányítási **URI** értékét, majd válassza a **nyilvános ügyfél (mobil és asztali)** lehetőséget a legördülő menüben.
1. A regisztráció befejezését követően az Azure AD egy egyedi alkalmazásazonosítót rendel az alkalmazáshoz. Erre az értékre szükség lesz a következő lépések során, ezért másolja ki az alkalmazás oldaláról.
1. Válassza az **API-engedélyek**lehetőséget, majd **adjon hozzá egy engedélyt**.  Keresse meg és válassza ki a **teendők listázása szolgáltatást**, adja hozzá a **user_impersonation hozzáférési TodoListService** engedélyt a **delegált engedélyek**területen, majd válassza az **engedélyek hozzáadása**elemet.
1. Nyissa meg a TeendolistaUgyfel projektben található `App.config` fájlt a Visual Studióban, és adja meg konfigurációs értékeit az `<appSettings>` részben.

    * Az `ida:Tenant` az Azure AD-bérlő neve, például contoso.onmicrosoft.com.
    * Az `ida:ClientId` az Azure Portalról kimásolt alkalmazásazonosító.
    * A `todo:TodoListResourceId` a Teendőlista szolgáltatáshoz tartozó alkalmazásazonosító URI, amelyet az Azure Portalon megadott.

1. Távolítsa el, majd hozza létre és futtassa mindegyik projektet.
1. Ha még nem tette meg, hozzon létre új felhasználót a bérlőben *.onmicrosoft.com tartománnyal.
1. Jelentkezzen be a Teendőlista ügyfélbe ezzel a felhasználóval, és adjon hozzá néhány feladatot a felhasználó teendőlistájához.

## <a name="next-steps"></a>További lépések

* A referenciaként használható kész mintát (a konfigurációs értékek nélkül) a [GitHubról](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) töltheti le. Továbbléphet a további identitáskezelési forgatókönyvekre.
