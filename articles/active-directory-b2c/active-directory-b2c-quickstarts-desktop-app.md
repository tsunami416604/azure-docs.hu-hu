---
title: Quickstart - Set up sign-in for a desktop app using Azure Active Directory B2C
description: In this Quickstart, run a sample WPF desktop application that uses Azure Active Directory B2C to provide account sign-in.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed92605c7fb74186ddde6ff193a1365146494594
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420224"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Gyors útmutató: Asztali alkalmazásba való bejelentkezés konfigurálása Azure Active Directory B2C-vel

Azure Active Directory B2C (Azure AD B2C) provides cloud identity management to keep your application, business, and customers protected. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy asztali Windows megjelenítési alaprendszerbeli (WPF-) alkalmazással jelentkezik be egy közösségi identitásszolgáltatót használva, és az Azure AD B2C által védett webes API-t hív meg.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) with the **ASP.NET and web development** workload.
- A social account from either Facebook, Google, or Microsoft.
- [Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Az alkalmazás futtatása a Visual Studióban

1. A mintaalkalmazás projektmappájában nyissa meg az **active-directory-b2c-wpf.sln** megoldást a Visual Studióban.
2. Nyomja le az **F5** billentyűt az alkalmazás hibakereséséhez.

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. Kattintson a **Sign in** (Bejelentkezés) gombra a **Sign Up or Sign In** (Regisztráció vagy bejelentkezés) munkafolyamat elindításához.

    ![Screenshot of the sample WPF application](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    The sample supports several sign-up options. These options include using a social identity provider or creating a local account using an email address. For this quickstart, use a social identity provider account from either Facebook, Google, or Microsoft.


2. Azure AD B2C presents a sign-in page for a fictitious company called Fabrikam for the sample web application. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Sign In or Sign Up page showing identity providers](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    You authenticate (sign in) using your social account credentials and authorize the application to read information from your social account. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

2. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

    Az új fiókprofil részletei előre ki vannak töltve a közösségi fiókja adataival.

## <a name="edit-your-profile"></a>Saját profil szerkesztése

Az Azure AD B2C-funkcióival a felhasználók frissíthetik a profiljukat. The sample web app uses an Azure AD B2C edit profile user flow for the workflow.

1. Az alkalmazás menüsávján kattintson az **Edit profile** (Profil szerkesztése) gombra a létrehozott profil szerkesztéséhez.

    ![Edit profile button highlighted in WPF sample app](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Válassza ki a létrehozott fiókhoz rendelt identitásszolgáltatót. For example, if you used Facebook as the identity provider when you created your account, choose Facebook to modify the associated profile details.

3. Módosítsa a **Display name** (Megjelenítendő név) és a **City** (Város) mezőt, majd kattintson a **Continue** (Folytatás) gombra.

    Ekkor a *Token info* (Jogkivonat adatai) szövegmezőben megjelenik egy új hozzáférési jogkivonat. Ha szeretné ellenőrizni a profilján végzett módosításokat, másolja és illessze be a hozzáférési jogkivonatot a https://jwt.ms jogkivonat-dekóderbe.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Kattintson a **Call API** (API meghívása) elemre, amellyel kérelmet küldhet a védett erőforrásnak.

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

Az Azure AD B2C felhasználói fiókkal sikeresen intézett hitelesített hívást egy Azure AD B2C által védett webes API-hoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Következő lépések

In this quickstart, you used a sample desktop application to:

* Sign in with a custom login page
* Sign in with a social identity provider
* Create an Azure AD B2C account
* Call a web API protected by Azure AD B2C

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
