---
title: Rács hozzáadása a távoli figyelési megoldás felhasználói felületéhez – Azure | Microsoft Docs
description: Ez a cikk bemutatja, hogyan adhat hozzá egy új GID-t egy oldalon a távoli figyelési megoldás webes felhasználói felületén.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.custom: devx-track-javascript
ms.openlocfilehash: 0eab7d743823109ed138ce19f35cea0ffb143422
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422725"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni rács hozzáadása a távoli figyelési megoldás gyorsított webes felhasználói felületéhez

Ebből a cikkből megtudhatja, hogyan adhat hozzá egy új rácsot a távoli figyelési megoldás webes felhasználói felületének egyik lapjához. A cikk a következőket ismerteti:

- Helyi fejlesztési környezet előkészítése.
- Új rács hozzáadása egy laphoz a webes felhasználói felületen.

A jelen cikkben szereplő példában szereplő táblázat a szolgáltatás azon adatait jeleníti meg, amelyek az [Egyéni szolgáltatás hozzáadása a távoli figyelési megoldáshoz webes felhasználói felület](iot-accelerators-remote-monitoring-customize-service.md) útmutatója című cikk bemutatja, hogyan adhat hozzá.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához a következő szoftverekre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt végezze el a következő cikkekben ismertetett lépéseket:

- [Adjon hozzá egy egyéni lapot a távoli figyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-page.md).
- [Egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsított webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Rács hozzáadása

Ha rácsot szeretne hozzáadni a webes felhasználói felülethez, fel kell vennie a rácsot definiáló forrásfájlokat, és módosítania kell néhány meglévő fájlt, hogy a webes KEZELŐFELÜLET tisztában legyen az új összetevővel.

### <a name="add-the-new-files-that-define-the-grid"></a>A rácsot definiáló új fájlok hozzáadása

A kezdéshez az **src/walkthrough/Components/Pages/pageWithGrid/exampleGrid** mappa tartalmazza a rácsot definiáló fájlokat:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Másolja a **src/walkthrough/Components/Pages/pageWithGrid/exampleGrid** mappát a **src/Components/Pages/example** mappába.

### <a name="add-the-grid-to-the-page"></a>A rács hozzáadása az oldalhoz

Módosítsa az **src/Components/Pages/example/basicPage.container.js** a következő módon a szolgáltatási definíciók importálásához:

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

Módosítsa a **src/Components/Pages/example/basicPage.js** az alábbiak szerint a rács hozzáadásához:

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

Módosítsa a következőt a **src/Components/Pages/example/basicPage.test.js** a tesztek frissítéséhez:

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

Ha a webes felhasználói felület még nem fut helyileg, futtassa a következő parancsot az adattár helyi példányának gyökerében:

```cmd/sh
npm start
```

Az előző parancs helyileg futtatja a felhasználói felületet `http://localhost:3000/dashboard` . Navigáljon a **példa** lapra, és tekintse meg a szolgáltatásból származó adatok táblázatát.

## <a name="select-rows"></a>Sorok kiválasztása

Két lehetőség közül választhat, amelyek lehetővé teszik a felhasználók számára a sorok kiválasztását a rácsban:

### <a name="hard-select-rows"></a>Sorok rögzített kijelölése

Ha egy felhasználónak egyszerre több sorban kell működnie, használja a jelölőnégyzeteket a sorokban:

1. A sorok kiválasztásának engedélyezése egy **checkboxColumn** a rácshoz megadott **columnDefs** való hozzáadásával. a **checkboxColumn** a **/src/Components/Shared/pcsGrid/pcsGrid.jsban **van definiálva:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. A kiválasztott elemek eléréséhez a belső Grid API-ra fog hivatkozni:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Adjon meg környezeti gombokat a laphoz, ha a rács egyik sora rögzített:

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

1. Ha a helyi gombra kattint, a munkahelyén a következő műveleteket végezheti el:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Lágy sorok kijelölése

Ha a felhasználónak csak egyetlen sorban kell működnie, a **columnDefs**egy vagy több oszlopához állítson be egy Soft-Select hivatkozást.

1. A **exampleGridConfig.jsban **adja hozzá a **SoftSelectLinkRenderer** -t a **columnDef** **cellRendererFramework** .

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

1. Ha egy puha kijelölési hivatkozásra kattint, a elindítja a **onSoftSelectChange** eseményt. Hajtson végre bármilyen műveletet az adott sorhoz, például nyisson meg egy részletek menüjét. Ez a példa egyszerűen a konzolra ír:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan adhat hozzá vagy szabhat testre lapokat a webes felhasználói felületen a távoli figyelési megoldás-gyorsító segítségével.

Most már definiált egy rácsot, a következő lépés [egy egyéni nyíl hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-flyout.md) , amely a példa oldalon jelenik meg.

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md).
