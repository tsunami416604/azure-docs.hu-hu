---
title: 'Oktatóanyag: A felhasználói felület testreszabása'
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan szabhatja testre az alkalmazások felhasználói felületét az Azure Active Directory B2C-ben az Azure Portal használatával.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b2b2bc8dd4e60348553228b8b418df252a8c426a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186250"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Oktatóanyag: A felhasználói élmény felületének testreszabása az Azure Active Directory B2C-ben

A gyakoribb felhasználói élmény, például a regisztráció, a bejelentkezés és a profilszerkesztés, [használhatja a felhasználói folyamatok](user-flow-overview.md) az Azure Active Directory B2C (Azure AD B2C) használatával. Az ebben az oktatóanyagban található információk segítségével [megtudhatja, hogyan szabhatja testre a felhasználói felületet (UI)](customize-ui-overview.md) a saját HTML- és CSS-fájljai használatával.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felhasználói felület testreszabási fájljainak létrehozása
> * A felhasználói folyamat frissítése a fájlok használatához
> * A testreszabott felhasználói felület tesztelése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy felhasználói folyamatot,](tutorial-create-user-flows.md) amely lehetővé teszi a felhasználók számára, hogy regisztráljanak és jelentkezzenek be az alkalmazásba.

## <a name="create-customization-files"></a>Testreszabási fájlok létrehozása

Hozzon létre egy Azure-tárfiókot és tárolót, majd helyezze az alapvető HTML- és CSS-fájlokat a tárolóba.

### <a name="create-a-storage-account"></a>Create a storage account

Bár a fájlokat sokféleképpen tárolhatja, ebben az oktatóanyagban az [Azure Blob storage-ban](../storage/blobs/storage-blobs-introduction.md)tárolhatja őket.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure-előfizetést tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés** szűrőt a felső menüben, és válassza ki az előfizetést tartalmazó könyvtárat. Ez a könyvtár eltér az Azure B2C-bérlőt tartalmazó könyvtártól.
3. Válassza az Összes szolgáltatás lehetőséget az Azure Portal bal felső sarkában, keresse meg és válassza **a Storage-fiókok lehetőséget.**
4. Válassza a **Hozzáadás** lehetőséget.
5. Az **Erőforráscsoport**csoportban válassza **az Új létrehozása**lehetőséget, adja meg az új erőforráscsoport nevét, majd kattintson az **OK**gombra.
6. Adja meg a tárfiók nevét. A választott névnek egyedinek kell lennie az Azure-ban, 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
7. Válassza ki a tárfiók helyét, vagy fogadja el az alapértelmezett helyet.
8. Fogadja el az összes többi alapértelmezett értéket, válassza **a Véleményezés + létrehozás**lehetőséget, majd kattintson a **Létrehozás gombra.**
9. A tárfiók létrehozása után válassza az **Ugrás az erőforrásra**lehetőséget.

### <a name="create-a-container"></a>Tároló létrehozása

1. A tárfiók áttekintő lapján válassza a **Blobok**lehetőséget.
2. Válassza a **Tároló**lehetőséget, adja meg a tároló nevét, válassza a **Blob (csak blobok névtelen olvasási hozzáférése)** lehetőséget, majd kattintson az **OK**gombra.

### <a name="enable-cors"></a>CORS engedélyezése

 Az Azure AD B2C-kód a böngészőben egy modern és szabványos megközelítést használ az egyéni tartalom betöltéséhez egy felhasználói folyamatban megadott URL-címről. A több forrásból származó erőforrások megosztása (CORS) lehetővé teszi, hogy a weboldalon lévő korlátozott erőforrásokat más tartományokból lehessen kérni.

