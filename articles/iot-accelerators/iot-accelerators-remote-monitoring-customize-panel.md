---
title: Panel hozzáadása a távfigyelési megoldás felhasználói felületéhez – Azure | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan vehet fel új panelt az irányítópultra a távfigyelési megoldásgyorsító webes felhasználói felületén.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447063"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni panel hozzáadása az irányítópulthoz a távfigyelési megoldásgyorsító webes felhasználói felületén

Ez a cikk bemutatja, hogyan vehet fel új panelt egy irányítópult-lapra a Távoli figyelési megoldásgyorsító webes felhasználói felületén. A cikk a következőket írja le:

- Hogyan készítsünk egy helyi fejlesztési környezetben.
- Új panel hozzáadása az irányítópult-laphoz a webes felhasználói felületen.

A cikk példapanelje a meglévő irányítópult-lapon jelenik meg.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek végrehajtásához a következő szoftverre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt hajtsa végre az [Egyéni lap hozzáadása a távoli figyelési megoldásgyorsító webes felhasználói felületének](iot-accelerators-remote-monitoring-customize-page.md) cikkében található lépéseket.

## <a name="add-a-panel"></a>Panel hozzáadása

Ha panelt szeretne hozzáadni a webes felhasználói felülethez, hozzá kell adnia a panelt meghatározó forrásfájlokat, majd módosítania kell az irányítópultot a panel megjelenítéséhez.

### <a name="add-the-new-files-that-define-the-panel"></a>A panelt meghatározó új fájlok hozzáadása

A kezdéshez az **src/walkthrough/components/pages/dashboard/panels/examplePanel** mappa tartalmazza a panelt meghatározó fájlokat, többek között a következőket:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Másolja az **src/walkthrough/components/pages/dashboard/panels/examplePanel** mappát az **src/components/pages/dashboard/panels** mappába.

Adja hozzá a következő exportálást az **src/walkthrough/components/pages/dashboard/panels/panels/index.js** fájlhoz:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>A panel hozzáadása az irányítópulthoz

A panel hozzáadásához módosítsa az **src/components/pages/dashboard/dashboard.js kapcsolót.**

Adja hozzá a példapanelt a panelekről származó behozatalok listájához:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Adja hozzá a következő celladefiníciót az oldaltartalom rácsához:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Az úszó panel tesztelése

Ha a webes felhasználói felület még nem fut helyileg, futtassa a következő parancsot a tárház helyi példányának gyökerében:

```cmd/sh
npm start
```

Az előző parancs helyileg futtatja a felhasználói felületet a ban. [http://localhost:3000/dashboard](http://localhost:3000/dashboard) Az **irányítópult** lapon megtekintheti az új panelt.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedhet a távoli figyelési megoldás gyorsítójában a webes felhasználói felület irányítópultjainak hozzáadásához vagy testreszabásához rendelkezésre álló erőforrásokról.

A távfigyelési megoldásgyorsítóról a [Távoli figyelési architektúra című témakörben](iot-accelerators-remote-monitoring-sample-walkthrough.md)talál további általános tudnivalókat.
