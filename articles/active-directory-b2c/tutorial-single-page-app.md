---
title: 'Oktatóanyag: Hitelesítés engedélyezése egyoldalas alkalmazásban'
titleSuffix: Azure AD B2C
description: Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Active Directory B2C használatával a javascript-alapú egyoldalas alkalmazások (SPA) felhasználói bejelentkezését.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 435800d9c6bfd9131d50681a9808f9836104fac0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183343"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Oktatóanyag: Hitelesítés engedélyezése egy egyoldalas alkalmazásban az Azure Active Directory B2C (Azure AD B2C) használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Active Directory B2C (Azure AD B2C) segítségével a bejelentkezést, és a felhasználók regisztrálása egy egyoldalas alkalmazásban (SPA). Az Azure AD B2C lehetővé teszi, hogy az alkalmazások hitelesítése a közösségi fiókok, vállalati fiókok és az Azure Active Directory-fiókok nyílt szabványos protokollok használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás frissítése az Azure AD B2C-ben
> * A minta konfigurálása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépései folytatása előtt a következő Azure AD B2C-erőforrásokra van szükség:

* [Azure AD B2C-bérlő](tutorial-create-tenant.md)
* [A](tutorial-register-applications.md) bérlőben regisztrált alkalmazás
* [A](tutorial-create-user-flows.md) bérlőben létrehozott felhasználói folyamatok

Ezenkívül a helyi fejlesztési környezetben a következőkre van szükség:

* Kódszerkesztő, például [Visual Studio-kód](https://code.visualstudio.com/) vagy [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) vagy újabb
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Az alkalmazás frissítése

A második oktatóanyag, amely az előfeltételek részeként befejeződött, regisztrált egy webalkalmazást az Azure AD B2C-ben. Az oktatóanyagban a mintával való kommunikáció engedélyezéséhez hozzá kell adnia egy átirányítási URI-t az alkalmazáshoz az Azure AD B2C-ben.

Használhatja az aktuális **alkalmazások** tapasztalat, vagy az új, egységes **alkalmazás regisztrációk (Előzetes verzió)** az alkalmazás frissítéséhez. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az Azure **AD B2C**lehetőséget.
1. Válassza **az Alkalmazások**lehetőséget, majd válassza ki a *webapp1* alkalmazást.
1. A **Válasz URL-cím csoportban**adja hozzá a t. `http://localhost:6420`
1. Kattintson a **Mentés** gombra.
1. A tulajdonságok lapon rögzítse az **alkalmazásazonosítót**. Az alkalmazásazonosítót egy későbbi lépésben használhatja, amikor frissíti a kódot az egyoldalas webalkalmazásban.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, válassza a **Saját alkalmazások** lapot, majd a *webapp1* alkalmazást.
1. Válassza **a Hitelesítés**lehetőséget, majd válassza az Új felület **kipróbálása** lehetőséget (ha látható).
1. A **Web**csoportban jelölje be `http://localhost:6420`az URI **hozzáadása** hivatkozást, írja be a beadási parancsot, majd kattintson a Mentés **gombra.**
1. Válassza az **Áttekintés** lehetőséget.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben, amikor frissíti a kódot az egyoldalas webalkalmazásban.

* * *

## <a name="get-the-sample-code"></a>A mintakód letöltése

Ebben az oktatóanyagban konfigurálja a GitHubról letöltött kódmintát. A minta bemutatja, hogy egy egyoldalas alkalmazás hogyan használhatja az Azure AD B2C felhasználói regisztrációés bejelentkezés, és hívja meg a védett webes API-t.

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>A minta frissítése

Most, hogy megszerezte a mintát, frissítse a kódot az Azure AD B2C bérlői nevével és az alkalmazásazonosítóval, amelyet egy korábbi lépésben rögzített.

1. Nyissa `index.html` meg a fájlt a mintakönyvtár gyökerében.
1. A `msalConfig` definícióban módosítsa az **ügyfélazonosító** értékét a korábbi lépésben rögzített alkalmazásazonosítóval. Ezután frissítse a **hatóság** URI-értékét az Azure AD B2C bérlői névvel. Frissítse az URI-t az egyik előfeltételben létrehozott regisztrációs/bejelentkezési felhasználói folyamat nevével (például *B2C_1_signupsignin1).*

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Az oktatóanyagban használt felhasználói folyamat neve **B2C_1_signupsignin1**. Ha más felhasználói folyamatnevet használ, adja meg `authority` ezt a nevet az értékben.

## <a name="run-the-sample"></a>Minta futtatása

1. Nyisson meg egy konzolablakot, és váltson a mintát tartalmazó könyvtárra. Példa:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Futtassa az alábbi parancsot:

    ```
    npm install && npm update
    node server.js
    ```

    A konzolablak a helyileg futó Node.js kiszolgáló portszámát jeleníti meg:

    ```
    Listening on port 6420...
    ```

1. Lépjen `http://localhost:6420` a böngészőben az alkalmazás megtekintéséhez.

A minta támogatja a regisztrációt, a bejelentkezést, a profilszerkesztést és a jelszó-visszaállítást. Ez az oktatóanyag kiemeli, hogy a felhasználó hogyan regisztrál egy e-mail cím használatával.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

> [!WARNING]
> A regisztráció vagy a bejelentkezés után [elégtelen engedélyhiba](#error-insufficient-permissions)jelenhet meg. A kódminta aktuális megvalósítása miatt ez a hiba várható. Ez a probléma a kódminta egy későbbi verziójában oldódik meg, ekkor ez a figyelmeztetés el lesz távolítva.

1. A Bejelentkezés gombra a B2C_1_signupsignin1 korábbi lépésben megadott *felhasználói* folyamat elindításához válassza a **Bejelentkezés** lehetőséget.
1. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, válassza a **Regisztráció most** hivatkozást.
1. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési/regisztrációs felhasználói folyamat által bemutatott regisztrációs oldal](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Válassza **a Create lehetőséget,** ha helyi fiókot szeretne létrehozni az Azure AD B2C címtárban.

Ha a Létrehozás lehetőséget **választja,** a regisztrációs oldal bezárul, és a bejelentkezési lap újra megjelenik.

Most már használhatja az e-mail címét és jelszavát, hogy jelentkezzen be az alkalmazásba.

### <a name="error-insufficient-permissions"></a>Hiba: elégtelen engedélyek

A bejelentkezés után az alkalmazás elégtelen engedélyhibát adhat vissza:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Ez a hibaüzenet azért jelenik meg, mert a webalkalmazás a *fabrikamb2c*bemutatókönyvtár által védett webes API-hoz próbál hozzáférni. Mivel a hozzáférési jogkivonat csak az Azure AD-címtárban érvényes, az API-hívás nem engedélyezett.

A hiba kijavításához folytassa a sorozat következő oktatóanyagával (lásd: [Következő lépések)](#next-steps)a védett webes API létrehozásához a címtárhoz.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Az alkalmazás frissítése az Azure AD B2C-ben
> * A minta konfigurálása az alkalmazás használatára
> * Regisztráció a felhasználói folyamat használatával

Most lépni a következő bemutató a sorozatban, hogy hozzáférést biztosítson a védett webes API-t a SPA:

> [!div class="nextstepaction"]
> [Oktatóanyag: Hozzáférés a ASP.NET Core webes API-hoz egy SPA-ból az Azure AD B2C >](tutorial-single-page-app-webapi.md)
