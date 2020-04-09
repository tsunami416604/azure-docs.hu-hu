---
title: 'Oktatóanyag: Hitelesítés engedélyezése egyoldalas alkalmazásban'
titleSuffix: Azure AD B2C
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Active Directory B2C használatával a javascript-alapú egyoldalas alkalmazások (SPA) felhasználói bejelentkezését.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d7cd437f597fc34fe83904715fc2e459dfe4550f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875559"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Oktatóanyag: Hitelesítés engedélyezése egy egyoldalas alkalmazásban az Azure AD B2C-vel

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Active Directory B2C (Azure AD B2C) segítségével a felhasználók regisztrálása és bejelentkezése egy egyoldalas alkalmazásban (SPA).

Ebben az oktatóanyagban az első egy kétrészes sorozatban:

> [!div class="checklist"]
> * Válasz URL-cím hozzáadása az Azure AD B2C-bérlőben regisztrált alkalmazáshoz
> * Kódminta letöltése a GitHubról
> * A mintaalkalmazás kódjának módosítása a bérlővel való együttműködésre
> * Regisztráció a feliratkozási/bejelentkezési felhasználói folyamattal

A [sorozat következő oktatóanyaga](tutorial-single-page-app-webapi.md) engedélyezi a kódminta webes API-részét.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépései folytatása előtt a következő Azure AD B2C-erőforrásokra van szükség:

* [Azure AD B2C-bérlő](tutorial-create-tenant.md)
* [A](tutorial-register-applications.md) bérlőben regisztrált alkalmazás
* [A](tutorial-create-user-flows.md) bérlőben létrehozott felhasználói folyamatok

Ezenkívül a helyi fejlesztési környezetben a következőkre van szükség:

