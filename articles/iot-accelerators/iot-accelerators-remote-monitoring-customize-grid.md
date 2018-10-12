---
title: Rács ad hozzá a távoli figyelési megoldás felhasználói felület – Azure |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá egy új csoportazonosító egy oldalon, a távoli figyelési megoldás gyorsító webes felhasználói felületen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: 71f2164c9c419604c513261df7e1264060a2c374
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094571"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egy egyéni rács ad hozzá a távoli figyelési megoldás gyorsító webes felhasználói felületen

Ez a cikk bemutatja, hogyan adhat hozzá egy új rács egy lapra a távoli figyelési megoldás gyorsító webes felhasználói felületen. A cikk ismerteti:

- Hogyan készítheti elő a helyi fejlesztési környezetet.
- Hogyan adhat hozzá egy új rács egy oldal, a webes felhasználói felületen.

A példában ez a cikk a rácson az adatokat a szolgáltatásból, amely a [adjon hozzá egy egyéni szolgáltatás a távoli figyelési megoldás gyorsító webes felhasználói Felületére](iot-accelerators-remote-monitoring-customize-service.md) útmutató a cikk bemutatja, hogyan adhat hozzá.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések végrehajtásához szüksége van a következő szoftvereknek telepítve a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt kell végeznie az alábbi cikkekben leírt lépéseket:

- [Egyéni oldal hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-page.md).
- [Egy egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Adja hozzá a rács

Rács hozzáadása a webes felhasználói felületen, szüksége adja hozzá a forrásfájlokat, amelyek meghatározzák a rács, és néhány meglévő fájlokat, hogy a webes felhasználói felület az új összetevő tisztában módosításához.

### <a name="add-the-new-files-that-define-the-grid"></a>Adja hozzá az új fájlokat, amelyek meghatározzák a rács

Az első lépésekhez, a **src/forgatókönyv/components/oldalak/pageWithGrid/exampleGrid** mappa a fájlokat, amelyek meghatározzák a rács tartalmazza:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Másolás a **src/forgatókönyv/components/oldalak/pageWithGrid/exampleGrid** mappát a **példa src/components/oldalak** mappát.

### <a name="add-the-grid-to-the-page"></a>A rács hozzáadása az oldalhoz

Módosítsa a **src/components/pages/example/basicPage.container.js** importálása a service definíciókat a következő:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Módosítsa a **src/components/pages/example/basicPage.js** a következőképpen adja hozzá a rács:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Módosítsa a **src/components/pages/example/basicPage.test.js** módon, hogy a tesztek frissítése:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>A rács tesztelése

Ha a webes felhasználói felület még nem fut helyi, futtassa a következő parancsot a tárház helyi példányának gyökérmappájában:

```cmd/sh
npm start
```

Az előző parancs futtatása helyileg, a felhasználói felület [ http://localhost:3000/dashboard ](http://localhost:3000/dashboard). Keresse meg a **példa** oldalon tekintheti meg a rács megjelenítési adatokat a szolgáltatásból.

## <a name="select-rows"></a>Sorok kiválasztása

A felhasználók engedélyezésének sorok kiválasztásához a rács két lehetőség van:

### <a name="hard-select-rows"></a>Rögzített válassza sorok

Ha egy felhasználó egyszerre több sort működjön, használja a jelölőnégyzetek alapján:

1. A sorok rögzített kijelölés engedélyezése hozzáadásával egy **checkboxColumn** , a **columnDefs** a rácshoz megadott. **checkboxColumn** meghatározott **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. A kijelölt elemek eléréséhez, kap egy hivatkozást a belső rács API:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Adja meg a környezet gombokra kattintva a lap a rács egyik sorára keményen kiválasztásakor:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Amikor egy környezetben gombra kattint, kérje le a munkája elvégzéséhez keményen kijelölt elemek:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Helyreállítható válassza sorok

Ha a felhasználó csak egyetlen sor ahhoz van szüksége, az egy vagy több oszlop helyreállítható válassza kapcsolat konfigurálásához a **columnDefs**.

1. A **exampleGridConfig.js**, adjon hozzá **SoftSelectLinkRenderer** , a **cellRendererFramework** számára egy **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Egy helyreállítható válassza hivatkozásra kattint, amikor elindítja a **onSoftSelectChange** esemény. Hajtsa végre, bármilyen művelet van szükség a sorhoz, például a részletek úszó megnyitása. Ebben a példában egyszerűen ír a konzolon:

    ```js
    onSoftSelectChange = (rowId, rowEvent) => {
      const { onSoftSelectChange } = this.props;
      const obj = (this.gridApi.getDisplayedRowAtIndex(rowId) || {}).data;
      if (obj) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', obj);
        this.setState({ softSelectedObj: obj });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(obj, rowEvent);
      }
    }
    ```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az erőforrások hozzáadása vagy a webes felhasználói felület a távoli figyelési megoldásgyorsító oldalainak testreszabása segíti.

Most már beállított egy rácsot, a következő lépés az, hogy [adjon hozzá egy egyéni úszó a távoli figyelési megoldás gyorsító webes felhasználói Felületére](iot-accelerators-remote-monitoring-customize-flyout.md) , amely a lap megjeleníti.

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md).