1. A menüben válassza a **CORS**lehetőséget.
2. Az **Engedélyezett eredetek**mezőbe írja be a be írt `https://your-tenant-name.b2clogin.com`értéket. Cserélje `your-tenant-name` le az Azure AD B2C-bérlő nevét. Például: `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor minden kisbetűt használnia kell.
3. Az Engedélyezett módszerek `GET``PUT` **csoportban**válassza a lehetőséget, és `OPTIONS`válassza a lehetőséget.
4. **Az Engedélyezett fejlécek**mezőbe írjon be csillagot (*).
5. **A Kitett fejlécek**mezőbe írjon be egy csillagot (*).
6. A **maximális életkor**mezőbe írja be a 200 értéket.

    ![CORS konfigurációs lap az Azure Blob storage-ban az Azure Portalon](./media/tutorial-customize-ui/enable-cors.png)

5. Kattintson a **Mentés** gombra.

### <a name="create-the-customization-files"></a>A testreszabási fájlok létrehozása

A regisztrációs felület testreszabásához először hozzon létre egy egyszerű HTML- és CSS-fájlt. A HTML-kódot tetszés szerint konfigurálhatja, de rendelkeznie `api`kell egy **div** elemmel, amelynek azonosítója . Például: `<div id="api"></div>`. Az Azure AD B2C `api` elemeket injektál a tárolóba, amikor az oldal megjelenik.

1. Egy helyi mappában hozza létre a következő `your-storage-account` fájlt, és győződjön `your-container` meg arról, hogy a tárfiók nevére és a létrehozott tároló nevére vált. Például: `https://store1.blob.core.windows.net/b2c/style.css`.

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

    Az oldal tetszőleges módon tervezhető, de az **api** div elem szükséges minden létrehozott HTML-testreszabási fájlhoz.

3. Mentse a fájlt *egyéni-ui.html*.
4. Hozza létre a következő egyszerű CSS-t, amely a regisztrációs vagy bejelentkezési oldalon az összes elemet középre igazítja, beleértve az Azure AD B2C által befecskendezett elemeket is.

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

5. Mentse a fájlt *style.css*néven.

### <a name="upload-the-customization-files"></a>A testreszabási fájlok feltöltése

Ebben az oktatóanyagban tárolja a létrehozott fájlokat a tárfiókban, hogy az Azure AD B2C elérheti őket.

1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, keresse meg és válassza **a Storage-fiókok lehetőséget.**
2. Jelölje ki a létrehozott **tárfiókot,** válassza a Blobok lehetőséget, majd válassza ki a létrehozott tárolót.
3. Válassza **a Feltöltés**lehetőséget, keresse meg az *egyéni ui.html* fájlt, és jelölje ki a **Feltöltés**parancsot.

    ![Blob lap feltöltése a portálon a Feltöltés gombbal és a kiemelt Fájlokkal](./media/tutorial-customize-ui/upload-blob.png)

4. Másolja a feltöltött fájl URL-címét az oktatóanyag későbbi részében történő használatra.
5. Ismételje meg a *3.*

## <a name="update-the-user-flow"></a>A felhasználói folyamat frissítése

1. Válassza az **Összes szolgáltatás** lehetőséget az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Azure AD B2C parancsot.**
2. Válassza a **Felhasználói folyamatok (házirendek)** lehetőséget, majd válassza ki a felhasználói folyamat *B2C_1_signupsignin1.*
3. Válassza **a Lapelrendezések**lehetőséget, majd az **Egyesített regisztráció vagy bejelentkezés lap csoportban**kattintson az **Igen** az **egyéni laptartalmának használatához**.
4. Az **Egyéni lap URI-jában**adja meg a korábban rögzített *egyéni-ui.html* fájl URI-ját.
5. A lap tetején válassza a **Mentés gombot.**

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Az Azure AD B2C-bérlőben válassza a **Felhasználói folyamatok** lehetőséget, és válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
2. A lap tetején kattintson a **Felhasználói folyamat futtatása gombra.**
3. Kattintson a **Felhasználói folyamat futtatása** gombra.

    ![Felhasználói folyamatlap futtatása a regisztrációs vagy bejelentkezési felhasználói folyamathoz](./media/tutorial-customize-ui/run-user-flow.png)

    A következő példához hasonló lapot kell látnia, amelynek elemei a létrehozott CSS-fájl alapján középre igazítottak:

    ![A regisztrációt vagy bejelentkezési lapot megjelenítő webböngésző egyéni felhasználói felületi elemekkel](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Felhasználói felület testreszabási fájljainak létrehozása
> * A felhasználói folyamat frissítése a fájlok használatához
> * A testreszabott felhasználói felület tesztelése

> [!div class="nextstepaction"]
> [Nyelvi testreszabás az Azure Active Directory B2C-ben](user-flow-language-customization.md)
