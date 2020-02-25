---
title: Time Series Insights integrálása távoli figyeléssel – Azure | Microsoft Docs
description: Ebben az útmutatóban megtudhatja, hogyan konfigurálhat Time Series Insights olyan meglévő távoli figyelési megoldáshoz, amely még nem tartalmaz Time Series Insights.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77564644"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Az Azure Time Series Insights integrálása a távoli monitorozással

A Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amellyel kezelheti a Felhőbeli IoT. A Time Series Insights az idősoros adatok tárolására és kezelésére, valamint az események egyidejű feltárására és megjelenítésére, a kiváltó okok elemzésére, valamint több hely és eszköz összehasonlítására használható.

A távoli figyelési megoldás gyorsítása mostantól lehetővé teszi az automatikus üzembe helyezést és az Time Series Insights-integrációt. Ebben a útmutatóban megtudhatja, hogyan konfigurálhat Time Series Insights olyan meglévő távoli figyelési megoldáshoz, amely még nem tartalmaz Time Series Insights.

> [!NOTE]
> Time Series Insights jelenleg nem érhető el az Azure China Cloud-ban. Az Azure China Cloud-ban az új távoli figyelési megoldás-gyorsító üzembe helyezések minden tárterülethez Cosmos DB használnak.

## <a name="prerequisites"></a>Előfeltételek

A útmutató elvégzéséhez már üzembe kell helyeznie egy távoli figyelési megoldást:

