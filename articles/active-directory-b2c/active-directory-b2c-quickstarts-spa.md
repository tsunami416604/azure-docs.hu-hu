---
title: "Teszt meghajtó az Azure AD B2C egyoldalas alkalmazás |} Microsoft Docs"
description: "Teszt meghajtó jelentkezzen be, regisztráció, profil szerkesztése, és alaphelyzetbe állítja a jelszót felhasználói útvonal be egy tesztkörnyezetben az Azure AD B2C használatával"
services: active-directory-b2c
documentationcenter: 
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 5a8a46af-28bb-4b70-a7f0-01a5240d0255
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 22da1ae317ba685d32f93d3331cf794b568891ec
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-single-page-application-configured-with-azure-ad-b2c"></a>A konfigurált Azure AD B2C egyoldalas alkalmazások tesztelése

## <a name="about-this-sample"></a>A minta ismertetése

Az Azure Active Directory B2C az alkalmazás, az üzleti és a védett ügyfelek felhő Identitáskezelés biztosít.  A gyors üzembe helyezés egylapos mintaalkalmazás bemutatásához használja:

* Használja a **regisztráció vagy bejelentkezés** házirend létrehozásához vagy a közösségi identitásszolgáltató vagy e-mail cím használatával helyi fiókkal jelentkezzen be. 
* **Az API felület meghívásakor** a megjelenített név lekérése az Azure AD B2C-vel védett erőforrás.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/downloads/) szoftvert a következő számítási feladatokkal:
    - **ASP.NET és webfejlesztés**

* [Node.js](https://nodejs.org/en/download/) telepítése

* Egy közösségi fiók vagy Facebook, Google, vagy a Twitteren. Ha egy közösségi fiók nem rendelkezik, érvényes e-mail címet szükség.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>A minta letöltése

[Töltse le, vagy klónozza a mintaalkalmazást](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) a Githubról.

## <a name="run-the-sample-application"></a>Futtassa a mintaalkalmazást

A minta futtatásához a Node.js parancssorból: 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

A konzolablakban a webalkalmazás fut a számítógépen a portnak a számát mutatja.

```
Listening on port 6420...
```

Nyissa meg `http://localhost:6420` egy webböngészőben a webalkalmazáshoz való hozzáférés.


![A böngészőben mintaalkalmazás](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>Fiók létrehozása

Kattintson a **bejelentkezési** gombra kattintva indítsa el az Azure AD B2C **regisztráció vagy bejelentkezés** munkafolyamat. Egy fiók létrehozásakor egy meglévő közösségi identitás-szolgáltató fiókja vagy egy e-mail fiókot is használhatja.

### <a name="sign-up-using-a-social-identity-provider"></a>Jelentkezzen egy közösségi identitásszolgáltató

Közösségi identitásszolgáltató használatával, kattintson a használni kívánt identitásszolgáltató gombjára. Ha e-mail címmel szeretné végrehajtani, Ugrás a [jelentkezzen egy e-mail címet](#sign-up-using-an-email-address) szakasz.

![Bejelentkezés vagy regisztráció szolgáltató](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

Hitelesítés (bejelentkezés) a közösségi fiókja hitelesítő adatait, és az alkalmazás az adatok olvasása a közösségi fiók használatával kell. Hozzáférés biztosítása, amelyet az alkalmazás profiladatok kérhetnek le a közösségi fiókot, amilyen például a nevét, és a település. 

![Hitelesítését és engedélyezését, közösségi fiók használatával](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

Az új profil fiókadatok előre megadott, a közösségi fiók adataival. 

![Új fiók regisztrációs profil részleteit](media/active-directory-b2c-quickstarts-spa/new-account-sign-up-profile-details-spa.png)

A megjelenített név, beosztás és város mezők frissítése, és kattintson a **Folytatás**.  Az értékek használják az Azure AD B2C-felhasználói fiók profilját.

Sikeresen létrehozott egy új Azure AD B2C által használt felhasználói fiók egy identitásszolgáltatóval. 

Következő lépés: [hívható meg egy erőforrás](#call-a-resource) szakasz.

### <a name="sign-up-using-an-email-address"></a>Jelentkezzen egy e-mail címet

Ha a hitelesítés egy közösségi fiókot használja, egy érvényes e-mail címet használ az Azure AD B2C-felhasználói fiókot is létrehozhat. Egy Azure AD B2C helyi felhasználói fiókot az Azure Active Directory, az identitás-szolgáltatóként. Az e-mail cím használatához kattintson a **nincs fiókja? Feliratkozás most** hivatkozásra.

![Bejelentkezés vagy regisztráció e-mail](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-email-spa.png)

Adjon meg egy érvényes e-mail címet, és kattintson a **ellenőrző kód küldése**. Érvényes e-mail címet kell az Azure AD B2C megkapta az ellenőrzőkódot. 

Adja meg az e-mailben kap, és kattintson a **ellenőrizze a kódot**.

A profiladatok, és kattintson **létrehozása**.

![Iratkozzon fel az új e-mail fiók](media/active-directory-b2c-quickstarts-spa/sign-up-new-account-profile-email-web.png)

Sikeresen létrehozott egy új Azure AD B2C helyi felhasználói fiókot.

## <a name="call-a-resource"></a>Egy erőforrás hívása

Miután bejelentkezett, kattintson a **Web API hívása** szeretné, hogy a megjelenített név a JSON-objektumként webes API-hívás által visszaadott gombra. 

![Webes API-válasz](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

## <a name="next-steps"></a>Következő lépések

A következő lépés, hogy a saját Azure AD B2C bérlő létrehozása és konfigurálása a minta futtatásához a bérlőnek a használatával. 

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure-portálon](active-directory-b2c-get-started.md)