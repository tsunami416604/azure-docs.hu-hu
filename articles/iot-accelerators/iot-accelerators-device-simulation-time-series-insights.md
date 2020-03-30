---
title: Szimulált telemetriai adatok megjelenítése a Time Series Insights segítségével – Azure | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a Time Series Insights-környezetet az Eszközszimulációs megoldásgyorsító által létrehozott telemetriai adatok feltárására és elemzésére.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889347"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>A Time Series Insights használatával vizualizálja az Eszközszimulációi megoldásgyorsítótól küldött telemetriai adatokat

Az eszközszimulációs megoldásgyorsító lehetővé teszi, hogy telemetriát hozzon létre szimulált eszközökről az IoT-megoldások teszteléséhez. Ez az útmutató bemutatja, hogyan vizualizálhatja és elemezheti a szimulált telemetriai adatokat egy Time Series Insights-környezet használatával.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató ban leírt lépések végrehajtásához aktív Azure-előfizetésre van szüksége. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

Az útmutató lépései feltételezik, hogy üzembe helyezte az Eszközszimulációs megoldásgyorsítót az Azure-előfizetésében. Ha még nem telepítette a megoldásgyorsítót, kövesse a telepítés és a [felhőalapú eszközszimulációs megoldás](quickstart-device-simulation-deploy.md) rövid útmutatójának lépéseit.

Ez a cikk feltételezi, hogy a megoldásgyorsító neve **contoso-szimuláció.** Cserélje le a **contoso-szimulációt** a megoldásgyorsító nevére a következő lépések végrehajtásával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Fogyasztói csoport létrehozása

Létre kell hoznia egy dedikált fogyasztói csoportot az IoT-központban a Telemetriát a Time Series Insights streameléséhez. A Time Series Insights eseményforrásának kizárólagos, az IoT Hub-fogyasztói csoport használatával kell rendelkeznie.

A következő lépések az Azure CLI használatával az Azure Cloud Shell a fogyasztói csoport létrehozásához:

1. Az IoT hub az eszközszimulációs megoldásgyorsító üzembe helyezésekor létrehozott számos erőforrás egyike. Hajtsa végre a következő parancsot az IoT hub nevének megkeresésére – ne felejtse el használni a megoldásgyorsító nevét:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Az IoT hub a **Microsoft.Devices/IotHubs**típusú erőforrás.

1. Adjon hozzá egy **devicesimulationtsi** nevű fogyasztói csoportot a hubhoz. A következő parancs használja a hub és a megoldásgyorsító nevét:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Most már bezárhatja az Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Új Time Series Insights-környezet létrehozása

