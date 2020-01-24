---
title: Az oldal újratöltésének elkerülése (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy miként kerülheti el a lapok újratöltését a tokenek a Microsoft-hitelesítési kódtár használatával való csendes beszerzése és megújítása közben a JavaScripthez (MSAL. js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e68798861d5799a4314bd9cd9b2eeeadb926a90f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696146"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>A MSAL. js használatával a tokenek beszerzése és megújítása előtt Kerülje a lap újratöltését
A JavaScripthez készült Microsoft Authentication Library (MSAL. js) rejtett `iframe` elemeket használ a jogkivonatok háttérben való csendes beszerzéséhez és megújításához. Az Azure AD visszaadja a tokent a jogkivonat-kérelemben megadott regisztrált redirect_urinak (alapértelmezés szerint ez az alkalmazás legfelső szintű lapja). Mivel a válasz 302, a `iframe`a betöltéshez `redirect_uri` megfelelő HTML-kódot eredményez. Általában az alkalmazás `redirect_uri` a legfelső szintű lap, ezért az újratöltést okoz.

Más esetekben, ha az alkalmazás főoldalára való navigáláshoz hitelesítés szükséges, a beágyazott `iframe` elemeket vagy `X-Frame-Options: deny` hibát okozhat.

Mivel a MSAL. js nem tudja elhagyni az Azure AD által kiadott 302-et, és a visszaadott jogkivonat feldolgozásához szükséges, nem tudja megakadályozni, hogy a `redirect_uri` betöltse a `iframe`.

Ha el szeretné kerülni, hogy a teljes alkalmazás újratöltődik újra, vagy más hibák okozzák a problémát, kövesse ezeket a megoldásokat.

## <a name="specify-different-html-for-the-iframe"></a>Különböző HTML-elemek megadása az IFRAME számára

Állítsa a konfiguráció `redirect_uri` tulajdonságát egy egyszerű lapra, amelyhez nincs szükség hitelesítésre. Győződjön meg arról, hogy az megfelel a Azure Portalban regisztrált `redirect_uri`nak. Ez nem befolyásolja a felhasználó bejelentkezési folyamatát, mivel a MSAL menti a kezdőlapot, amikor a felhasználó megkezdi a bejelentkezési folyamatot, és a bejelentkezés befejeződése után visszairányítja a pontos helyre.

## <a name="initialization-in-your-main-app-file"></a>Inicializálás a fő alkalmazás fájljában

Ha az alkalmazás úgy van strukturálva, hogy van egy központi JavaScript-fájl, amely meghatározza az alkalmazás inicializálási, útválasztási és egyéb elemeit, akkor az alkalmazás-modulok feltételeit attól függően is betöltheti, hogy az alkalmazás betöltődik-e `iframe` vagy sem. Példa:

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

## <a name="next-steps"></a>Következő lépések
További információ az [egyoldalas alkalmazások (Spa)](scenario-spa-overview.md) a MSAL. js használatával történő létrehozásáról.