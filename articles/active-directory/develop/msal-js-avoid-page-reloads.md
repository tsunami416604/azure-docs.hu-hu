---
title: Elkerülése érdekében (Microsoft-hitelesítési tár JavaScript-) lapon újra |} Az Azure
description: Ismerje meg a lap újra elkerülésével való beszerzéséhez és a csendes használatával a Microsoft-hitelesítési tár (MSAL.js) JavaScript-előfizetésüket megújító jogkivonatokat.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420468"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Kerülje a lapon újra való beszerzéséhez és a csendes az MSAL.js használatával előfizetésüket megújító tokenek
A Microsoft Authentication Library for JavaScript (MSAL.js) használ, rejtett `iframe` elemeit, amelyek beszerzése és megújítása jogkivonatok csendes a háttérben. Az Azure AD visszaküldi a jogkivonatot a jogkivonat a kérelemben megadott regisztrált redirect_uri tulajdonsága (alapértelmezés szerint ez a a az alkalmazás gyökér lap). Mivel a válasz egy 302, azonban azt eredményezi a HTML megfelelő a `redirect_uri` betöltött első a `iframe`. Általában az alkalmazás `redirect_uri` a legfelső szintű lapra, és ennek hatására, hogy töltse be újra.

Más esetben, ha ellenőrizheti, hogy az alkalmazás gyökér lap megköveteli a hitelesítést, azt vezethet a beágyazott `iframe` elemek vagy `X-Frame-Options: deny` hiba.

Mivel az MSAL.js nem vethető el az Azure AD által kibocsátott 302, és a folyamat a visszaadott jogkivonat szükséges, nem tudja megakadályozni az `redirect_uri` a betöltött első a `iframe`.

A teljes alkalmazás újbóli betöltéséből újra, vagy egyéb hibák, ennek oka elkerülése érdekében ezek a megoldások végrehajtásával.

## <a name="specify-different-html-for-the-iframe"></a>Adja meg a különböző HTML-jét az IFRAME-keret

Állítsa be a `redirect_uri` egy egyszerű lap, hitelesítést nem igénylő konfigurációs tulajdonság. Győződjön meg arról, hogy megegyezik a kell a `redirect_uri` regisztrálva az Azure Portalon. Felhasználói bejelentkezési élmény ez nincs hatással, mert MSAL menti a kezdőlapon, amikor a felhasználó a bejelentkezési folyamat kezdődik, és átirányítja a felhasználót vissza pontos helyét bejelentkezési befejeződése után.

## <a name="initialization-in-your-main-app-file"></a>A fő alkalmazásfájl a inicializálása

Ha az alkalmazás felépítése, hogy egy központi Javascript-fájlt, amely meghatározza az alkalmazás inicializálása, Útválasztás és egyebek van, az alapján, hogy az alkalmazás betöltése az app-modulok feltételesen betöltheti egy `iframe` vagy sem. Példa:

In AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

Az Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>További lépések
Tudjon meg többet [egy egyoldalas alkalmazás (SPA) felépítésére](scenario-spa-overview.md) MSAL.js használatával.