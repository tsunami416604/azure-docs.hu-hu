---
title: Szolgáltatás hozzáadása a távoli figyelési megoldás felhasználói felület – Azure |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá egy új szolgáltatás a távoli figyelési megoldás gyorsító webes felhasználói felületen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094577"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egy egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen

Ez a cikk bemutatja, hogyan adhat hozzá egy új szolgáltatás a távoli figyelési megoldás gyorsító webes felhasználói felületen. A cikk ismerteti:

- Hogyan készítheti elő a helyi fejlesztési környezetet.
- Hogyan adhat hozzá egy új szolgáltatás a webes felhasználói felületen.

A példa service ebben a cikkben biztosít az adatok egy rácsot, amely a [adjon hozzá egy egyéni rács a távoli figyelési megoldás gyorsító webes felhasználói Felületére](iot-accelerators-remote-monitoring-customize-grid.md) útmutató a cikk bemutatja, hogyan adhat hozzá.

A React alkalmazások esetén a szolgáltatás általában kommunikál egy háttér-szolgáltatás. A távoli figyelési megoldásgyorsító közé, hogy az IoT hub-kezelőből és a konfiguráció mikroszolgáltatások együttműködő szolgáltatásokat.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések végrehajtásához szüksége van a következő szoftvereknek telepítve a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

Hajtsa végre a lépéseket a [egyéni lap hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-page.md) útmutató a cikk a folytatás előtt.

## <a name="add-a-service"></a>Szolgáltatás hozzáadása

Szolgáltatás hozzáadása a webes felhasználói felületen, szüksége adhat hozzá a forrásfájlokat, amelyek meghatározzák a szolgáltatást, néhány fájlt, hogy ismeri az új szolgáltatás a webes felhasználói felületen.

### <a name="add-the-new-files-that-define-the-service"></a>Adja hozzá az új fájlokat, amelyek meghatározzák a szolgáltatás

Az első lépésekhez, a **src/forgatókönyv/szolgáltatások** mappa a fájlokat, amelyek meghatározzák egy egyszerű szolgáltatást tartalmazza:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Szolgáltatások bevezetése hogyan kapcsolatos további információkért lásd: [a reaktív programozási bemutatása, hogy hiányzó](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Másolás **exampleService.js** , a **src/szolgáltatások** mappát, és átmásolja **exampleModels.js** , a **src-services-modellekhez** mappát.

Frissítés a **index.js** fájlt a **src/szolgáltatások** mappát, amelybe exportálni az új szolgáltatást:

```js
export * from './exampleService';
```

Frissítés a **index.js** fájlt a **src-services-modellekhez** mappát, amelybe exportálni az új modell:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Az áruházból a szolgáltatás felé irányuló beállítása

Az első lépésekhez, a **csökkentő src/forgatókönyv/tároló** mappa tartalmaz egy minta nyomáscsökkentő:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Másolás **exampleReducer.js** , a **src/tároló/csökkentő** mappát.

További információ a nyomáscsökkentő és **Epics**, lásd: [redux rendszernek megfigyelhetőnek](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>A közbenső szoftver konfigurálása

Konfigurálja a közbenső szoftverek, adja hozzá a nyomáscsökkentő, hogy a **rootReducer.js** fájlt a **src/tároló** mappa:

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Adja hozzá a epics, hogy a **rootEpics.js** fájlt a **src/tároló** mappa:

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az erőforrások segíteni fog hozzáadni, vagy testre szabhatja a szolgáltatások a távoli figyelési megoldásgyorsító a webes felhasználói felületen.

Most már beállított egy szolgáltatás, a következő lépés az, hogy [adjon hozzá egy egyéni rács a távoli figyelési megoldás gyorsító webes felhasználói Felületére](iot-accelerators-remote-monitoring-customize-grid.md) , amely a szolgáltatás által visszaadott adatokat jeleníti meg.

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md).
