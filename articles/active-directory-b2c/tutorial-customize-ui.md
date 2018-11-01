---
title: Oktatóanyag – az alkalmazások az Azure Active Directory B2C a felhasználói felület testreszabása |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a felhasználói felület az alkalmazások az Azure Active Directory B2C az Azure portal használatával.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9c206ac7a13ea222a01cac78c447c0764f753517
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634060"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>Oktatóanyag: Az alkalmazások az Azure Active Directory B2C a felhasználói felület testreszabása

A gyakori felhasználói élményt például a regisztrációs, bejelentkezést és profilszerkesztést, használhatja [beépített szabályzatok](active-directory-b2c-reference-policies.md) Azure Active Directory (Azure AD) B2C-ben. Ebben az oktatóanyagban szereplő információk segítségével megtudhatja, hogyan [testre szabhatja a felhasználói felület (UI)](customize-ui-overview.md) , ezek a tapasztalatok saját HTML és CSS-fájlok használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felhasználói felület testreszabása fájlok létrehozása
> * A fájlok használó regisztrációs és bejelentkezési szabályzat létrehozása
> * A testre szabott felhasználói felület teszteléséhez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre a saját [Azure AD B2C-bérlő](tutorial-create-tenant.md), hozzon létre egyet most. Egy meglévő bérlőt is használhatja, ha van ilyen egy korábbi oktatóanyagban.

## <a name="create-customization-files"></a>Testreszabás-fájlok létrehozása

Egy Azure-tárfiókot és tárolót hoz létre, és helyezze el alapvető HTML és CSS-fájlokat a tárolóban.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Bár ebben az oktatóanyagban számos módon tárolhatja a fájlokat, azokat a tárolni [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md).

1. Győződjön meg arról, hogy használja a könyvtárat, amely tartalmazza az Azure-előfizetés. Válassza ki a **címtár és előfizetés-szűrő** a felső menüben, és válassza ki az előfizetést tartalmazó könyvtárba. Ez a könyvtár eltér az Azure B2C-bérlő tartalmazza.

    ![Váltás előfizetési könyvtár](./media/tutorial-customize-ui/switch-directories.png)

2. Válassza ki az összes szolgáltatás az Azure portal bal felső sarkában, keresése és kiválasztása **tárfiókok**. 
3. Válassza a **Hozzáadás** lehetőséget.
4. A **erőforráscsoport**válassza **új létrehozása**, adja meg az új erőforráscsoport nevét, és kattintson **OK**.
5. Adja meg a tárfiók nevét. A választott névnek egyedinek kell lennie az Azure-ban, 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
6. Válassza ki a tárfiók helyét, vagy fogadja el az alapértelmezett helyet. 
7. Fogadja el az összes többi alapértelmezett értéket, válassza ki **felülvizsgálat + létrehozása**, és kattintson a **létrehozás**.
8. A tárfiók létrehozása után válassza ki a **erőforrás megnyitása**.

### <a name="create-a-container"></a>Tároló létrehozása

1. A storage-fiók áttekintése lapon válassza ki a **Blobok**.
2. Válassza ki **tároló**, adja meg a tároló nevét, majd a **Blob (névtelen olvasási hozzáférés csak blobokhoz)**, és kattintson a **OK**.

### <a name="enable-cors"></a>CORS engedélyezése

 Egy böngészőben az Azure AD B2C-kód egyéni tartalom betöltése egy házirendben megadott URL-címről a modern és szabványos megközelítést használ. Eltérő eredetű erőforrások megosztása (CORS) lehetővé teszi a korlátozott erőforrások egy weblap más tartományokból a kötelező.

1. Válassza a menüben **CORS**.
2. A **engedélyezett eredetek**, **engedélyezett fejlécek**, és **közzétett fejlécek**, adja meg `your-tenant-name.b2clogin.com`. Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével. Például: `fabrikam.b2clogin.com`.
3. A **engedélyezett műveletek**, válassza ki mindkét `GET` és `OPTIONS`.
4. A **maximális életkora**, adja meg a 200-as.

    ![CORS engedélyezése](./media/tutorial-customize-ui/enable-cors.png)

5. Kattintson a **Save** (Mentés) gombra.

### <a name="create-the-customization-files"></a>Hozza létre a testreszabási fájlokat

A felhasználói felület, a bejelentkezési felület testreszabása, először hozzon létre egy egyszerű HTML és CSS fájlt. Konfigurálhatja a kívánt módon, de rendelkeznie kell a HTML- **div** elem azonosítója, amelyet az `api`. Például: `<div id="api"></div>`. Az Azure AD B2C-kódtárba elemeket a `api` tárolót, ha az oldal jelenik meg.

