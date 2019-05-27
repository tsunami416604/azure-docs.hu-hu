---
title: Megjelenítheti az eszköz szimulálása a telemetriai adatok a Time Series Insights – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Time Series Insights-környezet vizsgálata és elemzése az Eszközszimuláció megoldásgyorsító által létrehozott telemetriát.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5d20adc11e0d679e12fd060e719593a50180db8e
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834864"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Az Eszközszimuláció megoldásgyorsító által küldött telemetriai adatok megjelenítése a Time Series Insights használatával

Az Eszközszimuláció megoldásgyorsító lehetővé teszi az IoT-megoldások teszteléséhez szimulált eszközök hoznak létre telemetriai adatokat. Ez az útmutató bemutatja, hogyan jelenítheti meg és elemezheti a szimulált telemetriát a Time Series Insights-környezet használata.

## <a name="prerequisites"></a>Előfeltételek

Ez az Útmutató lépéseit kövesse, aktív Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ez az útmutató lépései azt feltételezik, hogy az Eszközszimuláció megoldásgyorsító helyezte az Azure-előfizetéshez. Ha még nem telepítette a megoldásgyorsító, kövesse a [üzembe helyezése és futtatása egy felhőalapú szimuláció megoldás](quickstart-device-simulation-deploy.md) rövid.

Ez a cikk feltételezi, hogy a megoldásgyorsító neve **contoso-szimuláció**. Cserélje le **contoso-szimuláció** , mint a megoldásgyorsító nevét a következő lépéseket.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Hozzon létre egy fogyasztói csoportot

Dedikált fogyasztói csoportot a Time Series Insights stream telemetriát az IoT hub létrehozásához szükséges. A Time Series Insights-eseményforrás egy IoT Hub fogyasztói csoport kizárólagos használatát kell rendelkeznie.

Az alábbi lépéseket az Azure CLI az Azure Cloud shellben hozzon létre a fogyasztói csoportot használja:

1. Az IoT hub az Eszközszimuláció megoldásgyorsító üzembe helyezésekor létrehozott több erőforrás szerepel. Hajtsa végre a következő parancsot az IoT hub nevére find - ne felejtse el a megoldásgyorsító nevét használja:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Az IoT hub, hogy az erőforrás típusa **Microsoft.Devices/IotHubs**.

1. Adjon hozzá egy nevű fogyasztói csoportot **devicesimulationtsi** a hubhoz. Az alábbi parancsban a hubot és a megoldás gyorsító nevét használja:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Most már bezárhatja az Azure Cloud Shellt.

## <a name="create-a-new-time-series-insights-environment"></a>Új Time Series Insights-környezet létrehozása

