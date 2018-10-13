---
title: A távoli figyelési megoldás felhasználói felület – Azure ad hozzá egy úszó menü |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá egy új úszó menü egy oldalon, a távoli figyelési megoldás gyorsító webes felhasználói felületen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167426"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>A távoli figyelési megoldás gyorsító webes felhasználói felületen ad hozzá egy egyéni úszó menü

Ez a cikk bemutatja, hogyan adhat hozzá egy új úszó menü egy lapra a távoli figyelési megoldás gyorsító webes felhasználói felületen. A cikk ismerteti:

- Hogyan készítheti elő a helyi fejlesztési környezetet.
- Hogyan adhat hozzá egy új úszó menü egy oldal, a webes felhasználói felületen.

Ebben a cikkben a példa úszó menü jeleníti meg az oldalon, a rácshoz, amely a [adjon hozzá egy egyéni rács a távoli figyelési megoldás gyorsító webes felhasználói Felületére](iot-accelerators-remote-monitoring-customize-grid.md) útmutató a cikk bemutatja, hogyan adhat hozzá.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések végrehajtásához szüksége van a következő szoftvereknek telepítve a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt kell végeznie az alábbi cikkekben leírt lépéseket:

- [Egyéni oldal hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-page.md).
- [Egy egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-service.md)
- [Egy egyéni rács ad hozzá a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Adjon hozzá egy úszó menü

Adjon hozzá egy úszó menü a webes felhasználói Felületére, szüksége adja hozzá a forrásfájlokat, amelyek meghatározzák az úszó menü, és néhány meglévő fájlokat, hogy a webes felhasználói felület az új összetevő tisztában módosításához.

### <a name="add-the-new-files-that-define-the-flyout"></a>Adja hozzá az új fájlokat, amelyek meghatározzák az úszó menü

Az első lépésekhez, a **src/forgatókönyv/components/oldalak/pageWithFlyout/menük nélküli/exampleFlyout** mappa a fájlokat, amelyek meghatározzák egy úszó menü tartalmazza:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Másolás a **src/forgatókönyv/components/oldalak/pageWithFlyout/menük nélküli** mappát a **példa src/components/oldalak** mappát.

### <a name="add-the-flyout-to-the-page"></a>Az úszó menü hozzáadása az oldalhoz

Módosítsa a **src/components/pages/example/basicPage.js** hozzáadása a úszó menü.

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

Adjon hozzá egy gombot, a környezeti menüjéhez úszó menü megnyitásához:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Az oldal tartalmát valamilyen szöveget és a úszó menü tároló hozzáadása:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Tesztelje a úszó menü

Ha a webes felhasználói felület még nem fut helyi, futtassa a következő parancsot a tárház helyi példányának gyökérmappájában:

```cmd/sh
npm start
```

Az előző parancs futtatása helyileg, a felhasználói felület [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Keresse meg a **példa** lapot, és kattintson **nyílt úszó menü**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az erőforrások hozzáadása vagy a webes felhasználói felület a távoli figyelési megoldásgyorsító oldalainak testreszabása segíti.

Most már meg van adva egy úszó menü egy oldal, a következő lépés az, hogy [a Vezérlőpult hozzáadása az irányítópulthoz, a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-panel.md).

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md).