[Az Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás az IoT-léptékű idősorozat-adatok felhőben történő kezeléséhez. Új Time Series Insights-környezet létrehozása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Válassza **az Erőforrás internetes** > **dolgok** > **internete, az idősorozatok elemzése lehetőséget:**

    ![Új idősorozat-elemzések](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Ha a Time Series Insights-környezetet ugyanabban az erőforráscsoportban szeretné létrehozni, mint a megoldásgyorsítót, használja az alábbi táblázatértékeit:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | A következő képernyőkép a **Contoso-TSI**nevet használja. A lépés során válassza ki saját egyedi nevét. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **contoso-szimuláció**. Használja a megoldásgyorsító nevét. |
    | Hely | Ez a példa **az USA keleti részét**használja. Hozza létre a környezetet ugyanabban a régióban, mint az eszköz szimulációs gyorsító. |
    | SKU |**S1** |
    | Kapacitás | **1** |

    ![Idősorozat-elemzési adatok létrehozása](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > A Time Series Insights-környezet hozzáadása ugyanahhoz az erőforráscsoporthoz, mint a megoldásgyorsító azt jelenti, hogy a megoldásgyorsító törlésekor törlődik.

1. Kattintson **a Létrehozás gombra.** A környezet létrehozása eltarthat néhány percig.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrást az IoT hubhoz való csatlakozáshoz. Használja az előző lépésekben létrehozott fogyasztói csoportot. A Time Series Insights eseményforráshoz egy másik szolgáltatás által nem használt dedikált fogyasztói csoport ra van szükség.

1. Az Azure Portalon keresse meg az új Time Series-környezetet.

1. A bal oldalon kattintson az **Eseményforrások elemre:**

    ![Eseményforrások megtekintése](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson a **Hozzáadás gombra:**

    ![Eseményforrás hozzáadása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT-központ új eseményforrásként való konfigurálásához használja az alábbi táblázatban szereplő értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Esemény forrásának neve | A következő képernyőkép a **contoso-iot-hub**nevet használja. A lépés végrehajtásával saját egyedi nevet használjon. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **Az IoT Hub használata elérhető előfizetésekből** |
    | Előfizetés azonosítója | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | IoT Hub neve | **contoso-simulation7d894**. Használja az IoT hub nevét az eszközszimulációs megoldásgyorsítóból. |
    | Iot Hub szabályzatneve | **iothubowner** |
    | A központ iot-kulcsa | Ez a mező automatikusan ki töltődik. |
    | IoT Hub fogyasztói csoport | **eszközszimulációs** |
    | Eseményszerializációs formátum | **JSON** |
    | Időbélyeg-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson **a Létrehozás gombra.**

> [!NOTE]
> További [felhasználóknak](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) is hozzáférést adhat a Time Series Insights-kezelőhöz.

## <a name="start-a-simulation"></a>Szimuláció indítása

A Time Series Insights-kezelő használata előtt konfigurálja az eszközszimulációs megoldásgyorsítót, hogy telemetriát hozzon létre. A következő képernyőkép 10 hűtőeszközzel futó szimulációt mutat be:

![Eszközszimuláció futtatása](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

A Time Series Insights explorer egy webalkalmazás, amely segítségével vizualizálhatja a telemetriai adatokat.

1. Az Azure Portalon válassza a Time Series Insights **Áttekintés lapját.**

1. A Time Series Insights explorer webalkalmazás megnyitásához kattintson az **Ugrás a környezetbe**gombra:

    ![Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Az időválasztó panelen válassza az **Utolsó 30 percet** a gyorsidők menüből, és kattintson a **Keresés**gombra:

    ![Time Series Insights-kereső keresése](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. A bal oldali kifejezéspanelen válassza ki a **hőmérsékletet** **mértékként** és **iothub-connection-device-id-t** **felosztási** értékként:

    ![Time Series Insights-kezelő lekérdezése](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Kattintson a jobb gombbal a diagramra, és válassza **az Események felfedezése parancsot:**

    ![Time Series Insights-felfedezőesemények](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Az eseményadatok egy rácsban jelennek meg:

    ![Time Series Insights explorer tábla](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Kattintson a perspektívanézet gombra:

    ![A Time Series Insights-kezelő perspektívája](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Ide **+** kattintva új lekérdezést adhat a perspektívához:

    ![A Time Series Insights-kezelő lekérdezés hozzáadása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Válassza az **Utolsó 30 percet** az időtartamként, a **Páratartalom** **mértékként**és az **iothub-connection-device-id** lehetőséget a **Felosztás i.** értékként:

    ![Time Series Insights-kezelő lekérdezése](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kattintson a perspektivikus nézet gombra az eszköz telemetriai irányítópultjának megtekintéséhez:

    ![A Time Series Insights-kezelő irányítópultja](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy vizsgálja meg a további, hagyja a megoldásgyorsító telepítve.

Ha már nincs szüksége a megoldásgyorsítóra, törölje azt a [Kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról, jelölje ki, majd kattintson a **Megoldás törlése**gombra.

Ha hozzáadta a Time Series Insights környezetet a megoldásgyorsító erőforráscsoportjához, az automatikusan törlődik a megoldásgyorsító törlésekor. Ellenkező esetben manuálisan el kell távolítania a Time Series Insights környezetet az Azure Portalról.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne tudni arról, hogyan fedezhet fel és kérdezhet le adatokat a Time Series Insights-kezelőben, olvassa el az [Azure Time Series Insights-kezelő című témakört.](../time-series-insights/time-series-insights-explorer.md)
