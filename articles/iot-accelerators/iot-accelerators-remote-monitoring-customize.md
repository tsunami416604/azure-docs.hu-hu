---
title: A távoli figyelési megoldás felhasználói felületének testreszabása – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan érheti el a távoli figyelési megoldás felhasználói felületének forráskódját, és hogyan teheti lehetővé a testreszabást.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "68608004"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldáshoz tartozó gyorssegéd testreszabása

Ez a cikk tájékoztatást nyújt arról, hogyan érheti el a forráskódot, és hogyan szabhatja testre a távoli figyelési megoldás gyorsító FELÜLETét.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Helyi fejlesztési környezet előkészítése a felhasználói felületen

A távoli figyelési megoldás gyorsított felhasználói felületének kódja a Resolution. js keretrendszer használatával valósítható meg. A forráskódot az [Azure-IOT-PCs-Remote-Monitoring-WebUI](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub-tárházban találja.

Ha módosítani szeretné a felhasználói felületet, helyileg is futtathatja a példányt. Az olyan műveletek végrehajtásához, mint például a telemetria beolvasása, a helyi másolat a megoldás egy telepített példányához csatlakozik.

Az alábbi lépések ismertetik a helyi környezet beállításának folyamatát a felhasználói felület fejlesztéséhez:

1. Telepítse a megoldás-gyorsító **alapszintű** példányát a **számítógépek** CLI használatával. Jegyezze fel az üzemelő példány nevét és a virtuális géphez megadott hitelesítő adatokat. További információ: [üzembe helyezés a parancssori felület használatával](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Ha engedélyezni szeretné az SSH-hozzáférést ahhoz a virtuális géphez, amely a megoldásban lévő szolgáltatásokat üzemelteti, használja a Azure Portal vagy a Azure Cloud Shell. Például:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Csak a tesztelés és a fejlesztés során engedélyezze az SSH-hozzáférést. Ha engedélyezi az SSH- [t, akkor azonnal le kell tiltania, amint befejezte a használatát](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. A virtuális gép nevének és nyilvános IP-címének megkereséséhez használja a Azure Portal vagy a Azure Cloud Shell. Például:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Használja az SSH-t a virtuális géphez való kapcsolódáshoz. Használja az előző lépés IP-címét és a **számítógépeken** a megoldás üzembe helyezéséhez megadott hitelesítő adatokat. A `ssh` parancs a Azure Cloud Shellban érhető el.

1. Ha engedélyezni szeretné a helyi UX-t a kapcsolódáshoz, futtassa a következő parancsokat a virtuális gép bash rendszerhéjában:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Miután a parancs befejeződik, és elindul a webhely, leválaszthatja a virtuális gépet.

1. Az [Azure-IOT-PC-Remote-Monitoring-WebUI](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) adattár helyi példányában szerkessze a **. env** fájlt, és adja hozzá az üzembe helyezett megoldás URL-címét:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. A parancssorban navigáljon a `azure-iot-pcs-remote-monitoring-webui` mappa helyi példányára.

1. A szükséges kódtárak telepítéséhez és a felhasználói felület helyi futtatásához futtassa a következő parancsokat:

    ```cmd/sh
    npm install
    npm start
    ```

1. Az előző parancs helyileg futtatja a felhasználói felületet a\/http:/localhost: 3000/irányítópulton. A kód szerkeszthető a hely futása közben, és a frissítés dinamikusan megtekinthető.

## <a name="customize-the-layout"></a>Az elrendezés testreszabása

A távoli figyelési megoldás minden lapja olyan vezérlőkből áll, amelyeket a forráskód *panelnek* nevezünk. Az **irányítópult** -oldal öt panelből áll: áttekintés, Térkép, riasztások, telemetria és elemzések. Megtalálhatja az egyes lapokat és a hozzá tartozó paneleket meghatározó forráskódot a [számítógépek – távoli figyelés – WebUI GitHub-](https://github.com/Azure/pcs-remote-monitoring-webui) tárházban. Például az **irányítópult** oldalát, elrendezését és a lapon lévő paneleket definiáló kód a [src/Components/Pages/Dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) mappában található.

Mivel a panelek a saját elrendezését és méretezését kezelik, egyszerűen módosíthatók a lapok elrendezése. Végezze el a következő módosításokat **PageContent** a `src/components/pages/dashboard/dashboard.js` fájl PageContent eleme számára:

* Cserélje le a Térkép és a telemetria panelek pozícióit.
* Módosítsa a Térkép és az elemzési panel relatív szélességét.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Panel elrendezésének módosítása](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Hozzáadhat egy panel több példányát is, vagy akár több verziót is, ha [duplikálja és testreszabja a panelt](#duplicate-and-customize-an-existing-control). Az alábbi példa bemutatja, hogyan adhat hozzá két példányt a telemetria panelhez. A módosítások elvégzéséhez szerkessze `src/components/pages/dashboard/dashboard.js` a következő fájlt:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Ezután különböző telemetria tekinthet meg az egyes paneleken:

![Több telemetria panel](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Meglévő vezérlő duplikálása és testreszabása

A következő lépések azt ismertetik, hogyan lehet duplikálni egy meglévő panelt, módosítani, majd használni a módosított verziót. A lépések a **riasztások** panelt használják példaként:

1. A tárház helyi példányán készítsen másolatot a **riasztások** mappáról a `src/components/pages/dashboard/panels` mappában. Nevezze el az új másolási **cust_alerts**.

1. A **cust_alerts** mappában található **alertsPanel. js** fájlban szerkessze a **CustAlertsPanel**osztály nevét:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Adja hozzá a következő sort a `src/components/pages/dashboard/panels/index.js` fájlhoz:

    ```javascript
    export * from './cust_alerts';
    ```

1. `CustAlertsPanel` Cserélje `alertsPanel` le a `src/components/pages/dashboard/dashboard.js` fájlt a fájlba:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Most lecserélte az eredeti **riasztások** panelt egy **CustAlerts**nevű másolatra. Ez a másolat megegyezik az eredetivel. Most már módosíthatja a másolatot. Például az oszlopok rendezésének módosításához a **riasztások** panelen:

1. Nyissa meg az `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` fájlt.

1. Módosítsa az oszlopok definícióit az alábbi kódrészletben látható módon:

    ```javascript
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

Az alábbi képernyőfelvételen a **riasztások** panel új verziója látható:

![riasztások panel frissítve](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>A telemetria diagram testreszabása

A `src/components/pages/dashboard/panels/telemtry` mappában lévő fájlok a telemetria diagramot az **irányítópult** lapon határozzák meg. A felhasználói felület lekéri a telemetria a `src/services/telemetryService.js` fájlból a megoldás hátterének végéről. A következő lépések bemutatják, hogyan módosíthatja a telemetria diagramon megjelenő időszakot 15 és 5 perc között:

1. A `src/services/telemetryService.js` fájlban keresse meg a **getTelemetryByDeviceIdP15M**nevű függvényt. Készítsen másolatot a függvényről, és módosítsa a másolást a következőképpen:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Ha ezt az új függvényt szeretné használni az telemetria diagram feltöltéséhez, `src/components/pages/dashboard/dashboard.js` nyissa meg a fájlt. Keresse meg azt a sort, amely inicializálja a telemetria-streamet, és módosítsa a következőképpen:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

A telemetria diagramon most már a telemetria-adatmennyiség öt perce látható:

![Egy napot ábrázoló telemetria diagram](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Új KPI hozzáadása

Az **irányítópult** lap a KPI-ket jeleníti meg az **elemzési** panelen. Ezeket a KPI-ket a `src/components/pages/dashboard/dashboard.js` fájl számítja ki. A KPI-k a `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` fájl alapján jelennek meg. A következő lépések azt ismertetik, hogyan számítható ki és jeleníthető meg egy új KPI-érték az **irányítópult** lapon. A következő példa egy új százalékos változás hozzáadása a figyelmeztetési riasztások KPI-ben:

1. Nyissa meg az `src/components/pages/dashboard/dashboard.js` fájlt. Módosítsa úgy a **initialState** objektumot, hogy az a következőképpen tartalmazzon **warningAlertsChange** tulajdonságot:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Módosítsa a **currentAlertsStats** objektumot úgy, hogy az **totalWarningCount** tulajdonságként szerepeljen:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Az új KPI kiszámítása. Keresse meg a kritikus riasztások számának számítását. A kód duplikálása és a másolás módosítása a következőképpen történik:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Adja meg az új **warningAlertsChange** KPI-t a KPI-adatfolyamban:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Adja meg az új **warningAlertsChange** KPI-t a felhasználói felület megjelenítéséhez használt állapot-adatközpontban:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Frissítse a KPI-k panelnek átadott adatfájlokat:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Ezzel befejezte a `src/components/pages/dashboard/dashboard.js` fájl módosításait. A következő lépések azt írják le, hogy milyen módosításokat `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` kell végezni a fájlban az új KPI megjelenítéséhez:

1. Módosítsa a következő kódrészletet az új KPI-érték lekéréséhez a következő módon:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Módosítsa a korrektúrát úgy, hogy az az új KPI-értéket a következőképpen jelenítse meg:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

Az **irányítópult** lap most megjeleníti az új KPI értékét:

![Figyelmeztetési KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>A Térkép testreszabása

A megoldásban található Térkép-összetevők részleteiért tekintse meg a [Térkép testreszabása](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) lapot a githubon.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Egyéb testreszabási beállítások

A távoli figyelési megoldásban a bemutató és a vizualizációk réteg további módosításához szerkesztheti a kódot. A vonatkozó GitHub-adattárak a következők:

* [Az Azure IoT-megoldások (.NET) konfigurációs szolgáltatása](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Az Azure IoT-megoldások (Java) konfigurációs szolgáltatása](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Azure IoT számítógépek távoli monitorozása webes felhasználói felület](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan szabhatja testre a webes felhasználói felületet a távoli figyelési megoldás-gyorsító segítségével. A felhasználói felület testreszabásával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Egyéni lap hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-page.md)
* [Egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsított webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-service.md)
* [Egyéni rács hozzáadása a távoli figyelési megoldás gyorsított webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-grid.md)
* [Egyéni menü hozzáadása a távoli figyelési megoldás webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Egyéni panel hozzáadása az irányítópulthoz a távoli figyelési megoldás gyorsító webes FELÜLETén](iot-accelerators-remote-monitoring-customize-panel.md)

A távoli figyelési megoldás-gyorsító részletes ismertetését lásd: [távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md)

További információ a távoli figyelési megoldással kapcsolatos szolgáltatások testreszabásáról: [webszolgáltatás testreszabása és újbóli üzembe helyezése](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
