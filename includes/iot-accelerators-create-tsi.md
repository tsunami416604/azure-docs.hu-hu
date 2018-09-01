---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8c114ed137089e70899e601ebdc1d4d39f562601
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43383230"
---
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

[Az Azure Time Series Insights](../articles/time-series-insights/time-series-insights-overview.md) teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás IoT-méretű idősoros adatok felhőbeli kezeléséhez. Új Time Series Insights-környezet létrehozása:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

1. Válassza ki **erőforrás létrehozása** > **IOT-** > **Time Series Insights**:

    ![Új Time Series Insights](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. A Time Series Insights-környezet létrehozásához, a megoldásgyorsító ugyanabban az erőforráscsoportban, az alábbi táblázatban található értékek használatára:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | A nevet használja az alábbi képernyőképen **Contoso – TSI**. Válassza ki a saját egyedi nevet, ha e lépés elvégzése után. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **Contoso-szimuláció**. A megoldásgyorsító nevét használja. |
    | Hely | Ez a példa **USA keleti Régiójában**. A környezet létrehozása és az eszköz szimulálása gyorsító ugyanabban a régióban. |
    | SKU |**S1** |
    | Kapacitás | **1** |

    ![A Time Series Insights létrehozása](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > Hozzáadása a Time Series Insights ugyanazt az erőforráscsoportot, a megoldásgyorsító környezet azt jelenti, hogy törölné a rendszer a megoldásgyorsító törlésekor.

1. Kattintson a **Create** (Létrehozás) gombra. A környezet hozható létre néhány percet is igénybe vehet.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrás csatlakozni az IoT hubhoz. A fogyasztói csoportot az előző lépésekben létrehozott használni. Egy Time Series Insights-eseményforrás egy másik szolgáltatás nincs használatban dedikált fogyasztói csoportot igényel.

1. Az Azure Portalon keresse meg az új Time Series-környezetet.

1. Kattintson a bal oldali **eseményforrások**:

    ![Esemény források megtekintése](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. Kattintson a **hozzáadása**:

    ![Eseményforrás hozzáadása](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. Az IoT hub új esemény forrásként konfigurálásához használja az alábbi táblázatban az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Forrás neve | A nevet használja az alábbi képernyőképen **contoso-iot-hub**. Használja a saját egyedi névre, ha e lépés elvégzése után. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **Az IoT Hub használata a rendelkezésre álló előfizetések közül** |
    | Előfizetési azonosító | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | IOT hub nevét | **Contoso-simulation7d894**. Az Eszközszimuláció megoldásgyorsító az IoT-központ nevét használja. |
    | IOT hub házirend neve | **iothubowner** |
    | IOT hub házirendjének kulcsa | Ez a mező automatikusan fel van töltve. |
    | IOT hub fogyasztói csoport | **devicesimulationtsi** |
    | Eseményszerializációs formátum | **JSON** |
    | Időbélyegző-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> Is [további felhasználóknak hozzáférést](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) a Time Series Insights Explorer.