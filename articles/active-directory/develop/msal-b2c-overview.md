---
title: Ismerje meg, hogyan integrálható az Azure AD B2C használatával a Microsoft-hitelesítési tár (MSAL)
description: A Microsoft-hitelesítési tár (MSAL) lehetővé teszi az alkalmazásfejlesztők integrálása az Azure AD B2C-vel, és annak érdekében, hogy a védett webes API-jainak hívására szerzi be a jogkivonatokat. Webes API-k a Microsoft Graph, más Microsoft APIS, külső webes API-k vagy saját webes API-t is lehet.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87bd8834f840c2246bf3adc1d1f9cd9b8f635915
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191021"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>A Microsoft-hitelesítési tár (MSAL) integrálása az Azure Active Directory B2C-vel

A Microsoft-hitelesítési tár (MSAL) lehetővé teszi, hogy a felhasználók hitelesítése a közösségi és a helyi identitások segítségével az alkalmazásfejlesztők [Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). Az Azure Active Directory (Azure AD) B2C egy identitáskezelő szolgáltatás, amely lehetővé teszi az ügyfelek regisztrációjának és bejelentkezésének testreszabását és felügyeletét, valamint a profiljuk kezelését az alkalmazások használatakor.

Az Azure Active Directory (Azure AD) B2C is lehetővé teszi saját arculat és testre szabhatja a felhasználói felület (UI) a zökkenőmentes felhasználói élményt nyújtson az ügyfél-alkalmazások.

Ez az oktatóanyag bemutatja, hogyan integrálhatja az Azure Active Directory (Azure AD) B2C-vel a Microsoft-hitelesítési tár (MSAL) használatával.


## <a name="prerequisites"></a>Előfeltételek

Ha még nem hozott létre a saját [Azure AD B2C-bérlő](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), hozzon létre egyet most. Használhat egy meglévő Azure AD B2C-bérlőben. 

## <a name="javascript"></a>Javascript

A következő lépéseket mutatja be, hogyan egy egyoldalas alkalmazás Azure AD B2C használata felhasználói regisztrációs, bejelentkezési, és védett webes API-hívás.

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

Hitelesítés végrehajtására, akkor először regisztrálja az alkalmazást. Regisztrálja az alkalmazást, kövesse az [regisztrálhatja alkalmazását](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) a részletes lépéseket.

### <a name="steps-2-download-applications"></a>2. lépést: Alkalmazások letöltése

Töltse le a zip-fájlt, vagy klónozza a Githubon található mintát.
>git-klón https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>3. lépést: Hitelesítés

1. Nyissa meg a minta az index.html fájlt.

2. Konfigurálja a mintát a alkalmazás Azonosítóját és kulcsát, az alkalmazás regisztrálása során korábban rögzített. A következő kódsorokat módosítsa az értékeket cserélje le a címtár és az API-k nevei:

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

Neve a [felhasználói folyamat](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) B2C_1_signupsignin1 a jelen oktatóanyagban használt van. Ha egy másik folyamat felhasználónevet használ, használja a felhasználói interakciósorozat neve hatóság érték.


### <a name="configure-application-to-use-b2clogincom"></a>Alkalmazás konfigurálása használatára `b2clogin.com`

Használható `b2clogin.com` helyén `login.microsoftonline.com` , átirányítási URL-cím beállításakor identitásszolgáltatót előfizetésre, és jelentkezzen be az Azure Active Directory (Azure AD) B2C-alkalmazásban.

**`b2clogin.com`** azaz 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` következő használható:

- A cookie-k fejlécében a Microsoft-szolgáltatások által felhasznált terület csökken.
- Az URL-címek már nem a Microsoft hivatkozást tartalmaz. Ha például az Azure AD B2C-alkalmazást valószínűleg hivatkozik login.microsoftonline.com


 "A b2clogin.com" használatához frissítenie az alkalmazás konfigurációját.  

1. Frissítés ValidateAuthority: állítsa be a **validateAuthority** tulajdonságot `false`. Amikor **validateAuthority** értéke hamis értékre, átirányítások jogosultak a b2clogin.com-on.

Az alábbi példa bemutatja, hogyan lehet, hogy a tulajdonság értéke:
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
> Az Azure AD B2C-alkalmazást valószínűleg több helyen, például a felhasználói folyamat hivatkozások és a token végpontok login.microsoftonline.com hivatkozik. Győződjön meg arról, hogy az engedélyezési végpont, a jogkivonat-végpont és a kiállító használatához saját bérlőt name.b2clogin.com frissítve lett-e.

Kövesse ezt [MSAL JS minta](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) használatával a Microsoft-hitelesítési tár előzetes verzióját (msal.js) JavaScript-hozzáférési jogkivonatot kapjon, és a egy API-t az Azure AD B2C által védett.

## <a name="next-steps"></a>További lépések

További információk:

- [Egyéni szabályzatok](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Felhasználói felület testreszabása](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)