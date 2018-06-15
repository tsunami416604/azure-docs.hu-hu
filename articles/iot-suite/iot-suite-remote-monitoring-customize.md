---
title: A távoli figyelésére szolgáló megoldás UI - Azure testreszabása |} Microsoft Docs
description: Ez a cikk bemutatja hogyan használható a távoli megfigyelési megoldásgyorsító felhasználói felület forráskódja és néhány testreszabásokat.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: be20d45b380f66208884f15f4644f36f2a403837
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33774050"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>A távoli megfigyelési megoldásgyorsító testreszabása

Ez a cikk bemutatja hogyan hozzáférni a forráskódot, és a távoli megfigyelési megoldásgyorsító felhasználói felület testreszabása. A cikk ismerteti:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>A helyi környezet előkészítése a felhasználói felületen

A távoli megfigyelési megoldásgyorsító UI kód React.js keretében segítségével van megvalósítva. Megtalálhatja a forráskódban a [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub-tárházban.

A felhasználói felület módosításához helyileg futtathatja egy példányát. A helyi példány csatlakozik a megoldás például telemetriai adatainak lekérése műveletek elvégzéséhez telepített példányát.

A helyi felhasználói felület fejlesztési környezet beállítása a folyamat lépései:

1. Központi telepítése egy **alapvető** a megoldás gyorsító használatával példánya a **számítógépek** parancssori felület. Jegyezze fel a nevét, valamint a központi telepítés a virtuális géphez megadott hitelesítő adatok. További információkért lásd: [telepítheti a parancssori felület használatával](iot-suite-remote-monitoring-deploy-cli.md).

1. Az Azure-portálon vagy a [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ahhoz, hogy a virtuális géphez, amelyen a megoldásban mikroszolgáltatások SSH-elérést. Példa:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Az Azure-portálon vagy a [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nevét és a virtuális gép nyilvános IP-cím kereséséhez. Példa:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Az SSH használata a virtuális gépet, az IP-címet az előző lépést, és a futtatásakor megadott hitelesítő adatok használatával csatlakozni **számítógépek** üzembe helyezéséhez.

1. Ahhoz, hogy a helyi UX való csatlakozáshoz, futtassa az alábbi parancsokat a rendszerhéjakba a virtuális gépen:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Miután meggyőződött arról, a parancs végrehajtását, és a webhely elindul, leválaszthatja, a virtuális gépről.

1. A helyi példánya a [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) -tárházban, szerkesztése a **.env** fájl hozzáadása a telepített megoldás URL-címe:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. A helyi másolat készítése a parancsot a parancssorba a `azure-iot-pcs-remote-monitoring-webui` mappa, a szükséges kódtárak telepítése, és helyi futtatásra felhasználói felülete a következő parancsokat:

    ```cmd/sh
    npm install
    npm start
    ```

1. Az előző parancsot futtatja a felhasználói felület lévő helyi http://localhost:3000/dashboard. A kód szerkesztése a hely futása közben, és dinamikusan frissítéséhez látható.

## <a name="customize-the-layout"></a>Az elrendezés testreszabása

A távoli figyelésére szolgáló megoldás oldalainak tevődnek össze a vezérlők, a továbbiakban *panelek* a forráskód. Például a **irányítópult** lap öt panelek épül fel: áttekintése, térkép, riasztások, Telemetriai és KPI-k. A forráskód minden lapon és a panelek definiáló található a [számítógépek-távelérési-figyelés-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-tárházban. Például, ha a kódot, amely meghatározza a **irányítópult** lap, az elrendezés és a panelek az oldalon található a [irányítópult src/összetevőinek/lapok](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) mappát.

A panelek kezeli a saját elrendezés és a méretezés, mert a lap elrendezése könnyen módosíthatja. Például a következő módosításai a **PageContent** eleme a `src/components/pages/dashboard/dashboard.js` fájl felcserélni a hellyel korábban a térkép és telemetriai panelt, és a térkép és a KPI panelek relatív szélességének módosítása:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Változás paraméterpanel-elrendezése](media/iot-suite-remote-monitoring-customize/layout.png)

> [!NOTE]
> A térkép nincs konfigurálva a helyi környezetben.

Azt is megteheti az azonos panel több példányát, vagy több verziója Ha Ön [duplikált, és szabja testre a panel](#duplicate-and-customize-an-existing-control). A következő példa bemutatja, hogyan adja hozzá a telemetriai adatok panel két példánya szerkesztésével a `src/components/pages/dashboard/dashboard.js` fájlt:

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

Megtekintheti a különféle telemetriai minden panelen:

![Több telemetriai panelek](media/iot-suite-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> A térkép nincs konfigurálva a helyi környezetben.

## <a name="duplicate-and-customize-an-existing-control"></a>Duplikált, és szabja testre a meglévő vezérlőelem

A lépései használata a **riasztások** panel példa bemutatja, hogy egy meglévő panel ismétlődő, módosítsa ezt a és a módosított verzióját használja:

1. A tárház helyi példánya, a másolat készítése a **riasztások** mappájában a `src/components/pages/dashboard/panels` mappát. Az új név **cust_alarms**.

1. A a **alarmsPanel.js** fájlt a **cust_alarms** mappában kell lennie az osztály nevét szerkesztése **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Adja hozzá a következő sort a `src/components/pages/dashboard/panels/index.js` fájlt:

    ```nodejs
    export * from './cust_alarms';
    ```

1. Cserélje le `AlarmsPanel` rendelkező `CustAlarmsPanel` a a `src/components/pages/dashboard/dashboard.js` fájlt:

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Az eredeti most kicserélt **riasztások** nevű másolatát Vezérlőpult **CustAlarms**. Ezt a másolatot megegyezik az eredeti. Módosíthatja a példány most. Ahhoz például, hogy alakítani az oszlopot a rendezési a **riasztások** panel:

1. Nyissa meg az `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` fájlt.

1. Az oszlopdefiníciók módosíthatja, ahogy az a következő kódrészletet:

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

Az alábbi képernyőfelvételen látható új verzióját a **riasztások** panel:

![Riasztások panel frissítése](media/iot-suite-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>A telemetriai adatok diagram testreszabása

A telemetriai adatok diagram a **irányítópult** lap határozzák meg a fájlokat a `src/components/pages/dashboard/panels/telemtry` mappát. A felhasználói felület a megoldás háttérrendszeréhez a telemetriai adatok átveszi a `src/services/telemetryService.js` fájlt. A következő lépések bemutatják az adott időszakban jelenik meg a telemetriai adatok diagram 15 perc és 5 perc módosítása:

1. Az a `src/services/telemetryService.js` fájlt, keresse meg a hívott függvény **getTelemetryByDeviceIdP15M**. Ez a függvény egy példányának, és módosítsa a példányt a következőképpen:

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. A telemetriai adatok diagram feltöltése az új funkció használatával, nyissa meg a `src/components/pages/dashboard/dashboard.js` fájlt. Keresse meg, amely a telemetriai adatok adatfolyam inicializálja a sort, és módosítsa a következőképpen:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

A telemetriai adatok diagram láthatók az öt perc telemetriai adatok:

![Telemetria diagramját egy nap](media/iot-suite-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Új KPI hozzáadása

A **irányítópult** lap megjeleníti a KPI-k a **rendszer KPI-k** panel. Ezen KPI-k a kiszámítása a `src/components/pages/dashboard/dashboard.js` fájlt. A KPI-k által megjelennek-e a `src/components/pages/dashboard/panels/kpis/kpisPanel.js` fájlt. A következő lépések azt ismertetik, hogyan új KPI érték kiszámításához és a a **irányítópult** lap. A bemutatott példában százalékos módosítást hozzáadása a figyelmeztető riasztások KPI:

1. Nyissa meg az `src/components/pages/dashboard/dashboard.js` fájlt. Módosítsa a **initialState** objektum tartalmazza a **warningAlarmsChange** tulajdonság az alábbiak szerint:

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Módosítsa a **currentAlarmsStats** felvenni objektum **totalWarningCount** tulajdonságként:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Az új KPI kiszámításához. A kritikus riasztások darabszám számítását található. A kód duplikált, és módosítsa a példányt a következőképpen:

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Tartalmazza az új **warningAlarmsChange** a KPI-adatfolyamban KPI:

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. A KPI-k panel átadott adatok frissítéséhez:

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Most már befejezte a változásai a `src/components/pages/dashboard/dashboard.js` fájlt. A következő lépések írják le a változások végezze el a `src/components/pages/dashboard/panels/kpis/kpisPanel.js` fájlt az új KPI megjelenítéséhez:

1. Módosítsa a következő kódsort az új KPI értékének beolvasása az alábbiak szerint:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Módosítsa a kód az új KPI érték megjelenítéséhez az alábbiak szerint:

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

A **irányítópult** lap most új KPI értékét jeleníti meg:

![Figyelmeztetés KPI](media/iot-suite-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>A térkép testreszabása

Tekintse meg a [testreszabás térkép](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) a lapot a Githubon a leképezési összetevők a megoldásban.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Egyéb testreszabási lehetőségek

A távoli figyelésére szolgáló megoldás a bemutató és képi megjelenítéseket réteg további módosításához, szerkesztheti a kódot. A megfelelő GitHub-adattárak a következők:

* [A konfigurációs mikroszolgáltatási az Azure IoT-megoldások (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [A konfigurációs mikroszolgáltatási Azure IoT-megoldások (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Az Azure IoT-számítógépek távoli webes felhasználói felület figyelése](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta kapcsolatos segítséget a távoli megfigyelési megoldásgyorsító a webes felhasználói felület testreszabása számára elérhető erőforrások.

További elméleti kapcsolatos további információkért a távoli megfigyelési megoldásgyorsító: [távoli megfigyelési architektúrája](iot-suite-remote-monitoring-sample-walkthrough.md)

A távoli figyelésére szolgáló megoldás testreszabásával kapcsolatos további információkért lásd: [testreszabás és helyezze üzembe újra egy mikroszolgáltatási](iot-suite-microservices-example.md)
<!-- Next tutorials in the sequence -->