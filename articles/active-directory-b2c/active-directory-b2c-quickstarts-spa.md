---
title: 'Rövid útmutató: bejelentkezés beállítása egyoldalas alkalmazáshoz (SPA)'
titleSuffix: Azure AD B2C
description: Ebben a rövid útmutatóban egy olyan minta egyoldalas alkalmazást futtathat, amely Azure Active Directory B2Ct használ a fiókok bejelentkezésének biztosításához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef6fd46ef51cff9823c93e2297a738f95494f577
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948334"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rövid útmutató: Bejelentkezés beállítása egyoldalas alkalmazáshoz az Azure Active Directory B2C használatával

A Azure Active Directory B2C (Azure AD B2C) biztosítja az alkalmazások, a vállalkozások és az ügyfelek védelmét a Felhőbeli identitások kezelésével. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy egyoldalas alkalmazással és egy közösségi identitásszolgáltatóval fog bejelentkezni, és meghív egy, az Azure AD B2C által védett webes API-t.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- A [Visual Studio 2019](https://www.visualstudio.com/downloads/) a **ASP.net és a webes fejlesztési** számítási feladattal
- [Node.js](https://nodejs.org/en/download/)
- Facebook-, Google-vagy Microsoft-beli közösségi fiók
- Mintakód a GitHubról: [Active-Directory-B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    [Letöltheti a zip-archívumot](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) vagy klónozást készíthet a tárházból:

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

    ![Egy egyoldalas alkalmazás mintája a böngészőben megjelenítve](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    A minta több regisztrációs beállítást is támogat, beleértve a közösségi identitásszolgáltató használatát vagy helyi fiók e-mail-címmel való létrehozását. Ebben a rövid útmutatóban a Facebook, a Google vagy a Microsoft által használt közösségi identitás-szolgáltatói fiókot használhatja.

2. A Azure AD B2C egy Fabrikam nevű fiktív cég bejelentkezési oldalát jeleníti meg a minta webalkalmazáshoz. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Jelentkezzen be vagy regisztráljon az Identity Provider gombjait megjelenítő oldalra](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    A hitelesítéshez (bejelentkezéshez) használja a közösségi fiók hitelesítő adatait, és engedélyezze az alkalmazásnak, hogy beolvassa az adatokat a közösségi fiókból. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

3. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Kattintson a **webes API-t meghívó** gombra a megjelenítendő név JSON-objektumként való visszaadásához a webes API meghívásából.

![Példa a webes API-választ megjelenítő böngészőben elérhető alkalmazásra](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Az egyoldalas mintaalkalmazás a védett webes API-erőforrás felé küldött kérésbe belefoglal egy hozzáférési jogkivonatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy mintául szolgáló egyoldalas alkalmazást használt a következőre:

* Bejelentkezés egyéni bejelentkezési oldallal
* Jelentkezzen be egy közösségi identitás-szolgáltatóval
* Azure AD B2C fiók létrehozása
* Azure AD B2C által védett webes API meghívása

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
