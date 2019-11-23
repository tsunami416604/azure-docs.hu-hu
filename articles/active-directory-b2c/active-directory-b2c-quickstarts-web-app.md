---
title: Quickstart - Set up sign in for an ASP.NET application using Azure Active Directory B2C
description: In this Quickstart, run a sample ASP.NET web app that uses Azure Active Directory B2C to provide account sign-in.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 114e82147b03fb500197a2129b7960101f85a455
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420196"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Quickstart: Set up sign in for an ASP.NET application using Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) provides cloud identity management to keep your application, business, and customers protected. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy ASP.NET-alkalmazással és egy közösségi identitásszolgáltatóval fog bejelentkezni, és meghív egy, az Azure AD B2C által védett webes API-t.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) with the **ASP.NET and web development** workload.
- A social account from Facebook, Google, or Microsoft.
- [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), vagy klónozza a mintául szolgáló webalkalmazást a GitHubról.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    There are two projects are in the sample solution:

    - **TaskWebApp** – Ez a webalkalmazás egy feladatlistát hoz létre és szerkeszt. The web application uses the **sign-up or sign-in** user flow to sign up or sign in users.
    - **TaskService** – Ez a webes API a feladatlista-funkciók létrehozását, olvasását, frissítését és törlését támogatja. A webes API-nak az Azure AD B2C biztosít védelmet, és a webalkalmazással hívható meg.

## <a name="run-the-application-in-visual-studio"></a>Az alkalmazás futtatása a Visual Studióban

1. A mintaalkalmazás projektmappájában nyissa meg a **B2C-WebAPI-DotNet.sln** megoldást a Visual Studióban.
2. Ebben a rövid útmutatóban egy időben futtatja a **TaskWebApp** és a **TaskService** projektet. Kattintson a jobb gombbal a **B2C-WebAPI-DotNet** megoldásra a Megoldáskezelőben, majd válassza az **Indítási projektek beállítása** lehetőséget.
3. Válassza a **Több kezdőprojekt** elemet, és mindkét projektnél módosítsa a **Művelet** értékét **Indításra**.
4. Kattintson az **OK** gombra.
5. Nyomja le az **F5** billentyűt mindkét alkalmazás hibakereséséhez. Mindegyik alkalmazás saját böngészőlapon nyílik meg:

    - `https://localhost:44316/` – Az ASP.NET-es webalkalmazás. A gyors útmutatóban közvetlenül kommunikál az alkalmazással.
    - `https://localhost:44332/` – Az ASP.NET-es webalkalmazás által meghívott webes API.

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. Kattintson a **Sign up / Sign in** (Regisztráció/Bejelentkezés) hivatkozásra az ASP.NET-es webalkalmazásban a munkafolyamat elindításához.

    ![Sample ASP.NET web app in browser with sign up/sign link highlighted](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    A minta több regisztrációs beállítást is támogat, beleértve a közösségi identitásszolgáltató használatát vagy helyi fiók e-mail-címmel való létrehozását. For this quickstart, use a social identity provider account from either Facebook, Google, or Microsoft.

2. Azure AD B2C presents a sign-in page for a fictitious company called Fabrikam for the sample web application. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Sign In or Sign Up page showing identity provider buttons](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    You authenticate (sign in) using your social account credentials and authorize the application to read information from your social account. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

3. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

## <a name="edit-your-profile"></a>Saját profil szerkesztése

Az Azure Active Directory B2C-funkcióival a felhasználók frissíthetik a profiljukat. The sample web app uses an Azure AD B2C edit profile user flow for the workflow.

1. Az alkalmazás menüsávján kattintson a profil nevére, és válassza az **Edit profile** (Profil szerkesztése) parancsot a létrehozott profil szerkesztéséhez.

    ![Sample web app in browser with Edit profile link highlighted](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. Módosítsa a **Display name** (Megjelenített név) és a **City** (Város) mezőket, majd kattintson a **Continue** (Folytatás) gombra a profil frissítéséhez.

    A módosított profil a webalkalmazás kezdőlapjának jobb felső részén jelenik meg.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

1. Kattintson a **To-Do List** (Feladatlista) elemre a feladatlista elemeinek megadásához és módosításához.

2. Adjon meg szöveget a **New Item** (Új elem) szövegmezőben. Kattintson az **Add** (Hozzáadás) gombra az Azure AD B2C által védett, feladatlista-elemet hozzáadó webes API meghívásához.

    ![Sample web app in browser with Add a to-do list item](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    Az ASP.NET-es webalkalmazás a védett webes API-erőforrás felé küldött, a felhasználó feladatlista-elemeire vonatkozó műveletek végrehajtására irányuló kérésbe belefoglalja az Azure AD hozzáférési jogkivonatot.

Az Azure AD B2C felhasználói fiókkal sikeresen intézett hitelesített hívást egy Azure AD B2C által védett webes API-hoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Következő lépések

In this quickstart, you used a sample ASP.NET application to:

* Sign in with a custom login page
* Sign in with a social identity provider
* Create an Azure AD B2C account
* Call a web API protected by Azure AD B2C

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
