---
title: Egyoldalas alkalmazás konfigurálása – Microsoft identitásplatform | Azure
description: További információ egyoldalas alkalmazás létrehozásáról (az alkalmazás kódkonfigurációja)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f1e0bf44515aab18019b19b4f0a6f84183e5aac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160083"
---
# <a name="single-page-application-code-configuration"></a>Egyoldalas alkalmazás: Kód konfigurációja

További információ az egyoldalas alkalmazás (SPA) kódjának konfigurálásáról.

## <a name="msal-libraries-that-support-implicit-flow"></a>Implicit folyamatot támogató MSAL-függvénytárak

A Microsoft identity platform a következő Microsoft Authentication Library (MSAL) tárakat biztosítja az implicit folyamat támogatásához az iparág által ajánlott biztonsági eljárások alkalmazásával:  

| MSAL könyvtár | Leírás |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Egyszerű JavaScript-kódtár bármely olyan ügyféloldali webalkalmazásban, amely JavaScript- vagy SPA-keretrendszereken keresztül készült, például az Angular, a Vue.js és a React.js. |
| ![MSAL szögletes](media/sample-v2-code/logo_angular.png) <br/> [MSAL szögletes](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Burkolja a core MSAL.js könyvtár, hogy egyszerűsítse a használatát egyoldalas alkalmazásokban, amelyek az Angular keretrendszeren keresztül épülnek. Ez a könyvtár előzetes verzióban érhető el, és [ismert problémákat tartalmaz](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) bizonyos Szögletes verziókkal és böngészőkkel. |

## <a name="application-code-configuration"></a>Alkalmazáskód-konfiguráció

Az MSAL-tárban az alkalmazás regisztrációs adatai a tár inicializálása során konfigurációként lesznek átadhatók.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

A konfigurálható beállításokról az [Alkalmazás inicializálása az MSAL.js alkalmazással](msal-js-initializing-client-applications.md)című témakörben talál további információt.

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](scenario-spa-sign-in.md)