* [Visual Studio-kód](https://code.visualstudio.com/) vagy más kódszerkesztő
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Az alkalmazás frissítése

A második oktatóanyag, amely az előfeltételek részeként befejeződött, regisztrált egy webalkalmazást az Azure AD B2C-ben. Ha engedélyezni szeretné a kommunikációt az oktatóanyagban lévő kódmintával, adjon hozzá egy válasz URL-címet (más néven átirányítási URI-t) az alkalmazás regisztrációhoz.

Használhatja az aktuális **alkalmazások** tapasztalat, vagy az új, egységes **alkalmazás regisztrációk (Előzetes verzió)** az alkalmazás frissítéséhez. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az Azure **AD B2C**lehetőséget.
1. Válassza **az Alkalmazások**lehetőséget, majd válassza ki a *webapp1* alkalmazást.
1. A **Válasz URL-cím csoportban**adja hozzá a t. `http://localhost:6420`
1. Kattintson a **Mentés** gombra.
1. A tulajdonságok lapon rögzítse az **alkalmazásazonosítót**. Az alkalmazásazonosítót egy későbbi lépésben használhatja, amikor frissíti a kódot az egyoldalas webalkalmazásban.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, válassza a **Saját alkalmazások** lapot, majd a *webapp1* alkalmazást.
1. Válassza **a Hitelesítés**lehetőséget, majd válassza az Új felület **kipróbálása** lehetőséget (ha látható).
1. A **Web**csoportban jelölje be `http://localhost:6420`az URI **hozzáadása** hivatkozást, írja be a beadási parancsot, majd kattintson a Mentés **gombra.**
1. Válassza az **Áttekintés** lehetőséget.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben, amikor frissíti a kódot az egyoldalas webalkalmazásban.

* * *

## <a name="get-the-sample-code"></a>A mintakód letöltése

Ebben az oktatóanyagban konfigurálja a GitHubról a B2C-bérlővel való együttműködésre letöltött kódmintát. A minta bemutatja, hogy egy egyoldalas alkalmazás használhatja az Azure AD B2C a felhasználói regisztráció és a bejelentkezés, és hívja meg a védett webes API-t (engedélyezi a webes API-t a következő oktatóanyag a sorozatban).

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>A minta frissítése

Most, hogy megszerezte a mintát, frissítse a kódot az Azure AD B2C bérlői nevével és az alkalmazásazonosítóval, amelyet egy korábbi lépésben rögzített.

1. Nyissa meg az *authConfig.js* fájlt a *JavaScriptSPA* mappában.
1. Az `msalConfig` objektumban frissítse:
    * `clientId`egy korábbi lépésben rögzített **alkalmazásazonosítóval (ügyfélazonosítóval)**
    * `authority`URI az Azure AD B2C bérlő nevével és az előfeltételek részeként létrehozott regisztrációs/bejelentkezési felhasználói folyamat nevével (például *B2C_1_signupsignin1)*

    ```javascript
    const msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
            authority: "https://your-b2c-tenant.b2clogin.com/your-b2c-tenant.onmicrosoft.com/B2C_1_signupsignin1", // Update with your tenant and user flow names
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

## <a name="run-the-sample"></a>Minta futtatása

1. Nyisson meg egy konzolablakot, és váltson a mintát tartalmazó könyvtárra. Például:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Futtassa az alábbi parancsot:

    ```console
    npm install && npm update
    npm start
    ```

    A konzolablak a helyileg futó Node.js kiszolgáló portszámát jeleníti meg:

    ```console
    Listening on port 6420...
    ```
1. Tallózással tekintse `http://localhost:6420` meg a helyi számítógépen futó webalkalmazást.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="A helyileg futó egyoldalas alkalmazást megjelenítő webböngésző":::

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

Ez a mintaalkalmazás támogatja a regisztrációt, a bejelentkezést és a jelszó alaphelyzetbe állítását. Ebben az oktatóanyagban e-mail-cím használatával regisztrál.

1. Válassza **a Bejelentkezés** lehetőséget a *B2C_1_signupsignin1* egy korábbi lépésben megadott felhasználói folyamat elindításához.
1. Az Azure AD B2C egy bejelentkezési lapot jelenít meg, amely egy regisztrációs hivatkozást tartalmaz. Mivel még nincs fiókja, válassza a **Regisztráció most** hivatkozást.
1. A regisztrációs munkafolyamat egy oldalt jelenít meg a felhasználó személyazonosságának e-mail-cím használatával történő összegyűjtéséhez és ellenőrzéséhez. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Az Azure AD B2C felhasználói folyamatáltal megjelenített regisztrációs oldal":::

1. Válassza **a Create lehetőséget,** ha helyi fiókot szeretne létrehozni az Azure AD B2C címtárban.

Ha a Létrehozás lehetőséget **választja,** az alkalmazás megjeleníti a bejelentkezett felhasználó nevét.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Egyoldalas alkalmazást megjelenítő webböngésző bejelentkezett felhasználóval":::

Ha tesztelni szeretné a bejelentkezést, válassza a **Kijelentkezés** gombot, majd válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be a regisztrációkor megadott e-mail címmel és jelszóval.

### <a name="what-about-calling-the-api"></a>Mi a helyzet az API hívásával?

Ha a bejelentkezés után a **Hívás API-gomb,** megjelenik a regisztrációs/bejelentkezési felhasználói folyamat lap az API-hívás eredményei helyett. Ez várható, mert még nem konfigurálta az alkalmazás API-részét az *Azure* AD B2C-bérlőben regisztrált webAPI-alkalmazással való kommunikációra.

Ezen a ponton az alkalmazás továbbra is próbál kommunikálni a demo bérlőben regisztrált API-val (fabrikamb2c.onmicrosoft.com), és mivel nincs hitelesítve a bérlővel, a regisztrációs/bejelentkezési lap jelenik meg.

Lépjen tovább a sorozat következő oktatóanyagára a védett API engedélyezéséhez (lásd a [Következő lépések szakaszt).](#next-steps)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy egyoldalas alkalmazást konfigurált, hogy az Azure AD B2C-bérlőben egy felhasználói folyamattal működjön együtt, hogy regisztrációt és bejelentkezési képességet biztosítson. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Válasz URL-címe az Azure AD B2C-bérlőben regisztrált alkalmazáshoz
> * Kódminta letöltése a GitHubról
> * Módosította a mintaalkalmazás kódját, hogy működjön együtt a bérlővel
> * Regisztráció a feliratkozási/bejelentkezési felhasználói folyamattal

Most lépni a következő bemutató a sorozatban, hogy hozzáférést biztosítson a védett webes API-t a SPA:

> [!div class="nextstepaction"]
> [Oktatóanyag: A webes API védelme és hozzáférés megadása egyoldalas alkalmazásból >](tutorial-single-page-app-webapi.md)
