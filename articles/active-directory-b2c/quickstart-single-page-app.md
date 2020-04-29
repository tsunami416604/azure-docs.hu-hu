---
title: 'Rövid útmutató: bejelentkezés beállítása egyoldalas alkalmazáshoz (SPA)'
titleSuffix: Azure AD B2C
description: Ebben a rövid útmutatóban egy olyan minta egyoldalas alkalmazást futtathat, amely Azure Active Directory B2Ct használ a fiókok bejelentkezésének biztosításához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a754873aeafe8d4e7b48d49647469874ff40f7e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875877"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rövid útmutató: Bejelentkezés beállítása egyoldalas alkalmazáshoz az Azure Active Directory B2C használatával

A Azure Active Directory B2C (Azure AD B2C) biztosítja az alkalmazások, a vállalkozások és az ügyfelek védelmét a Felhőbeli identitások kezelésével. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy egyoldalas alkalmazással és egy közösségi identitásszolgáltatóval fog bejelentkezni, és meghív egy, az Azure AD B2C által védett webes API-t.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Facebook-, Google-vagy Microsoft-beli közösségi fiók
- Mintakód a GitHubról: [Active-Directory-B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    [Letöltheti a zip-archívumot](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) vagy klónozást készíthet a tárházból:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. Indítsa el a kiszolgálót úgy, hogy futtatja a következő parancsokat a Node.js-parancssorban:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    npm start
    ```

    A Server *. js* által indított kiszolgáló megjeleníti a figyelt portot:

    ```console
    Listening on port 6420...
    ```

1. Nyissa meg az alkalmazás URL-címét. Például: `http://localhost:6420`.

    ![Egy egyoldalas alkalmazás mintája a böngészőben megjelenítve](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. A felhasználói út elindításához válassza a **Bejelentkezés** lehetőséget.
1. A Azure AD B2C egy Fabrikam nevű fiktív cég bejelentkezési oldalát jeleníti meg a minta webalkalmazáshoz. Ha közösségi identitás-szolgáltatón keresztül szeretne regisztrálni, válassza ki a használni kívánt identitás-szolgáltató gombját.

    ![Jelentkezzen be vagy regisztráljon az Identity Provider gombjait megjelenítő oldalra](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    A hitelesítéshez (bejelentkezéshez) használja a közösségi fiók hitelesítő adatait, és engedélyezze az alkalmazásnak, hogy beolvassa az adatokat a közösségi fiókból. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

1. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Válassza a **Call API** lehetőséget, hogy a megjelenített nevet JSON-objektumként adja vissza a webes API-ból.

![Példa a webes API-választ megjelenítő böngészőben elérhető alkalmazásra](./media/quickstart-single-page-app/call-api-spa.png)

Az egyoldalas mintaalkalmazás a védett webes API-erőforrás felé küldött kérésbe belefoglal egy hozzáférési jogkivonatot.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy mintául szolgáló egyoldalas alkalmazást használt a következőre:

- Jelentkezzen be egy közösségi identitás-szolgáltatóval
- Azure AD B2C felhasználói fiók létrehozása (automatikusan létrehozva a bejelentkezéskor)
- Azure AD B2C által védett webes API meghívása

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
