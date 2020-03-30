---
title: 'Rövid útmutató: Bejelentkezés beállítása asztali alkalmazáshoz'
titleSuffix: Azure AD B2C
description: Ebben a rövid útmutatóban futtasson egy minta WPF asztali alkalmazást, amely az Azure Active Directory B2C használatával biztosítja a fiókbejelentkezést.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebed2f5e8664bd4336219f9387b8d27c8f3a1c59
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78187304"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Gyors útmutató: Asztali alkalmazásba való bejelentkezés konfigurálása Azure Active Directory B2C-vel

Az Azure Active Directory B2C (Azure AD B2C) felhőalapú identitáskezelést biztosít az alkalmazás, az üzleti és az ügyfelek védelme érdekében. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon. Ebben a rövid útmutatóban egy asztali Windows megjelenítési alaprendszerbeli (WPF-) alkalmazással jelentkezik be egy közösségi identitásszolgáltatót használva, és az Azure AD B2C által védett webes API-t hív meg.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) a **ASP.NET és a webfejlesztési** munkaterhelés.
- Közösségi fiók a Facebookról, a Google-tól vagy a Microsofttól.
- [Töltsön le egy zip-fájlt,](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) vagy klónozza az [Azure-Samples/active-directory-b2c-dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) repository-t a GitHubról.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Az alkalmazás futtatása a Visual Studióban

1. A mintaalkalmazás projektmappájában nyissa meg az **active-directory-b2c-wpf.sln** megoldást a Visual Studióban.
2. Nyomja le az **F5** billentyűt az alkalmazás hibakereséséhez.

## <a name="sign-in-using-your-account"></a>Bejelentkezés saját fiókkal

1. Kattintson a **Sign in** (Bejelentkezés) gombra a **Sign Up or Sign In** (Regisztráció vagy bejelentkezés) munkafolyamat elindításához.

    ![Képernyőkép a minta WPF alkalmazásról](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    A minta számos regisztrációs lehetőséget támogat. Ezek a lehetőségek közé tartozik a közösségi identitásszolgáltató használata, vagy egy helyi fiók létrehozása e-mail címmel. Ehhez a rövid útmutatóhoz használjon közösségi identitásszolgáltatói fiókot a Facebookról, a Google-ról vagy a Microsofttól.


2. Az Azure AD B2C egy fabrikam nevű fiktív cég bejelentkezési lapját mutatja be a mintawebalkalmazáshoz. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Bejelentkezés vagy Bejelentkezés lap identitásszolgáltatókkal](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    A közösségi fiók hitelesítő adataival hitelesíti (bejelentkezik), és engedélyezi az alkalmazásszámára, hogy adatokat olvasson be a közösségi fiókjából. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését.

2. Fejezze be az identitásszolgáltató bejelentkezési folyamatát.

    Az új fiókprofil részletei előre ki vannak töltve a közösségi fiókja adataival.

## <a name="edit-your-profile"></a>Saját profil szerkesztése

Az Azure AD B2C-funkcióival a felhasználók frissíthetik a profiljukat. A minta webalkalmazás egy Azure AD B2C szerkesztési profil felhasználói folyamatot használ a munkafolyamathoz.

1. Az alkalmazás menüsávján kattintson az **Edit profile** (Profil szerkesztése) gombra a létrehozott profil szerkesztéséhez.

    ![A PROFIL szerkesztése gomb kiemelve a WPF mintaalkalmazásban](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Válassza ki a létrehozott fiókhoz rendelt identitásszolgáltatót. Ha például a fiók létrehozásakor a Facebookot használtad identitásszolgáltatóként, a Facebook lehetőséget választva módosíthatod a kapcsolódó profiladatokat.

3. Módosítsa a **Display name** (Megjelenítendő név) és a **City** (Város) mezőt, majd kattintson a **Continue** (Folytatás) gombra.

    Ekkor a *Token info* (Jogkivonat adatai) szövegmezőben megjelenik egy új hozzáférési jogkivonat. Ha szeretné ellenőrizni a profilján végzett módosításokat, másolja és illessze be a hozzáférési jogkivonatot a https://jwt.ms jogkivonat-dekóderbe.

## <a name="access-a-protected-api-resource"></a>Védett API-erőforrás elérése

Kattintson a **Call API** (API meghívása) elemre, amellyel kérelmet küldhet a védett erőforrásnak.

![API meghívása](./media/quickstart-native-app-desktop/call-api-wpf.png)

Az alkalmazás a védett webes API-erőforrás felé küldött kérésbe belefoglalja az Azure AD hozzáférési jogkivonatot. A webes API a hozzáférési jogkivonatban található megjelenített nevet küldi vissza.

Az Azure AD B2C felhasználói fiókkal sikeresen intézett hitelesített hívást egy Azure AD B2C által védett webes API-hoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy asztali mintaalkalmazást használt a következőkre:

* Bejelentkezés egyéni bejelentkezési lappal
* Bejelentkezés közösségi identitásszolgáltatóval
* Azure AD B2C-fiók létrehozása
* Az Azure AD B2C által védett webes API hívása

Most már hozzákezdhet saját Azure AD B2C-bérlőjének létrehozásához.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](tutorial-create-tenant.md)
