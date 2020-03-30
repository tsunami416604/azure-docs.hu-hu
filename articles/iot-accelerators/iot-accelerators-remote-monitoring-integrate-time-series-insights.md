---
title: A Time Series Insights integrálása a távoli figyeléssel - Azure | Microsoft dokumentumok
description: Ebben a how-to megtudhatja, hogyan konfigurálhatja a Time Series Insights egy meglévő távfigyelési megoldás, amely még nem tartalmazza a Time Series Insights.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564644"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Az Azure Time Series Insights integrálása a távoli monitorozással

Az Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás az IoT-léptékű idősorozat-adatok felhőben történő kezeléséhez. A Time Series Insights segítségével idősorozat-adatokat tárolhat és kezelhet, eseményeket fedezhet fel és jeleníthet meg egyidejűleg, kiváltó ok-elemzéseket végezhet, valamint több webhelyet és eszközt hasonlíthat össze.

A távfigyelési megoldásgyorsító mostantól automatikus üzembe helyezést és integrációt biztosít a Time Series Insights szolgáltatással. Ebben az útmutatóban megtudhatja, hogyan konfigurálhatja a Time Series Insights-ot egy meglévő távoli figyelési megoldáshoz, amely még nem tartalmazza a Time Series Insights-ot.

> [!NOTE]
> A Time Series Insights jelenleg nem érhető el az Azure China felhőben. Az Azure China felhőben az új távoli figyelési megoldásgyorsítók a Cosmos DB-t használják az összes tárhoz.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez már üzembe kell helyeznie egy távoli figyelési megoldást:

