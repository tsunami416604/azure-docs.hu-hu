---
title: Oktatóanyag – a hitelesítés engedélyezése egy egyoldalas alkalmazásban – Azure Active Directory B2C
description: Ismerje meg, hogyan használhatja az Azure Active Directory B2C egy egyoldalas (JavaScript-) alkalmazás felhasználói bejelentkezésének biztosításához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/08/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 496cf801a44638af61306b43791abce9466e2cb2
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835683"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Oktatóanyag: Az Azure Active Directory B2C egy egyoldalas alkalmazásban hitelesítés engedélyezése

Ez az oktatóanyag bemutatja, hogyan lehet Azure Active Directory (Azure AD) B2C segítségével jelentkezzen be a felhasználók és regisztrálásához egy egyoldalas alkalmazásban (SPA). Az Azure AD B2C lehetővé teszi az alkalmazások hitelesítését közösségi hálózati, vállalati fiókok és az Azure Active Directory-fiókok nyílt szabványú protokollokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Frissítse az alkalmazást az Azure AD B2C-vel
> * A minta az alkalmazás konfigurálása
> * Iratkozzon fel a felhasználói folyamat használata

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a lépéseket folytatása előtt a következő Azure AD B2C-erőforrások helyen lesz szüksége:

* [Az Azure AD B2C-bérlő](tutorial-create-tenant.md)
* [Regisztrált alkalmazás](tutorial-register-applications.md) a bérlőben
* [Felhasználói folyamatok létrehozott](tutorial-create-user-flows.md) a bérlőben

Ezenkívül a következő szüksége a helyi fejlesztési környezetet:

* Szerkesztő, például Code [Visual Studio Code](https://code.visualstudio.com/) vagy [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET core SDK 2.0.0-s](https://www.microsoft.com/net/core) vagy újabb
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Az alkalmazás frissítése

A második oktatóanyagban az Előfeltételek részeként végrehajtott egy webalkalmazás, Azure AD B2C-ben regisztrált. Az oktatóanyagban a minta való kommunikáció biztosításához, hozzá kell átirányítási URI-t az alkalmazáshoz az Azure AD B2C-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
1. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
1. Válassza ki **alkalmazások**, majd válassza ki a *webapp1* alkalmazás.
1. A **válasz URL-cím**, adjon hozzá `http://localhost:6420`.
1. Kattintson a **Mentés** gombra.
1. A Tulajdonságok lapon jegyezze fel a **Alkalmazásazonosító**. Az Alkalmazásazonosító egy későbbi lépésben használja, a egyoldalas webalkalmazást a kód frissítésekor.

## <a name="get-the-sample-code"></a>A mintakód letöltése

Ebben az oktatóanyagban a Githubról letölthető egy kódmintát fog konfigurálni. A minta azt mutatja be, hogyan egy egyoldalas alkalmazás az Azure AD B2C-t a felhasználói regisztráció és a regisztrációs, és hívhat meg egy védett webes API-t.

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>A minta frissítése

Most, hogy beszerezte a mintát, frissítse a kódot az Azure AD B2C bérlő neve és a egy korábbi lépésben rögzített az Alkalmazásazonosítót.

1. Nyissa meg a `index.html` fájlt a mintául szolgáló könyvtár gyökerében.
1. Az a `msalConfig` definíciót, módosítsa a **clientId** az alkalmazás azonosítójával egy korábbi lépésben rögzített érték. Ezután frissítse a **hatóság** az Azure AD B2C-bérlő nevét az URI értéket. Az URI-t is frissítheti a regisztrálási-regisztrálási vagy bejelentkezési felhasználói folyamata az Előfeltételek egyikében létrehozott nevét (például *B2C_1_signupsignin1*).

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

    A felhasználói folyamatot, a jelen oktatóanyagban használt név **B2C_1_signupsignin1**. Ha egy másik folyamat felhasználónevet használ, adja meg ezt a nevet a a `authority` értéket.

## <a name="run-the-sample"></a>Minta futtatása

1. Nyisson meg egy konzolablakot, és módosítsa a mintát tartalmazó könyvtárra. Példa:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Futtassa az alábbi parancsot:

    ```
    npm install && npm update
    node server.js
    ```

    A konzolablakban a helyileg futó Node.js-kiszolgáló port számát jeleníti meg:

    ```
    Listening on port 6420...
    ```

1. Navigáljon a `http://localhost:6420` az alkalmazás megtekintéséhez a böngészőben.

A minta regisztrációs, bejelentkezési, Profilszerkesztési támogatja, és új jelszó kérésére. Ez az oktatóanyag kiemeli, hogyan regisztrál egy felhasználó e-mail-címmel.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Kattintson a **bejelentkezési** az alkalmazás felhasználói regisztráció. Ez a **B2C_1_signupsignin1** az előző lépésben megadott felhasználói folyamatot.
1. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, kattintson a **Regisztráció** hivatkozásra.
1. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A munkafolyamat az a felhasználó jelszavát, és a felhasználói folyamat meghatározott attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    ![A bejelentkezési-a vagy a regisztráláshoz felhasználói folyamat által a regisztrációs oldalra](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-címtárban.

Amikor rákattint **létrehozás**, a bejelentkezési oldal bezárul, és a bejelentkezési oldal ismét megjelenik.

Az e-mail cím és jelszó használatával most jelentkezzen be az alkalmazást.

### <a name="error-insufficient-permissions"></a>Hiba: nem megfelelő engedélyek

Miután bejelentkezik, az alkalmazás nem megfelelő engedélyek hibaüzenetet jelenít meg – ez a **várt**:

`ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.`

Ezt a hibaüzenetet kapja, mert a végrehajtani kívánt erőforrások eléréséhez a bemutató könyvtárból, de ha a hozzáférési jogkivonat csak az Azure AD-címtár esetében érvényes. Az API-hívás ezért nincs engedélyezve.

Folytassa a következő oktatóanyag a sorozat (lásd: [lépések](#next-steps)) hozhat létre egy védett webes API-t a címtárhoz.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Frissítse az alkalmazást az Azure AD B2C-vel
> * A minta az alkalmazás konfigurálása
> * Iratkozzon fel a felhasználói folyamat használata

Most már léphet tovább a sorozat következő oktatóanyaga az SPA egy védett webes API hozzáférést:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az ASP.NET Core webes API-t egy egyoldalas alkalmazásból az Azure Active Directory B2C használatával való hozzáférés engedélyezése](active-directory-b2c-tutorials-spa-webapi.md)
