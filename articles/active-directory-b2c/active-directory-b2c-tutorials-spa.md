---
title: Oktatóanyag – a hitelesítés engedélyezése egy egyoldalas alkalmazásban – Azure Active Directory B2C |} A Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t egy egyoldalas (JavaScript-) alkalmazás felhasználói bejelentkezésének biztosításához.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 813c7131ff0a56e843e728cd78fff969b1d90fcc
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756325"
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

* [Felhasználói folyamatok létrehozása](tutorial-create-user-flows.md) ahhoz, hogy az alkalmazás felhasználói élményt. 
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) telepítése **ASP.NET és webfejlesztési** számítási feladattal.
* Telepítse a [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) vagy újabb
* [Node.js](https://nodejs.org/en/download/) telepítése

## <a name="update-the-application"></a>Az alkalmazás frissítése

Az oktatóanyagban az Előfeltételek részeként végrehajtott egy webalkalmazás, Azure AD B2C-ben hozzáadott. Az oktatóanyagban a minta való kommunikáció biztosításához, hozzá kell átirányítási URI-t az alkalmazáshoz az Azure AD B2C-ben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki a *webapp1* alkalmazás.
5. A **válasz URL-cím**, adjon hozzá `http://localhost:6420`.
6. Kattintson a **Mentés** gombra.
7. A Tulajdonságok lapon jegyezze fel az Alkalmazásazonosítót, amelyek a webalkalmazás konfigurálásakor fogja használni.
8. Válassza ki **kulcsok**válassza **kulcs létrehozása**, és válassza ki **mentése**. Jegyezze fel a kulcs, amelyet a webalkalmazás konfigurálásakor fogja használni.

## <a name="configure-the-sample"></a>A minta konfigurálásához

Ebben az oktatóanyagban a minta azt a githubról letölthető fog konfigurálni. A minta azt mutatja be, hogyan egy egyoldalas alkalmazás Azure AD B2C használata felhasználói regisztrációs, bejelentkezési, és védett webes API-hívás.

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), vagy a klónozza a GitHubon található mintát.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

A beállítások módosítása:

1. Nyissa meg a `index.html` a mintában lévő fájlt.
2. Konfigurálja a mintát a alkalmazás Azonosítóját és kulcsát, korábban feljegyzett. A következő kódsorokat módosítsa az értékeket cserélje le a címtár és az API-k nevei:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    A felhasználói folyamatot, a jelen oktatóanyagban használt név **B2C_1_signupsignin1**. Ha egy másik folyamat felhasználónevet használ, használja a felhasználói interakciósorozat neve a `authority` értéket.

## <a name="run-the-sample"></a>Minta futtatása

1. Indítsa el a Node.js parancssort.
2. Váltson arra a könyvtárra, amelyben a Node.js-minta található. Például: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Futtassa az alábbi parancsot:

    ```
    npm install && npm update
    node server.js
    ```

    A konzolablakban megjelenik annak a portnak a száma, amelynél az alkalmazás található.
    
    ```
    Listening on port 6420...
    ```

4. Keresse meg a címet a böngésző segítségével `http://localhost:6420` az alkalmazás megtekintéséhez.

A minta regisztrációs, bejelentkezési, Profilszerkesztési támogatja, és új jelszó kérésére. Ez az oktatóanyag kiemeli, hogyan regisztrál egy felhasználó e-mail-címmel.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Kattintson a **bejelentkezési** az alkalmazás felhasználói regisztráció. Ez a **B2C_1_signupsignin1** az előző lépésben meghatározott felhasználói folyamatot.
2. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, kattintson a **Regisztráció** hivatkozásra. 
3. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A munkafolyamat az a felhasználó jelszavát, és a felhasználói folyamat meghatározott attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket. 

    ![Regisztrációs munkafolyamat](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-címtárban.

A felhasználó, e-mail-címmel jelentkezik be, és használja az SPA-alkalmazás használhatja.

> [!NOTE]
> Bejelentkezés után az alkalmazás egy „nem megfelelő engedélyek” hibaüzenetet jelenít meg. Ezt a hibaüzenetet azért kapja, mert a bemutató címtár egyik erőforrásához próbál meg hozzáférni. Mivel a hozzáférési jogkivonat csak az Ön Azure AD-címtárához érvényes, az API-hívás nincs engedélyezve. Lépjen tovább a következő oktatóanyagra, amelyben egy védett webes API-t hozhat létre a címtár számára.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Frissítse az alkalmazást az Azure AD B2C-vel
> * A minta az alkalmazás konfigurálása
> * Iratkozzon fel a felhasználói folyamat használata

> [!div class="nextstepaction"]
> [Oktatóanyag: Az ASP.NET Core webes API-t egy egyoldalas alkalmazásból az Azure Active Directory B2C használatával való hozzáférés engedélyezése](active-directory-b2c-tutorials-spa-webapi.md)
