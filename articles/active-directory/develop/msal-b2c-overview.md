---
title: Az MSAL használata az Azure Active Directory B2CLearn szolgáltatással | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library for JavaScript (MSAL.js) lehetővé teszi az alkalmazások számára, hogy együttműködjenek az Azure AD B2C-vel, és jogkivonatokat szerezzenek be a biztonságos webes API-k hívásához. Ezek a webes API-k lehetnek a Microsoft Graph, más Microsoft API-k, mások webes API-i vagy saját webes API-k.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: dc8a330bc09f37f7941534ed7c17d1ffd14d08c5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875962"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Az Azure Active Directory B2C szolgáltatással való munka a JavaScript hez tartozó Microsoft Authentication Library for JavaScript használatával

[A Microsoft Authentication Library for JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) lehetővé teszi a JavaScript-fejlesztők számára, hogy az [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)segítségével hitelesítsék a felhasználókat a közösségi és helyi identitással. Az Azure AD B2C identitáskezelési szolgáltatásként való használatával testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezzenek be és kezeljék a profiljukat az alkalmazások használata során.

Az Azure AD B2C lehetővé teszi az alkalmazások felhasználói felületének márkajelzését és testreszabását a hitelesítési folyamat során, hogy zökkenőmentes élményt nyújtson ügyfeleinek.

Ez a cikk bemutatja, hogyan használhatja az MSAL.js azure AD B2C használatával, és összefoglalja a legfontosabb pontokat, amelyeket ismernie kell. A teljes körű vita és oktatóanyag, kérjük, olvassa el [az Azure AD B2C dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozta létre a saját [Azure AD B2C-bérlő,](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)kezdje létre most (is használhatja a meglévő Azure AD B2C bérlő, ha már rendelkezik).

Ez a bemutató két részből áll:

- hogyan védheti meg a webes API-t.
- hogyan regisztrálhat egy egyoldalas alkalmazást a *webes* API hitelesítéséhez és hívásához.

## <a name="nodejs-web-api"></a>Node.js webes API

> [!NOTE]
> Ebben a pillanatban, MSAL.js a Node még fejlesztés alatt áll (lásd az [ütemtervet](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Addig is javasoljuk, hogy használja [a Passport-azure-ad,](https://github.com/AzureAD/passport-azure-ad)a Microsoft által kifejlesztett és támogatott Node.js hitelesítési könyvtárhasználatát.

A következő lépések bemutatják, hogyan használhatja egy **webes API az** Azure AD B2C-t saját maga védelmére és a kiválasztott hatókörök ügyfélalkalmazások számára való elérhetővé tétele érdekében.

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

A webes API védelme az Azure AD B2C, először regisztrálnia kell azt. Részletes lépésekért [lásd az alkalmazás regisztrálása](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) című témakört.

### <a name="step-2-download-the-sample-application"></a>2. lépés: A mintaalkalmazás letöltése

Töltse le a mintát zip-fájlként, vagy klónozza a GitHubról:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>3. lépés: Hitelesítés konfigurálása

1. Nyissa `config.js` meg a mintát a fájlt.

2. Konfigurálja a mintát az alkalmazás korábbi, az alkalmazás regisztrálása során beszerzett hitelesítő adatokkal. Módosítsa a következő kódsorokat az értékek ügyfélazonosító, állomás, tenantId és házirend nevének lecserélésével.

```JavaScript
const clientID = "<Application ID for your Node.js Web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

További információért tekintse meg ezt a [Node.js B2C webes API-mintát.](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)

---

## <a name="javascript-spa"></a>JavaScript SPA

A következő lépések bemutatják, hogy egy **egyoldalas alkalmazás** hogyan használhatja az Azure AD B2C-t a regisztrációhoz, a bejelentkezéshez és a védett webes API-k hívásához.

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

A hitelesítés megvalósításához először regisztrálnia kell az alkalmazást. Részletes lépésekért [lásd az alkalmazás regisztrálása](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) című témakört.

### <a name="step-2-download-the-sample-application"></a>2. lépés: A mintaalkalmazás letöltése

Töltse le a mintát zip-fájlként, vagy klónozza a GitHubról:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3. lépés: Hitelesítés konfigurálása

Az alkalmazás konfigurálása két szempontból érdekes:

- API-végpontok és elérhető hatókörök konfigurálása
- Hitelesítési paraméterek és tokenhatókörök konfigurálása

1. Nyissa `apiConfig.js` meg a mintát a fájlt.

2. Konfigurálja a mintát a korábban a webes API regisztrálása során kapott paraméterekkel. Módosítsa a következő kódsorokat az értékek nek a webes API és a kitett hatókörök címére való lecserélésével.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello" 
    };
   ```

3. Nyissa `authConfig.js` meg a mintát a fájlt.

4. Konfigurálja a mintát az egyoldalas alkalmazás regisztrálása során korábban kapott paraméterekkel. Módosítsa a következő kódsorokat az értékek ügyfélazonosítóval, a hatóságmetaadatokkal és a tokenkérelem-hatókörekkel való lecserélésével.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

További információért, kijelenti magát ez [JavaScript B2C egyoldalas alkalmazás minta](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>További lépések

További információk:
- [Felhasználói folyamatok](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Egyéni szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Felhasználói felület testreszabása](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
