---
title: Single-page application (alkalmazás kód konfiguráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazás (az alkalmazás kódja konfigurációját)
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
ms.openlocfilehash: 6236f0fa2400113225e04ffd4884cf743d1e250a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138292"
---
# <a name="single-page-application---code-configuration"></a>Egyoldalas alkalmazás - konfigurációs kódot

Ismerje meg, hogyan konfigurálhatja a kódot az egyoldalas alkalmazás (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Implicit folyamat támogató MSAL kódtárak

A Microsoft identity platform biztosít MSAL.js szalagtár támogatása az implicit folyamat segítségével az iparág ajánlott biztonságos eljárásokat.  

Az implicit folyamatot támogató kódtárak a következők:

| Az MSAL könyvtár | Leírás |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | JavaScript kódtár egyszerű bármely ügyféloldali webes alkalmazás például az Angular, oldalon a Vue.js, React.js JavaScript SPA- vagy keretrendszerek használatával jönnek létre a használatra. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | A core MSAL.js könyvtár egyszerűsítése érdekében használja az Angular keretében létrehozott egyoldalas alkalmazások burkolót. Ez a kódtár előzetes verzióban érhető el, és rendelkezik [ismert problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) bizonyos Angular verziók és böngészők. |

## <a name="application-code-configuration"></a>Kód Alkalmazáskonfiguráció

A MSAL könyvtárban az alkalmazás regisztrációs adatok van át configuration kódtár inicializálása során.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
A rendelkezésre álló konfigurálható beállításokkal kapcsolatos további részletekért lásd: [MSAL.js alkalmazás inicializálása](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
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
