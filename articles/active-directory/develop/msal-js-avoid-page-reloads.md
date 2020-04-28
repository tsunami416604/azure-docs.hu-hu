---
title: Az oldal újratöltésének elkerülése (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy miként kerülheti el a lapok újratöltését a tokenek a Microsoft-hitelesítési kódtár használatával való csendes beszerzése és megújítása közben a JavaScripthez (MSAL. js).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084958"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>A MSAL. js használatával a tokenek beszerzése és megújítása előtt Kerülje a lap újratöltését
A Microsoft hitelesítési könyvtára a JavaScripthez (MSAL. js) `iframe` rejtett elemeket használ a jogkivonatok csendes beszerzéséhez és megújításához a háttérben. Az Azure AD visszaadja a tokent a jogkivonat-kérelemben megadott regisztrált redirect_urinak (alapértelmezés szerint ez az alkalmazás legfelső szintű lapja). Mivel a válasz 302, az a következőhöz tartozó `redirect_uri` HTML-kódot eredményezi:. `iframe` Általában az alkalmazás `redirect_uri` a legfelső szintű lap, és ez az eszköz újratöltését okozza.

Más esetekben, ha az alkalmazás főoldalára való navigálás hitelesítés szükséges, a beágyazott `iframe` elemek vagy `X-Frame-Options: deny` hibák vezethetnek.

Mivel a MSAL. js nem tudja elhagyni az Azure AD által kiadott 302-et, és a visszaadott jogkivonat feldolgozásához szükséges, nem akadályozhatja meg a `redirect_uri` betöltését a `iframe`alkalmazásban.

Ha el szeretné kerülni, hogy a teljes alkalmazás újratöltődik újra, vagy más hibák okozzák a problémát, kövesse ezeket a megoldásokat.

## <a name="specify-different-html-for-the-iframe"></a>Különböző HTML-elemek megadása az IFRAME számára

Állítsa be `redirect_uri` a config tulajdonságot egy egyszerű lapra, amely nem igényel hitelesítést. Győződjön meg arról, hogy az megfelel a `redirect_uri` regisztrált Azure Portal. Ez nem befolyásolja a felhasználó bejelentkezési folyamatát, mivel a MSAL menti a kezdőlapot, amikor a felhasználó megkezdi a bejelentkezési folyamatot, és a bejelentkezés befejeződése után visszairányítja a pontos helyre.

## <a name="initialization-in-your-main-app-file"></a>Inicializálás a fő alkalmazás fájljában

Ha az alkalmazás úgy van strukturálva, hogy az alkalmazás inicializálási, útválasztási és egyéb elemeinek definiálására egy központi JavaScript-fájl van, akkor az alkalmazás moduljait feltételesen is betöltheti, attól függően, `iframe` hogy az alkalmazás betöltődik-e vagy sem. Például:

A AngularJS: app. js fájlban

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

Szögletes: app. Module. TS

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
További információ az [egyoldalas alkalmazások (Spa)](scenario-spa-overview.md) a MSAL. js használatával történő létrehozásáról.