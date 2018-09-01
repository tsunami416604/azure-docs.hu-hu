---
title: Eszköz szimulálása telemetriai adatainak az Azure Time Series Insights megjelenítése |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Time Series Insights-környezet vizsgálata és elemzése az Eszközszimuláció megoldásgyorsító által létrehozott telemetriát.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 49cecca4e5ebef9f43fdda16cfa1fdc2ad7b6f94
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43383267"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Az Eszközszimuláció megoldásgyorsító által küldött telemetriai adatok megjelenítése a Time Series Insights használatával

Az Eszközszimuláció megoldásgyorsító lehetővé teszi az IoT-megoldások teszteléséhez szimulált eszközök hoznak létre telemetriai adatokat. Ez az útmutató bemutatja, hogyan jelenítheti meg és elemezheti a szimulált telemetriát a Time Series Insights-környezet használata.

## <a name="prerequisites"></a>Előfeltételek

Ez az Útmutató lépéseit kövesse, aktív Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ez az útmutató lépései azt feltételezik, hogy az Eszközszimuláció megoldásgyorsító helyezte az Azure-előfizetéshez. Ha még nem telepítette a megoldásgyorsító, kövesse a [üzembe helyezése és futtatása egy felhőalapú szimuláció megoldás](quickstart-device-simulation-deploy.md) rövid.

Ez a cikk feltételezi, hogy a megoldásgyorsító neve **contoso-szimuláció**. Cserélje le **contoso-szimuláció** , mint a megoldásgyorsító nevét a következő lépéseket.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="start-a-simulation"></a>A szimuláció indítása

A Time Series Insights explorer használatához konfigurálja az Eszközszimuláció megoldásgyorsító, hogy létrejöjjön valamennyi telemetria. Az alábbi képernyőképen látható, egy futó szimuláció 10 hűtő eszköz:

![Eszközszimuláció fut](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

A Time Series Insights explorer egy webalkalmazás segítségével vizualizálhatja a telemetriai adatokat.

1. Az Azure Portalon válassza ki a Time Series Insights **áttekintése** fülre.

1. A Time Series Insights explorer webalkalmazás megnyitásához kattintson a **Ugrás a környezet**:

    ![Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Válassza ki az időkiválasztási panelre, **elmúlt 30 percben** az időpontokban menüben, majd kattintson a gyors **keresési**:

    ![Idő Series Insights explorer keresés](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

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

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg, és kérdezhet le adatokat a Time Series Insights Explorer kapcsolatos további tudnivalókért lásd: [Azure Time Series Insights explorer](../time-series-insights/time-series-insights-explorer.md).
