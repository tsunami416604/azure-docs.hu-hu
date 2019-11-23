---
title: Quickstart - Set up sign-in for a single-page app using Azure Active Directory B2C
description: In this Quickstart, run a sample single-page application that uses Azure Active Directory B2C to provide account sign-in.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7dc3a52ca9f227f8476cf74286be917b5b6ba021
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420182"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Rövid útmutató: Bejelentkezés beállítása egyoldalas alkalmazáshoz az Azure Active Directory B2C használatával

Azure Active Directory B2C (Azure AD B2C) provides cloud identity management to keep your application, business, and customers protected. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy egyoldalas alkalmazással és egy közösségi identitásszolgáltatóval fog bejelentkezni, és meghív egy, az Azure AD B2C által védett webes API-t.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) with the **ASP.NET and web development** workload
- [Node.js](https://nodejs.org/en/download/)
- Social account from Facebook, Google, or Microsoft
- Code sample from GitHub: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
    
    You can [download the zip archive](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) or clone the repository:

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

    ![Single-page application sample app shown in browser](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    A minta több regisztrációs beállítást is támogat, beleértve a közösségi identitásszolgáltató használatát vagy helyi fiók e-mail-címmel való létrehozását. For this quickstart, use a social identity provider account from either Facebook, Google, or Microsoft.

2. Azure AD B2C presents a sign-in page for a fictitious company called Fabrikam for the sample web application. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Sign In or Sign Up page showing identity provider buttons](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    You authenticate (sign in) using your social account credentials and authorize the application to read information from your social account. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

3. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Kattintson a **webes API-t meghívó** gombra a megjelenítendő név JSON-objektumként való visszaadásához a webes API meghívásából.

![Sample application in browser showing the web API response](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Az egyoldalas mintaalkalmazás a védett webes API-erőforrás felé küldött kérésbe belefoglal egy hozzáférési jogkivonatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Következő lépések

In this quickstart, you used a sample single-page application to:

* Sign in with a custom login page
* Sign in with a social identity provider
* Create an Azure AD B2C account
* Call a web API protected by Azure AD B2C

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
