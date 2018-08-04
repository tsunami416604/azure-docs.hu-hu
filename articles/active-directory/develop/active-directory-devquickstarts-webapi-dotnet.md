---
title: Az Azure AD .NET webes API első lépések |} A Microsoft Docs
description: Hogyan hozhat létre egy .NET MVC webes API-t, amely integrálható az Azure AD-hitelesítés és engedélyezés.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: ca506d821fe3534468c0d370dd51464e5df90f79
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504661"
---
# <a name="azure-ad-net-web-api-getting-started"></a>Az Azure AD .NET webes API első lépések
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Védett erőforrásokhoz való hozzáférést biztosító alkalmazás létrehozásakor, meg kell ismernie a jogosulatlan hozzáférés megakadályozása érdekében ezeket az erőforrásokat.
Az Azure Active Directory (Azure AD) révén egyszerű és könnyen érthető megjegyzésblokkok írására segítségével védelmet biztosíthatnak a webes API-k az OAuth 2.0 tulajdonosi hozzáférés jogkivonatok csak néhány sornyi kóddal.

Az ASP.NET web apps a Közösség által készített OWIN közbenső szoftvert a .NET-keretrendszer 4.5 része a Microsoft általi implementációja használatával végezheti el a védelmet. Itt fogjuk használni az OWIN "Teendőlista" webes API készítése:

* Azt jelzi, amely az API-k védelme.
* Ellenőrzi, hogy a webes API-hívásokat tartalmaznak egy érvényes hozzáférési jogkivonatot.

A a Do List API-t készíteni, akkor először kell:

1. Alkalmazás regisztrálása az Azure AD-ben.
2. Állítsa be az alkalmazás használatához az OWIN hitelesítési folyamatot.
3. A webes API meghívásához ügyfélalkalmazás konfigurálása.