1. Egy helyi mappába, a következő fájl létrehozásához, és győződjön meg arról, hogy módosítsa `your-storage-account` a tárfiók nevére és `your-container` a létrehozott tároló nevére. Például: `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Az oldal megtervezhetők úgy szeretne, olyan módon, de a **api** div elembe a bármely Ön által létrehozott HTML-testreszabási fájl megadása kötelező. 

3. Mentse a fájlt az *egyéni-ui.html*.
4. Hozza létre a következő egyszerű CSS, amelyek azonos adatközpontban üzemelő összes elem a regisztrálási vagy bejelentkezési oldalon, beleértve az elemeket, amelyek az Azure AD B2C-kódtárba.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Mentse a fájlt az *style.css*.

### <a name="upload-the-customization-files"></a>A Testreszabás fájlok feltöltése

Ebben az oktatóanyagban létrehozott storage-fiókban, hogy az Azure AD B2C-vel érhetik el azokat a fájlokat fogja tárolni.

1. Válasszon **minden szolgáltatás** az Azure portal bal felső sarkában lévő keresése és kiválasztása **tárfiókok**.
2. Válassza ki a létrehozott tárfiókot, jelölje be **Blobok**, majd válassza ki a tárolót, amelyet létrehozott.
3. Válassza ki **feltöltése**, keresse meg és válassza a *egyéni-ui.html* fájlt, és kattintson a **feltöltése**.

    ![Testreszabás fájlok feltöltése](./media/tutorial-customize-ui/upload-blob.png)

4. Másolja az URL-címet az oktatóanyag későbbi részében használni a feltöltött fájl.
5. Ismételje meg a műveletet 3. és 4. lépés a *style.css* fájlt.

## <a name="create-a-sign-up-and-sign-in-policy"></a>Regisztrációs és bejelentkezési szabályzat létrehozása

Ebben az oktatóanyagban szereplő lépések végrehajtásához szüksége egy teszt alkalmazás és a regisztrálási vagy bejelentkezési szabályzat létrehozása az Azure AD B2C-ben. Ebben az oktatóanyagban a többi felhasználói élményt, például a profil szerkesztését leírt elveket alkalmazhat.

### <a name="create-an-azure-ad-b2c-application"></a>Az Azure AD B2C-alkalmazás létrehozása

Kommunikáció az Azure AD B2C-vel hoz létre a bérlő alkalmazás keresztül történik. Az alábbi lépéseket, hozzon létre egy alkalmazást, amely átirányítja a visszaadott engedélyezési jogkivonatot [ https://jwt.ms ](https://jwt.ms).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
4. Válassza ki **alkalmazások**, majd válassza ki **Hozzáadás**.
5. Adja meg egy nevet az alkalmazásnak, például *testapp1*.
6. A **Web App / Web API**, jelölje be `Yes`, majd adja meg `https://jwt.ms` a a **válasz URL-cím**.
7. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-the-policy"></a>A szabályzat létrehozása

A testreszabási fájlokat teszteléséhez hozzon létre egy beépített regisztrálási vagy bejelentkezési szabályzatot, amely a korábban létrehozott alkalmazást használja.

1. Válassza ki az Azure AD B2C-bérlőben **regisztrálási vagy bejelentkezési szabályzatok**, és kattintson a **Hozzáadás**.
2. Adjon meg egy nevet a házirend. Ha például *signup_signin*. Az előtag *B2C_1* automatikusan hozzáadódik a nevet a házirend létrehozásakor.
3. Válassza ki **Identitásszolgáltatók**állítsa be **regisztrációs E-mail** egy helyi fiókot, és kattintson a **OK**.
4. Válassza ki **regisztrálási attribútumok**, válassza ki a regisztráció során az ügyfél gyűjteni kívánt attribútumokat. Például állítsa be **ország/régió**, **megjelenítendő név**, és **irányítószám**, és kattintson a **OK**.
5. Válassza ki **alkalmazásjogcímek**, válassza ki a jogcímeket, amelyeket szeretne visszaküldött hitelesítő jogkivonatokban sikeres regisztrációs vagy bejelentkezési élmény után az alkalmazásnak. Válassza ki például **megjelenítendő név**, **identitásszolgáltató**, **irányítószám**, **felhasználó egyik új** és **felhasználó Objektumazonosítója** , és kattintson a **OK**.
6. Válassza ki **oldal-UI testreszabása**válassza **egyesített regisztrálási vagy bejelentkezési oldal**, és kattintson a **Igen** a **használata egyéni lap**.
7. A **egyéni oldal URI-ja**, adja meg az URL-címet a *egyéni-ui.html* fájlt, hogy Ön korábban rögzített, és kattintson a **OK**.
8. Kattintson a **Create** (Létrehozás) gombra.

## <a name="test-the-policy"></a>A házirend tesztelése

1. Válassza ki az Azure AD B2C-bérlőben **regisztrálási vagy bejelentkezési szabályzatok**, majd válassza ki a létrehozott szabályzatot. Ha például *B2C_1_signup_signin*.
2. Győződjön meg arról, hogy az Ön által létrehozott alkalmazás van kijelölve, **válassza ki az alkalmazás**, és kattintson a **Futtatás most**.

    ![Futtassa a regisztrálási vagy bejelentkezési szabályzat](./media/tutorial-customize-ui/signup-signin.png)

    Az Ön által létrehozott CSS-fájlon alapul, a következő példában az elemeket, középre igazítva a hasonló lap kell megjelennie:

    ![Csoportházirend-eredmények](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Felhasználói felület testreszabása fájlok létrehozása
> * A fájlok használó regisztrációs és bejelentkezési szabályzat létrehozása
> * A testre szabott felhasználói felület teszteléséhez

> [!div class="nextstepaction"]
> [Az Azure Active Directory B2C a nyelvi testreszabás](active-directory-b2c-reference-language-customization.md)