---
title: Az Azure Time Series Insights integrálása a távoli Monitorozással |} A Microsoft Docs
description: Ebben az útmutatóban, megtudhatja, hogyan konfigurálhatja a Time Series Insights, amely már nem tartalmazza a Time Series Insights távoli figyelési megoldás.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e6dcbf9d185b45c18261e47e9d575adf40812611
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53253816"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Az Azure Time Series Insights integrálása a távoli monitorozással

Az Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amellyel IoT-méretű idősoros adatok felhőbeli kezelését végezheti el. A Time Series Insights tárolására és idősorozat-adatok kezelése, Fedezze fel és események megjelenítése egyszerre, egyetlen elvégezheti az alapvető okok elemzése, és összehasonlíthat több helyet és eszközöket használhatja.

A távoli figyelési megoldásgyorsító az automatikus központi telepítési és a Time Series Insights-integráció mostantól biztosít. Ebben az útmutatóban megismerheti, hogyan konfigurálhatja a Time Series Insights, amely már nem tartalmazza a Time Series Insights távoli figyelési megoldás.

> [!NOTE]
> A Time Series Insights jelenleg nem áll rendelkezésre az Azure China felhőben. Új távoli figyelési megoldás gyorsító üzemelő példányok az Azure China felhőben Cosmos DB összes tárolót használja.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához szüksége már üzembe helyezte a távoli figyelési megoldást:

