---
title: Távoli megfigyelés az Azure Time Series Insights-adatok megjelenítése |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Time Series Insights-környezet vizsgálata és elemzése az idősorozat-adatok a távoli figyelési megoldás.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 10617c129212d8196897af750c02647f0086c8e5
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185890"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Távoli Monitorozási adatok vizualizálása a Time Series Insights

Az operátornak érdemes lehet további kiterjesztése a megadott box adatok képi megjelenítés, a távoli figyelési szolgáltatás által biztosított előkonfigurálása megoldás. A megoldásgyorsító kívül a szolgáltatással való TSI biztosít. Ebben az útmutatóban, megtudhatja, hogyan konfigurálhatja a Time Series Insights eszköz telemetriai adatok elemzéséhez, és észlelje a rendellenességeket.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához a következőkre lesz szüksége:

* [A távoli figyelési előre konfigurált megoldás üzembe helyezése](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Hozzon létre egy fogyasztói csoportot

Szüksége lesz egy dedikált fogyasztói csoport létrehozása az IoT hub streamelési adatok a Time Series Insights használandó.

> [!NOTE]
> Adatok lekérése az Azure IoT Hub fogyasztói csoportok alkalmazások használják. Minden felhasználói csoport legfeljebb öt kimeneti fogyasztók számára lehetővé teszi. Egy új felhasználói csoportot kell létrehozni, a minden ötödik kimeneti fogadóként és legfeljebb 32 fogyasztói csoportot is létrehozhat.

1. Az Azure Portalon a Cloud Shell gombra.

1. Hajtsa végre a következő parancsot egy új felhasználói csoport létrehozása:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Új Time Series Insights-környezet létrehozása

Az Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amellyel IoT-méretű idősoros adatok felhőbeli kezelését végezheti el. Nagy mértékben skálázható adattárolást kínál idősoros adatokhoz, és lehetővé teszi, hogy másodpercek alatt feldolgozza és elemezze a világ bármely részéről beérkező események milliárdjait. A Time Series Insights használja, tárolhatja és kezelheti a terabájtnyi idősoros adatot, ismerje meg, és milliárd esemény egyszerre megjelenítése, egyetlen elvégezheti az alapvető okok elemzése, és összehasonlíthat több helyet és objektumot.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

1. Válassza ki **erőforrás létrehozása** > **IOT-** > **Time Series Insights**.

    ![Új Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. A Time Series Insights-környezet létrehozásához használja az alábbi táblázatban az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | A nevet használja az alábbi képernyőképen **contorosrmtsi**. Válassza ki a saját egyedi nevet, ha e lépés elvégzése után. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **Hozzon létre új**. A nevet használjuk **ContosoRM**. |
    | Hely | Használjuk **USA keleti Régiójában**. A környezet létrehozása és a távoli figyelési megoldás ugyanabban a régióban. |
    | SKU |**S1** |
    | Kapacitás | **1** |
    | Rögzítés az irányítópulton | **Igen** |

    ![A Time Series Insights létrehozása](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Kattintson a **Create** (Létrehozás) gombra. A környezetben kell létrehozni egy kis időt is igénybe vehet.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrás csatlakozni az IoT hubhoz. Győződjön meg arról, hogy használja a fogyasztói csoportot az előző lépésekben létrehozott-e. A Time Series Insights igényel minden egyes szolgáltatást, hogy dedikált fogyasztói csoportot nem használatban van egy másik szolgáltatás.

1. Keresse meg az új Time Series-környezetet.

1. A bal oldalon válassza ki a **eseményforrások**.

    ![Esemény források megtekintése](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson a **Hozzáadás** parancsra.

    ![Eseményforrás hozzáadása](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT hub új esemény forrásként konfigurálásához használja az alábbi táblázatban az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Forrás neve | A nevet használja az alábbi képernyőképen **contosorm-iot-hub**. Használja a saját egyedi névre, ha e lépés elvégzése után. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **Az IoT Hub használata a rendelkezésre álló előfizetések közül** |
    | Előfizetési azonosító | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | IOT hub nevét | **contosorma57a6**. A távoli figyelési megoldás az IoT-központ nevét használja. |
    | IOT hub házirend neve | **iothubowner** győződjön meg, hogy a használt szabályzat egy tulajdonos házirend. |
    | IOT hub házirendjének kulcsa | Ez a mező automatikusan fel van töltve. |
    | IOT hub fogyasztói csoport | **timeseriesinsights** |
    | Eseményszerializációs formátum | **JSON**     | Időbélyegző-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> Ha további felhasználókat a hozzáférési jogot a Time Series Insights explorer van szüksége, használhatja az alábbi lépéseket [adathozzáférés](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

A Time Series Insights explorer egy webalkalmazást, amely segítséget nyújt az adatok vizualizációkat hozhat létre.

1. Válassza ki a **áttekintése** fülre.

1. Kattintson a **nyissa meg a környezet**, hogy megnyissa a Time Series Insights explorer webalkalmazás.

    ![Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. Az időkiválasztási panelre, válassza ki **utolsó 12 órából** az időpontokban menüben, majd kattintson a gyors **keresési**.

    ![Idősorozat-elemzések Explorer Keresés](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. A feltételek a bal oldali panelen válassza ki a mérték értékét **hőmérséklet** és a felosztás által érték **iothub-kapcsolat-eszközazonosító**.

    ![Idősorozat-Insights Explorer-lekérdezés](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Kattintson a jobb gombbal a diagramra, majd válassza a **események tallózása**.

    ![Time Series Insights Explorer események](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. A rács táblázatos formátumban jelennek az eseményeket.

    ![Time Series Insights Explorer tábla](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. A perspektíva megtekintése gombra.

    ![Time Series Insights Explorer perspektíva](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kattintson a **Hozzáadás** a perspektíva egy új lekérdezés létrehozásához.

    ![Time Series Insights Explorerben lekérdezés hozzáadása](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Válassza ki a rövid idő **utolsó 12 órából**, a mérték **páratartalom** és a egy felosztása, **iothub-kapcsolat-eszközazonosító**.

    ![Idősorozat-Insights Explorer-lekérdezés](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Kattintson a perspektíva megtekintése gombra az eszköz mérőszámok irányítópult megtekintésére.

    ![Time Series Insights Explorer irányítópultja](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, és kérdezhet le adatokat a Time Series Insights Explorer kapcsolatos tudnivalókért lásd: [Azure Time Series Insights explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