* [A távfigyelési megoldás gyorsítójának telepítése](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Fogyasztói csoport létrehozása

Hozzon létre egy dedikált fogyasztói csoportot az IoT Hubon, amely et a Time Series Insights adatok streameléséhez kell használni.

> [!NOTE]
> Fogyasztói csoportok az alkalmazások segítségével adatokat az Azure IoT Hub. Minden fogyasztói csoport legfeljebb öt kimeneti fogyasztó számára teszi lehetővé a felhasználók számára. Hozzon létre egy új fogyasztói csoportot minden öt kimeneti fogadók és hozhat létre akár 32 fogyasztói csoportok.

1. Az Azure Portalon kattintson a Cloud Shell gombra.

1. Új fogyasztói csoport létrehozásához hajtsa végre a következő parancsot. Használja az IoT hub nevét a távoli figyelés központi telepítésében, és a távoli figyelés központi telepítésének nevét erőforráscsoport ként:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Idősorozat-elemzési adatok üzembe helyezése

Ezután telepítse a Time Series Insights-ot további erőforrásként a távoli figyelési megoldásba, és csatlakoztassa az IoT hubhoz.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Válassza **az Erőforrás internetes** > **dolgok** > **internete, idősorozat-elemzések**lehetőséget.

    ![Új idősorozat-elemzések](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. A Time Series Insights-környezet létrehozásához használja az alábbi táblázatértékeit:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | A következő képernyőkép a **contorosrmtsi**nevet használja. A lépés során válassza ki saját egyedi nevét. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **A meglévő használata.** Válassza ki a meglévő távfigyelési erőforráscsoport nevét. |
    | Hely | Az **USA keleti részét**használjuk. Hozzon létre a környezetet ugyanabban a régióban, mint a távoli figyelési megoldás, ha lehetséges. |
    | SKU |**S1** |
    | Kapacitás | **1** |

    ![Idősorozat-elemzési adatok létrehozása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Kattintson **a Létrehozás gombra.** Beletelhet egy kis időbe, amíg létrejön a környezet.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrást az IoT hubhoz való csatlakozáshoz. Győződjön meg arról, hogy az előző lépésekben létrehozott fogyasztói csoportot használja. A Time Series Insights megköveteli, hogy minden szolgáltatás egy dedikált fogyasztói csoport nem használja egy másik szolgáltatás.

1. Keresse meg az új Time Series Insights-környezetet.

1. A bal oldalon válassza az **Eseményforrások lehetőséget.**

    ![Eseményforrások megtekintése](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson a **Hozzáadás** gombra.

    ![Eseményforrás hozzáadása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT-központ új eseményforrásként való konfigurálásához használja az alábbi táblázatban szereplő értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Eseményforrás neve | A következő képernyőkép a **contosorm-iot-hub**nevet használja. A lépés végrehajtásával saját egyedi nevet használjon. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **Az IoT Hub használata elérhető előfizetésekből** |
    | Előfizetés azonosítója | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | IoT Hub neve | **contosorma57a6**. Használja az IoT hub nevét a távoli figyelési megoldásból. |
    | Iot Hub szabályzatneve | **iothubowner** Győződjön meg arról, hogy a használt házirend tulajdonosi házirend. |
    | A központ iot-kulcsa | Ez a mező automatikusan ki töltődik. |
    | IoT Hub fogyasztói csoport | **idősorok betekintése** |
    | Eseményszerializációs formátum | **JSON**     | 
    | Időbélyeg-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson **a Létrehozás gombra.**

## <a name="configure-the-data-access-policy"></a>Az adatelérési házirend konfigurálása

Annak érdekében, hogy a távfigyelési megoldáshoz hozzáféréssel rendelkező összes felhasználó képes legyen a Time Series Insights-kezelő ben lévő adatok feltárására, adja hozzá az alkalmazást és a felhasználókat az Azure Portalon az adatelérési szabályzatok alá. 

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza ki a **ContosoRM** erőforráscsoportot.

1. Válassza **ki a contosormtsi** az Azure-erőforrások listájában.

1. Válassza **az Adatelérési házirendek** lehetőséget a szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

1. A **Hozzáadás gombra** a **Felhasználói szabály kiválasztása** ablaktábla megnyitásához válassza a Hozzáadás gombot.

   Ha nincs engedélye a szerepkörök hozzárendeléséhez, nem jelenik meg a **Hozzáadás** lehetőség.

1. A **Szerepkör** legördülő listában válasszon ki egy szerepkört, például a **Reader** és **a Contributor**.

1. A **Kiválasztás** listában válasszon ki egy felhasználót, csoportot vagy alkalmazást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör-hozzárendelés létrehozásához. Néhány pillanat múlva a rendszerbiztonsági tag van rendelve a szerepkör adatelérési házirendekben.

> [!NOTE]
> Ha további felhasználóknak kell hozzáférést biztosítania a Time Series Insights-kezelőhöz, ezekkel a lépésekkel biztosíthatja az [adatokhoz való hozzáférést.](../time-series-insights/time-series-insights-data-access.md#grant-data-access)

## <a name="configure-azure-stream-analytics"></a>Az Azure Stream Analytics konfigurálása 

A következő lépés az, hogy konfigurálja az Azure Stream Analytics Manager mikroszolgáltatás, hogy ne küldjön üzeneteket a Cosmos DB és tárolja őket csak a Time Series Insights. Hagyja ki ezt a lépést, ha szeretné duplikálni az üzeneteket a Cosmos DB-ben.

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza ki a **ContosoRM** erőforráscsoportot.

1. Keresse meg az Azure Stream Analytics (ASA) streamelési feladat az erőforrások listájában. Az erőforrás neve **streamelési feladatokkal kezdődik.**

1. A tetején kattintson a gombra az ASA streamelési feladatok leállításához.

1. Szerkesztheti az ASA-lekérdezést, és távolítsa el a **SELECT**, **INTO**és **FROM** záradékokat, amelyek a Cosmos DB-ben lévő üzenetek adatfolyamára mutatnak. Ezeknek a záradékoknak a lekérdezés alján kell lenniük, és a következő példához hasonlóan kell kinézniük:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Indítsa újra az Azure Stream Analytics streamelési feladatokat.

7. Az Azure Stream Analytics-kezelő mikroszolgáltatáslegújabb módosításait a következő parancs parancsának beírásával kérje le a parancssorba:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>A telemetriai mikroszolgáltatás konfigurálása

A legújabb Telemetriai mikroszolgáltatás lekérése a következő parancs beírásával a parancssorba:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Nem kötelező]* A webes felhasználói felület konfigurálása a Time Series Insights-kezelőre mutató hivatkozáshoz

Az adatok nak a Time Series Insights-kezelőben való egyszerű megtekintéséhez javasoljuk, hogy a felhasználói felület testreszabásával egyszerűen kapcsolódjon a környezethez. Ehhez a következő paranccsal húzza le a webes felhasználói felület legújabb módosításait:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>A környezeti változók konfigurálása

A Time Series Insights-integráció befejezéséhez konfigurálnia kell a központi telepítés környezetét a frissített mikroszolgáltatásokhoz.

### <a name="basic-deployments"></a>Alapvető telepítések

Konfigurálja a `basic` központi telepítés környezetét a frissített mikroszolgáltatásokhoz.

1. Az Azure Portalon kattintson az **Azure Active Directory** fülre a bal oldali panelen.

1. Kattintson **az alkalmazásregisztrációk ra.**

1. Keresse meg, és kattintson a **ContosoRM-alkalmazásra.**

1. Nyissa meg a **Beállítások** > **kulcsok lapot,** és hozzon létre egy új kulcsot az alkalmazáshoz. Győződjön meg arról, hogy a kulcsértéket biztonságos helyre másolja.

1. A [legújabb docker-írásyaml fájl lekérése](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) a GitHub-tárból a legújabb címke használatával. 

1. SSH a virtuális gépbe az [SSH-kulcsok létrehozására és használatára](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)vonatkozó lépések végrehajtásával.

1. A csatlakozás `cd /app`után írja be a szöveget.

1. Adja hozzá a következő környezeti változókat a docker-írásyaml fájl és a `env-setup` virtuális gép parancsfájljában lévő minden mikroszolgáltatáshoz:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Keresse meg a **telemetriai szolgáltatás,** és is szerkeszti a docker-írásfájl hozzáadásával ugyanazokat a környezeti változókat a fenti.

1. Nyissa meg az **ASA-kezelő szolgáltatást,** és szerkessze a docker-összeállítási fájlt a hozzá adásával. `PCS_TELEMETRY_STORAGE_TYPE`

1. Indítsa újra a `sudo ./start.sh` docker-tárolókat a virtuális gépről.

> [!NOTE]
> A környezeti változók fenti konfigurációja az 1.0.2 előtti távoli figyelési verziókra érvényes.

### <a name="standard-deployments"></a>Szabványos telepítések

A telepítés `standard` környezetének konfigurálása a fenti frissített mikroszolgáltatásokhoz

1. A parancssorból `kubectl proxy`futtassa a parancsot. További információ: [A Kubernetes API elérése.](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server)

1. Nyissa meg a Kubernetes felügyeleti konzolt.

1. Keresse meg a konfigurációs térképet a következő új környezeti változók hozzáadásához az 1SI-hez:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Szerkessze a telemetriai szolgáltatáspod sablony fájlját:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Szerkessze az ASA manager szolgáltatáspod sablony fájlját:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>További lépések

* Ha többet szeretne megtudni arról, hogyan fedezheti fel az adatokat, és hogyan diagnosztizálhat riasztást a Time Series Insights felfedezőjében, tekintse meg a [kiváltó ok elemzéséről szóló](iot-accelerators-remote-monitoring-root-cause-analysis.md)oktatóanyagunkat.

* Az adatok nak a Time Series Insights-kezelőben való feltárásáról és lekérdezéséről az [Azure Time Series Insights-kezelő](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)dokumentációjában olvashat.
