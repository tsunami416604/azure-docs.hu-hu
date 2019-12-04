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
ms.openlocfilehash: 5ec98a62aab0b8d87645f3f58dc28219abe4e4fc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766276"
---
# <a name="single-page-application-code-configuration"></a>Egyoldalas alkalmazás: kód konfigurálása

Megtudhatja, hogyan konfigurálhatja az egyoldalas alkalmazás (SPA) kódját.

## <a name="msal-libraries-that-support-implicit-flow"></a>Az implicit folyamatot támogató MSAL-kódtárak

A Microsoft Identity platform a következő Microsoft Authentication Library-(MSAL-) kódtárakat biztosítja az implicit folyamat támogatásához az iparág által ajánlott biztonsági eljárások használatával:  

| MSAL-könyvtár | Leírás |
|--------------|--------------|
| ![MSAL. js](media/sample-v2-code/logo_js.png) <br/> [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Egyszerű JavaScript-függvénytár bármely olyan ügyféloldali webalkalmazásban, amelyet JavaScript vagy SPA keretrendszerek, például a szögletes, a Vue. js és a reakciós. js használatával építettek. |
| ![MSAL szögletes](media/sample-v2-code/logo_angular.png) <br/> [MSAL szögletes](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Az alapszintű MSAL. js függvénytár burkolója, amely egyszerűbbé teszi a szögletes keretrendszeren alapuló egyoldalas alkalmazásokban való használatát. Ez a könyvtár előzetes verzióban érhető el, és [ismert hibákat](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) tartalmaz bizonyos szögletes verziók és böngészők esetében. |

## <a name="application-code-configuration"></a>Alkalmazás kódjának konfigurálása

Egy MSAL-könyvtárban a könyvtár inicializálásakor az alkalmazás regisztrációs adatai a konfigurációnak megfelelően továbbítódnak.

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
A konfigurálható beállításokkal kapcsolatos további információkért lásd: [alkalmazás inicializálása a MSAL. js-szel](msal-js-initializing-client-applications.md).

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Bejelentkezés és kijelentkezés](scenario-spa-sign-in.md)
