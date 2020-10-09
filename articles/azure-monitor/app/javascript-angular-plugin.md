---
title: Szögletes beépülő modul a Application Insights JavaScript SDK-hoz
description: Szögletes beépülő modul telepítése és használata Application Insights JavaScript SDK-hoz.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843892"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Szögletes beépülő modul a Application Insights JavaScript SDK-hoz

A Application Insights JavaScript SDK szögletes beépülő modulja a következőket teszi lehetővé:

- Az útválasztó változásainak követése
- Szögletes komponensek használati statisztikája

> [!WARNING]
> A szögletes beépülő modul nem ECMAScript 3 (ES3) kompatibilis.

## <a name="getting-started"></a>Első lépések

NPM-csomag telepítése:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Alapvető használat

Application Insights példányának beállítása az alkalmazás belépési összetevőjében:

```js
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Ha a `trackMetric` metódust szeretné használni a szögletes összetevő-használat nyomon követéséhez, adja hozzá `AngularPluginService` szolgáltatóként a fájlt a szolgáltatók listájában `app.module.ts` .

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Egy összetevő élettartamának nyomon követéséhez hívja `trackMetric` meg az `ngOnDestroy` adott összetevő metódusát. Az összetevő elpusztulása után egy olyan eseményt indít el, `trackMetric` amely a felhasználó időpontját és az összetevő nevét küldi el.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Következő lépések

- A JavaScript SDK-val kapcsolatos további tudnivalókért tekintse meg a [Application Insights JavaScript SDK dokumentációját](javascript.md) .
- [Szögletes beépülő modul a GitHubon](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)