[Az Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás IoT-méretű idősoros adatok felhőbeli kezeléséhez. Új Time Series Insights-környezet létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki **erőforrás létrehozása** > **IOT-** > **Time Series Insights**:

    ![Új Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. A Time Series Insights-környezet létrehozásához, a megoldásgyorsító ugyanabban az erőforráscsoportban, az alábbi táblázatban található értékek használatára:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | A nevet használja az alábbi képernyőképen **Contoso – TSI**. Válassza ki a saját egyedi nevet, ha e lépés elvégzése után. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **Contoso-szimuláció**. A megoldásgyorsító nevét használja. |
    | Location egység | Ez a példa **USA keleti Régiójában**. A környezet létrehozása és az eszköz szimulálása gyorsító ugyanabban a régióban. |
    | Termékváltozat |**S1** |
    | Kapacitás | **1** |

    ![A Time Series Insights létrehozása](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Hozzáadása a Time Series Insights ugyanazt az erőforráscsoportot, a megoldásgyorsító környezet azt jelenti, hogy törölné a rendszer a megoldásgyorsító törlésekor.

1. Kattintson a **Create** (Létrehozás) gombra. A környezet hozható létre néhány percet is igénybe vehet.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrás csatlakozni az IoT hubhoz. A fogyasztói csoportot az előző lépésekben létrehozott használni. Egy Time Series Insights-eseményforrás egy másik szolgáltatás nincs használatban dedikált fogyasztói csoportot igényel.

1. Az Azure Portalon keresse meg az új Time Series-környezetet.

1. Kattintson a bal oldali **eseményforrások**:

    ![Esemény források megtekintése](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson a **hozzáadása**:

    ![Eseményforrás hozzáadása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT hub új esemény forrásként konfigurálásához használja az alábbi táblázatban az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Forrás neve | A nevet használja az alábbi képernyőképen **contoso-iot-hub**. Használja a saját egyedi névre, ha e lépés elvégzése után. |
    | Source | **IoT Hub** |
    | Importálási beállítás | **Az IoT Hub használata a rendelkezésre álló előfizetések közül** |
    | Előfizetés azonosítója | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Iot hub neve | **Contoso-simulation7d894**. Az Eszközszimuláció megoldásgyorsító az IoT-központ nevét használja. |
    | Iot hub szabályzatneve | **iothubowner** |
    | Iot hub szabályzatkulcsa | Ez a mező automatikusan fel van töltve. |
    | Iot hub fogyasztói csoport | **devicesimulationtsi** |
    | Eseményszerializációs formátum | **JSON** |
    | Időbélyeg-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> Is [további felhasználóknak hozzáférést](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) a Time Series Insights Explorer.

## <a name="start-a-simulation"></a>A szimuláció indítása

A Time Series Insights explorer használatához konfigurálja az Eszközszimuláció megoldásgyorsító, hogy létrejöjjön valamennyi telemetria. Az alábbi képernyőképen látható, egy futó szimuláció 10 hűtő eszköz:

![Eszközszimuláció fut](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

A Time Series Insights explorer egy webalkalmazás segítségével vizualizálhatja a telemetriai adatokat.

1. Az Azure Portalon válassza ki a Time Series Insights **áttekintése** fülre.

1. A Time Series Insights explorer webalkalmazás megnyitásához kattintson a **Ugrás a környezet**:

    ![Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Válassza ki az időkiválasztási panelre, **elmúlt 30 percben** az időpontokban menüben, majd kattintson a gyors **keresési**:

    ![Time Series Insights explorer search](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. A feltételek a bal oldali panelen válassza ki a **hőmérséklet** , a **mérték** és **iothub-kapcsolat-eszközazonosító** , a **Split által** érték:

    ![Time Series Insights explorer lekérdezés](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Kattintson a jobb gombbal a diagramra, majd válassza a **események tallózása**:

    ![Time Series Insights explorer események](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Az eseményadatokat a rácsban látható:

    ![Time Series Insights explorer tábla](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. A perspektíva megtekintése gombra:

    ![Time Series Insights explorer perspektíva](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kattintson a **+** szempontjából egy új lekérdezés hozzáadása:

    ![Time Series Insights explorer lekérdezés hozzáadása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Válassza ki **elmúlt 30 percben** , az azt az időtartományt, **páratartalom** , a **mérték**, és **iothub-kapcsolat-eszközazonosító** , a **Split által** érték:

    ![Time Series Insights explorer lekérdezés](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. A perspektíva nézet gombra az eszköz telemetriai irányítópult megtekintése:

    ![Time Series Insights explorer irányítópultja](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, vizsgálódáshoz, hagyja a megoldásgyorsító üzembe helyezve.

Ha már nincs szüksége a megoldásgyorsító, törölje azt a [kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapon jelölje ki, majd **megoldás törlése**.

Ha a Time Series Insights-környezet a megoldásgyorsító erőforráscsoport adott, a rendszer automatikusan törli a megoldásgyorsító törlésekor. Ellenkező esetben azokat manuálisan kell eltávolítani a Time Series Insights-környezet az Azure Portalról.

## <a name="next-steps"></a>További lépések

Ismerje meg, és kérdezhet le adatokat a Time Series Insights Explorer kapcsolatos további tudnivalókért lásd: [Azure Time Series Insights explorer](../time-series-insights/time-series-insights-explorer.md).
