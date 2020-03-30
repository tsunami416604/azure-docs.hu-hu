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
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5d4319f47530a91bcceb9b2dba94c6aa8e4c388
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78183890"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rövid útmutató: Bejelentkezés beállítása egyoldalas alkalmazáshoz az Azure Active Directory B2C használatával

Az Azure Active Directory B2C (Azure AD B2C) felhőalapú identitáskezelést biztosít az alkalmazás, az üzleti és az ügyfelek védelme érdekében. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy egyoldalas alkalmazással és egy közösségi identitásszolgáltatóval fog bejelentkezni, és meghív egy, az Azure AD B2C által védett webes API-t.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) a **ASP.NET és a webfejlesztési** munkaterhelés
- [Node.js](https://nodejs.org/en/download/)
- Közösségi fiók a Facebookról, a Google-tól vagy a Microsofttól
- Kódminta a GitHubról: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    [Letöltheti a zip archívumot,](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) vagy klónozhatja a tárolót:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

1. Indítsa el a kiszolgálót úgy, hogy futtatja a következő parancsokat a Node.js-parancssorban:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    A Server.js megadja a helyi gazdagépen figyelt port számát.

    ```
    Listening on port 6420...
    ```

2. Nyissa meg az alkalmazás URL-címét. Például: `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. A munkafolyamat indításához kattintson a **bejelentkezési** gombra.

    ![Egyoldalas alkalmazásminta alkalmazás a böngészőben](./media/quickstart-single-page-app/sample-app-spa.png)

    A minta több regisztrációs beállítást is támogat, beleértve a közösségi identitásszolgáltató használatát vagy helyi fiók e-mail-címmel való létrehozását. Ehhez a rövid útmutatóhoz használjon közösségi identitásszolgáltatói fiókot a Facebookról, a Google-ról vagy a Microsofttól.

2. Az Azure AD B2C egy fabrikam nevű fiktív cég bejelentkezési lapját mutatja be a mintawebalkalmazáshoz. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Bejelentkezés vagy Bejelentkezés lap az identitásszolgáltató gombjaival](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    A közösségi fiók hitelesítő adataival hitelesíti (bejelentkezik), és engedélyezi az alkalmazásszámára, hogy adatokat olvasson be a közösségi fiókjából. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

3. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Kattintson a **webes API-t meghívó** gombra a megjelenítendő név JSON-objektumként való visszaadásához a webes API meghívásából.

![Mintaalkalmazás a böngészőben a webes API-választ megjelenítő](./media/quickstart-single-page-app/call-api-spa.png)

Az egyoldalas mintaalkalmazás a védett webes API-erőforrás felé küldött kérésbe belefoglal egy hozzáférési jogkivonatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy mintaegyoldalas alkalmazást használt a következőkre:

* Bejelentkezés egyéni bejelentkezési lappal
* Bejelentkezés közösségi identitásszolgáltatóval
* Azure AD B2C-fiók létrehozása
* Az Azure AD B2C által védett webes API hívása

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
