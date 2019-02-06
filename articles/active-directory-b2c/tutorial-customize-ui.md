---
title: Oktatóanyag – testre szabhatja a felhasználói élmények - felület az Azure Active Directory B2C |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a felhasználói felület az alkalmazások az Azure Active Directory B2C az Azure portal használatával.
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f3bc1789d0b521b0d91ca42ebe472fed0225d87b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752381"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Oktatóanyag: Az Azure Active Directory B2C felhasználói élmények a felület testreszabása

A gyakori felhasználói élményt például a regisztrációs, bejelentkezést és profilszerkesztést, használhatja [felhasználókövetési adatai](active-directory-b2c-reference-policies.md) Azure Active Directory (Azure AD) B2C-ben. Ebben az oktatóanyagban szereplő információk segítségével megtudhatja, hogyan [testre szabhatja a felhasználói felület (UI)](customize-ui-overview.md) , ezek a tapasztalatok saját HTML és CSS-fájlok használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felhasználói felület testreszabása fájlok létrehozása
> * Ahhoz, hogy a fájlokat a felhasználói folyamat frissítése
> * A testre szabott felhasználói felület teszteléséhez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Felhasználói folyamat létrehozása](tutorial-create-user-flows.md) regisztráljon, és jelentkezzen be az alkalmazást használók.

## <a name="create-customization-files"></a>Testreszabás-fájlok létrehozása

Egy Azure-tárfiókot és tárolót hoz létre, és helyezze el alapvető HTML és CSS-fájlokat a tárolóban.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Bár ebben az oktatóanyagban számos módon tárolhatja a fájlokat, azokat a tárolni [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy használja a könyvtárat, amely tartalmazza az Azure-előfizetés. Válassza ki a **címtár és előfizetés-szűrő** a felső menüben, és válassza ki az előfizetést tartalmazó könyvtárba. Ez a könyvtár eltér az Azure B2C-bérlő tartalmazza.
3. Válassza ki az összes szolgáltatás az Azure portal bal felső sarkában, keresése és kiválasztása **tárfiókok**. 
4. Válassza a **Hozzáadás** lehetőséget.
5. A **erőforráscsoport**válassza **új létrehozása**, adja meg az új erőforráscsoport nevét, és kattintson **OK**.
6. Adja meg a tárfiók nevét. A választott névnek egyedinek kell lennie az Azure-ban, 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
7. Válassza ki a tárfiók helyét, vagy fogadja el az alapértelmezett helyet. 
8. Fogadja el az összes többi alapértelmezett értéket, válassza ki **felülvizsgálat + létrehozása**, és kattintson a **létrehozás**.
9. A tárfiók létrehozása után válassza ki a **erőforrás megnyitása**.

### <a name="create-a-container"></a>Tároló létrehozása

1. A storage-fiók áttekintése lapon válassza ki a **Blobok**.
2. Válassza ki **tároló**, adja meg a tároló nevét, majd a **Blob (névtelen olvasási hozzáférés csak blobokhoz)**, és kattintson a **OK**.

### <a name="enable-cors"></a>CORS engedélyezése

 Egy böngészőben az Azure AD B2C-kód egyéni tartalom betöltéséhez a felhasználói folyamat a megadott URL-címről a modern és szabványos megközelítést használ. Eltérő eredetű erőforrások megosztása (CORS) lehetővé teszi a korlátozott erőforrások egy weblap más tartományokból a kötelező.

1. Válassza a menüben **CORS**.
2. A **engedélyezett eredetek**, adja meg `https://your-tenant-name.b2clogin.com`. Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével. Például: `https://fabrikam.b2clogin.com`. Csak kisbetűk használata a bérlő nevének megadásakor kell.
3. A **engedélyezett metódusok**, válassza ki mindkét `GET` és `OPTIONS`.
4. A **engedélyezett fejlécek**, írjon be egy csillagot (*).
5. A **közzétett fejlécek**, írjon be egy csillagot (*).
6. A **maximális életkora**, adja meg a 200-as.

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

## <a name="update-the-user-flow"></a>A felhasználói folyamat frissítése

1. Válasszon **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **Azure AD B2C-vel**.
2. Válassza ki **felhasználókövetési adatai (szabályzatok)**, majd válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
3. Válassza ki **elrendezések lapon**, majd a **egyesített regisztrálási vagy bejelentkezési oldal**, kattintson a **Igen** a **egyéni Laptartalom használata**.
4. A **egyéni oldal URI-ja**, adja meg az URI-JÁNAK a *egyéni-ui.html* korábban feljegyzett fájlt.
5. A lap tetején válassza **mentése**.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Válassza ki az Azure AD B2C-bérlőben **felhasználói folyamatok** , és válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
2. Kattintson a lap tetején **felhasználói folyamat futtatása**.
3. Kattintson a **felhasználói folyamat futtatása** gombra.

    ![Regisztrálási vagy bejelentkezési felhasználói folyamat futtatása](./media/tutorial-customize-ui/run-user-flow.png)

    Az Ön által létrehozott CSS-fájlon alapul, a következő példában az elemeket, középre igazítva a hasonló lap kell megjelennie:

    ![Felhasználói folyamat eredmények](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan lehet:

> [!div class="checklist"]
> * Felhasználói felület testreszabása fájlok létrehozása
> * Ahhoz, hogy a fájlokat a felhasználói folyamat frissítése
> * A testre szabott felhasználói felület teszteléséhez

> [!div class="nextstepaction"]
> [Az Azure Active Directory B2C a nyelvi testreszabás](active-directory-b2c-reference-language-customization.md)