* [A távoli figyelési megoldásgyorsító üzembe helyezése](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Hozzon létre egy fogyasztói csoportot

Hozzon létre egy dedikált fogyasztói csoportot az IoT hub streamelési adatok a Time Series Insights használható.

> [!NOTE]
> Adatok lekérése az Azure IoT Hub fogyasztói csoportok alkalmazások használják. Minden felhasználói csoport legfeljebb öt kimeneti fogyasztók számára lehetővé teszi. Egy új felhasználói csoportot kell létrehozni, a minden ötödik kimeneti fogadóként és legfeljebb 32 fogyasztói csoportot is létrehozhat.

1. Az Azure Portalon a Cloud Shell gombra.

1. Futtassa a következő parancsot egy új felhasználói csoport létrehozásához. A távoli figyelési környezetében az IoT hub nevére, és a távoli figyelési központi telepítés nevét használja az erőforráscsoport neve:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>A Time Series Insights üzembe helyezése

Ezután helyezze üzembe a Time Series Insights további erőforrásként a távoli figyelési megoldás, és csatlakoztassa az IoT hubon.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

1. Válassza ki **erőforrás létrehozása** > **IOT-** > **Time Series Insights**.

    ![Új Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. A Time Series Insights-környezet létrehozásához használja az alábbi táblázatban az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | A nevet használja az alábbi képernyőképen **contorosrmtsi**. Válassza ki a saját egyedi nevet, ha e lépés elvégzése után. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **Meglévő használata**. Válassza ki a meglévő távoli megfigyelés erőforráscsoport nevét. |
    | Hely | Használjuk **USA keleti Régiójában**. Ha lehetséges létrehozni a környezetet és a távoli figyelési megoldás ugyanabban a régióban. |
    | SKU |**S1** |
    | Kapacitás | **1** |

    ![A Time Series Insights létrehozása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Kattintson a **Create** (Létrehozás) gombra. A környezetben kell létrehozni egy kis időt is igénybe vehet.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrás csatlakozni az IoT hubhoz. Győződjön meg arról, hogy használja a fogyasztói csoportot az előző lépésekben létrehozott-e. A Time Series Insights igényel minden egyes szolgáltatást, hogy dedikált fogyasztói csoportot nem használatban van egy másik szolgáltatás.

1. Keresse meg az új Time Series Insights-környezethez.

1. A bal oldalon válassza ki a **eseményforrások**.

    ![Esemény források megtekintése](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson a **Hozzáadás** parancsra.

    ![Eseményforrás hozzáadása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT hub új esemény forrásként konfigurálásához használja az alábbi táblázatban az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Eseményforrás nevének | A nevet használja az alábbi képernyőképen **contosorm-iot-hub**. Használja a saját egyedi névre, ha e lépés elvégzése után. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **Az IoT Hub használata a rendelkezésre álló előfizetések közül** |
    | Előfizetés azonosítója | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | IOT hub nevét | **contosorma57a6**. A távoli figyelési megoldás az IoT-központ nevét használja. |
    | IOT hub házirend neve | **iothubowner** győződjön meg, hogy a használt szabályzat egy tulajdonos házirend. |
    | IOT hub házirendjének kulcsa | Ez a mező automatikusan fel van töltve. |
    | IOT hub fogyasztói csoport | **timeseriesinsights** |
    | Eseményszerializációs formátum | **JSON**     | Időbélyegző-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="configure-the-data-access-policy"></a>Az adat-hozzáférési házirend konfigurálása

Győződjön meg arról, hogy a távoli figyelési megoldás hozzáféréssel rendelkező felhasználók képesek adatfeltárás a Time Series Insights Explorer, az alkalmazás és az adathozzáférési házirendek a felhasználók hozzáadása az Azure Portalon. 

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza ki a **ContosoRM** erőforráscsoportot.

1. Válasszon **contosormtsi** Azure-erőforrások listájában.

1. Válasszon **az adathozzáférési házirendek** szerepkör-hozzárendelések aktuális listájának megtekintéséhez.

1. Válasszon **Hozzáadás** megnyitásához a **válassza ki a felhasználói szabály** ablaktáblán.

   Nem rendelkezik engedélyekkel szerepkörök hozzárendeléséhez, ha nem látja a **Hozzáadás** lehetőséget.

1. Az a **szerepkör** legördülő listában válassza ki a megfelelő szerepkör **olvasó** és **közreműködői**.

1. A **Kiválasztás** listában válasszon ki egy felhasználót, csoportot vagy alkalmazást. Ha a listában nem látja a rendszerbiztonsági tagot, írhat a **Kiválasztás** mezőbe megjelenítendő nevek, e-mail-címek és objektumazonosítók a címtárban történő kereséséhez.

1. Kattintson a **Mentés** gombra a szerepkör-hozzárendelés létrehozásához. Néhány pillanat múlva a rendszerbiztonsági tagot az adathozzáférési házirendek a szerepét tölti.

> [!NOTE]
> Ha további felhasználókat a hozzáférési jogot a Time Series Insights explorer van szüksége, használhatja az alábbi lépéseket [adathozzáférés](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Az Azure Stream Analytics konfigurálása 

A következő lépés, hogy konfigurálja az Azure Stream Analytics Manager mikroszolgáltatás lemondásához üzenetek küldése a Cosmos DB-hez, és csak a Time Series Insights tárolja őket. Hagyja ki ezt a lépést, ha az, Cosmos DB-ben az üzenetek ismétlődő szeretné.

1. A navigációs listában válassza az **Erőforráscsoportok** lehetőséget.

1. Válassza ki a **ContosoRM** erőforráscsoportot.

1. Keresse meg az Azure Stream Analytics (ASA) a streamelési feladat az erőforrások listájában. Az erőforrás neve kezdődik **streamingjobs -**.

1. A képernyő felső részén a gombra kattintva állítsa le az ASA streamelési feladatok.

1. Az ASA lekérdezés szerkesztése és eltávolítása a **kiválasztása**, **INTO**, és **FROM** , amelyek az üzenetek mutasson adatfolyam Cosmos DB-ben. Ezekkel a záradékokkal legyen alján a lekérdezést, és keresse meg az alábbi példához hasonlóan:

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

6. Indítsa újra az Azure Stream Analytics streamelési feladatok.

7. Kérje le a változásokat az Azure Stream Analytics manager mikroszolgáltatás írja be a következő parancsot a parancssorba:

.NET: 

```
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:
```
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>A telemetriai adatok mikroszolgáltatás konfigurálása

Kérje le a legújabb Telemetriai mikroszolgáltatás be a parancssorba a következő parancs beírásával:

.NET:
```
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Opcionális]*  Konfigurálja a webes felhasználói felület a Time Series Insights explorer összekapcsolása

A Time Series Insights Explorer saját adatainak megtekintéséhez egyszerűen, javasoljuk, hogy könnyen összekapcsolása a környezet a felhasználói felület testreszabása. Ehhez kérje le a legutóbbi változtatásokat a webes felhasználói felületére, a következő paranccsal:

```
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>A környezeti változók konfigurálása

A Time Series Insights-integráció végrehajtásához szüksége lesz az üzembe helyezés a frissített mikroszolgáltatások a környezet beállításához.

### <a name="basic-deployments"></a>Alapszintű központi telepítések

A környezet konfigurálása `basic` a frissített mikroszolgáltatások üzembe helyezés.

1. Az Azure Portalon kattintson a a **Azure Active Directory** lapra a bal oldali panelen.

1. Kattintson a **alkalmazásregisztrációk**.

1. Keresse meg és kattintson a **ContosoRM** alkalmazás.

1. Navigáljon a **beállítások** > **kulcsok** , majd hozzon létre egy új kulcsot az alkalmazáshoz. Ellenőrizze, hogy a kulcs-érték másolja biztonságos helyre.

1. Kérje le a [legfrissebb docker compose yaml-fájl](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) GitHub-adattárból legújabb címke használatával. 

1. SSH-t a virtuális gép a című rész lépéseit követve [hogyan hozhat létre és használhat SSH-kulcsok](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. A csatlakozás után írja be a `cd /app`.

1. Adja hozzá az alábbi környezeti változókat az egyes mikroszolgáltatások a docker compose yaml-fájlt, és a `env-setup` szkriptet a virtuális gépen:

    ```
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Keresse meg a **a telemetriai szolgáltatás** és is szerkesztése a docker compose ugyanazt a fenti környezeti változók hozzáadásával fájlt.

1. Keresse meg a **ASA-kezelő szolgáltatás** és szerkesztése a docker compose fájl hozzáadásával `PCS_TELEMETRY_STORAGE_TYPE`.

1. Indítsa újra a docker-tárolók használatával `sudo ./start.sh` a virtuális gépről.

### <a name="standard-deployments"></a>Normál telepítések

A környezet konfigurálása `standard` fenti frissített mikroszolgáltatásokhoz üzembe helyezése

1. A parancssorban futtassa `kubectl proxy`. További információkért lásd: [fér hozzá a Kubernetes API-t](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Nyissa meg a Kubernetes felügyeleti konzolt.

1. Keresse meg a konfigurációs térkép hozzáadása az alábbi új környezeti változókat a TSI:

    ```
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. A sablon yaml-fájl a telemetriai szolgáltatás pod szerkesztése:

    ```
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

5. A sablonfájl yaml az ASA-kezelő szolgáltatás pod szerkesztése:

    ```
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>További lépések

* Feltárhatja az adatait, és a egy riasztást a Time Series Insights Explorer diagnosztikája kapcsolatos további információkért lásd a jelen oktatóanyagban [legfelső szintű végző okok](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Ismerje meg, és kérdezhet le adatokat a Time Series Insights Explorer kapcsolatban további információt a dokumentációban a [Azure Time Series Insights explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
