---
title: 'Oktatóanyag: a felhasználói felület testreszabása'
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan szabhatja testre Azure Active Directory B2C alkalmazásai felhasználói felületét a Azure Portal használatával.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: eba9919b7a1d89e6aea8fb93ef8c4b3e92960368
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950867"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Oktatóanyag: a felhasználói élmények felületének testreszabása Azure Active Directory B2C

A gyakori felhasználói élmények, például a regisztráció, a bejelentkezés és a profil szerkesztése során Azure Active Directory B2C (Azure AD B2C) [felhasználói folyamatokat](active-directory-b2c-reference-policies.md) is használhat. Az oktatóanyagban található információk segítségével megtudhatja, hogyan [szabhatja testre a felhasználói felületét](customize-ui-overview.md) a saját HTML-és CSS-fájljaival.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felhasználói felület testreszabási fájljainak létrehozása
> * A felhasználói folyamat frissítése a fájlok használatára
> * A testreszabott felhasználói felület tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy felhasználói folyamatot](tutorial-create-user-flows.md) , amely lehetővé teszi a felhasználók számára az alkalmazásba való regisztrációt és bejelentkezést.

## <a name="create-customization-files"></a>Testreszabási fájlok létrehozása

Létrehoz egy Azure Storage-fiókot és-tárolót, majd elhelyezi az alapszintű HTML-és CSS-fájlokat a tárolóban.

### <a name="create-a-storage-account"></a>Create a storage account

Bár a fájlok többféleképpen is tárolhatók, ebben az oktatóanyagban az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)-ban tárolhatja őket.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Győződjön meg arról, hogy az Azure-előfizetését tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki az előfizetést tartalmazó könyvtárat. Ez a könyvtár nem azonos az Azure B2C-bérlőt tartalmazó címtárral.
3. A Azure Portal bal felső sarkában válassza a minden szolgáltatás lehetőséget, majd keresse meg és válassza ki a **Storage-fiókokat**.
4. Válassza a **Hozzáadás** lehetőséget.
5. Az **erőforráscsoport**területen válassza az **új létrehozása**elemet, adja meg az új erőforráscsoport nevét, majd kattintson **az OK**gombra.
6. Adja meg a tárfiók nevét. A választott névnek egyedinek kell lennie az Azure-ban, 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
7. Válassza ki a Storage-fiók helyét, vagy fogadja el az alapértelmezett helyet.
8. Fogadja el az összes többi alapértelmezett értéket, válassza a **felülvizsgálat + létrehozás**lehetőséget, majd kattintson a **Létrehozás**gombra.
9. A Storage-fiók létrehozása után válassza az **Ugrás erőforráshoz**lehetőséget.

### <a name="create-a-container"></a>Tároló létrehozása

1. A Storage-fiók áttekintés lapján válassza a **Blobok**lehetőséget.
2. Válassza a **tároló**elemet, adja meg a tároló nevét, válassza a **blob (névtelen olvasási hozzáférés csak blobokhoz)** lehetőséget, majd kattintson **az OK**gombra.

### <a name="enable-cors"></a>CORS engedélyezése

 A böngészőben Azure AD B2C kód egy modern és standard megközelítést használ a felhasználói folyamatokban megadott URL-címek egyéni tartalmának betöltésére. Az eltérő eredetű erőforrás-megosztás (CORS) lehetővé teszi, hogy a weblapon lévő korlátozott erőforrások más tartományokból is kérhetők legyenek.

1. A menüben válassza a **CORS**lehetőséget.
2. Az **engedélyezett eredetek**mezőben adja meg a `https://your-tenant-name.b2clogin.com`. Cserélje le a `your-tenant-name`t a Azure AD B2C bérlő nevére. Például: `https://fabrikam.b2clogin.com`. A bérlő nevének megadásakor az összes kisbetűs betűt kell használnia.
3. Az **engedélyezett módszereknél**válassza a `GET`,`PUT`és `OPTIONS`lehetőséget.
4. Az **engedélyezett fejlécek**mezőbe írjon be egy csillagot (*).
5. A közzétett **fejlécek**esetében írjon be egy csillagot (*).
6. A **Max Age**értéknél adja meg a 200 értéket.

    ![A CORS konfigurációs lapja az Azure Blob Storage-ban Azure Portal](./media/tutorial-customize-ui/enable-cors.png)

