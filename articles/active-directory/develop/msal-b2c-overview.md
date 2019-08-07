---
title: Ismerje meg, hogyan működhetnek együtt az alkalmazások a Azure AD B2Cekkel a Microsoft Authentication Library használatával
description: A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások számára a Azure AD B2C és a jogkivonatok megszerzését a biztonságos webes API-k meghívásához. Ezek a webes API-k lehetnek Microsoft Graph, más Microsoft API-k, a másoktól származó webes API-k vagy a saját webes API-k.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 784ec507027d6ec0ac1b5288c101e2a76cab436e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835059"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>A Microsoft hitelesítési függvénytárának használata a Azure Active Directory B2C való együttműködéshez

A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások fejlesztői számára, hogy [Azure Active Directory B2C (Azure ad B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)használatával hitelesítsék a felhasználókat a közösségi és a helyi identitásokkal. Azure AD B2C egy Identitáskezelés szolgáltatás. A használatával testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat, amikor az alkalmazásaikat használják.

A Azure AD B2C lehetővé teszi az alkalmazások felhasználói felületének kiírását és testreszabását, hogy zökkenőmentes élményt nyújtson ügyfeleinek.

Ez az oktatóanyag azt mutatja be, hogyan használható a MSAL az Azure AD B2C való együttműködéshez.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre saját [Azure ad B2C bérlőt](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), hozzon létre egyet most. Meglévő Azure AD B2C bérlőt is használhat.

## <a name="javascript"></a>JavaScript

A következő lépések bemutatják, hogyan használható egy egyoldalas alkalmazás a Azure AD B2C a regisztrációhoz, a bejelentkezéshez és a védett webes API meghívásához.

### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása

A hitelesítés megvalósításához először regisztrálnia kell az alkalmazást. Lásd: [az alkalmazás regisztrálása](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) a részletes lépésekhez.

### <a name="step-2-download-the-sample-application"></a>2\. lépés: A mintaalkalmazás letöltése

Töltse le a mintát zip-fájlként, vagy klónozást a GitHubról:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3\. lépés: Hitelesítés konfigurálása

1. Nyissa meg az **index. html** fájlt a mintában.

1. Konfigurálja a mintát az alkalmazás regisztrálása során korábban rögzített alkalmazás-AZONOSÍTÓval és kulccsal. Módosítsa a kód következő sorait úgy, hogy lecseréli az értékeket a címtár és az API-k nevére:

   ```javascript
   // The current application coordinates were pre-registered in a B2C directory.

   const msalConfig = {
       auth:{
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
           b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
           webApi: 'http://localhost:5000/hello',
     };

   // create UserAgentApplication instance
   const myMSALObj = new UserAgentApplication(msalConfig);
   ```

Az oktatóanyagban szereplő [felhasználói folyamat](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) neve **B2C_1_signupsignin1**. Ha más felhasználói folyamatot használ, állítsa be a **szolgáltató** értékét erre a névre.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>4\. lépés: Az alkalmazás konfigurálása a használatára`b2clogin.com`

Átirányítási URL `b2clogin.com` - `login.microsoftonline.com` címként is használhatja. Ezt a Azure AD B2C alkalmazásban teheti meg, amikor beállít egy identitás-szolgáltatót a regisztrációhoz és a bejelentkezéshez.

`b2clogin.com` A környezetébena`https://your-tenant-name.b2clogin.com/your-tenant-guid` következő hatások használhatók:

- A Microsoft szolgáltatásai kevesebb helyet foglalnak el a cookie-fejlécben.
- Az URL-címek már nem tartalmaznak hivatkozást a Microsoftnak. A Azure AD B2C alkalmazás például valószínűleg a `login.microsoftonline.com`következőre hivatkozik:.

 A használatához `b2clogin.com`frissítenie kell az alkalmazás konfigurációját.  

- Állítsa a **validateAuthority** tulajdonságot `false`úgy, hogy az átirányítások `b2clogin.com` a használatával is előfordulhatnak.

Az alábbi példa bemutatja, hogyan állíthatja be a tulajdonságot:

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
> A Azure ad B2C alkalmazás valószínűleg több helyen `login.microsoftonline.com` hivatkozik, például a felhasználói folyamatok hivatkozásaira és a jogkivonat-végpontokra. Győződjön meg arról, hogy az engedélyezési végpont, a jogkivonat-végpont és a kiállító frissítve lett `your-tenant-name.b2clogin.com`a használatra.

Kövesse [ezt a MSAL JavaScript-mintát](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) a JavaScript MSAL előzetes verziójának használatával (MSAL. js). A minta egy hozzáférési jogkivonatot kap, és a Azure AD B2C által védett API-t hív meg.

## <a name="next-steps"></a>További lépések

További információk az alábbiakról:

- [Egyéni szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Felhasználói felület testreszabása](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)