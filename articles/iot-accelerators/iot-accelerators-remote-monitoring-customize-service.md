---
title: Szolgáltatás hozzáadása a távfigyelési megoldás felhasználói felületéhez – Azure | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan vehet fel egy új szolgáltatást a távfigyelési megoldás gyorsító webes felhasználói felületére.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447046"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni szolgáltatás hozzáadása a távfigyelési megoldás gyorsító webes felhasználói felületéhez

Ez a cikk bemutatja, hogyan vehet fel egy új szolgáltatást a távfigyelési megoldás gyorsító webes felhasználói felületére. A cikk a következőket írja le:

- Hogyan készítsünk egy helyi fejlesztési környezetben.
- Új szolgáltatás hozzáadása a webes felhasználói felülethez?

A cikkben szereplő példaszolgáltatás egy olyan rács adatait tartalmazza, amelyet az [Egyéni rács hozzáadása a Távoli figyelési megoldás gyorsító webfelhasználói felületéhez](iot-accelerators-remote-monitoring-customize-grid.md) bemutató útmutató cikk bemutatja, hogyan kell hozzáadni.

A React alkalmazásban a szolgáltatás általában egy háttérszolgáltatással kommunikál. Példák a távoli figyelési megoldás gyorsító közé tartoznak az IoT hub-kezelő vel és a konfigurációs mikroszolgáltatásokkal kommunikáló szolgáltatások.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek végrehajtásához a következő szoftverre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt hajtsa végre az [Egyéni lap hozzáadása a távoli figyelési megoldásgyorsító webes felhasználói felületútmutatójának](iot-accelerators-remote-monitoring-customize-page.md) lépéseit.

## <a name="add-a-service"></a>Szolgáltatás hozzáadása

Ha hozzá szeretne adni egy szolgáltatást a webes felhasználói felülethez, hozzá kell adnia a szolgáltatást meghatározó forrásfájlokat, és módosítania kell néhány meglévő fájlt, hogy a webes felhasználói felület tisztában legyen az új szolgáltatással.

### <a name="add-the-new-files-that-define-the-service"></a>A szolgáltatást meghatározó új fájlok hozzáadása

A kezdéshez az **src/walkthrough/services** mappa tartalmazza az egyszerű szolgáltatást meghatározó fájlokat:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

A szolgáltatások megvalósításának módjáról a Hiányzó [reaktív programozás bemutatása című témakörben olvashat bővebben.](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

**modell/példaModellek.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Másolja **az exampleService.js fájlt** az **src/services mappába,** és másolja az **exampleModels.js fájlt** az **src/services/models** mappába.

Frissítse az **index.js** fájlt az **src/services** mappában az új szolgáltatás exportálásához:

```js
export * from './exampleService';
```

Frissítse az **index.js** fájlt az **src/services/models** mappában az új modell exportálásához:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>A szolgáltatás hívásainak beállítása az üzletből

A kezdéshez az **src/walkthrough/store/reducers** mappa mintaszűkítőt tartalmaz:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Másolja **a exampleReducer.js fájlt** az **src/store/reducers** mappába.

Ha többet szeretne megtudni a szűkítő és **epics**, lásd [redux-megfigyelhető](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>A köztes szoftver konfigurálása

A köztes szoftver konfigurálásához adja hozzá a szűkítőt az **src/store** mappában lévő **rootReducer.js** fájlhoz:

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

Add hozzá az eposz a **rootEpics.js** fájlt a **src / store mappában:**

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

Ebben a cikkben megismerkedett a távoli figyelési megoldásgyorsító webes felhasználói felületén szolgáltatások hozzáadásához vagy testreszabásához rendelkezésre álló erőforrásokról.

Most már definiált egy szolgáltatást, a következő lépés az, hogy [egyéni rácshozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületéhez,](iot-accelerators-remote-monitoring-customize-grid.md) amely a szolgáltatás által visszaadott adatokat jeleníti meg.

A távfigyelési megoldásgyorsítóról a [Távoli figyelési architektúra című témakörben](iot-accelerators-remote-monitoring-sample-walkthrough.md)talál további általános tudnivalókat.
