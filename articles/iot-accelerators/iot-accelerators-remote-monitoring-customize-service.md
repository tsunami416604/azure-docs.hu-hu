---
title: Szolgáltatás hozzáadása a távoli figyelési megoldás felhasználói felületéhez – Azure | Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá új szolgáltatást a távoli figyelési megoldás webes felhasználói felületéhez.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 6689a6d8a19dd690aa19aeecd29ead35ef6c2db1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318347"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsított webes felhasználói felületéhez

Ez a cikk bemutatja, hogyan adhat hozzá új szolgáltatást a távoli figyelési megoldás webes felhasználói felületéhez. A cikk a következőket ismerteti:

- Helyi fejlesztési környezet előkészítése.
- Új szolgáltatás hozzáadása a webes felhasználói felülethez.

Az ebben a cikkben szereplő példában szereplő szolgáltatás olyan rácshoz biztosít adatkeretet, amely az [Egyéni rács hozzáadása a távoli figyelési megoldáshoz webes felhasználói felület](iot-accelerators-remote-monitoring-customize-grid.md) útmutatója című cikk bemutatja, hogyan adhat hozzá.

Egy reagáló alkalmazásban a szolgáltatás általában egy háttér-szolgáltatással kommunikál. A távoli figyelési megoldás gyorsító példái közé tartoznak azok a szolgáltatások, amelyek az IoT hub kezelőjével és a konfigurációs szolgáltatásokkal együtt működnek.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához a következő szoftverekre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt végezze el az [Egyéni lap hozzáadása a távoli figyelési megoldás gyorsítása webes kezelőfelülethez](iot-accelerators-remote-monitoring-customize-page.md) című témakör lépéseit.

## <a name="add-a-service"></a>Szolgáltatás hozzáadása

Egy szolgáltatás webes felhasználói felülethez való hozzáadásához hozzá kell adnia a szolgáltatást definiáló forrásfájlokat, és módosítania kell néhány meglévő fájlt, hogy a webes felhasználói felület tisztában legyen az új szolgáltatással.

### <a name="add-the-new-files-that-define-the-service"></a>Adja hozzá a szolgáltatást definiáló új fájlokat

Az első lépésekhez az **src/walkthrough/Services** mappa tartalmazza azokat a fájlokat, amelyek egyszerű szolgáltatást definiálnak:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

A szolgáltatások megvalósításával kapcsolatos további tudnivalókért tekintse meg [a hiányzó reaktív programozás bevezetését](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**modell/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Másolja **exampleService.js** a **src/Services** mappába, és másolja **exampleModels.js** a **src/Services/models** mappába.

Az új szolgáltatás exportálásához frissítse az **index.js** fájlt a **src/Services** mappában:

```js
export * from './exampleService';
```

Frissítse az **index.js** fájlt az **src/Services/models** mappában az új modell exportálásához:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>A szolgáltatáshoz intézett hívások beállítása az áruházból

Az első lépésekhez az **src/walkthrough/Store/szűkítős** mappa egy minta-csökkentőt tartalmaz:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Másolja **exampleReducer.js** a **src/Store/szűkítős** mappába.

Ha többet szeretne megtudni a csökkentő és az **Epics**szolgáltatásról, tekintse meg a következőt: [Redux-megfigyelhető](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>A köztes middleware konfigurálása

A middleware konfigurálásához adja hozzá a szűkítőt a **rootReducer.js** fájlhoz az **src/Store** mappában:

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

Adja hozzá az Epics-t a **rootEpics.js** fájlhoz az **src/Store** mappában:

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

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan adhat hozzá vagy szabhat testre szolgáltatásokat a webes felhasználói felületen a távoli figyelési megoldás-gyorsító segítségével.

Most már definiált egy szolgáltatást, a következő lépés [egy egyéni rács hozzáadása a távoli figyelési megoldás webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-grid.md) , amely a szolgáltatás által visszaadott adatait jeleníti meg.

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md).
