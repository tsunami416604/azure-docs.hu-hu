---
title: Panel hozzáadása a távoli figyelési megoldás felhasználói felületéhez – Azure | Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá új panelt az irányítópulthoz a távoli figyelési megoldás webes felhasználói felületén.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 1dcca8409022ba4cf1f988b7c777e3a1fa511060
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318364"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni panel hozzáadása az irányítópulthoz a távoli figyelési megoldás gyorsító webes FELÜLETén

Ez a cikk bemutatja, hogyan adhat hozzá egy új panelt a távoli figyelési megoldás webes felhasználói felületének irányítópult-lapjához. A cikk a következőket ismerteti:

- Helyi fejlesztési környezet előkészítése.
- Új panel hozzáadása egy irányítópult-laphoz a webes felhasználói felületen.

A jelen cikk példa panelje a meglévő irányítópult oldalon jelenik meg.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához a következő szoftverekre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt végezze el az [Egyéni lap hozzáadása a távoli figyelési megoldás-gyorsító webes felhasználói felülethez](iot-accelerators-remote-monitoring-customize-page.md) című cikket.

## <a name="add-a-panel"></a>Panel hozzáadása

Ha panelt szeretne felvenni a webes felhasználói felületre, fel kell vennie a panelt definiáló forrásfájlokat, majd módosítania kell az irányítópultot a panel megjelenítéséhez.

### <a name="add-the-new-files-that-define-the-panel"></a>Adja hozzá a panelt definiáló új fájlokat

A kezdéshez az **src/walkthrough/Components/Pages/Dashboard/panelek/examplePanel** mappa tartalmazza a panelt definiáló fájlokat, beleértve a következőket:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Másolja a **src/walkthrough/Components/Pages/Dashboard/panelek/examplePanel** mappát a **src/Components/Pages/Dashboard/Panels** mappába.

Adja hozzá a következő exportálást a **src/walkthrough/Components/Pages/Dashboard/panelek/index.js** fájlba:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>A panel hozzáadása az irányítópulthoz

A panel hozzáadásához módosítsa a **src/Components/Pages/Dashboard/dashboard.js** elemet.

Adja hozzá a példa panelt a panelekből származó importálások listájához:

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

Adja hozzá a következő cella-definíciót a rácshoz az oldal tartalmában:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>A menü tesztelése

Ha a webes felhasználói felület még nem fut helyileg, futtassa a következő parancsot az adattár helyi példányának gyökerében:

```cmd/sh
npm start
```

Az előző parancs helyileg futtatja a felhasználói felületet `http://localhost:3000/dashboard` . Az új panel megtekintéséhez navigáljon az **irányítópult** lapra.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogy milyen erőforrások érhetők el az irányítópultok webes felhasználói felületen való hozzáadásához és testreszabásához a távoli figyelési megoldás-gyorsító segítségével.

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md).
