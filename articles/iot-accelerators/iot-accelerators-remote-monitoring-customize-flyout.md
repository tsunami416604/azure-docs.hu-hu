---
title: A távoli figyelési megoldás felhasználói felület – Azure ad hozzá egy úszó |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá egy új úszó egy oldalon, a távoli figyelési megoldás gyorsító webes felhasználói felületen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 9ba58ca887332d2ea224320951b25031cacbef0d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094580"
---
# <a name="add-a-custom-fly-out-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egy egyéni úszó ad hozzá a távoli figyelési megoldás gyorsító webes felhasználói felületen

Ez a cikk bemutatja, hogyan adhat hozzá egy új úszó egy lapra a távoli figyelési megoldás gyorsító webes felhasználói felületen. A cikk ismerteti:

- Hogyan készítheti elő a helyi fejlesztési környezetet.
- Hogyan lehet egy új úszó hozzáadása egy laphoz a webes felhasználói felületen.

A példa úszó az ebben a cikkben az oldalon, a rácshoz jeleníti meg, amely a [adjon hozzá egy egyéni rács a távoli figyelési megoldás gyorsító webes felhasználói Felületére](iot-accelerators-remote-monitoring-customize-grid.md) útmutató a cikk bemutatja, hogyan adhat hozzá.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések végrehajtásához szüksége van a következő szoftvereknek telepítve a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt kell végeznie az alábbi cikkekben leírt lépéseket:

- [Egyéni oldal hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-page.md).
- [Egy egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-service.md)
- [Egy egyéni rács ad hozzá a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-fly-out"></a>Adjon hozzá egy úszó

Adjon hozzá egy úszó a webes felhasználói Felületére, szüksége adja hozzá a forrásfájlokat, amelyek meghatározzák az úszó, és néhány meglévő fájlokat, hogy a webes felhasználói felület az új összetevő tisztában módosításához.

### <a name="add-the-new-files-that-define-the-fly-out"></a>Adja hozzá az új fájlokat, amelyek meghatározzák az úszó

Az első lépésekhez, a **src/forgatókönyv/components/oldalak/pageWithFlyout/menük nélküli/exampleFlyout** mappa a fájlokat, amelyek meghatározzák egy úszó tartalmazza:

**exampleFlyout.container.js**

[!code-javascript[Example fly-out container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example fly-out container")]

**exampleFlyout.js**

[!code-javascript[Example fly-out](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example fly-out")]

Másolás a **src/forgatókönyv/components/oldalak/pageWithFlyout/menük nélküli** mappát a **példa src/components/oldalak** mappát.

### <a name="add-the-fly-out-to-the-page"></a>Az úszó hozzáadása az oldalhoz

Módosítsa a **src/components/pages/example/basicPage.js** a úszó hozzáadásához.

Adjon hozzá **bevásárlókocsiba** , a származó **összetevők/shared** , és adja hozzá az import **svgs** és **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Adjon hozzá egy **konstans** definíciója **closedFlyoutState** , és adja hozzá az állapot a konstruktorban:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Adja hozzá a következő funkciók az **BasicPage** osztály:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Adja hozzá a következő **konstans** érdekében a **render** függvény:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Adjon hozzá egy gombot, a helyi menü úszó megnyitásához:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Az oldal tartalmát valamilyen szöveget és a úszó tároló hozzáadása:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-fly-out"></a>Az úszó tesztelése

Ha a webes felhasználói felület még nem fut helyi, futtassa a következő parancsot a tárház helyi példányának gyökérmappájában:

```cmd/sh
npm start
```

Az előző parancs futtatása helyileg, a felhasználói felület [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Keresse meg a **példa** lapot, és kattintson **nyílt úszó menü**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az erőforrások hozzáadása vagy a webes felhasználói felület a távoli figyelési megoldásgyorsító oldalainak testreszabása segíti.

Most már meg van adva egy úszó egy oldal, a következő lépés az, hogy [a Vezérlőpult hozzáadása az irányítópulthoz, a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-panel.md).

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md).
