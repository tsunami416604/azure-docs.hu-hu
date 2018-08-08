---
title: A távoli figyelési megoldás testreszabása felhasználói felület – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti hogyan lehet elérni a távoli figyelési megoldásgyorsító felhasználói felület forráskódját, és egyes testreszabásokat.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2018
ms.topic: conceptual
ms.openlocfilehash: 9700a76284e2ee2a652ae0dbcbaa2885ab515f79
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596555"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>A távoli figyelési megoldásgyorsító testreszabása

Ez a cikk ismerteti, hogyan eléréséhez a Forráskód és a távoli figyelési megoldásgyorsító felhasználói felület testreszabása. A cikk ismerteti:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>A helyi fejlesztési környezet előkészítése a felhasználói felületen

A távoli figyelési megoldásgyorsító UI kód a React.js keretrendszer használatával lett megvalósítva. Annak a forráskódot a [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub-adattárban.

A változtatásokat a felhasználói felület, helyileg futtathatja egy példányát. A helyi példány egy különböző műveleteket, például a telemetriai adatok beolvasása a megoldás üzembe helyezett példány csatlakozik.

A helyi felhasználói felület fejlesztési környezet beállítása a folyamat lépései:

1. Üzembe helyezése egy **alapszintű** példányának a megoldás gyorsító a **számítógépek** CLI. Jegyezze fel a nevét, valamint az üzembe helyezés, a virtuális gép a megadott hitelesítő adatok. További információkért lásd: [üzembe helyezés parancssori felületről](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Az Azure Portal vagy a [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ahhoz, hogy az a megoldás a mikroszolgáltatásokat üzemeltető virtuális gép SSH-hozzáférését. Példa:

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    SSH-hozzáférés csak engedélyezze a fejlesztés és tesztelés során. Ha engedélyezi az SSH- [újra minél hamarabb tiltsa le,](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-azure-virtual-machines).

1. Az Azure Portal vagy a [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nevét és a virtuális gép nyilvános IP-cím megkereséséhez. Példa:

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. SSH-val csatlakozzon a virtuális géphez az IP-címet az előző lépést, és a futtatásakor a megadott hitelesítő adatok használatával **számítógépek** a megoldás üzembe helyezéséhez.

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

1. A helyi példányának parancsot a parancssorba a `azure-iot-pcs-remote-monitoring-webui` mappában futtassa a következő parancsokat a szükséges kódtárak telepítése és a felhasználói felület helyileg történő futtatása:

    ```cmd/sh
    npm install
    npm start
    ```

1. Az előző parancs futtatása helyileg, a felhasználói felület http://localhost:3000/dashboard. Szerkesztheti a kódot, a hely futása közben, és próbálja ki, hogy dinamikusan frissülnek.

## <a name="customize-the-layout"></a>Az elrendezés testreszabása

A távoli figyelési megoldás minden egyes oldalához tevődik össze a vezérlők, a továbbiakban *panelek* a forráskódban. Ha például a **irányítópult** lap öt panelek épül fel: áttekintése, térkép, riasztások, telemetriai adatok és a KPI-k. A forráskód minden lapon és a hozzá tartozó panelek meghatározó annak a [számítógépek-remote-monitoring-mire](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub-adattárban. Például, ha a kódot, amely meghatározza a **irányítópult** oldalon, az elrendezését, és a panelek az oldalon található a [irányítópult src/components/oldalak](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) mappát.

A panelek saját elrendezés és a méretezés kezelése, mivel könnyen módosíthatja az oldal elrendezése. Például a következő módosításai a **PageContent** eleme a `src/components/pages/dashboard/dashboard.js` fájl cseréje igazítja a térkép és a telemetria panelt, és módosítsa a relatív szélességét a térkép és a KPI panelek:

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

![Paraméterpanel-elrendezés módosítása](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> A leképezés nem történik meg a helyi környezetben.

Azt is megteheti az azonos panel több példányát, vagy több verzió Ha Ön [ismétlődő és a egy panel testreszabása](#duplicate-and-customize-an-existing-control). Az alábbi példa bemutatja, hogyan adhat hozzá a telemetriai adatok panel két példánya szerkesztésével a `src/components/pages/dashboard/dashboard.js` fájlt:

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

Különféle telemetriai majd minden egyes panelen tekintheti meg:

![Több telemetria panelek](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> A leképezés nem történik meg a helyi környezetben.

## <a name="duplicate-and-customize-an-existing-control"></a>Ismétlődő és a egy meglévő vezérlő testreszabása

Az alábbi lépéseket vázlat használata a **riasztások** panel egy meglévő panel ismétlődő, módosítsa, és a módosított verzióját használja példaként:

1. Az adattár helyi példányában, készítsen másolatot a **riasztások** mappájában a `src/components/pages/dashboard/panels` mappát. Nevezze el az új példány **cust_alarms**.

1. Az a **alarmsPanel.js** fájlt a **cust_alarms** mappában kell az osztály neve szerkesztése **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Adja hozzá a következő sort a `src/components/pages/dashboard/panels/index.js` fájlt:

    ```nodejs
    export * from './cust_alarms';
    ```

1. Cserélje le `AlarmsPanel` a `CustAlarmsPanel` a a `src/components/pages/dashboard/dashboard.js` fájlt:

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

Most már átvették a az eredeti **riasztások** panel egy másolatot nevű **CustAlarms**. Ezt a másolatot megegyezik az eredeti. Módosíthatja a példányt. Például alakítani az oszlopot a rendezéshez a **riasztások** panelen:

1. Nyissa meg az `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` fájlt.

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

![Frissítve a riasztások panelén](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>A telemetriai adatok diagram testreszabása

A telemetriai adatok diagramra a **irányítópult** lapon határozza meg a fájlokat a `src/components/pages/dashboard/panels/telemtry` mappát. A felhasználói felület a telemetria lekéri a megoldás háttérrendszere az a `src/services/telemetryService.js` fájlt. A következő lépések bemutatják, hogyan módosíthatja az adott időszakban, a telemetriai diagram 15 perc és 5 perc jelenik meg:

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

A **irányítópult** oldalon megjelennek a KPI-k a **rendszer KPI-k** panel. Ezen KPI-k alapján számítjuk ki a `src/components/pages/dashboard/dashboard.js` fájlt. A KPI-k által vannak leképezve a `src/components/pages/dashboard/panels/kpis/kpisPanel.js` fájlt. A következő lépések azt ismertetik, hogyan kiszámításához és a egy új KPI-érték megjelenítése a a **irányítópult** lapot. A példában is látható, hogy százalékot módosítást hozzáadása a figyelmeztető riasztások KPI:

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

1. Módosítsa a **currentAlarmsStats** objektum tartalmazza **totalWarningCount** tulajdonságként:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Az új KPI kiszámítása. Keresse meg a kritikus riasztások számának kiszámítása. Ismétlődő a kódot, és módosítsa a példányt a következőképpen:

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

1. Tartalmazza az új **warningAlarmsChange** a KPI-Stream KPI:

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

1. A KPI-k panel átadott adatok frissítése:

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

Most már befejezte a változásokat a `src/components/pages/dashboard/dashboard.js` fájlt. Az alábbi lépések bemutatják, hogy a módosítások a `src/components/pages/dashboard/panels/kpis/kpisPanel.js` fájlt az új KPI megjelenítéséhez:

1. Módosítsa a következő kódsort az új KPI-értékre a következő lekéréséhez:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Módosítsa a jelölőnyelvi az új KPI-értékre a következő megjelenítése:

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

* [A konfiguráció mikroszolgáltatás az Azure IoT-megoldások (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [A konfigurációs mikroszolgáltatás Azure IoT-megoldások (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Az Azure IoT-számítógépek távoli megfigyelés a webes felhasználói felületen](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az erőforrások segítenek testre szabni a webes felhasználói felület a távoli figyelési megoldásgyorsító a rendelkezésére.

További elméleti kapcsolatos további információkért a távoli figyelési megoldásgyorsító: [távoli figyelési architektúrával](iot-accelerators-remote-monitoring-sample-walkthrough.md)

A távoli figyelési megoldás testreszabásával kapcsolatos további információkért lásd: [testreszabása és ismételt mikroszolgáltatások üzembe helyezése](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->