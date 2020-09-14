---
title: Az Application Insights JavaScript SDK beépülő moduljának reagálása
description: Az reagáló beépülő modul telepítése és használata Application Insights JavaScript SDK-hoz.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3a11f77384c520bed9824841269be4ad998adba4
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056200"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Az Application Insights JavaScript SDK beépülő moduljának reagálása

A Application Insights JavaScript SDK beépülő moduljának reagálása lehetővé teszi a következőket:

- Útvonal-változások nyomon követése
- Az összetevők használati statisztikáinak megválaszolása

## <a name="getting-started"></a>Első lépések

NPM-csomag telepítése:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Alapvető használat

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Konfiguráció

| Name    | Alapértelmezett | Description                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| előzmények | null    | Az útválasztó előzményeinek megválaszolása. További információ: [reakciós útválasztó csomag dokumentációja](https://reactrouter.com/web/api/history). Ha meg szeretné tudni, hogyan érheti el az előzmények objektumot az összetevőkön kívül, tekintse meg a [reakciós útválasztó – gyakori kérdések](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Az összetevők használatának nyomon követése

A magasabb rendű összetevő-használat nyomon követéséhez alkalmazza a különböző reakciós összetevők használatát `withAITracking` .

Az eseményen keresztül méri a `ComponentDidMount` rendezvény idejét `ComponentWillUnmount` . Ahhoz azonban, hogy ez pontosabb legyen, ki kell vonnia azt az időpontot, amikor a felhasználó tétlen volt `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Ha meg szeretné tekinteni ezt a mérőszámot a Azure Portalben, navigáljon a Application Insights erőforráshoz, válassza a "metrikák" fület, és konfigurálja az üres diagramokat az egyéni metrika nevének megjelenítéséhez "az összetevő bevonásának ideje (másodpercben)" beállításnál válassza ki az összesítést (Sum, AVG stb.), és alkalmazza a felosztást "összetevő neve" értékre.

![Képernyőkép a diagramról, amely az egyéni metrika "az összetevő beosztásának időpontját (másodpercben)" felosztását jeleníti meg az "összetevő neve" alapján.](./media/javascript-react-plugin/chart.png)

Emellett egyéni lekérdezéseket is futtathat az Application Insights-adat felosztásához, amely szerint a jelentés és a vizualizációk előállítása a követelmények szerint történik. A Azure Portal navigáljon a Application Insights erőforráshoz az Áttekintés lap felső menüjéből válassza az "elemzés" lehetőséget, és futtassa a lekérdezést.

![Képernyőkép az egyéni metrika lekérdezési eredményeiről.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Akár 10 percet is igénybe vehet, amíg az új egyéni metrikák megjelennek az Azure Portalon.

## <a name="sample-app"></a>Minta alkalmazás

Tekintse meg a [Application Insights reagáló bemutatót](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Következő lépések

- A JavaScript SDK-val kapcsolatos további tudnivalókért tekintse meg a [Application Insights JavaScript SDK dokumentációját](javascript.md).
- A Kusto lekérdezési nyelvének megismeréséhez és a Log Analytics adatainak lekérdezéséhez tekintse meg a [naplózási lekérdezés áttekintését](../../azure-monitor/log-query/log-query-overview.md).
