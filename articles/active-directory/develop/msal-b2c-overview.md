---
title: A MSAL használata a Azure Active Directory B2CLearn használatával | Azure
titleSuffix: Microsoft identity platform
description: A JavaScripthez készült Microsoft Authentication Library (MSAL. js) lehetővé teszi, hogy az alkalmazások működjenek a Azure AD B2C, és jogkivonatokat szerezzenek a biztonságos webes API-k meghívásához. Ezek a webes API-k lehetnek Microsoft Graph, más Microsoft API-k, a másoktól származó webes API-k vagy a saját webes API-k.
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
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534482"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>A JavaScript használata a Microsoft Authentication Library használatával Azure Active Directory B2C

[A Microsoft Authentication Library for JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) lehetővé teszi a JavaScript-fejlesztők számára, hogy [Azure Active Directory B2C (Azure ad B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)használatával hitelesítsék a felhasználókat a közösségi és helyi identitásokkal. A Azure AD B2C identitás-kezelési szolgáltatásként való használatával testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat az alkalmazások használatakor.

A Azure AD B2C lehetővé teszi, hogy az alkalmazások felhasználói felületét a hitelesítési folyamat során kialakítsa és testreszabja, hogy zökkenőmentes élményt nyújtson ügyfeleinek.

Ez a cikk bemutatja, hogyan használható a MSAL. js a Azure AD B2C és a fontos pontok összefoglalásához, amelyekről tisztában kell lennie. A teljes vitát és oktatóanyagot a [Azure ad B2C dokumentációjában](https://docs.microsoft.com/azure/active-directory-b2c/overview)tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre saját [Azure ad B2C bérlőt](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), kezdjen el létrehozni egyet most (ha már rendelkezik ilyennel, használhat meglévő Azure ad B2C bérlőt is).

Ez a bemutató két részből áll:

- webes API-k elleni védelem.
- egy egyoldalas alkalmazás regisztrálása hitelesítéshez és *a webes API* meghívásához.

## <a name="nodejs-web-api"></a>NODE.js webes API

> [!NOTE]
> Jelenleg a Node-hoz készült MSAL. js még fejlesztés alatt áll (lásd az [ütemtervet](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Addig is javasoljuk, hogy használja a [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad)-t, a Microsoft által fejlesztett és támogatott Node. js-hez készült hitelesítési könyvtárat.

A következő lépések bemutatják, hogyan használhatják a **webes API** a Azure ad B2Ct saját maga elleni védelemre, és hogy a kijelölt hatókörök elérhetők legyenek egy ügyfélalkalmazás számára.

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

A webes API-k Azure AD B2C-mel való ellátásához először regisztrálnia kell. Lásd: [az alkalmazás regisztrálása](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) a részletes lépésekhez.

### <a name="step-2-download-the-sample-application"></a>2. lépés: a minta alkalmazás letöltése

Töltse le a mintát zip-fájlként, vagy klónozást a GitHubról:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>3. lépés: a hitelesítés konfigurálása

1. Nyissa `config.js` meg a fájlt a mintában.

2. Konfigurálja a mintát az alkalmazás regisztrálásakor korábban beszerzett hitelesítő adatokkal. Módosítsa az alábbi kódrészleteket úgy, hogy lecseréli az értékeket a clientID, a gazdagépre, a tenantId és a házirend nevére.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

További információkért tekintse meg ezt a [Node. js B2C webes API-mintát](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

A következő lépések bemutatják, hogyan használható egy **egyoldalas alkalmazás** a Azure ad B2C a regisztrációhoz, a bejelentkezéshez és a védett webes API meghívásához.

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

A hitelesítés megvalósításához először regisztrálnia kell az alkalmazást. Lásd: [az alkalmazás regisztrálása](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) a részletes lépésekhez.

### <a name="step-2-download-the-sample-application"></a>2. lépés: a minta alkalmazás letöltése

Töltse le a mintát zip-fájlként, vagy klónozást a GitHubról:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3. lépés: a hitelesítés konfigurálása

Az alkalmazás konfigurálása két fontos szempontot mutat be:

- API-végpont és elérhető hatókörök konfigurálása
- Hitelesítési paraméterek és jogkivonat-hatókörök konfigurálása

1. Nyissa `apiConfig.js` meg a fájlt a mintában.

2. Konfigurálja a mintát a webes API regisztrálása során korábban beszerzett paraméterekkel. Módosítsa a kód következő sorait úgy, hogy lecseréli az értékeket a webes API-ra és a feltehetően elérhető hatókörökre.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Nyissa `authConfig.js` meg a fájlt a mintában.

4. Konfigurálja a mintát az egyoldalas alkalmazás regisztrálása során korábban beszerzett paraméterekkel. Módosítsa a kód következő sorait úgy, hogy lecseréli az értékeket a ClientId, a szolgáltatói metaadatok és a jogkivonat-kérelmek hatókörével.

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

További információkért tekintse meg ezt a [JavaScript B2C egyoldalas alkalmazás mintát](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>További lépések

További információk:
- [Felhasználói folyamatok](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Egyéni szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Felhasználói felület testreszabása](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