Első lépésként [töltse le az alkalmazás skeleton](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). A Visual Studio 2013 megoldás minden egyes is. Emellett az Azure AD-bérlő, amelyben az alkalmazás regisztrálásához. Ha még nem tette meg, [megtudhatja, hogyan tehet szert egy](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>1. lépés: Alkalmazás regisztrálása az Azure ad-ben
Védelme érdekében az alkalmazás, először hozzon létre egy alkalmazást a saját bérlőjében, néhány kulcsfontosságú adatokat az Azure AD meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki az Azure AD-bérlő a fiókra a lapon, majd kattintson a jobb felső sarkában kattintson a **címtár váltása** navigációs és válassza ki a megfelelő bérlő.
 * Ezt a lépést kihagyhatja, csak egy Azure AD-bérlőt, ha a fiók alatt, vagy ha már kiválasztotta a megfelelő Azure AD-bérlővel.

3. A bal oldali navigációs ablakban kattintson az **Azure Active Directory** elemre.

4. Kattintson a **alkalmazásregisztrációk**, majd válassza ki **Hozzáadás**.

5. Kövesse az utasításokat, és hozzon létre egy új **webalkalmazás és/vagy webes API-t**.
  * **Név** ismerteti az alkalmazást a felhasználók számára. Adja meg **teendőlista szolgáltatás**.
  * **Átirányítási Uri** sémát és karakterlánc kombinációja, amely az Azure AD vissza az alkalmazás által kért jogkivonatokat. Adja meg `https://localhost:44321/` ehhez az értékhez.

6. Az a **beállítások** -> **tulajdonságok** oldalon az alkalmazás, frissítse az Alkalmazásazonosító URI-t. Adja meg a bérlő-specifikus azonosítója. Adja meg például a következőt: `https://contoso.onmicrosoft.com/TodoListService`.

7. A konfiguráció mentéséhez. Hagyja nyitva, a portálon, mert hamarosan regisztrálni az ügyfélalkalmazás is kell.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>2. lépés: Az alkalmazás beállítása az OWIN-hitelesítési folyamat használata
A bejövő kérések és a jogkivonatok érvényesítésére, szeretne kommunikálni az Azure ad-ben az alkalmazás beállítása.

1. A kezdéshez nyissa meg a megoldást, és az OWIN közbenső NuGet-csomagok hozzáadása a TodoListService projekthez a Package Manager konzol használatával.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Adjon hozzá egy OWIN indítási osztályt a nevű TodoListService projekthez `Startup.cs`.  Kattintson a jobb gombbal a projektre, válassza ki **Hozzáadás** > **új elem**, és keressen **OWIN**. Az OWIN közbenső szoftver meghívja a `Configuration(…)` metódust az alkalmazás indulásakor.

3. Módosítsa az osztálydeklarációt `public partial class Startup`. Általunk már megvalósított ebbe az osztályba tartozó, egy másik fájlban. Az a `Configuration(…)` metódus hívása legyen `ConfgureAuth(…)` hitelesítés a webalkalmazás beállítása.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Nyissa meg a fájlt `App_Start\Startup.Auth.cs` és megvalósítása a `ConfigureAuth(…)` metódust. A megadandó paraméterek `WindowsAzureActiveDirectoryBearerAuthenticationOptions` kommunikálni az Azure ad-ben az alkalmazás koordináták erre a célra.

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. Most már használhatja `[Authorize]` attribútumok segít megvédeni a tartományvezérlők és a műveleteket a JSON webes jogkivonat (JWT) hitelesítését. Megadhat az `Controllers\TodoListController.cs` -engedélyezés címkével rendelkező osztály. Ez kényszeríti a felhasználót, hogy jelentkezzen be az oldal elérése előtt.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Amikor egy jogosult hívó sikeresen hív meg egy a `TodoListController` API-k, a művelet igényelhet információkhoz juthat a hívóról. OWIN a jogcímek belül a tulajdonosi jogkivonat-n keresztül hozzáférést biztosít a `ClaimsPrincpal` objektum.  

6. Általános követelmény a webes API-khoz a jogkivonatban jelen lévő „hatókörök” érvényesítése. Ez biztosítja, hogy a felhasználó hozzájárult a szolgáltatáshoz való hozzáféréshez szükséges a, Do List engedélyeket.

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

7. Nyissa meg a `web.config` TodoListService projekt gyökérkönyvtárában található fájlt, és adja meg a konfigurációs értékeit az `<appSettings>` szakaszban.
  * `ida:Tenant` az a név az Azure AD-bérlő – például contoso.onmicrosoft.com.
  * `ida:Audience` az Alkalmazásazonosító URI-t az alkalmazás az Azure Portalon megadott van.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>3. lépés: Egy ügyfélalkalmazás konfigurálása és a szolgáltatás futtatásához
Ahhoz, hogy megjelenjenek a, tegye szolgáltatások listája működés közben, a To Do List-ügyfél konfigurálásához, így az Azure AD-ből tokenekhez és hívások indítása a Service kell.

1. Lépjen vissza a [az Azure portal](https://portal.azure.com).

2. Új alkalmazás létrehozása az Azure AD-bérlőben, és válassza ki **natív ügyfélalkalmazás** az eredményül kapott parancssorban.
  * **Név** ismerteti az alkalmazást a felhasználók számára.
  * Adja meg `http://TodoListClient/` számára a **átirányítási URI-t** értéket.

3. Regisztráció után az Azure AD az alkalmazáshoz rendeli hozzá egy egyedi Alkalmazásazonosítója. Ez az érték kell a következő lépésekben, ezért másolja ki az alkalmazás oldaláról.

4. Az a **beállítások** lapon jelölje be **szükséges engedélyek**, majd válassza ki **Hozzáadás**. Keresse meg és válassza ki az való tegye listában, adja hozzá a **hozzáférés TodoListService** engedélyt a **delegált engedélyek**, és kattintson a **kész**.

5. A Visual Studióban nyissa meg a `App.config` a TodoListClient a projektre, és adja meg a konfigurációs értékeit az `<appSettings>` szakaszban.

  * `ida:Tenant` az a név az Azure AD-bérlő – például contoso.onmicrosoft.com.
  * `ida:ClientId` az Alkalmazásazonosító, az Azure Portalról másolt van.
  * `todo:TodoListResourceId` az Alkalmazásazonosító URI a To Do List Service alkalmazás az Azure Portalon megadott van.

## <a name="next-steps"></a>További lépések
Végül tiszta, hozhat létre, és minden projekt futtatása. Ha még nem tette, itt-e az idő, hozzon létre egy új felhasználót a bérlőben a *. onmicrosoft.com tartomány. Jelentkezzen be, hogy a felhasználó To Do List ügyfél, és az egyes tevékenységek hozzáadása a felhasználók feladatlistáit.

Referenciaként az elkészült mintát (a konfigurációs értékek) nélkül érhető el a [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Most már továbbléphet több identitás helyzetekben.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
