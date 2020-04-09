---
title: 'Rövid útmutató: Bejelentkezés beállítása egyoldalas alkalmazáshoz (SPA)'
titleSuffix: Azure AD B2C
description: Ebben a rövid útmutatóban futtasson egy minta egyoldalas alkalmazást, amely az Azure Active Directory B2C használatával biztosítja a fiókbejelentkezést.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875877"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rövid útmutató: Bejelentkezés beállítása egyoldalas alkalmazáshoz az Azure Active Directory B2C használatával

Az Azure Active Directory B2C (Azure AD B2C) felhőalapú identitáskezelést biztosít az alkalmazás, az üzleti és az ügyfelek védelme érdekében. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy egyoldalas alkalmazással és egy közösségi identitásszolgáltatóval fog bejelentkezni, és meghív egy, az Azure AD B2C által védett webes API-t.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Közösségi fiók a Facebookról, a Google-tól vagy a Microsofttól
- Kódminta a GitHubról: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    [Letöltheti a zip archívumot,](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) vagy klónozhatja a tárolót:

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

    A *server.js* által indított kiszolgáló megjeleníti a figyelő portot:

    ```console
    Listening on port 6420...
    ```

1. Nyissa meg az alkalmazás URL-címét. Például: `http://localhost:6420`.

    ![Egyoldalas alkalmazásminta alkalmazás a böngészőben](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. A felhasználói út elindításához válassza a **Bejelentkezés** lehetőséget.
1. Az Azure AD B2C egy fabrikam nevű fiktív cég bejelentkezési lapját mutatja be a mintawebalkalmazáshoz. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, válassza ki a használni kívánt identitásszolgáltató gombját.

    ![Bejelentkezés vagy Bejelentkezés lap az identitásszolgáltató gombjaival](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    A közösségi fiók hitelesítő adataival hitelesíti (bejelentkezik), és engedélyezi az alkalmazásszámára, hogy adatokat olvasson be a közösségi fiókjából. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

1. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Válassza **a Hívás API-t,** ha a megjelenítendő nevet a webes API-ból JSON-objektumként szeretné visszaadni.

![Mintaalkalmazás a böngészőben a webes API-választ megjelenítő](./media/quickstart-single-page-app/call-api-spa.png)

Az egyoldalas mintaalkalmazás a védett webes API-erőforrás felé küldött kérésbe belefoglal egy hozzáférési jogkivonatot.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy mintaegyoldalas alkalmazást használt a következőkre:

- Bejelentkezés közösségi identitásszolgáltatóval
- Azure AD B2C felhasználói fiók létrehozása (bejelentkezéskor automatikusan létrehozva)
- Az Azure AD B2C által védett webes API hívása

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
