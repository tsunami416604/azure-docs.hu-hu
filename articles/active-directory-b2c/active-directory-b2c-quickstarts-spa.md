---
title: Az Azure AD B2C-t használó egyoldalas alkalmazás kipróbálása
description: Gyors útmutató a felhasználókat az Azure Active Directory B2C használatával hitelesítő és regisztráló egyoldalas mintaalkalmazás kipróbáláshoz.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 2/13/2018
ms.author: davidmu
ms.openlocfilehash: 02a0515ff7c461370f29a511ac576d857676cb2b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-test-drive-an-azure-ad-b2c-enabled-single-page-app"></a>Gyors útmutató: Az Azure AD B2C-t használó egyoldalas alkalmazás kipróbálása

Az Azure Active Directory (Azure AD) B2C felhőalapú identitáskezelést nyújt az alkalmazás, az üzlet és az ügyfelek védelme érdekében. Az Azure AD B2C nyílt szabványú protokollokkal teszi lehetővé az alkalmazások hitelesítését közösségi hálózati és vállalati fiókokon.

Ebben a gyors útmutatóban az Azure AD B2C-t használó egyoldalas mintaalkalmazással jelentkezik be egy közösségi identitásszolgáltatót használva, és az Azure AD B2C által védett webes API-t hív meg.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) az **ASP.NET és webfejlesztési** számítási feladattal.
* [Node.js](https://nodejs.org/en/download/) telepítése
* Egy Facebook, Google, Microsoft vagy Twitter közösségi fiók.

## <a name="download-the-sample"></a>A minta letöltése

[Töltse le a zip-fájlt](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip), vagy a klónozza a mintául szolgáló webalkalmazást a GitHubról.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása

A minta futtatása a Node.js parancssorból: 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

A Node.js-alkalmazás megadja a helyi gazdagépen figyelt port számát.

```
Listening on port 6420...
```

Nyissa meg az alkalmazás `http://localhost:6420` URL-címét egy webböngészőben.

![Mintaalkalmazás a böngészőben](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>Fiók létrehozása

Kattintson a **Login** (Bejelentkezés) gombra az Azure AD B2C **regisztrációs vagy bejelentkezési** munkafolyamatának elindításához egy Azure AD B2C-szabályzat alapján. 

A minta több regisztrációs beállítást is támogat, beleértve a közösségi identitásszolgáltató használatát vagy helyi fiók e-mail-címmel való létrehozását. Ehhez a gyors útmutatóhoz Facebook, Google vagy Twitter közösségi identitásszolgáltatótól származó fiókot használjon. 

### <a name="sign-up-using-a-social-identity-provider"></a>Regisztráció közösségi identitásszolgáltatóval

Az Azure AD B2C a minta-webalkalmazáshoz egy Wingtip Toys nevű fiktív márka egyéni bejelentkezési lapját jeleníti meg. 

1. Ha közösségi identitásszolgáltatóval szeretne regisztrálni, kattintson a használni kívánt identitásszolgáltató gombjára.

    ![Bejelentkezési vagy regisztrációs szolgáltató](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Hitelesíti magát (bejelentkezik) a közösségi fiók hitelesítő adataival, és feljogosítja az alkalmazást, hogy beolvassa a közösségi fiók adatait. A hozzáférés biztosításával az alkalmazás profiladatokat kérhet le a közösségi fiókból, például a nevét és a települését. 

2. Fejezze be az identitásszolgáltató bejelentkezési folyamatát. Ha például a Twittert választotta, írja be a Twitter-fiók hitelesítő adatait, majd kattintson **Sign in** (Bejelentkezés) gombra.

    ![Hitelesítés és engedélyezés közösségi fiókkal](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

    Az új fiókprofil részletei előre ki vannak töltve a közösségi fiókja adataival. 

3. Frissítse a Display Name (Megjelenített név), a Job Title (Beosztás) és a City (Város) mezőt, majd kattintson a **Continue** (Folytatás) gombra.  A rendszer a beírt értékeket használja az Azure AD B2C felhasználói fiókprofilhoz.

    Sikeresen létrehozott egy új, identitásszolgáltatót használó Azure AD B2C felhasználói fiókot. 

## <a name="access-a-protected-web-api-resource"></a>Védett webes API-erőforrás elérése

Kattintson a **Call Web API** (Webes API hívása) gombra a megjelenítendő név JSON-objektumként való visszaadásához a webes API meghívásából. 

![Webes API válasza](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Az egyoldalas mintaalkalmazás egy Azure AD hozzáférési jogkivonatot tartalmaz a védett webes API-erőforrás felé, a JSON-objektum visszaadására szolgáló művelet végrehajtására irányuló kérésben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure AD B2C-bérlőt ahhoz is használhatja, ha más Azure AD B2C gyors útmutatókat vagy oktatóanyagokat is ki szeretne próbálni. Ha már nincs szüksége rá, akkor [törölheti az Azure AD B2C-bérlőt](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>További lépések

Ebben a gyors útmutatóban az Azure AD B2C-t használó, mintául szolgáló ASP.NET-es alkalmazással bejelentkezett egy egyéni bejelentkezési oldalon, bejelentkezett egy közösségi identitásszolgáltatót használva, létrehozott egy Azure AD B2C-fiókot, és meghívott egy, az Azure AD B2C által védett webes API-t. 

A következő lépés egy saját Azure AD B2C-bérlő létrehozása és a minta konfigurálása a bérlővel való futtatáshoz. 

> [!div class="nextstepaction"]
> [Azure Active Directory B2C-bérlő létrehozása az Azure Portalon](active-directory-b2c-get-started.md)