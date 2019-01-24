---
title: Oktatóanyag – Egyoldalas alkalmazáson belüli, fiókokkal történő hitelesítés engedélyezése az Azure Active Directory B2C használatával | Microsoft Docs
description: Arra vonatkozó útmutató, hogyan használhatja az Azure Active Directory B2C-t egy egyoldalas (JavaScript-) alkalmazás felhasználói bejelentkezésének biztosításához.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 08372700e9740ca69ba73dfba49f9d120dfabc6d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850331"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Oktatóanyag: Az Azure Active Directory B2C fiókkal rendelkező egyoldalas alkalmazás hitelesítés engedélyezése

Az oktatóanyag azt mutatja be, hogyan használhatja az Azure Active Directory (Azure AD) B2C-t felhasználók bejelentkeztetéséhez és regisztrálásához egy egyoldalas alkalmazásban (single-page application, SPA). Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati, vállalati és Azure Active Directory-fiókokon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy mintául szolgáló egyoldalas alkalmazás regisztrálása az Azure AD B2C-címtárban.
> * Hozzon létre felhasználói folyamatokat a felhasználói regisztrációs, bejelentkezési, profil szerkesztésére és új jelszó kérésére.
> * A mintaalkalmazás konfigurálása az Azure AD B2C-címtár használatára.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Saját [Azure AD B2C-címtár](active-directory-b2c-get-started.md) létrehozása
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) telepítése **ASP.NET és webfejlesztési** számítási feladattal.
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) vagy újabb
* [Node.js](https://nodejs.org/en/download/) telepítése

## <a name="register-single-page-app"></a>Az egyoldalas alkalmazás regisztrálása

Az alkalmazásoknak [regisztrálva](../active-directory/develop/developer-glossary.md#application-registration) kell lenniük a címtárban, hogy [hozzáférési jogkivonatokat](../active-directory/develop/developer-glossary.md#access-token) kaphassanak az Azure Active Directoryból. Az alkalmazásregisztráció létrehozza a címtárban található alkalmazás [alkalmazásazonosítóját](../active-directory/develop/developer-glossary.md#application-id-client-id). 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-címtár globális rendszergazdájaként.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Az Azure Portalon válassza az **Azure AD B2C** lehetőséget a szolgáltatások listájából. 

2. A B2C beállításaiban kattintson az **Alkalmazások**, majd a **Hozzáadás** lehetőségre. 

    A mintául szolgáló webalkalmazás címtárban történő regisztrálásához használja a következő beállításokat:
    
    ![Új alkalmazás hozzáadása](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | Mintául szolgáló saját egyoldalas alkalmazás | Adjon meg egy olyan **nevet**, amely megfelelően körülírja az alkalmazást a felhasználók számára. | 
    | **Webalkalmazás vagy webes API szerepeltetése** | Igen | Egyoldalas alkalmazás esetében válassza az **Igen** lehetőséget. |
    | **Implicit folyamat engedélyezése** | Igen | Válassza az **Igen** lehetőséget, mivel az alkalmazás [OpenID Connect bejelentkezést](active-directory-b2c-reference-oidc.md) használ. |
    | **Válasz URL-cím** | `http://localhost:6420` | A válasz URL-címek olyan végpontok, amelyeken keresztül az Azure AD B2C visszaadja az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut (localhost), és a 6420-as porton figyel. |
    | **Natív ügyfél belefoglalása** | Nem | Mivel ez egy egyoldalas alkalmazás, nem pedig egy natív ügyfél, válassza a Nem lehetőséget. |
    
3. Kattintson a **Létrehozás** gombra az alkalmazás regisztrálásához.

A regisztrált alkalmazások az Azure AD B2C-címtár alkalmazásainak listájában jelennek meg. Válassza ki az egyoldalas alkalmazást a listáról. Megjelenik a regisztrált egyoldalas alkalmazás tulajdonságpanelje.

![Az egyoldalas alkalmazás tulajdonságai](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Jegyezze fel az **alkalmazás ügyfél-azonosítóját**. Az azonosító egyedi módon azonosítja az alkalmazást, és az oktatóanyag későbbi részében, az alkalmazás konfigurálásakor lesz rá szükség.

## <a name="create-user-flows"></a>Felhasználói folyamatok létrehozása

Az Azure AD B2C felhasználói folyamat a felhasználói élmény identitás tevékenység határozza meg. Bejelentkezés, regisztráció, a jelszó módosítása és a profilok szerkesztése például olyan közös felhasználókövetési adatai.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Regisztrálási vagy bejelentkezési felhasználói folyamat létrehozása

Jelentkezzen be a webalkalmazás eléréséhez szükséges felhasználói regisztrációhoz, hozzon létre egy **regisztrálási vagy bejelentkezési felhasználói folyamata**.

1. Az Azure AD B2C portáloldalán válassza **felhasználói folyamatok** kattintson **új felhasználói folyamat**.
2. Az a **ajánlott** lapra, majd **jelentkezzen be, és jelentkezzen be a**.

    A felhasználói folyamat konfigurálásához használja a következő beállításokat:

    ![Regisztrálási vagy bejelentkezési felhasználói folyamat hozzáadása](media/active-directory-b2c-tutorials-spa/add-susi-user-flow.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SiUpIn | Adjon meg egy **neve** a felhasználói folyamat. A felhasználói interakciósorozat neve a következő előtaggal kezdődik **B2C_1_**. A flow felhasználónév használata **B2C_1_SiUpIn** a mintakód. | 
    | **Identitásszolgáltatók** | Regisztráció e-mail-címmel | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |

3. A **felhasználói attribútumokról és jogcímekről**, kattintson a **Továbbiak megjelenítése** , és válassza ki a következő beállításokat:

    ![Regisztrálási vagy bejelentkezési felhasználói folyamat hozzáadása](media/active-directory-b2c-tutorials-spa/add-attributes-and-claims.png)

    | Oszlop      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Attribútum gyűjtése** | Megjelenítendő név és postai irányítószám | Válassza ki a regisztráció során a felhasználótól összegyűjtendő attribútumokat. |
    | **Vissza a jogcímet** | Megjelenítendő név, postai irányítószám, új felhasználó, a felhasználó objektumazonosítója | Válassza ki a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token) szerepeltetni kívánt [jogcímeket](../active-directory/develop/developer-glossary.md#claim). |

4. Kattintson az **OK** gombra.
5. Kattintson a **létrehozás** a felhasználói folyamat létrehozásához. 

### <a name="create-a-profile-editing-user-flow"></a>Profilszerkesztési felhasználói folyamata létrehozása

Ahhoz, hogy a hogy a felhasználók visszaállíthassák a saját felhasználói profil adatait saját, hozzon létre egy **Profilszerkesztési felhasználói folyamata**.

1. Az Azure AD B2C portáloldalán válassza **felhasználói folyamatok** kattintson **új felhasználói folyamat**.
2. Az a **ajánlott** lapra, majd **profilszerkesztést**.

    A felhasználói folyamat konfigurálásához használja a következő beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SiPe | Adjon meg egy **neve** a felhasználói folyamat. A felhasználói interakciósorozat neve a következő előtaggal kezdődik **B2C_1_**. A flow felhasználónév használata **B2C_1_SiPe** a mintakód. | 
    | **Identitásszolgáltatók** | Bejelentkezés helyi fiókba | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |

3.  A **felhasználói attribútumok**, kattintson a **Továbbiak megjelenítése** , és válassza ki a következő beállításokat:

    | Oszlop      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Attribútum gyűjtése** | Megjelenítendő név és postai irányítószám | Válassza ki azokat az attribútumokat, amelyeket a felhasználók módosíthatnak a profilszerkesztés során. |
    | **Vissza a jogcímet** | Megjelenítendő név, postai irányítószám, a felhasználó objektumazonosítója | Válassza ki azokat a [jogcímeket](../active-directory/develop/developer-glossary.md#claim), amelyeket egy sikeres profilszerkesztés után szerepeltetni szeretne a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token). |

4. Kattintson az **OK** gombra.
5. Kattintson a **létrehozás** a felhasználói folyamat létrehozásához. 

### <a name="create-a-password-reset-user-flow"></a>Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

Engedélyezze az új jelszó kérését az alkalmazásban, meg kell hoznia egy **jelszó-visszaállítási felhasználói folyamat**. A felhasználói folyamat során a jelszó-változtatási és -jogkivonatokat, amelyeket az alkalmazás fogad a tartalmát fogyasztói élményt ismerteti a művelet sikeres elvégzésekor.

1. Az Azure AD B2C portáloldalán válassza **felhasználói folyamatok** kattintson **új felhasználói folyamat**.
2. Az a **ajánlott** lapra, majd **új jelszó kérésére vonatkozó**.

    A felhasználói folyamat konfigurálásához használja a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Name (Név)** | SSPR | Adjon meg egy **neve** a felhasználói folyamat. A felhasználói interakciósorozat neve a következő előtaggal kezdődik **B2C_1_**. A flow felhasználónév használata **B2C_1_SSPR** a mintakód. | 
    | **Identitásszolgáltatók** | Új jelszó kérése e-mail-cím használatával | Az identitásszolgáltató alapján lehet kétséget kizáróan azonosítani a felhasználót. |

3. A **alkalmazásjogcímek**, kattintson a **Továbbiak megjelenítése** , és válassza ki az alábbi beállítást:

    | Oszlop      | Ajánlott érték  | Leírás                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Vissza a jogcímet** | A felhasználó objektumazonosítója | Válassza ki azokat a [jogcímeket](../active-directory/develop/developer-glossary.md#claim), amelyeket egy új jelszó sikeres kérése után szerepeltetni kíván a [hozzáférési jogkivonatban](../active-directory/develop/developer-glossary.md#access-token). |

4. Kattintson az **OK** gombra.
5. Kattintson a **létrehozás** a felhasználói folyamat létrehozásához. 

## <a name="update-single-page-app-code"></a>Az egyoldalas alkalmazás kódjának frissítése

Most, hogy regisztrálta az alkalmazást és a felhasználói folyamatok létrehozása, konfigurálása, az alkalmazások Azure AD B2C-címtárát a kell. Ebben az oktatóanyagban egy, a GitHubról letölthető SPA JavaScript-mintaalkalmazást fog konfigurálni. 

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
A mintaalkalmazás bemutatja, hogyan használhatja egy egyoldalas alkalmazás az Azure AD B2C-t felhasználók regisztrációjához és bejelentkeztetéséhez, illetve egy védett webes API meghívásához. Az alkalmazás használja az alkalmazás regisztrációját a címtárban, és konfigurálja a létrehozott felhasználói folyamatok módosítani szeretné. 

Az alkalmazás beállításainak módosításához:

1. Nyissa meg az `index.html` fájlt az egyoldalas Node.js-mintaalkalmazásban.
2. Konfigurálja a mintát az Azure AD B2C-címtár regisztrációs információival. Módosítsa a következő kódsorokat (ne felejtse el az értékeket lecserélni a címtár és az API-k nevére):

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_<Sign-up or sign-in policy name>",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    A felhasználói folyamat neve, a jelen oktatóanyagban használt **B2C_1_SiUpIn**. Ha egy másik folyamat felhasználónevet használ, használja a felhasználói interakciósorozat neve a `authority` értéket.

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

4. Egy böngészőben lépjen a `http://localhost:6420` címre az alkalmazás megtekintéséhez.

A mintaalkalmazás támogatja a regisztrációt, a bejelentkezést, a profil szerkesztését és az új jelszó kérését. Ez az oktatóanyag kiemeli, hogyan regisztrál egy felhasználó az alkalmazás használatára e-mail-címmel. Ha szeretne, más forgatókönyvekkel kísérletezhet önállóan.

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

1. Kattintson a **Bejelentkezés** gombra, és jelentkezzen be az SPA-alkalmazás felhasználójaként. Ez a **B2C_1_SiUpIn** az előző lépésben meghatározott felhasználói folyamatot.

2. Az Azure AD B2C megjelenít egy bejelentkezési oldalt egy regisztrációs hivatkozással. Mivel még nincs fiókja, kattintson a **Regisztráció** hivatkozásra. 

3. A regisztrációs munkafolyamat megjelenít egy lapot, amely a felhasználó azonosító adatait gyűjti be és ellenőrzi az e-mail-cím alapján. A munkafolyamat az a felhasználó jelszavát, és a felhasználói folyamat meghatározott attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket. 

    ![Regisztrációs munkafolyamat](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kattintson a **Létrehozás** gombra egy helyi fiók létrehozására az Azure AD B2C-címtárban.

Mostantól a felhasználók bejelentkezhetnek az e-mail-címükkel, és használhatják az SPA-alkalmazást.

> [!NOTE]
> Bejelentkezés után az alkalmazás egy „nem megfelelő engedélyek” hibaüzenetet jelenít meg. Ezt a hibaüzenetet azért kapja, mert a bemutató címtár egyik erőforrásához próbál meg hozzáférni. Mivel a hozzáférési jogkivonat csak az Ön Azure AD-címtárához érvényes, az API-hívás nincs engedélyezve. Lépjen tovább a következő oktatóanyagra, amelyben egy védett webes API-t hozhat létre a címtár számára. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-címtárat ahhoz is használhatja, ha más Azure AD B2C-oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-címtárat](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan hozzon létre egy Azure AD B2C-címtár, felhasználói folyamatok létrehozása és frissítése a mintául szolgáló egyoldalas alkalmazást az Azure AD B2C-címtár használata. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan lehet regisztrálni, konfigurálni és meghívni egy védett webes API-t egy asztali alkalmazásból.

> [!div class="nextstepaction"]
> [Azure AD B2C-kódminták](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
