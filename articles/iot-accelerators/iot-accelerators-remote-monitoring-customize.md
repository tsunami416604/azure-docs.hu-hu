---
title: A távoli figyelési megoldás felhasználói felületének testreszabása – Azure | Microsoft dokumentumok
description: Ez a cikk arról nyújt tájékoztatást, hogy miként érheti el a távfigyelési megoldás gyorsító felhasználói felületének forráskódját, és hogyan érhet el néhány testreszabást.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: eb3d5fea68b5b1b6e648943cb3dbaab5857e9e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608004"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>A távfigyelési megoldás gyorsítójának testreszabása

Ez a cikk aforráskód eléréséről és a távfigyelési megoldásgyorsító felhasználói felületének testreszabásáról nyújt tájékoztatást.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Helyi fejlesztési környezet előkészítése a felhasználói felületszámára

A Távoli figyelési megoldásgyorsító felhasználói felületi kódja a React.js keretrendszer használatával valósul meg. A forráskódot az [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub tárházban találja.

A felhasználói felület módosításához helyileg futtathatja annak másolatát. A műveletek, például a telemetriai adatok beolvasása, a helyi példány csatlakozik a megoldás üzembe helyezett példányához.

A következő lépések ismertetik a felhasználói felület fejlesztéséhez szükséges helyi környezet beállításának folyamatát:

1. Telepítse a megoldásgyorsító **alappéldányát** a **CLI pc-k** használatával. Jegyezze fel a központi telepítés nevét és a virtuális géphez megadott hitelesítő adatokat. További információ: [Deploy using the CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. SSH-hozzáférés engedélyezéséhez a virtuális gép, amely a megoldásban a mikroszolgáltatásokat üzemelteti, használja az Azure Portalon vagy az Azure Cloud Shell. Példa:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Csak a tesztelés és fejlesztés során engedélyezze az SSH-hozzáférést. Ha engedélyezi az [SSH-t, tiltsa le, amint befejezte a használatát.](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines)

1. Az Azure Portalon vagy az Azure Cloud Shell ben megkeresheti a virtuális gép nevét és nyilvános IP-címét. Példa:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Az SSH segítségével csatlakozzon a virtuális géphez. Használja az előző lépés IP-címét, és a megadott hitelesítő adatokat, amikor a **rendszer futtatta** a megoldást. A `ssh` parancs elérhető az Azure Cloud Shellben.

1. A helyi felhasználói élmény csatlakoztatásához futtassa a következő parancsokat a virtuális gép bash rendszerhéjában:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Miután látta, hogy a parancs befejeződik, és a webhely elindul, leválaszthatja a virtuális gépet.

1. Az [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) tárház helyi példányában az **.env** fájl szerkesztésével adja hozzá a telepített megoldás URL-címét:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. A parancssorban keresse meg a `azure-iot-pcs-remote-monitoring-webui` mappa helyi példányát.

1. A szükséges könyvtárak telepítéséhez és a felhasználói felület helyi futtatásához futtassa a következő parancsokat:

    ```cmd/sh
    npm install
    npm start
    ```

1. Az előző parancs helyileg futtatja\/a felhasználói felületet a következő címen: /localhost:3000/dashboard. A webhely futása közben szerkesztheti a kódot, és dinamikusan láthatja a frissítést.

## <a name="customize-the-layout"></a>Az elrendezés testreszabása

A távoli figyelési megoldás minden lapja egy vezérlőkészletből áll, amelyet a forráskódban *paneleknek* neveznek. Az **Irányítópult** lap öt panelből áll: Áttekintés, Térkép, Riasztások, Telemetria és Analytics. A [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub repository-ban megtalálhatja az egyes lapokat és paneljeit meghatározó forráskódot. Az **Irányítópult** lapot, annak elrendezését és a lapon lévő paneleket meghatározó kód például az [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) mappában található.

Mivel a panelek saját elrendezést és méretezést kezelnek, egyszerűen módosíthatja az oldal elrendezését. A fájl **PageContent** elemének következő `src/components/pages/dashboard/dashboard.js` módosításai:

* Cseréld ki a térkép és a telemetriai panelek pozícióit.
* Módosítsa a térkép és az elemzési panelek relatív szélességét.

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

![Panelelrendezés módosítása](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Ugyanannak a panelnek több példányát is hozzáadhatja, vagy több verziót is hozzáadhat, ha [másol és testreszab egy panelt.](#duplicate-and-customize-an-existing-control) A következő példa bemutatja, hogyan adhat hozzá két példányt a telemetriai panel. A módosítások végrehajtása korszerkeszti a `src/components/pages/dashboard/dashboard.js` fájlt:

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

Ezután megtekintheti a különböző telemetriai adatokat az egyes paneleken:

![Több telemetriai panel](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Meglévő vezérlő másolása és testreszabása

A következő lépések bemutatják, hogyan lehet egy meglévő panelt megkettőzni, módosítani, majd használni a módosított verziót. A lépések példaként a **riasztások** panelt használják:

1. A tárház helyi példányában készítsen másolatot **alerts** a mappában `src/components/pages/dashboard/panels` lévő riasztások mappáról. Nevezze el az új másolatot **cust_alerts**.

1. A **cust_alerts** mappában található **alertsPanel.js** fájlban szerkesztsd a **CustAlertsPanel**osztály nevét:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Adja hozzá a `src/components/pages/dashboard/panels/index.js` következő sort a fájlhoz:

    ```javascript
    export * from './cust_alerts';
    ```

1. Csere `alertsPanel` `CustAlertsPanel` a `src/components/pages/dashboard/dashboard.js` fájlban:

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

Most lecserélte az eredeti **riasztások** panelt egy **CustAlerts nevű példányra.** Ez a másolat megegyezik az eredetivel. Most már módosíthatja a másolatot. Ha például módosítani szeretné az oszlop sorrendjét a **riasztások** panelen:

1. Nyissa meg az `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` fájlt.

1. Módosítsa az oszlopdefiníciókat a következő kódrészletben látható módon:

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

Az alábbi képernyőképen a **riasztások** panel új verziója látható:

![riasztások panel frissítve](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>A telemetriai diagram testreszabása

A mappában `src/components/pages/dashboard/panels/telemtry` lévő fájlok határozzák meg a telemetriai diagramot az **Irányítópult** lapon. A felhasználói felület lekéri a telemetriai `src/services/telemetryService.js` adatokat a megoldás háttérrendszeréből a fájlban. A következő lépések bemutatják, hogyan módosíthatja a telemetriai diagramon megjelenő időszakot 15 percről 5 percre:

1. A `src/services/telemetryService.js` fájlban keresse meg a **getTelemetryByDeviceIdP15M**nevű függvényt. Készítsen másolatot erről a funkcióról, és módosítsa a másolatot az alábbiak szerint:

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

1. Ha ezzel az új függvénnyel szeretné feldolgozni `src/components/pages/dashboard/dashboard.js` a telemetriai diagramot, nyissa meg a fájlt. Keresse meg a telemetriai adatfolyamot inicializáló sort, és módosítsa az alábbiak szerint:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

A telemetriai diagram most a telemetriai adatok öt percét jeleníti meg:

![Telemetriai diagram, amely egy nap](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Új Fő teljesítménymutató hozzáadása

Az **Irányítópult** lapon a Fő alkalmazási szint az **Analytics** panelen jelenik meg. Ezeket a kpi-ket a fájl ban számítja ki a `src/components/pages/dashboard/dashboard.js` rendszer. A kpi-k megjelenítését `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` a fájl teszi meg. Az alábbi lépések bemutatják, hogyan számíthatki és jeleníthet meg egy új Fő teljesítménymutató-értéket az **Irányítópult** lapon. A bemutatott példa az, hogy egy új százalékos változás a figyelmeztető riasztások KPI:

1. Nyissa meg az `src/components/pages/dashboard/dashboard.js` fájlt. Módosítsa a **initialState** objektumot úgy, hogy az a következő **figyelmeztetést tartalmazza:**

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

1. Módosítsa a **currentAlertsStats** objektumot úgy, hogy **az totalWarningCount** tulajdonságként szerepeljen:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Számítsa ki az új KPI-t. Keresse meg a kritikus riasztások száma számítását. Kettőzze meg a kódot, és módosítsa a másolatot az alábbiak szerint:

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

1. Az új **figyelmeztetés felvételeRiasztásokVáltozási** kpi-t tartalmaz a KPI-adatfolyamban:

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

1. Az új **figyelmeztetés tartalmazzaA riasztásmódosítási** kpi-t a felhasználói felület megjelenítéséhez használt állapotadatokban:

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

1. Frissítse a Fő ip-felpanelnek átadott adatokat:

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

Ezzel befejezte a `src/components/pages/dashboard/dashboard.js` fájl módosításait. A következő lépések az új `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` fő teljesítménymutató megjelenítéséhez a fájlban elvégzett módosításokat ismertetik:

1. Módosítsa a következő kódsort az új KPI-érték beolvasásához az alábbiak szerint:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Módosítsa a jelölést úgy, hogy az új KPI-érték a következőképpen jelenjen meg:

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

Az **Irányítópult** lap mostantól az új KPI-értéket jeleníti meg:

![Figyelmeztetés KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>A térkép testreszabása

A megoldás térképösszetevőinek részleteit a GitHub térképösszetevőinek [testreszabása](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) című lapon találja.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Egyéb testreszabási lehetőségek

A távoli figyelési megoldás bemutató- és vizualizációs rétegének további módosításához szerkesztheti a kódot. A megfelelő GitHub-adattárak a következők:

* [Az Azure IoT Solutions (.NET) konfigurációs mikroszolgáltatása](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Az Azure IoT-megoldások (Java) konfigurációs mikroszolgáltatása](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Azure IoT PC-k távoli figyelési webes felhasználói felülete](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>További lépések

Ebben a cikkben a távoli figyelési megoldás gyorsítójában a webes felhasználói felület testreszabásához rendelkezésre álló erőforrásokról szerzett tudomást. Ha többet szeretne tudni a felhasználói felület testreszabásáról, olvassa el az alábbi cikkeket:

* [Egyéni lap hozzáadása a távfigyelési megoldásgyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-page.md)
* [Egyéni szolgáltatás hozzáadása a távfigyelési megoldás gyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-service.md)
* [Egyéni rács hozzáadása a távfigyelési megoldásgyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-grid.md)
* [Egyéni úszó panel hozzáadása a távfigyelési megoldásgyorsító webes felhasználói felületéhez](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Egyéni panel hozzáadása az irányítópulthoz a távfigyelési megoldásgyorsító webes felhasználói felületén](iot-accelerators-remote-monitoring-customize-panel.md)

A távfigyelési megoldásgyorsítóval kapcsolatos további információkért lásd: [Távoli figyelési architektúra](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távfigyelési megoldás mikroszolgáltatásainak testreszabásáról a [Mikroszolgáltatás testreszabása és újratelepítése](iot-accelerators-microservices-example.md)című témakörben talál további információt.
<!-- Next tutorials in the sequence -->
