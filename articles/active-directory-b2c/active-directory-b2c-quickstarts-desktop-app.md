---
title: "Teszt meghajtó egy Azure AD B2C asztali alkalmazás |} Microsoft Docs"
description: "Teszt meghajtó jelentkezzen be, regisztráció, profil szerkesztése, és alaphelyzetbe állítja a jelszót felhasználói útvonal be egy tesztkörnyezetben az Azure AD B2C használatával"
services: active-directory-b2c
documentationcenter: .net
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 86293627-26fb-4e96-a76b-f263f9a945bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 9653d86dd5635016512bf6e6fafbf7195145ed07
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-desktop-application-configured-with-azure-ad-b2c"></a>Azure AD B2C konfigurált asztali alkalmazások tesztelése

Az Azure Active Directory B2C az alkalmazás, az üzleti és a védett ügyfelek felhő Identitáskezelés biztosít.  A gyors üzembe helyezés használ egy minta Windows megjelenítési alaprendszer (WPF) egy asztali alkalmazás bemutatása:

* Használja a **regisztráció vagy bejelentkezés** házirend létrehozásához vagy a közösségi identitásszolgáltató vagy e-mail cím használatával helyi fiókkal jelentkezzen be. 
* **Az API felület meghívásakor** a megjelenített név lekérése az Azure AD B2C-vel védett erőforrás.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - **.NET asztali fejlesztési**

* Egy közösségi fiók vagy Facebook, Google, vagy a Twitteren. Ha egy közösségi fiók nem rendelkezik, érvényes e-mail címet szükség.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

[Töltse le, vagy klónozza a mintaalkalmazást](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) a Githubról.

## <a name="run-the-app-in-visual-studio"></a>Futtassa az alkalmazást a Visual Studióban

A minta alkalmazás projekt mappában nyissa meg a `active-directory-b2c-wpf.sln` a Visual Studio megoldás. 

Válassza ki **Debug > Start Debugging** össze és futtathatja az alkalmazást. 

## <a name="create-an-account"></a>Fiók létrehozása

Kattintson a **bejelentkezés** elindítani a **regisztráció vagy bejelentkezés** munkafolyamat. Egy fiók létrehozásakor egy meglévő közösségi identitás-szolgáltató fiókja vagy egy e-mail fiókot is használhatja.

![Mintaalkalmazás](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

### <a name="sign-up-using-a-social-identity-provider"></a>Jelentkezzen egy közösségi identitásszolgáltató

Közösségi identitásszolgáltató használatával, kattintson a használni kívánt identitásszolgáltató gombjára. Ha e-mail címmel szeretné végrehajtani, Ugrás a [jelentkezzen egy e-mail címet](#sign-up-using-an-email-address) szakasz.

![Bejelentkezés vagy regisztráció szolgáltató](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

Hitelesítés (bejelentkezés) a közösségi fiókja hitelesítő adatait, és az alkalmazás az adatok olvasása a közösségi fiók használatával kell. Hozzáférés biztosítása, amelyet az alkalmazás profiladatok kérhetnek le a közösségi fiókot, amilyen például a nevét, és a település. 

![Hitelesítését és engedélyezését, közösségi fiók használatával](media/active-directory-b2c-quickstarts-desktop-app/twitter-authenticate-authorize-wpf.png)

Az új profil fiókadatok előre megadott, a közösségi fiók adataival. Kívánja, kattintson a részletek módosíthatja **Folytatás**.

![Új fiók regisztrációs profil részleteit](media/active-directory-b2c-quickstarts-desktop-app/new-account-sign-up-profile-details-wpf.png)

Sikeresen létrehozott egy új Azure AD B2C által használt felhasználói fiók egy identitásszolgáltatóval. Bejelentkezés, miután a hozzáférési jogkivonat megjelenik-e a a *Token adatai* szövegmezőben. A hozzáférési jogkivonat a API erőforráshoz történő hozzáféréskor.

![Engedélyezési jogkivonat](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

Következő lépés: [a profil szerkesztése Ugrás](#edit-your-profile) szakasz.

### <a name="sign-up-using-an-email-address"></a>Jelentkezzen egy e-mail címet

Ha a hitelesítés egy közösségi fiókot használja, egy érvényes e-mail címet használ az Azure AD B2C-felhasználói fiókot is létrehozhat. Egy Azure AD B2C helyi felhasználói fiókot az Azure Active Directory, az identitás-szolgáltatóként. Az e-mail cím használatához kattintson a **nincs fiókja? Feliratkozás most** hivatkozásra.

![Bejelentkezés vagy regisztráció e-mail](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-email-wpf.png)

Adjon meg egy érvényes e-mail címet, és kattintson a **ellenőrző kód küldése**. Érvényes e-mail címet kell az Azure AD B2C megkapta az ellenőrzőkódot.

Adja meg az e-mailben kap, és kattintson a **ellenőrizze a kódot**.

A profiladatok, és kattintson **létrehozása**.

![Iratkozzon fel az új e-mail fiók](media/active-directory-b2c-quickstarts-desktop-app/sign-up-new-account-profile-email-wpf.png)

Sikeresen létrehozott egy új Azure AD B2C helyi felhasználói fiókot. Bejelentkezés, miután a hozzáférési jogkivonat megjelenik-e a a *Token adatai* szövegmezőben. A hozzáférési jogkivonat a API erőforráshoz történő hozzáféréskor.

![Engedélyezési jogkivonat](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="edit-your-profile"></a>Szerkesztheti a profilját

Az Azure Active Directory B2C engedélyezése a felhasználók számára, frissítse a profilokat lehetőségeket kínál. Kattintson a **profilszerkesztés** a létrehozott profil szerkesztése.

![Profil szerkesztése](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

Válassza ki a létrehozott fiókkal társított identitásszolgáltató. Ha Twitter az identitás-szolgáltatóként használ, a fiók létrehozásakor, például válassza az Twitter módosítani a hozzárendelt profil részleteit.

![Válassza ki a profilhoz társított szolgáltató szerkesztése](media/active-directory-b2c-quickstarts-desktop-app/edit-account-choose-provider-wpf.png)

Módosítsa a **megjelenített név** vagy **Város**. 

![Profil frissítése](media/active-directory-b2c-quickstarts-desktop-app/update-profile-wpf.png)

Megjelenik egy új hozzáférési jogkivonat a *Token info* szövegmezőben. Ha szeretné ellenőrizni a profil módosításai, másolja és illessze be a hozzáférési jogkivonat a token dekóder https://jwt.ms.

![Engedélyezési jogkivonat](media/active-directory-b2c-quickstarts-desktop-app/twitter-auth-token.png)

## <a name="access-a-resource"></a>Elért egy erőforrást

Kattintson a **API hívása** indítson egy lekérdezést az Azure AD B2C biztonságos erőforrás https://fabrikamb2chello.azurewebsites.net/hello. 

![API hívása](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

Az alkalmazás tartalmazza a hozzáférési jogkivonat jelenik meg a *Token info* beviteli mező a kérelemben. Az API-t küld vissza a megjelenített névbe, a hozzáférési jogkivonat található.

## <a name="next-steps"></a>Következő lépések

A következő lépés, hogy a saját Azure AD B2C bérlő létrehozása és konfigurálása a minta futtatásához a bérlőnek a használatával. 

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure-portálon](active-directory-b2c-get-started.md)