5. Kattintson a **Save** (Mentés) gombra.

### <a name="create-the-customization-files"></a>A testreszabási fájlok létrehozása

A regisztrációs élmény felhasználói felületének testreszabásához hozzon létre egy egyszerű HTML-és CSS-fájlt. Tetszőleges módon konfigurálhatja a HTML-kódot, de a **div** elemnek `api`azonosítójának kell lennie. Például: `<div id="api"></div>`. A Azure AD B2C elemeket szúr be a `api` tárolóba, amikor megjelenik az oldal.

1. A helyi mappában hozza létre a következő fájlt, és ügyeljen arra, hogy a `your-storage-account` a Storage-fiók nevére módosítsa, és `your-container` a létrehozott tároló nevére. Például: `https://store1.blob.core.windows.net/b2c/style.css`.

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

    A lap bármilyen módon kialakítható, de a létrehozott HTML-testreszabási fájlhoz az **API** div elem szükséges.

3. Mentse a fájlt *Custom-UI. html*néven.
4. Hozza létre a következő egyszerű CSS-t, amely a regisztrációs vagy bejelentkezési oldalon lévő összes elemet központilag tartalmazza, beleértve a Azure AD B2C Beinjektált elemeket is.

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

5. Mentse a fájlt *Style. css*néven.

### <a name="upload-the-customization-files"></a>A testreszabási fájlok feltöltése

Ebben az oktatóanyagban a Storage-fiókban létrehozott fájlokat tárolja, hogy Azure AD B2C el tudja érni őket.

1. A Azure Portal bal felső sarkában válassza a **minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki a **Storage-fiókokat**.
2. Válassza ki a létrehozott Storage-fiókot, válassza a **Blobok**lehetőséget, majd válassza ki a létrehozott tárolót.
3. Válassza a **feltöltés**lehetőséget, keresse meg és válassza ki a *Custom-UI. html* fájlt, majd kattintson a **feltöltés**elemre.

    ![BLOB-oldal feltöltése a portálon a feltöltés gombbal és a kijelölt fájlokkal](./media/tutorial-customize-ui/upload-blob.png)

4. Másolja a feltöltött fájl URL-címét az oktatóanyag későbbi részében való használatra.
5. Ismételje meg a 3. és a 4. lépést a *Style. css* fájl esetében.

## <a name="update-the-user-flow"></a>A felhasználói folyamat frissítése

1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
2. Válassza a **felhasználói folyamatok (szabályzatok)** lehetőséget, majd válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
3. Válassza **ki a lapelrendezések lehetőséget**, majd **az egyesített regisztráció vagy a bejelentkezés lapon**kattintson az **Igen** lehetőségre az **Egyéni oldal tartalmának használatához**.
4. Az **Egyéni lap URI-ja**mezőben adja meg a korábban rögzített *Custom-UI. html* fájl URI azonosítóját.
5. A lap tetején válassza a **Mentés**lehetőséget.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget, és válassza ki a *B2C_1_signupsignin1* felhasználói folyamatot.
2. A lap tetején kattintson a **felhasználói folyamat futtatása**elemre.
3. Kattintson a **felhasználói folyamat futtatása** gombra.

    ![Felhasználói folyamat futtatása lap a regisztrációs vagy bejelentkezési felhasználói folyamathoz](./media/tutorial-customize-ui/run-user-flow.png)

    A létrehozott CSS-fájl alapján a következő példához hasonló oldalnak kell megjelennie:

    ![Webböngészőt, amely az egyéni felhasználói felületi elemekkel való regisztrációt vagy bejelentkezési oldalt jeleníti meg](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Felhasználói felület testreszabási fájljainak létrehozása
> * A felhasználói folyamat frissítése a fájlok használatára
> * A testreszabott felhasználói felület tesztelése

> [!div class="nextstepaction"]
> [Nyelvi Testreszabás a Azure Active Directory B2Cban](active-directory-b2c-reference-language-customization.md)
