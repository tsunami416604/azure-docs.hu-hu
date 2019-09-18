---
title: Oktatóanyag – hozzáférés biztosítása egy ASP.NET webes API-hoz – Azure Active Directory B2C | Microsoft Docs
description: Útmutató a ASP.NET webes API-k és a ASP.NET-webalkalmazások általi meghívásához a Active Directory B2C használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ec6b667dfc554c037d9b0a56e52bc8f212812812
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064721"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Oktatóanyag: Hozzáférés biztosítása egy ASP.NET webes API-hoz a Azure Active Directory B2C használatával

Ez az oktatóanyag bemutatja, hogyan hívhat meg egy védett webes API-erőforrást Azure Active Directory B2C (Azure AD B2C) egy ASP.NET-webalkalmazásból.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a lépéseket és az [előfeltételeket az oktatóanyagban: Engedélyezze a hitelesítést egy webalkalmazásban Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md)használatával.

## <a name="add-a-web-api-application"></a>Webes API-alkalmazás hozzáadása

A webes API-erőforrásokat regisztrálni kell a bérlőn, mielőtt azok elfogadják és reagálni tudják a védett erőforrásokra vonatkozó kéréseket olyan ügyfélalkalmazások számára, amelyek hozzáférési jogkivonatot mutatnak be.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
5. Adja meg az alkalmazás nevét. Például: *webapi1*.
6. A **Web App/web API** belefoglalása és az **implicit folyamat engedélyezése**lehetőségnél válassza az **Igen**lehetőséget.
7. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `https://localhost:44332`következőt:.
8. Az **alkalmazás-azonosító URI-ja**mezőben adja meg a webes API-hoz használt azonosítót. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel az alkalmazás AZONOSÍTÓját, amelyet a webalkalmazás konfigurálásakor használni fog.

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetővé teszik a védett erőforrásokhoz való hozzáférés szabályozását. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ebben az oktatóanyagban hatókörök segítségével határozzuk meg az olvasási és írási engedélyeket a webes API számára.

1. Válassza az **alkalmazások**, majd a *webapi1*lehetőséget.
2. Válassza a **közzétett hatókörök**elemet.
3. A **hatókör**, `Hello.Read`a és a Leírás mezőbe írja be `Read access to hello`a következőt:.
4. A **hatókör**, `Hello.Write`a és a Leírás mezőbe írja be `Write access to hello`a következőt:.
5. Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök segítségével ügyfélalkalmazások számára engedélyezhető a webes API-hoz való hozzáférés.

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy alkalmazásból, meg kell adnia az alkalmazás engedélyeit az API-nak. Az előfeltételként szolgáló oktatóanyagban létrehozott egy webalkalmazást a *webapp1*nevű Azure ad B2Cban. Ezt az alkalmazást használhatja a webes API meghívásához.

1. Válassza az **alkalmazások**, majd a *webapp1*lehetőséget.
2. Válassza az **API-hozzáférés**lehetőséget, majd kattintson a **Hozzáadás**gombra.
3. Az **API kiválasztása** legördülő menüben válassza a *webapi1*lehetőséget.
4. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált **Hello. Read** és **Hello. Write** hatóköröket.
5. Kattintson az **OK** gombra.

Az alkalmazás regisztrálva van a védett webes API meghívásához. A felhasználók a Azure AD B2C használatával hitelesítik az alkalmazást. Az alkalmazás a védett webes API-hoz való hozzáféréshez Azure AD B2C engedélyezési engedélyt kap.

## <a name="configure-the-sample"></a>A minta konfigurálása

Most, hogy regisztrálta a webes API-t, és hatókörök vannak definiálva, a webes API-t a Azure AD B2C bérlő használatára konfigurálja. Ebben az oktatóanyagban egy mintául szolgáló webes API-t fog konfigurálni. A minta webes API az előfeltételként megadott oktatóanyagban letöltött projekt részét képezi.

Két projekt szerepel a mintául szolgáló megoldásban:

A következő két projekt szerepel a minta megoldásban:

- **TaskWebApp** – Feladatlista létrehozása és szerkesztése. A minta a **regisztrációs vagy bejelentkezési** felhasználói folyamat használatával regisztrálja vagy bejelentkezik a felhasználók számára.
- **TaskService** – támogatja a Feladatlista létrehozását, olvasását, frissítését és törlését. Az API-t a Azure AD B2C és a TaskWebApp által védettnek nevezzük.

### <a name="configure-the-web-application"></a>A webalkalmazás konfigurálása

1. Nyissa meg a **B2C-WebAPI-DotNet** megoldást a Visual Studióban.
2. Nyissa meg a **Web.config** elemet a **TaskWebApp** projektben.
3. Az API helyi futtatásához használja az **api:TaskServiceUrl** localhost-beállítást. Az alábbiak szerint módosítsa a Web.config fájlt:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Konfigurálja az API URI-ját. Ez az az URI, amelyet a webalkalmazás az API-kérelem elvégzéséhez használ. A kért engedélyeket is konfigurálja.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>A webes API konfigurálása

1. Nyissa meg a **Web.config** elemet a **TaskService** projektben.
2. Konfigurálja az API-t a bérlő használatához.

    ```csharp
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Állítsa be az ügyfél-azonosítót az API regisztrált alkalmazásazonosítójára.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Frissítse a felhasználói folyamat beállítását a regisztrációs és bejelentkezési felhasználói folyamat nevével.

    ```csharp
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Konfigurálja a hatókör beállításait, hogy egyezzenek a portálon korábban létrehozottakkal.

    ```csharp
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Minta futtatása

A **TaskWebApp** és a **TaskService** projektet is futtatnia kell.

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, és válassza az **Indítási projektek beállítása...** lehetőséget.
2. **Több indítási projektet**is kijelölhet.
3. Mindkét projektnél módosítsa a **Művelet** értékét **Indításra**.
4. A konfiguráció mentéséhez kattintson **az OK** gombra.
5. Nyomja le az **F5** gombot mindkét alkalmazás futtatásához. Minden alkalmazás a saját böngésző lapján nyílik meg. `https://localhost:44316/` a webalkalmazás.
    `https://localhost:44332/` a webes API.

6. A webalkalmazásban kattintson a **regisztráció/bejelentkezés** lehetőségre a webalkalmazásba való bejelentkezéshez. Használja a korábban létrehozott fiókot.
7. A bejelentkezést követően kattintson **a Feladatlista** elemre, és hozzon létre feladatlistát.

Ha feladatlistát hoz létre, a webalkalmazás egy kérést küld a webes API-nak a Feladatlista elem létrehozásához. A védett webalkalmazás meghívja a védett webes API-t a Azure AD B2C-bérlőben.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webes API-alkalmazás hozzáadása
> * Hatókörök konfigurálása webes API-hoz
> * Engedélyek megadása a webes API számára
> * A minta beállítása az alkalmazás használatára

> [!div class="nextstepaction"]
> [Oktatóanyag: Identitás-szolgáltatók hozzáadása az alkalmazásokhoz Azure Active Directory B2C](tutorial-add-identity-providers.md)
