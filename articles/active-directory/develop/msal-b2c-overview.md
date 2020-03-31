---
title: Az MSAL használata az Azure Active Directory B2CLearn szolgáltatással | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások számára az Azure AD B2C-vel való együttműködést, és a védett webes API-k hívására vonatkozó jogkivonatokat. Ezek a webes API-k lehetnek a Microsoft Graph, más Microsoft API-k, mások webes API-i vagy saját webes API-k.
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
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696452"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Az Azure Active Directory B2C szolgáltatással való együttműködést a Microsoft hitelesítési tárának használata

A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazásfejlesztők számára, hogy az [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)használatával hitelesítsék a felhasználókat a közösségi és helyi identitással. Az Azure AD B2C egy identitáskezelési szolgáltatás. Használatával testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálnak, jelentkezzenek be és kezeljék a profiljukat az alkalmazások használata során.

Az Azure AD B2C lehetővé teszi az alkalmazások felhasználói felületének márkajelzését és testreszabását is, hogy zökkenőmentes élményt nyújtson ügyfeleinek.

Ez az oktatóanyag bemutatja, hogyan használhatja az MSAL-t az Azure AD B2C-vel való együttműködést.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozta létre a saját [Azure AD B2C-bérlő,](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)hozzon létre egyet most. Egy meglévő Azure AD B2C-bérlő is használható.

## <a name="javascript"></a>JavaScript

A következő lépések bemutatják, hogy egy egyoldalas alkalmazás hogyan használhatja az Azure AD B2C-t a regisztrációhoz, a bejelentkezéshez és a védett webes API-k hívásához.

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

A hitelesítés megvalósításához először regisztrálnia kell az alkalmazást. Részletes lépésekért [lásd az alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) című témakört.

### <a name="step-2-download-the-sample-application"></a>2. lépés: A mintaalkalmazás letöltése

Töltse le a mintát zip-fájlként, vagy klónozza a GitHubról:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3. lépés: Hitelesítés konfigurálása

1. Nyissa meg az **index.html** fájlt a mintában.

1. Konfigurálja a mintát az ügyfélazonosítóval és a korábban az alkalmazás regisztrálása során rögzített kulccsal. Módosítsa a következő kódsorokat úgy, hogy az értékeket a könyvtár és az API-k nevére cseréli le:

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

Az oktatóanyag [felhasználói folyamatának](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) neve **B2C_1_signupsignin1**. Ha más felhasználói folyamatnevet használ, állítsa a **jogosultsági** értéket erre a névre.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>4. lépés: Az alkalmazás konfigurálása a használatra`b2clogin.com`

Használhatja `b2clogin.com` helyett átirányítási `login.microsoftonline.com` URL-t. Ezt az Azure AD B2C-alkalmazásban teszi meg, amikor identitásszolgáltatót állít be a regisztrációhoz és a bejelentkezéshez.

Használata `b2clogin.com` keretében a `https://your-tenant-name.b2clogin.com/your-tenant-guid` következő hatásai vannak:

- A Microsoft-szolgáltatások kevesebb helyet foglalnak el a cookie-k fejlécében.
- Az URL-ek már nem tartalmaznak hivatkozást a Microsoftra. Például az Azure AD B2C alkalmazás `login.microsoftonline.com`valószínűleg hivatkozik.

 A `b2clogin.com`használatához frissítenie kell az alkalmazás konfigurációját.  

- Állítsa a **validateAuthority** tulajdonságot a `false`, `b2clogin.com` hogy az átirányítások használatával is előfordulhassanak.

A következő példa bemutatja, hogyan állíthatja be a tulajdonságot:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Az Azure AD B2C-alkalmazás `login.microsoftonline.com` valószínűleg több helyen hivatkozik, például a felhasználói folyamat hivatkozások és a token végpontok. Győződjön meg arról, hogy az engedélyezési végpont, a `your-tenant-name.b2clogin.com`tokenvégpont és a kibocsátó frissítése megtörtént.

Kövesse [ezt az MSAL JavaScript mintát](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) a JavaScript MSAL Preview (MSAL.js) használatáról. A minta kap egy hozzáférési jogkivonatot, és az Azure AD B2C által védett API-t hív meg.

## <a name="next-steps"></a>További lépések

További információk:

- [Egyéni házirendek](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Felhasználói felület testreszabása](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)