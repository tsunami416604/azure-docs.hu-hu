---
title: Az Azure AD szolgáltatással integrálható, .NET-alapú webes API létrehozása hitelesítéshez és engedélyezéshez | Microsoft Docs
description: Hogyan lehet az Azure AD szolgáltatással integrálható, .NET MVC webes API-t létrehozni hitelesítéshez és engedélyezéshez?
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f87573e23f2c0f48e54b6f03289969aab930e15c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200598"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Gyors útmutató: .NET webes API-t, amely az Azure AD-hitelesítési és engedélyezési szolgáltatásainak készítése

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

## <a name="step-1-register-an-application-with-azure-ad"></a>1. lépés: Alkalmazás regisztrálása az Azure ad-ben

Hogy biztonságossá tegye alkalmazását, először létre kell hoznia egy alkalmazást a bérlőjében, és meg kell adnia az Azure AD-nek néhány kulcsfontosságú információt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure AD-bérlő kiválasztásához kattintson a fiókjára az oldal jobb felső részén, válassza a **Címtár váltása** elemet, majd válassza ki a megfelelő bérlőt.
    * Ezt a lépést kihagyhatja, ha fiókjában csak egy Azure AD-bérlővel rendelkezik, vagy ha már kiválasztotta a megfelelő Azure AD-bérlőt.

3. Válassza ki az **Azure Active Directory** elemet a bal oldali navigációs panelen.
4. Válassza az **Alkalmazásregisztrációk**, majd a **Hozzáadás** elemet.
5. Kövesse az utasításokat, és hozzon létre egy új **webalkalmazást és/vagy webes API-t**.
    * A **név** az alkalmazást ismerteti a felhasználók számára. Adja meg a **Teendőlista szolgáltatás** nevet.
    * Az **Átirányítási URI** séma- és sztringkombinációt az Azure AD az alkalmazás által kért jogkivonatok visszaadására használja. Adja meg ehhez a következő értéket: `https://localhost:44321/`.

6. Frissítse az alkalmazásazonosító URI-ját az alkalmazás **Beállítások > Tulajdonságok** lapján. Adjon meg egy bérlőspecifikus azonosítót. Adja meg például a következőt: `https://contoso.onmicrosoft.com/TodoListService`.
7. Mentse a konfigurációt. Hagyja megnyitva a portált, mert rövidesen az ügyfélalkalmazást is regisztrálnia kell.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>2. lépés: Az OWIN-hitelesítési folyamat használata az alkalmazás beállítása

A bejövő kérések és jogkivonatok ellenőrzése érdekében be kell állítania alkalmazását az Azure AD-vel való kommunikációhoz.

1. Először nyissa meg a megoldást, és adja hozzá az OWIN közbenső szoftver NuGet-csomagjait a TeendolistaSzolgaltatas projekthez a csomagkezelő konzol használatával.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Adjon hozzá egy `Startup.cs` nevű OWIN indítási osztályt a TeendolistaSzolgaltatas projekthez.  Kattintson a jobb gombbal a projektre, válassza a **Hozzáadás > Új elem** lehetőséget, majd keresse meg az **OWIN** elemet. Az OWIN közbenső szoftver meghívja a `Configuration(…)` metódust az alkalmazás indulásakor.

3. Módosítsa az osztálydeklarációt a következőre: `public partial class Startup`. Egy másik fájlban már implementáltuk Önnek az osztály egy részét. A `Configuration(…)` metódusban hívja meg a `ConfgureAuth(…)` elemet a hitelesítés beállításához webes alkalmazása számára.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Nyissa meg az `App_Start\Startup.Auth.cs` fájlt, és hajtsa végre a `ConfigureAuth(…)` metódust. A `WindowsAzureActiveDirectoryBearerAuthenticationOptions` alatt megadott paraméterek koordinátákként fognak szolgálni alkalmazása számára az Azure AD-vel való kommunikációhoz.

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

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>3. lépés: Ügyfélalkalmazás konfigurálása és a szolgáltatás futtatásához

Mielőtt működésbe hozná a Teendőlista szolgáltatást, konfigurálnia kell a Teendőlista ügyfelet, hogy az jogkivonatokat kaphasson az Azure AD-ből, és hívásokat kezdeményezhessen a szolgáltatás felé.

1. Lépjen vissza az [Azure Portalra](https://portal.azure.com).
1. Hozzon létre egy új alkalmazást az Azure AD-bérlőben, és válassza a **Natív ügyfélalkalmazás** lehetőséget a megnyíló ablakban.
    * A **név** az alkalmazást ismerteti a felhasználók számára.
    * Az **Átirányítási URI** értékéhez írja be a következőt: `http://TodoListClient/`.

1. A regisztráció befejezését követően az Azure AD egy egyedi alkalmazásazonosítót rendel az alkalmazáshoz. Erre az értékre szükség lesz a következő lépések során, ezért másolja ki az alkalmazás oldaláról.
1. A **Beállítások** lapon válassza a **Szükséges engedélyek**, majd a **Hozzáadás** lehetőséget. Keresse meg és válassza ki a Teendőlista szolgáltatást, adja hozzá a **TeendolistaSzolgaltatas hozzáféréséhez szükséges** engedélyt a **Delegált engedélyek** alatt, majd válassza a **Kész** lehetőséget.
1. Nyissa meg a TeendolistaUgyfel projektben található `App.config` fájlt a Visual Studióban, és adja meg konfigurációs értékeit az `<appSettings>` részben.

    * Az `ida:Tenant` az Azure AD-bérlő neve, például contoso.onmicrosoft.com.
    * Az `ida:ClientId` az Azure Portalról kimásolt alkalmazásazonosító.
    * A `todo:TodoListResourceId` a Teendőlista szolgáltatáshoz tartozó alkalmazásazonosító URI, amelyet az Azure Portalon megadott.

1. Távolítsa el, majd hozza létre és futtassa mindegyik projektet.
1. Ha még nem tette meg, hozzon létre új felhasználót a bérlőben *.onmicrosoft.com tartománnyal.
1. Jelentkezzen be a Teendőlista ügyfélbe ezzel a felhasználóval, és adjon hozzá néhány feladatot a felhasználó teendőlistájához.

## <a name="next-steps"></a>További lépések

* A referenciaként használható kész mintát (a konfigurációs értékek nélkül) a [GitHubról](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) töltheti le. Továbbléphet a további identitáskezelési forgatókönyvekre.
