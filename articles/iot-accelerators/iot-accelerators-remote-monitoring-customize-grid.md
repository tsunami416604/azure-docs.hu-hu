---
title: Rács hozzáadása a távfigyelési megoldás felhasználói felületéhez – Azure | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan adhat hozzá új gid-t egy laphoz a távoli figyelési megoldásgyorsító webes felhasználói felületén.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447097"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Egyéni rács hozzáadása a távfigyelési megoldásgyorsító webes felhasználói felületéhez

Ez a cikk bemutatja, hogyan adhat hozzá új rácsot egy laphoz a távoli figyelési megoldásgyorsító webes felhasználói felületén. A cikk a következőket írja le:

- Hogyan készítsünk egy helyi fejlesztési környezetben.
- Új rács hozzáadása a webes felhasználói felület egyik oldalához?

A cikkben szereplő példarács megjeleníti a szolgáltatás adatait, amelyet az [Egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsító webfelhasználói felületéhez](iot-accelerators-remote-monitoring-customize-service.md) bemutató útmutató cikk bemutatja, hogyan kell hozzáadni.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek végrehajtásához a következő szoftverre van szükség a helyi fejlesztői gépen:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Előkészületek

A folytatás előtt hajtsa végre a következő cikkekben ismertetett lépéseket:

- [Egyéni lap hozzáadása a Távfigyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-page.md).
- [Egyéni szolgáltatás hozzáadása a távfigyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Rács hozzáadása

Ha rácsot szeretne hozzáadni a webes felhasználói felülethez, hozzá kell adnia a rácsot meghatározó forrásfájlokat, és módosítania kell néhány meglévő fájlt, hogy a webes felhasználói felület tisztában legyen az új összetevővel.

### <a name="add-the-new-files-that-define-the-grid"></a>A rácsot meghatározó új fájlok hozzáadása

A kezdéshez az **src/walkthrough/components/pages/pageWithGrid/exampleGrid** mappa a rácsot meghatározó fájlokat tartalmazza:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Másolja az **src/walkthrough/components/pages/pageWithGrid/exampleGrid** mappát az **src/components/pages/example** mappába.

### <a name="add-the-grid-to-the-page"></a>Rács hozzáadása a laphoz

Módosítsa az **src/components/pages/example/basicPage.container.js fájlt** az alábbiak szerint a szolgáltatásdefiníciók importálásához:

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

Módosítsa az **src/components/pages/example/basicPage.js kapcsolót** a rács hozzáadásához:

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

Módosítsa az **src/components/pages/example/basicPage.test.js kapcsolót** a tesztek frissítéséhez:

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

Ha a webes felhasználói felület még nem fut helyileg, futtassa a következő parancsot a tárház helyi példányának gyökerében:

```cmd/sh
npm start
```

Az előző parancs helyileg futtatja a felhasználói felületet a ban. [http://localhost:3000/dashboard](http://localhost:3000/dashboard) Nyissa meg a **Példa** lapot a szolgáltatás rácsmegjelenítési adatainak megtekintéséhez.

## <a name="select-rows"></a>Sorok kijelölése

Két lehetőség van arra, hogy a felhasználó sorokat jelöljön ki a rácsban:

### <a name="hard-select-rows"></a>Sorok kijelölése

Ha a felhasználónak egyszerre több sort kell használnia, használjon jelölőnégyzeteket a sorokon:

1. A sorok kijelölésének engedélyezése egy **jelölőnégyzetoszlop** hozzáadásával a rácshoz megadott **oszlophoz.** **checkboxColumn** a **/src/components/shared/pcsGrid/pcsGrid.js**nyelven van definiálva:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. A kijelölt elemek eléréséhez a belső rács API-jára mutató hivatkozás tkap:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Környezeti gombok biztosítása a laphoz, ha a rács egy sora nehezen van kijelölve:

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

1. Amikor egy környezet gombra kattint, a nehezen kiválasztott elemeket a következőkhöz végezze el:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Sorok lágy kijelölése

Ha a felhasználónak csak egyetlen sorban kell eljárnia, állítson be egy lágyan kijelölő hivatkozást az oszlop egy vagy több **oszlopához.**

1. Az **exampleGridConfig.js fájlban**adja hozzá a **SoftSelectLinkRenderer programot** **cellRendererFramework** elemként egy **columnDef**hez.

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

1. Amikor egy soft-select linkre kattintanak, az elindítja az **onSoftSelectChange** eseményt. Hajtsa végre a sorhoz szükséges műveletet, például nyisson meg egy részletes úszó panelt. Ez a példa egyszerűen írja a konzol:

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

## <a name="next-steps"></a>További lépések

Ebben a cikkben a távoli figyelési megoldás gyorsítójában a webes felhasználói felület lapjainak hozzáadásához vagy testreszabásához rendelkezésre álló erőforrásokról szerzett tudomást.

Most már definiált egy rácsot, a következő lépés az, hogy [egyéni úszó panelt ad hozzá a távoli figyelési megoldásgyorsító webes felhasználói felületéhez,](iot-accelerators-remote-monitoring-customize-flyout.md) amely a példalapon jelenik meg.

A távfigyelési megoldásgyorsítóról a [Távoli figyelési architektúra című témakörben](iot-accelerators-remote-monitoring-sample-walkthrough.md)talál további általános tudnivalókat.
