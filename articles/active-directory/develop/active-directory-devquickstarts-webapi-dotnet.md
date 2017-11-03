---
title: "Az Azure AD .NET webes API bevezetés |} Microsoft Docs"
description: "Hogyan hozhat létre a .NET MVC webes API-k, amely az Azure AD a hitelesítéshez és engedélyezéshez."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: f44d75f45073a5d9aa9b1863ed227aba4efcf785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="help-protect-a-web-api-by-using-bearer-tokens-from-azure-ad"></a>A webes API védelme az Azure AD tulajdonosi jogkivonatok segítségével
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Ha a védett erőforrásokhoz való hozzáférést biztosít az alkalmazás most felépítése, meg kell ismernie történő jogosulatlan hozzáférés elkerülése érdekében ezeket az erőforrásokat.
Az Azure Active Directory (Azure AD) elérhetővé válnak az egyszerű és magától értetődő védelme egy webes API-t OAuth 2.0 tulajdonosi hozzáférés segítségével csak néhány sornyi kódot a jogkivonatokat.

Az ASP.NET web apps a közösségi szerkesztésű OWIN köztes a .NET-keretrendszer 4.5 része a Microsoft általi implementációja használatával végezheti el a védelmet. Itt az OWIN fogjuk használni, amely "Teendőlista" webes API-k létrehozása:

* Jelöl ki, amelyek API-k védett.
* Ellenőrzi, hogy a webes API-hívások tartalmaznak egy érvényes jogkivonat.

A való tegye lista API létrehozásához először kell:

1. Alkalmazás regisztrálása az Azure AD-ben.
2. Az alkalmazás beállítása az OWIN hitelesítési folyamatot használja.
3. A webes API hívásához ügyfélalkalmazás konfigurálása.

