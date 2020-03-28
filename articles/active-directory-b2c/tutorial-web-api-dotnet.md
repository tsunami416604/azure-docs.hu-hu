---
title: 'Oktatóanyag: Hozzáférés adása egy ASP.NET webes API-hoz'
titleSuffix: Azure AD B2C
description: Oktatóanyag arról, hogyan használhatja az Active Directory B2C-t egy ASP.NET webes API védelmére, és hogyan hívhatja meg egy ASP.NET webalkalmazásból.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 23531bd4c53dc2fc4851a1e4718fca0e9c3bfc1c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78187423"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Oktatóanyag: Hozzáférés adása egy ASP.NET webes API-hoz az Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy védett webes API-erőforrást az Azure Active Directory B2C (Azure AD B2C) egy ASP.NET webalkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API-hoz
> * A minta konfigurálása az alkalmazás használatára

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a lépéseket és előfeltételeket az [Oktatóanyagban: Hitelesítse a hitelesítést egy webalkalmazásban az Azure Active Directory B2C használatával.](tutorial-web-app-dotnet.md)

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

A webes API-erőforrásokat regisztrálni kell a bérlőben, mielőtt elfogadnák és válaszolnának a hozzáférési jogkivonatot tartalmazó ügyfélalkalmazások védett erőforrás-kéréseire.

Ha regisztrálegy alkalmazást az Azure AD B2C-bérlőben, használhatja az aktuális **alkalmazások** vagy az új egyesített **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
4. Válassza **az Alkalmazások**lehetőséget, majd a **Hozzáadás**lehetőséget.
5. Adjon nevet az alkalmazásnak. Például *webapi1*.
6. A **Webapp/web API belefoglalása csoportban**válassza az **Igen**lehetőséget.
7. A **válasz URL-cím,** adjon meg egy végpontot, ahol az Azure AD B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta `https://localhost:44332`helyileg fut, és a .
8. **Az App ID URI mezőben**adja meg a webes API-hoz használt azonosítót. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson **a Létrehozás gombra.**
10. A tulajdonságok lapon rögzítse a webalkalmazás konfigurálásakor használni kívánt alkalmazásazonosítót.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** Például *webapi1*.
1. Az **ÁTirányítás URI,válassza**ki **a Web**lehetőséget, majd adjon meg egy végpontot, ahol az Azure AD B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta `https://localhost:44332`helyileg fut, és a .
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

* * *

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetőséget nyújtanak a védett erőforrásokhoz való hozzáférés szabályozására. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ebben az oktatóanyagban hatókörök segítségével határozzuk meg az olvasási és írási engedélyeket a webes API számára.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Engedélyek megadása

A védett webes API-t egy alkalmazásból, meg kell adnia az alkalmazás engedélyeket az API-t. Az előfeltételként szolgáló oktatóanyagban létrehozott egy webalkalmazást az Azure AD B2C nevű *webapp1.* Ezzel az alkalmazással hívja meg a webes API-t.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Az alkalmazás regisztrálva van a védett webes API hívásához. A felhasználó hitelesíti magát az Azure AD B2C az alkalmazás használatához. Az alkalmazás beszerzi az Azure AD B2C engedélyezési engedélyt a védett webes API eléréséhez.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy a webes API regisztrálva van, és a hatókörök definiálva, konfigurálja a webes API-t az Azure AD B2C-bérlő használatára. Ebben az oktatóanyagban egy mintául szolgáló webes API-t fog konfigurálni. A minta webes API szerepel a projekt letöltött az előfeltételi oktatóanyag.

Két projekt szerepel a mintául szolgáló megoldásban:

* **TaskWebApp** - Feladatlista létrehozása és szerkesztése. A minta a **regisztrációs vagy bejelentkezési** felhasználói folyamat segítségével regisztrál, vagy jelentkezzen be a felhasználók.
* **TaskService** – Támogatja a feladatlista létrehozásához, olvasásához, frissítéséhez és törléséhez. Az API-t az Azure AD B2C védi, és a TaskWebApp szólítja meg.

### <a name="configure-the-web-application"></a>A webalkalmazás konfigurálása

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
1. A **TaskWebApp** projektben nyissa meg a **Web.config programot.**
1. Az API helyi futtatásához használja az **api:TaskServiceUrl** localhost-beállítást. Az alábbiak szerint módosítsa a Web.config fájlt:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Konfigurálja az API URI-ját. Ez az az URI, amelyet a webalkalmazás az API-kérelem hez használ. A kért engedélyeket is konfigurálja.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. A **TaskService** projektben nyissa meg a **Web.config programot.**
1. Konfigurálja az API-t a bérlő használatához.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Állítsa be az ügyfélazonosítót a regisztrált web *API-alkalmazás,webapi1 alkalmazásazonosítójára.*

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Frissítse a felhasználói folyamat beállítást a regisztrációs és bejelentkezési felhasználói folyamat *nevével, B2C_1_signupsignin1.*

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Konfigurálja úgy a hatókörök et, hogy azok megfeleljenek a portálon létrehozottaknak.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Minta futtatása

A **TaskWebApp** és a **TaskService** projektet is futtatnia kell.

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, és válassza az **Indítási projektek beállítása...** lehetőséget.
1. Válassza **a Több indítási projekt lehetőséget.**
1. Mindkét projektnél módosítsa a **Művelet** értékét **Indításra**.
1. A konfiguráció mentéséhez kattintson az **OK** gombra.
1. Nyomja le az **F5** gombot mindkét alkalmazás futtatásához. Minden alkalmazás megnyílik a saját böngészőablakában.
    * `https://localhost:44316/`a webes alkalmazás.
    * `https://localhost:44332/` a webes API.

1. A webalkalmazásban válassza ki a **regisztráció / bejelentkezés** a webalkalmazásba való bejelentkezéshez. Használja a korábban létrehozott fiókot.
1. Miután bejelentkezett, válassza a **Teendők listát,** és hozzon létre egy feladatlistaelemet.

Amikor létrehoz egy feladatlista elemet, a webalkalmazás kéri a webes API-t a feladatlista elem létrehozásához. A védett webalkalmazás az Azure AD B2C által védett webes API-t hívja meg.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API-hoz
> * A minta konfigurálása az alkalmazás használatára

> [!div class="nextstepaction"]
> [Oktatóanyag: Identitásszolgáltatók hozzáadása az alkalmazásokhoz az Azure Active Directory B2C-ben](tutorial-add-identity-providers.md)
