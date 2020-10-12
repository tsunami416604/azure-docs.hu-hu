---
title: A távoli figyelési megoldás felhasználói felületének hozzáadása az Azure-hoz | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá egy új, a távoli figyelési megoldás gyorsítása webes felhasználói felületén lévő képernyőt egy oldalon.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318415"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni menü hozzáadása a távoli figyelési megoldás webes felhasználói felületéhez

Ebből a cikkből megtudhatja, hogyan adhat hozzá egy új, a távoli figyelési megoldás gyorsítása webes felhasználói felületének egyik lapjához tartozó ikont. A cikk a következőket ismerteti:

- Helyi fejlesztési környezet előkészítése.
- Új képernyő hozzáadása egy laphoz a webes felhasználói felületen.

Az ebben a cikkben található példa menü azon a rácson jelenik meg, amelyben az [Egyéni rács hozzáadása a távoli figyelési megoldáshoz webes felhasználói felület](iot-accelerators-remote-monitoring-customize-grid.md) útmutatója című cikk bemutatja, hogyan adhat hozzá.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához a következő szoftverekre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt végezze el a következő cikkekben ismertetett lépéseket:

- [Adjon hozzá egy egyéni lapot a távoli figyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-page.md).
- [Egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsított webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-service.md)
- [Egyéni rács hozzáadása a távoli figyelési megoldás gyorsított webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Úszó panel hozzáadása

Ha a webes felhasználói felülethez hozzá szeretne adni egy kijelzőt, hozzá kell adnia a kinézetet definiáló forrásfájlokat, és módosítania kell néhány meglévő fájlt, hogy a webes KEZELŐFELÜLET tisztában legyen az új összetevővel.

### <a name="add-the-new-files-that-define-the-flyout"></a>Adja hozzá a kitűzést definiáló új fájlokat

Az első lépésekhez az **src/walkthrough/Components/Pages/pageWithFlyout/menüs/exampleFlyout** mappa tartalmazza azokat a fájlokat, amelyek a következőt határozzák meg:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Másolja a **src/walkthrough/Components/Pages/pageWithFlyout/a menüket** tartalmazó mappát a **src/Components/Pages/example** mappába.

### <a name="add-the-flyout-to-the-page"></a>A képernyő hozzáadása az oldalhoz

Módosítsa az **src/Components/Pages/example/basicPage.jselemet ** a következő érték hozzáadásához:.

Adja hozzá a **BTN** az **összetevők/megosztott** importálások és a **svgs** és a **ExampleFlyoutContainer**importálásához:

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

Adjon hozzá egy **CONST** -definíciót a **closedFlyoutState** , és adja hozzá az állapothoz a konstruktorban:

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

Adja hozzá a következő **CONST** -definíciókat a **Render** függvényhez:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Adjon hozzá egy gombot a menü megnyitásához a helyi menüben:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Adjon hozzá egy szöveget és a kiképernyőt a lap tartalmához:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>A menü tesztelése

Ha a webes felhasználói felület még nem fut helyileg, futtassa a következő parancsot az adattár helyi példányának gyökerében:

```cmd/sh
npm start
```

Az előző parancs helyileg futtatja a felhasználói felületet `http://localhost:3000/dashboard` . Navigáljon a **példa** lapra, és kattintson a **nyílra**.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan adhat hozzá vagy szabhat testre lapokat a webes felhasználói felületen a távoli figyelési megoldás-gyorsító segítségével.

Most, hogy már meghatározta a kinyíló elemet egy oldalon, a következő lépés [egy panel hozzáadása az irányítópulthoz a távoli figyelési megoldás webes felhasználói felületén](iot-accelerators-remote-monitoring-customize-panel.md).

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md).