* [A távoli figyelési megoldás-gyorsító üzembe helyezése](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Hozzon létre egy fogyasztói csoportot

Hozzon létre egy dedikált fogyasztói csoportot a IoT Hub, hogy az adatátvitelhez használni lehessen a Time Series Insights.

> [!NOTE]
> A fogyasztói csoportokat az alkalmazások használják az Azure-IoT Hub adatainak lekérésére. Minden fogyasztói csoport legfeljebb öt kimeneti fogyasztót tesz lehetővé. Hozzon létre egy új fogyasztói csoportot minden öt kimeneti mosogatóhoz, és legfeljebb 32 fogyasztói csoportot hozhat létre.

1. A Azure Portal kattintson a Cloud Shell gombra.

1. A következő parancs végrehajtásával hozzon létre egy új fogyasztói csoportot. Használja az IoT hub nevét a távoli figyelési telepítésben, valamint a távoli figyelési telepítés nevét az erőforráscsoport neveként:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Time Series Insights üzembe helyezése

Ezután telepítse a Time Series Insightst további erőforrásként a távoli figyelési megoldásba, és kapcsolódjon az IoT hub-hoz.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

1. Válassza **az erőforrás létrehozása** > **eszközök internetes hálózata** > **Time Series Insights**lehetőséget.

    ![Új Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Time Series Insights környezet létrehozásához használja az alábbi táblázatban szereplő értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | A következő képernyőkép a **contorosrmtsi**nevet használja. A lépés elvégzése után válassza ki a saját egyedi nevét. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **Meglévő használata**. Válassza ki a meglévő távoli figyelési erőforráscsoport nevét. |
    | Hely | Az **USA keleti**régióját használjuk. Ha lehetséges, hozza létre a környezetet ugyanabban a régióban, mint a távoli figyelési megoldás. |
    | SKU |**S1** |
    | Kapacitás | **1** |

    ![Time Series Insights létrehozása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Kattintson a **Létrehozás** gombra. A környezet létrehozása eltarthat egy kis ideig.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrás az IoT hubhoz való kapcsolódáshoz. Győződjön meg arról, hogy az előző lépésekben létrehozott fogyasztói csoportot használja. Time Series Insights megköveteli, hogy az egyes szolgáltatások olyan dedikált fogyasztói csoportot használjanak, amelyet más szolgáltatás nem használ.

1. Navigáljon az új Time Series Insights-környezethez.

1. A bal oldalon válassza az **eseményforrás**lehetőséget.

    ![Eseményforrás megtekintése](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson az **Hozzáadás** parancsra.

    ![Eseményforrás hozzáadása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT hub új eseményforrásként való konfigurálásához használja az alábbi táblázatban szereplő értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Eseményforrás neve | A következő képernyőkép a **contosorm-IOT-hub**nevet használja. A lépés elvégzéséhez használja a saját egyedi nevét. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **IoT Hub használata az elérhető előfizetések közül** |
    | Előfizetés azonosítója | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | IOT hub neve | **contosorma57a6**. Használja a IoT hub nevét a távoli figyelési megoldásból. |
    | IOT hub-házirend neve | **iothubowner** Győződjön meg arról, hogy a használt szabályzat egy tulajdonosi házirend. |
    | IOT hub-házirend kulcsa | Ez a mező automatikusan fel van töltve. |
    | IOT hub fogyasztói csoport | **timeseriesinsights** |
    | Eseményszerializációs formátum | **JSON**     | 
    | Időbélyegző-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson a **Létrehozás** gombra.

## <a name="configure-the-data-access-policy"></a>Az adatelérési házirend konfigurálása

Annak ellenőrzéséhez, hogy a távoli figyelési megoldáshoz hozzáféréssel rendelkező összes felhasználó képes-e az adatelemzésre a Time Series Insights Explorerben, adja hozzá az alkalmazást és a felhasználókat a Azure Portal adatelérési házirendek területén. 

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza ki a **ContosoRM** erőforráscsoportot.

1. Az Azure-erőforrások listájában válassza a **contosormtsi** lehetőséget.

1. Válassza az **adatelérési házirendek** lehetőséget a szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

1. A **Hozzáadás** gombra kattintva nyissa meg a **felhasználói szabály kiválasztása** panelt.

   Ha nem rendelkezik jogosultsággal a szerepkörök hozzárendeléséhez, nem jelenik meg a **Hozzáadás** lehetőség.

1. A **szerepkör** legördülő listában válasszon ki egy olyan szerepkört, mint az **olvasó** és a **közreműködő**.

1. A **Kiválasztás** listában válasszon ki egy felhasználót, csoportot vagy alkalmazást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör-hozzárendelés létrehozásához. Néhány pillanat elteltével a rendszerbiztonsági tag hozzá van rendelve a szerepkörhöz az adatelérési házirendekben.

> [!NOTE]
> Ha további felhasználóknak is hozzáférést kell biztosítania a Time Series Insights Intézőhöz, az alábbi lépésekkel biztosíthatja az [adathozzáférést](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Azure Stream Analytics konfigurálása 

A következő lépés a Azure Stream Analytics Manager-szolgáltatás konfigurálása, hogy az üzenetek ne legyenek elküldve Cosmos DB és csak Time Series Insights tárolja őket. Hagyja ki ezt a lépést, ha a Cosmos DBban szeretné duplikálni az üzeneteket.

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza ki a **ContosoRM** erőforráscsoportot.

1. Keresse meg az Azure Stream Analytics (ASA) folyamatos átviteli feladatot az erőforrások listájában. Az erőforrás neve a **streamingjobs-** vel kezdődik.

1. A felső részen kattintson a gombra az ASA streaming-feladatok leállításához.

1. Szerkessze az ASA-lekérdezést, és távolítsa el a **Select**, **into**és **from** záradékokat, amelyek a Cosmos db lévő üzenetek streamre mutatnak. Ezeknek a záradékoknak a lekérdezés alján kell lenniük, és az alábbi példához hasonlóan kell kinéznie:

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

6. Indítsa újra a Azure Stream Analytics streaming-feladatokat.

7. A következő parancs parancssorba való beírásával lekérheti a Azure Stream Analytics Manager Service legújabb módosításait:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>A telemetria-szolgáltatás konfigurálása

A legújabb telemetria-szolgáltatás lekéréséhez írja be a következő parancsot a parancssorba:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Nem kötelező]* A webes felhasználói felület konfigurálása a Time Series Insights Explorerrel való hivatkozáshoz

A Time Series Insights Explorerben egyszerűen megtekintheti az adatait, ezért a felhasználói felület testreszabásával egyszerűen hivatkozhat a környezetre. Ehhez a következő paranccsal kérje le a webes felhasználói felület legújabb módosításait:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Környezeti változók konfigurálása

A Time Series Insights integráció befejezéséhez konfigurálnia kell az üzemelő példányok környezetét a frissített szolgáltatásokhoz.

### <a name="basic-deployments"></a>Alapszintű központi telepítések

Konfigurálja `basic` központi telepítésének környezetét a frissített Service-szolgáltatásokhoz.

1. A Azure Portal kattintson a bal oldali panel **Azure Active Directory** fülére.

1. Kattintson **Alkalmazásregisztrációk**.

1. Keresse meg a **ContosoRM** alkalmazást, és kattintson rá.

1. Navigáljon a **beállítások** > a **kulcsok** elemre, majd hozzon létre egy új kulcsot az alkalmazáshoz. Ügyeljen arra, hogy a kulcs értékét a biztonságos helyre másolja.

1. A legfrissebb címkével lekérheti a GitHub-tárház [legújabb YAML-fájlját](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) . 

1. SSH-t a virtuális gépre az [ssh-kulcsok létrehozása és használata](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)című témakör lépéseit követve.

1. A csatlakozás után írja be a következőt: `cd /app`.

1. Adja hozzá az alábbi környezeti változókat a Docker-összeállítás YAML fájljában található minden egyes szolgáltatáshoz, valamint a `env-setup` parancsfájlt a virtuális gépen:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. A fenti környezeti változók hozzáadásával navigáljon a **telemetria szolgáltatáshoz** , és szerkessze a Docker-összeállítási fájlt is.

1. Navigáljon a **ASA Manager szolgáltatáshoz** , és szerkessze a Docker-összeállítási fájlt `PCS_TELEMETRY_STORAGE_TYPE`hozzáadásával.

1. Indítsa újra a Docker-tárolókat a virtuális gép `sudo ./start.sh` használatával.

> [!NOTE]
> A környezeti változók fenti konfigurációja a távoli figyelési verziók esetében érvényes a 1.0.2 előtt

### <a name="standard-deployments"></a>Szabványos központi telepítések

`standard` üzemelő példány környezetének konfigurálása a frissített Micro Services-szolgáltatásokhoz

1. A parancssorban futtassa a `kubectl proxy`. További információ: [hozzáférés a KUBERNETES API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server)-hoz.

1. Nyissa meg a Kubernetes felügyeleti konzolt.

1. A konfigurációs Térkép megkeresésével adja hozzá a következő új környezeti változókat az ÁME-hez:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Szerkessze a telemetria Service Pod sablon YAML-fájlját:

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

5. Szerkessze az ASA Manager Service Pod sablon YAML-fájlját:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan vizsgálhatja meg adatait, és hogyan diagnosztizálhatja a riasztásokat a Time Series Insights Explorerben, tekintse meg a [kiváltó okok elemzését](iot-accelerators-remote-monitoring-root-cause-analysis.md)ismertető oktatóanyagot.

* Az Time Series Insights Explorerben megjelenő információk megismeréséhez és lekérdezéséhez tekintse meg a [Azure Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)dokumentációját.
