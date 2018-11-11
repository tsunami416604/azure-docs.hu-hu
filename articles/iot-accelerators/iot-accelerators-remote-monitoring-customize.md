---
title: A távoli figyelési megoldás testreszabása felhasználói felület – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti hogyan lehet elérni a távoli figyelési megoldásgyorsító felhasználói felület forráskódját, és egyes testreszabásokat.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: 53361ed460917fff42008283429967eff2e80ab2
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345096"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldásgyorsító testreszabása

Ez a cikk ismerteti, hogyan eléréséhez a Forráskód és a távoli figyelési megoldásgyorsító felhasználói felület testreszabása.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>A helyi fejlesztési környezet előkészítése a felhasználói felületen

A távoli figyelési megoldásgyorsító UI kód a React.js keretrendszer használatával lett megvalósítva. Annak a forráskódot a [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub-adattárban.

A változtatásokat a felhasználói felület, helyileg futtathatja egy példányát. Például a telemetriai adatok beolvasása művelet végrehajtásához a helyi példány csatlakozik a megoldás egy telepített példányát.

A helyi felhasználói felület fejlesztési környezet beállítása a folyamat lépései:

1. Üzembe helyezése egy **alapszintű** példányának a megoldás gyorsító a **számítógépek** CLI. Jegyezze fel a nevét, valamint az üzembe helyezés, a virtuális gép a megadott hitelesítő adatok. További információkért lásd: [üzembe helyezés parancssori felületről](iot-accelerators-remote-monitoring-deploy-cli.md).

1. A megoldás a mikroszolgáltatásokat üzemeltető virtuális géphez SSH-hozzáférés engedélyezéséhez használja az Azure portal vagy az Azure Cloud Shell. Példa:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Fejlesztés és tesztelés során csak engedélyezze az SSH-hozzáférést. Ha engedélyezi az SSH- [, amint elkészült, használja azt tiltsa le,](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Az Azure portal vagy az Azure Cloud Shell használatával keresse meg a nevet és a virtuális gép nyilvános IP-címét. Példa:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Az SSH használatával csatlakozhat a virtuális géphez. Az IP-címet az előző lépést, és a futtatásakor a megadott hitelesítő adatok használata **számítógépek** a megoldás üzembe helyezéséhez. A `ssh` parancs érhető el az Azure Cloud shellben.

1. Ahhoz, hogy a helyi UX csatlakozni, a virtuális gépen a bash felületen futtassa az alábbi parancsokat:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Miután látja, hogy a parancs végrehajtása befejeződött, és a webhely elindul, leválaszthatja a virtuális gépről.

1. A helyi példányának a [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) adattárban szerkesztése a **.env** fájlját, hogy a telepített megoldás URL-címe:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Egy parancssort, keresse meg a helyi példányát a `azure-iot-pcs-remote-monitoring-webui` mappát.

1. A szükséges kódtárak telepítése, és a felhasználói felület helyi futtatását, futtassa a következő parancsokat:

    ```cmd/sh
    npm install
    npm start
    ```

1. Az előző parancs futtatása helyileg, a felhasználói felület http://localhost:3000/dashboard. Szerkesztheti a kódot, a hely futása közben, és próbálja ki, hogy dinamikusan frissülnek.

## <a name="customize-the-layout"></a>Az elrendezés testreszabása

A távoli figyelési megoldás minden egyes oldalához tevődik össze a vezérlők, a továbbiakban *panelek* a forráskódban. A **irányítópult** lap öt panelek épül fel: áttekintése, térkép, riasztások, telemetriai adatokat és elemzések. A forráskód minden lapon és a hozzá tartozó panelek meghatározó annak a [számítógépek-remote-monitoring-mire](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-adattárban. Például, ha a kódot, amely meghatározza a **irányítópult** oldalon, az elrendezését, és a panelek az oldalon található a [irányítópult src/components/oldalak](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) mappát.

A panelek saját elrendezés és a méretezés kezelése, mivel könnyen módosíthatja az oldal elrendezése. A következő módosításokat a **PageContent** eleme a `src/components/pages/dashboard/dashboard.js` fájlt:

* Felcserélés igazítja a térkép és a telemetria panelt.
* A térkép és elemzés paneleken relatív szélességének módosítása

```nodejs
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

![Paraméterpanel-elrendezés módosítása](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Azt is megteheti az azonos panel több példányát, vagy több verzió Ha Ön [ismétlődő és a egy panel testreszabása](#duplicate-and-customize-an-existing-control). Az alábbi példa bemutatja, hogyan adhat hozzá a telemetriai adatok panel két példánya. A módosítások végrehajtásához szerkesztheti az `src/components/pages/dashboard/dashboard.js` fájlt:

```nodejs
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

Különféle telemetriai majd minden egyes panelen tekintheti meg:

![Több telemetria panelek](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Ismétlődő és a egy meglévő vezérlő testreszabása

Az egyes lépései egy meglévő panel ismétlődő, módosítsa, és ezután a módosított verzióját használja. A lépések a **riasztások** példaként panel:

1. Az adattár helyi példányában, készítsen másolatot a **riasztások** mappájában a `src/components/pages/dashboard/panels` mappát. Nevezze el az új példány **cust_alerts**.

1. Az a **alertsPanel.js** fájlt a **cust_alerts** mappában kell az osztály neve szerkesztése **CustAlertsPanel**:

    ```nodejs
    export class CustAlertsPanel extends Component {
    ```

1. Adja hozzá a következő sort a `src/components/pages/dashboard/panels/index.js` fájlt:

    ```nodejs
    export * from './cust_alerts';
    ```

1. Cserélje le `alertsPanel` a `CustAlertsPanel` a a `src/components/pages/dashboard/dashboard.js` fájlt:

    ```nodejs
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

Most már lecserélte az eredeti **riasztások** panel egy másolatot nevű **CustAlerts**. Ez legyen ugyanaz, mint az eredeti. Módosíthatja a példányt. Például alakítani az oszlopot a rendezéshez a **riasztások** panelen:

1. Nyissa meg az `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` fájlt.

1. Módosítsa a magyarázat, az alábbi kódrészletben látható módon:

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

Az alábbi képernyőfelvételen az új verzió a **riasztások** panelen:

![frissített riasztások panel](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>A telemetriai adatok diagram testreszabása

A fájlokat a `src/components/pages/dashboard/panels/telemtry` mappát adja meg a telemetriai adatok diagram a **irányítópult** lapot. A felhasználói felület a telemetria lekéri a megoldás háttérrendszere az a `src/services/telemetryService.js` fájlt. A következő lépések bemutatják, hogyan módosíthatja az adott időszakban, a telemetriai adatok diagram 15 5 perc alatt jelenik meg:

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

1. Ez a függvény használatával töltse fel a telemetriai adatok diagram, nyissa meg a `src/components/pages/dashboard/dashboard.js` fájlt. Keresse meg azt a sort, inicializálja a telemetria-adatfolyam, és módosítsa a következőképpen:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

A telemetriai adatok diagram most már a telemetriai adatok öt perc alatt látható:

![Telemetria diagramját egy nap](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Új KPI hozzáadása

A **irányítópult** oldalon megjelennek a KPI-k a **Analytics** panel. Ezen KPI-k alapján számítjuk ki a `src/components/pages/dashboard/dashboard.js` fájlt. A KPI-k által vannak leképezve a `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` fájlt. A következő lépések azt ismertetik, hogyan kiszámításához és a egy új KPI-érték megjelenítése a a **irányítópult** lapot. A példában is látható, hogy százalékot módosítást hozzáadása a figyelmeztető riasztások KPI:

1. Nyissa meg az `src/components/pages/dashboard/dashboard.js` fájlt. Módosítsa a **initialState** objektum tartalmazza a **warningAlertsChange** tulajdonság az alábbiak szerint:

    ```nodejs
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

1. Módosítsa a **currentAlertsStats** objektum tartalmazza **totalWarningCount** tulajdonságként:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Az új KPI kiszámítása. Keresse meg a kritikus riasztások száma kiszámítása. Ismétlődő a kódot, és módosítsa a példányt a következőképpen:

    ```nodejs
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Tartalmazza az új **warningAlertsChange** a KPI-Stream KPI:

    ```nodejs
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

1. Tartalmazza az új **warningAlertsChange** KPI állapot adatainak megjelenítése a felhasználói felület:

    ```nodejs
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

1. A KPI-k panel átadott adatok frissítése:

    ```node.js
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

A módosítások a már végzett a `src/components/pages/dashboard/dashboard.js` fájlt. Az alábbi lépések bemutatják, hogy a módosítások a `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` fájlt az új KPI megjelenítéséhez:

1. Módosítsa a következő kódsort az új KPI-értékre a következő lekéréséhez:

    ```nodejs
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Módosítsa a jelölőnyelvi az új KPI-értékre a következő megjelenítése:

    ```nodejs
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

A **irányítópult** lap mostantól megjeleníti az új KPI-értékre:

![Figyelmeztetés KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>A térkép testreszabása

Tekintse meg a [testreszabás térkép](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) a GitHub lap a leképezési összetevők a megoldás részleteit.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Egyéb testreszabási lehetőségek

A távoli figyelési megoldásban a bemutatót, és a Vizualizációk réteg további módosításához, szerkesztheti a kódot. A vonatkozó GitHub-adattárak a következők:

* [A konfiguráció mikroszolgáltatás az Azure IoT-megoldások (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [A konfigurációs mikroszolgáltatás Azure IoT-megoldások (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Az Azure IoT-számítógépek távoli megfigyelés a webes felhasználói felületen](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az erőforrások segítenek testre szabni a webes felhasználói felület a távoli figyelési megoldásgyorsító a rendelkezésére. A felhasználói felület testreszabásával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Egyéni oldal hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-page.md)
* [Egy egyéni szolgáltatás hozzáadása a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-service.md)
* [Egy egyéni rács ad hozzá a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-grid.md)
* [A távoli figyelési megoldás gyorsító webes felhasználói felületen ad hozzá egy egyéni úszó menü](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Egy egyéni panel hozzáadása az irányítópulthoz, a távoli figyelési megoldás gyorsító webes felhasználói felületen](iot-accelerators-remote-monitoring-customize-panel.md)

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távoli figyelési megoldás mikroszolgáltatások testreszabásával kapcsolatos további információkért lásd: [testreszabása és ismételt üzembe helyezés mikroszolgáltatások](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
