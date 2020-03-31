---
title: Kerülje az oldal újratöltését (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként kerülheti el az oldal újratöltését, amikor a Microsoft JavaScript-hitelesítési könyvtár (MSAL.js) használatával csendben szerzi be és újítja meg a jogkivonatokat.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084958"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Kerülje az oldal újratöltését, amikor a tokeneket csendesen szerzi be és újítja meg az MSAL.js használatával
A Microsoft Authentication Library for JavaScript (MSAL.js) rejtett `iframe` elemeket használ a tokenek csendes beszerzésére és megújítására a háttérben. Az Azure AD visszaadja a jogkivonatot a jogkivonat-kérelemben megadott regisztrált redirect_uri (alapértelmezés szerint ez az alkalmazás gyökérlapja). Mivel a válasz egy 302, azt eredményezi, hogy `redirect_uri` `iframe`a HTML megfelelő betöltése a . Általában `redirect_uri` az alkalmazás a gyökéroldal, és ez okozza az újratöltést.

Más esetekben, ha az alkalmazás gyökérlapjára való navigálás hitelesítést igényel, beágyazott `iframe` elemekhez vagy `X-Frame-Options: deny` hibához vezethet.

Mivel az MSAL.js nem utasíthatja el az Azure AD által kiadott 302-es értéket, és a visszaadott jogkivonat feldolgozásához szükséges, nem akadályozhatja meg a `redirect_uri` betöltést a `iframe`.

A teljes alkalmazás újbóli újratöltésének vagy az ebből eredő egyéb hibák elkerülése érdekében kövesse az alábbi megoldásokat.

## <a name="specify-different-html-for-the-iframe"></a>Másik HTML megadása az iframe-hez

Állítsa `redirect_uri` be a tulajdonságot egy egyszerű oldalra, amely nem igényel hitelesítést. Meg kell győződnie arról, `redirect_uri` hogy megegyezik a regisztrált Az Azure Portalon. Ez nem befolyásolja a felhasználó bejelentkezési élményét, mivel az MSAL elmenti a kezdőlapot, amikor a felhasználó elindítja a bejelentkezési folyamatot, és a bejelentkezés befejezése után visszairányítja a pontos helyre.

## <a name="initialization-in-your-main-app-file"></a>Inicializálás a fő alkalmazásfájlban

Ha az alkalmazás úgy van felépítve, hogy van egy központi Javascript-fájl, amely meghatározza az alkalmazás inicializálását, útválasztását `iframe` és egyéb dolgait, feltételesen betöltheti az alkalmazásmodulokat attól függően, hogy az alkalmazás betöltődik-e egy vagy sem. Példa:

A következő városban: AngularJS: app.js

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
További információ [az egyoldalas alkalmazások (SPA)](scenario-spa-overview.md) msal.js használatával történő létrehozásáról.