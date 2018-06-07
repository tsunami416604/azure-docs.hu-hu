---
title: Megjelenítheti a figyelési adatok az Azure idő adatsorozat insights szolgáltatással távoli |} Microsoft Docs
description: Útmutató a idő adatsorozat Insights környezet vizsgálatát és elemzését a távoli figyelésére szolgáló megoldás idő adatsorozat adatainak beállításait.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: ed1a55c26ab1ef2aadb4906757205df0ca09d866
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655130"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Az idő adatsorozat insights szolgáltatással távoli figyelési adatok megjelenítése

Operátor érdemes további kiterjeszteni a out be adatok a távoli megfigyelési által biztosított a képi megjelenítés megoldás előkonfigurálására szolgálnak. A megoldásgyorsító kívül ÁME mezőben integrációját. Ez az útmutató megtudhatja konfigurálása idő adatsorozat elemzések számára telemetriát elemzése és rendellenességek észlelését.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató elvégzéséhez a következőkre lesz szüksége:

* [Az előre konfigurált távoli figyelésére szolgáló megoldás telepítése](iot-accelerators-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Egy felhasználói csoport létrehozása

Szüksége lesz az IoT hub adatfolyam idő adatsorozat insights használandó dedikált fogyasztói csoportot létrehozni.

> [!NOTE]
> Alkalmazások az fogyasztói csoportok segítségével olvasnak be adatokat Azure IoT-központot. Minden felhasználói csoport lehetővé teszi, hogy legfeljebb öt kimeneti fogyasztók. Minden ötödik kimeneti mosdók, és legfeljebb 32 fogyasztói csoportot hozhat létre egy új fogyasztói csoportot kell létrehoznia.

1. Az Azure portálon a felhő rendszerhéj gombra.

1. Hajtsa végre a következő parancsot egy új felhasználói csoport létrehozása:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Hozzon létre egy új idő adatsorozat Insights környezetet

Az Azure Time Series Insights egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amellyel IoT-méretű idősoros adatok felhőbeli kezelését végezheti el. Nagy mértékben skálázható adattárolást kínál idősoros adatokhoz, és lehetővé teszi, hogy másodpercek alatt feldolgozza és elemezze a világ bármely részéről beérkező események milliárdjait. Ezen idő adatsorozat Insights, tárolására és terabájt idősorozat adatok kezelése, vizsgálatát, és jelenítheti meg egyszerre több milliárd események, elemezheti a kiváltó okának, és hasonlítsa össze a különböző webhelyekre és eszközök.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com/).

1. Válassza ki **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** > **idő adatsorozat Insights**.

    ![Új idő adatsorozat Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Az idő adatsorozat Insights környezet létrehozása, az alábbi táblázatban a értékeket használja:

    | Beállítás | Érték |
    | ------- | ----- |
    | A környezeti neve | Az alábbi képernyőfelvételen a nevet használja, **contorosrmtsi**. Amikor végzett ezzel a lépéssel, válassza ki a saját egyedi nevét. |
    | Előfizetés | A legördülő listán válassza ki az Azure-előfizetéshez. |
    | Erőforráscsoport | **Új**. A név használjuk **ContosoRM**. |
    | Hely | Használjuk **USA keleti régiója**. A környezet létrehozása a távoli figyelésére szolgáló megoldás ugyanabban a régióban. |
    | SKU |**S1** |
    | Kapacitás | **1** |
    | Rögzítés az irányítópulton | **Igen** |

    ![Adatsorozat adatmodellünk idő](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Kattintson a **Create** (Létrehozás) gombra. A környezet létre kell néhány percet is igénybe vehet.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrás az IoT hub való kapcsolódáshoz. Győződjön meg arról, hogy a az előző lépésekben létrehozott fogyasztói csoportot használjon. Idő adatsorozat Insights minden szolgáltatás való dedikált fogyasztócsoportot nem használja egy másik szolgáltatás szükséges.

1. Nyissa meg az új adatsorozat környezetben.

1. Válassza ki a bal oldali **eseményforrások**.

    ![Nézet Eseményforrások](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson a **Hozzáadás** parancsra.

    ![Forrás hozzáadása](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT hub beállítása egy új eseményforrás, az alábbi táblázatban szereplő értékek használatához:

    | Beállítás | Érték |
    | ------- | ----- |
    | Eseményforrás neve | Az alábbi képernyőfelvételen a nevet használja, **contosorm-iot-központ**. Amikor végzett ezzel a lépéssel, használja a saját egyedi nevét. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **Az elérhető előfizetések IoT Hubjának használata** |
    | Előfizetési azonosító | A legördülő listán válassza ki az Azure-előfizetéshez. |
    | Az IOT-központ nevét | **contosorma57a6**. A távoli figyelésére szolgáló megoldás a az IoT hub nevét használja. |
    | Az IOT hub házirend neve | **iothubowner** használandó házirendet legyen egy tulajdonos házirendet. |
    | Az IOT hub házirend kulcs | A mező automatikusan feltöltődik értékkel. |
    | Az IOT hub fogyasztói csoportot | **timeseriesinsights** |
    | Eseményszerializációs formátum | **JSON**     | Időbélyeg-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson a **Create** (Létrehozás) gombra.

> [!NOTE]
> Ha szeretne további felhasználó hozzáférést biztosítson a idő adatsorozat Insights explorer, az alábbi lépéseket használhatja [adatok hozzáférést](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

Az idő adatsorozat Insights explorer a webes alkalmazás, amely segít az adatok képi megjelenítéseket készíthet.

1. Válassza ki a **áttekintése** fülre.

1. Kattintson a **nyissa meg a környezetben**, amely ekkor megnyílik a idő adatsorozat Insights explorer webalkalmazás.

    ![Time Series Insights Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. Jelölje ki a idő kijelölés panelen **utolsó 12 óra** a gyors időpontokban menüre, majd a **keresési**.

    ![Idő adatsorozat Insights Explorer Keresés](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. A feltételek a bal oldali panelen válassza ki a mérték értékének **hőmérséklet** és a felosztás által érték **IOT hubbal-kapcsolat-eszközazonosító**.

    ![Idő adatsorozat Insights Explorer lekérdezés](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Kattintson a jobb gombbal a diagramra, majd a **események tallózása**.

    ![Adatsorozat Insights Explorer események](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Az események megjelennek-e a rács táblázatos formátumban.

    ![Idő adatsorozat Insights Explorer tábla](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. A perspektíva megtekintése gombra.

    ![Idő adatsorozat Insights Explorer perspektíva](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kattintson a **Hozzáadás** perspektíva egy új lekérdezés létrehozásához.

    ![Idő adatsorozat Insights Explorer felvétele](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Válassza ki a gyors idő **utolsó 12 óra**, biztosítása **páratartalom** és a felosztás által a **IOT hubbal-kapcsolat-eszközazonosító**.

    ![Idő adatsorozat Insights Explorer lekérdezés](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. A perspektíva nézet gombra az eszköz metrikák az irányítópult megtekintéséhez.

    ![Idő adatsorozat Explorer irányítópult](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>További lépések

Vizsgálatát, és az idő adatsorozat Insights Explorer adatait kapcsolatos további tudnivalókért lásd: [Azure idő adatsorozat Insights explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-dashboard.png).
