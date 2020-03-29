---
title: Úszó panel hozzáadása a távoli figyelési megoldás felhasználói felületéhez - Azure | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan vehet fel új úszó panelt egy lapra a távoli figyelési megoldásgyorsító webes felhasználói felületén.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447114"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni úszó panel hozzáadása a távfigyelési megoldásgyorsító webes felhasználói felületéhez

Ez a cikk bemutatja, hogyan adhat hozzá egy új úszó panelt egy lapra a távoli figyelési megoldásgyorsító webes felhasználói felületén. A cikk a következőket írja le:

- Hogyan készítsünk egy helyi fejlesztési környezetben.
- Új úszó panel hozzáadása a webes felhasználói felület egyik lapjához?

A cikkben található példa úszó panel a lapon jelenik meg, amelyen az [egyéni rács hozzáadása a Távoli figyelési megoldás gyorsító webfelhasználói felületéhez](iot-accelerators-remote-monitoring-customize-grid.md) bemutatja, hogyan kell hozzáadni.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek végrehajtásához a következő szoftverre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt hajtsa végre a következő cikkekben ismertetett lépéseket:

- [Egyéni lap hozzáadása a Távfigyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-page.md).
- [Egyéni szolgáltatás hozzáadása a távfigyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-service.md)
- [Egyéni rács hozzáadása a távfigyelési megoldásgyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Úszó panel hozzáadása

Ha úszó panelt szeretne hozzáadni a webes felhasználói felülethez, hozzá kell adnia az úszó panelt meghatározó forrásfájlokat, és módosítania kell néhány meglévő fájlt, hogy a webes felhasználói felület tisztában legyen az új összetevővel.

### <a name="add-the-new-files-that-define-the-flyout"></a>Az úszó panelt meghatározó új fájlok hozzáadása

A kezdéshez az **src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout** mappa tartalmazza az úszó panelt meghatározó fájlokat:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**példaFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Másolja az **src/walkthrough/components/pages/pageWithFlyout/flyouts mappát** az **src/components/pages/example** mappába.

### <a name="add-the-flyout-to-the-page"></a>Az úszó panel hozzáadása a laphoz

Az úszó panel hozzáadásához módosítsa az **src/components/pages/example/basicPage.js kapcsolót.**

Adja hozzá a **Btn-t** az **összetevőkből/megosztott termékekből** származó behozatalhoz, és adja hozzá az **svg-k** és az **ExampleFlyoutContainer**importját:

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

Adjon hozzá egy **konstans definíciót** a **closedFlyoutState-hez,** és adja hozzá a konstruktor állapotához:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Adja hozzá a következő függvényeket a **BasicPage** osztályhoz:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Adja hozzá a következő **kont** definíciókat a **renderelési** függvényhez:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Az úszó panel megnyitásához adjon hozzá egy gombot a helyi menühöz:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Adjon hozzá szöveget és az úszó paneltárolót az oldal tartalmához:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Az úszó panel tesztelése

Ha a webes felhasználói felület még nem fut helyileg, futtassa a következő parancsot a tárház helyi példányának gyökerében:

```cmd/sh
npm start
```

Az előző parancs helyileg futtatja a felhasználói felületet a ban. [http://localhost:3000/dashboard](http://localhost:3000/dashboard) Nyissa meg a **Példa** lapot, és kattintson **az Úszó panel megnyitása gombra.**

## <a name="next-steps"></a>További lépések

Ebben a cikkben a távoli figyelési megoldás gyorsítójában a webes felhasználói felület lapjainak hozzáadásához vagy testreszabásához rendelkezésre álló erőforrásokról szerzett tudomást.

Most, hogy egy úszó panelt definiált egy oldalon, a következő lépés az, hogy [panelt ad hozzá az irányítópulthoz a Távoli figyelési megoldásgyorsító webes felhasználói felületén.](iot-accelerators-remote-monitoring-customize-panel.md)

A távfigyelési megoldásgyorsítóról a [Távoli figyelési architektúra című témakörben](iot-accelerators-remote-monitoring-sample-walkthrough.md)talál további általános tudnivalókat.