A kezdéshez [töltse le az alkalmazás vázat](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Egy Visual Studio 2013 megoldást. Akkor is kell, ahol regisztrálhatja alkalmazását Azure AD-bérlő. Ha Ön nem rendelkezik ilyennel, [beszerzéséről egy](active-directory-howto-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>1. lépés: Egy alkalmazás regisztrálása az Azure ad szolgáltatással
Biztonságossá az alkalmazásról, először létrehoz egy alkalmazást az Ön bérlőjében, és adjon meg néhány kulcsfontosságú adatokat az Azure AD.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A felső eszköztáron kattintson a fiókját. Az a **Directory** menüben válassza ki az Azure AD-bérlőt, ahová az alkalmazás regisztrálásához.

3. Kattintson a **több szolgáltatások** a bal oldali ablaktáblán, és válassza a **Azure Active Directory**.

4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.

5. Kövesse az utasításokat, és hozzon létre egy új **webalkalmazás és/vagy webes API**.
  * **Név** az alkalmazás a felhasználók számára ismerteti. Adja meg **teendőlista szolgáltatás**.
  * **Átirányítási Uri** protokollt és a karakterlánc kombinációját, amely az Azure AD szükséges az alkalmazás által kért jogkivonatokhoz adja vissza. Adja meg `https://localhost:44321/` ehhez az értékhez.

6. Az a **beállítások** -> **tulajdonságok** az alkalmazás lapján frissítse a App ID URI. Adjon meg egy bérlő-specifikus azonosítója. Adja meg például a következőt: `https://contoso.onmicrosoft.com/TodoListService`.

7. A konfiguráció mentéséhez. Hagyja megnyitva, a portál, mert biztosítani kell az ügyfélalkalmazás hamarosan regisztrálni.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>2. lépés: Az alkalmazás használatának beállítása a OWIN hitelesítési folyamatot
Érvényesíteni a bejövő kérelmeket és jogkivonatok, akkor be kell állítania az alkalmazás kommunikálni az Azure AD.

1. Első lépésként nyissa meg a megoldást, és az OWIN köztes NuGet-csomagok hozzáadása a TodoListService projekthez a Csomagkezelő konzol használatával.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Egy OWIN indítási osztály hozzáadása a TodoListService projekt neve `Startup.cs`.  Kattintson jobb gombbal a projektre, válassza ki **Hozzáadás** > **új elem**, majd keresse meg a **OWIN**. Az OWIN közbenső szoftver meghívja a `Configuration(…)` metódust az alkalmazás indulásakor.

3. Módosítsa az osztálydeklaráció való `public partial class Startup`. Azt korábban már megvalósított Ez az osztály tartozik, egy másik fájlban. Az a `Configuration(…)` metódus hívása legyen `ConfgureAuth(…)` hitelesítés a webalkalmazás beállítása.

    ```C#
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Nyissa meg a fájlt `App_Start\Startup.Auth.cs` és megvalósítását a `ConfigureAuth(…)` metódust. A megadott paraméterek `WindowsAzureActiveDirectoryBearerAuthenticationOptions` koordináták az alkalmazás az Azure AD kommunikálni fog szolgálni.

    ```C#
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

5. Mostantól a `[Authorize]` segít megvédeni a tartományvezérlőket és a JSON webes jogkivonat (JWT) tulajdonosi hitelesítéssel műveletek attribútumait. Adja a `Controllers\TodoListController.cs` osztály engedélyezés címke használatával. Ezzel kikényszeríti a felhasználót, hogy jelentkezzen be a lap elérése előtt.

    ```C#
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Amikor egy jogosult hívó sikeresen hív meg, egy a `TodoListController` API-k, a művelet módosítania kell információkhoz juthat a hívóról. A jogcímek belül a tulajdonosi jogkivonattal keresztül hozzáférést biztosít az OWIN a `ClaimsPrincpal` objektum.  

6. Általános követelmény a webes API-khoz a jogkivonatban jelen lévő „hatókörök” érvényesítése. Ez biztosítja, hogy a felhasználó hozzájárult a való tegye lista szolgáltatás eléréséhez szükséges engedélyekkel.

    ```C#
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

7. Nyissa meg a `web.config` a TodoListService projekt gyökérkönyvtárában található fájlt, és adja meg a konfigurációs értékeit a `<appSettings>` szakasz.
  * `ida:Tenant`az Azure AD-bérlő – például a contoso.onmicrosoft.com neve van.
  * `ida:Audience`az App ID URI az alkalmazás az Azure-portálon megadott van.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>3. lépés: Ügyfélalkalmazás konfigurálása és a szolgáltatás futtatásához
Ahhoz, hogy a To tegye lista Service működés közben, a teendőlista-ügyfél konfigurálása, hogy azok jogkivonatokhoz az Azure AD és a szolgáltatás meghíváshoz kell.

1. Lépjen vissza a [Azure-portálon](https://portal.azure.com).

2. Új alkalmazás létrehozása az Azure AD-bérlőn, és válassza ki **natív ügyfélalkalmazás** az eredményül kapott kér.
  * **Név** az alkalmazás a felhasználók számára ismerteti.
  * Adja meg `http://TodoListClient/` a a **átirányítási Uri-** érték.

3. Regisztráció befejezése után az Azure AD egy egyedi alkalmazás Azonosítót rendel az alkalmazáshoz. Ez az érték kell a következő lépésekben, ezért másolja az alkalmazás oldalról.

4. Az a **beállítások** lapon jelölje be **szükséges engedélyek**, majd válassza ki **Hozzáadás**. Keresse meg és válassza ki a való tegye lista szolgáltatást, vegye fel a **hozzáférés TodoListService** engedélyt a **delegált engedélyek**, és kattintson a **végzett**.

5. A Visual Studióban nyissa meg a `App.config` a TodoListClient a projektre, és írja be a konfigurációs értékeit a `<appSettings>` szakasz.

  * `ida:Tenant`az Azure AD-bérlő – például a contoso.onmicrosoft.com neve van.
  * `ida:ClientId`a rendszer az Azure-portálról másolt alkalmazás Azonosítóját.
  * `todo:TodoListResourceId`az App ID URI számára tegye lista szolgáltatásalkalmazás az Azure-portálon megadott van.

## <a name="next-steps"></a>Következő lépések
Végül törlése, elkészítéséhez és futtatása minden olyan projekthez. Ha még nem tette meg, most már az új felhasználó létrehozása a bérlő az idő a *. onmicrosoft.com tartományt. Jelentkezzen be, hogy a felhasználó a teendőlista ügyfélnek, és bizonyos feladatok hozzáadása a felhasználói feladatlistában.

Referenciaként az elkészült mintát (a konfigurációs értékek nélkül) érhető el a [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Most már továbbléphet további identitás forgatókönyvek.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
