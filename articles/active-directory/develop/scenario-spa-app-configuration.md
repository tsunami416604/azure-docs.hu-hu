---
title: Egyoldalas alkalmazás (az alkalmazás kódjának konfigurációja) – Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (az alkalmazás kódjának konfigurációja)
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
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891529"
---
# <a name="single-page-application---code-configuration"></a>Egyoldalas alkalmazás-kód konfiguráció

Megtudhatja, hogyan konfigurálhatja az egyoldalas alkalmazás (SPA) kódját.

## <a name="msal-libraries-supporting-implicit-flow"></a>Az implicit folyamatot támogató MSAL-kódtárak

A Microsoft Identity platform MSAL. js függvénytárat biztosít az implicit folyamat támogatásához az iparág ajánlott biztonsági eljárásainak használatával.  

Az implicit folyamatot támogató kódtárak a következők:

| MSAL-könyvtár | Leírás |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Egyszerű JavaScript-függvénytár bármely olyan ügyféloldali webalkalmazásban, amely JavaScript vagy SPA keretrendszerek használatával készült, például szögletes, Vue. js, reakciós. js stb. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | A Core MSAL. js függvénytár burkolója, amely egyszerűbbé teszi a szögletes keretrendszerrel létrehozott egyoldalas alkalmazásokban való használatát. Ez a könyvtár előzetes verzióban érhető el, és [ismert hibákat](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) tartalmaz bizonyos szögletes verziók és böngészők esetében. |

## <a name="application-code-configuration"></a>Alkalmazás kódjának konfigurálása

A MSAL-könyvtárban az alkalmazás regisztrációs adatai a könyvtár inicializálásakor a konfigurációnak megfelelően lettek átadva.

### <a name="javascript"></a>JavaScript

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
Az elérhető konfigurálható beállításokkal kapcsolatos további információkért lásd: [alkalmazás inicializálása a MSAL. js-szel](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bejelentkezés és kijelentkezés](scenario-spa-sign-in.md)
