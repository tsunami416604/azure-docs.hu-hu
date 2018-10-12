---
title: A panel ad hozzá a távoli figyelési megoldás felhasználói felület – Azure |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá egy új panel az irányítópulton, a távoli figyelési megoldás gyorsító webes felhasználói felületen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 656a2375b8b1033e9b698d5d8e1e2bbc32b32a79
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094569"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Egy egyéni panel hozzáadása az irányítópulthoz, a távoli figyelési megoldás gyorsító webes felhasználói felületen

Ez a cikk bemutatja, hogyan adhat hozzá egy új panel, egy irányítópult lapra a távoli figyelési megoldás gyorsító webes felhasználói felületen. A cikk ismerteti:

- Hogyan készítheti elő a helyi fejlesztési környezetet.
- Hogyan adhat hozzá egy új panel a webes felhasználói felületének egy irányítópult-oldalon.

Ebben a cikkben a példa panel megjeleníti a meglévő irányítópult-oldalon.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések végrehajtásához szüksége van a következő szoftvereknek telepítve a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

Hajtsa végre a lépéseket a [egyéni lap hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-page.md) a cikk a folytatás előtt.

## <a name="add-a-panel"></a>Adjon hozzá egy panel

A webes felhasználói felületen ad hozzá egy panel, szüksége adhat hozzá a forrásfájlokat, amelyek meghatározzák a panelen, majd a panelen megjelennek az irányítópulton.

### <a name="add-the-new-files-that-define-the-panel"></a>Adja hozzá az új fájlokat, amelyek meghatározzák a panel

Az első lépésekhez, a **src/forgatókönyv/összetevők/oldalak és irányítópult/panelek/examplePanel** mappa tartalmazza a fájlokat, amelyek meghatározzák egy panel, többek között:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Másolás a **src/forgatókönyv/összetevők/oldalak és irányítópult/panelek/examplePanel** mappát a **src/összetevők/oldalak és irányítópult/panelek** mappát.

Adja hozzá az alábbi adatok exportálása az **src/walkthrough/components/pages/dashboard/panels/index.js** fájlt:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>A Vezérlőpult hozzáadása az irányítópulthoz

Módosítsa a **src/components/pages/dashboard/dashboard.js** hozzáadása a panelen.

A példa panelen adhat import listája a panelek:

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

Adja hozzá a következő cella definíciójának az oldal tartalmát a rács:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-fly-out"></a>Az úszó tesztelése

Ha a webes felhasználói felület még nem fut helyi, futtassa a következő parancsot a tárház helyi példányának gyökérmappájában:

```cmd/sh
npm start
```

Az előző parancs futtatása helyileg, a felhasználói felület [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Keresse meg a **irányítópult** lap használatával jeleníthetők meg az új panelen.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az erőforrások segíteni fog hozzáadni, vagy alakítsa az irányítópultot a távoli figyelési megoldásgyorsító a webes felhasználói felületen.

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md).
