---
title: Az Azure IoT Hub – Power BI érzékelőktől kapott adatok valós idejű adatok vizualizációját |} A Microsoft Docs
description: A Power BI használatával jelenítheti meg az érzékelő összegyűjtött és az Azure IoT hubra küldött hőmérséklettel és páratartalommal kapcsolatos adatokat.
author: robinsh
keywords: valós idejű adatvizualizáció, élő adatvizualizáció, érzékelő adatmegjelenítés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: robinsh
ms.openlocfilehash: 5349a8a81c1d1361637b4fc9cf83e1ee83f5276a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265508"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Azure IoT Hub használata a Power BI valós idejű érzékelői adatainak megjelenítése

![Végpontok közötti diagram](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ismerteti az Azure IoT hub által fogadott Power BI használatával valós idejű érzékelői adatainak megjelenítése. Ha ki szeretné próbálni toe megjelenítheti az adatokat az IoT hub, a Web Apps, a [használata az Azure Web Apps az Azure IoT Hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>TEENDŐ

* Készüljön fel az IoT hub az adatok eléréséhez egy fogyasztói csoport hozzáadásával.

* Létrehozása, konfigurálása és az adatátvitelhez a Stream Analytics-feladat futtatása az IoT hub a Power BI-fiókjába.

* Hozzon létre, és az adatok megjelenítése Power BI-jelentés közzététele.

## <a name="what-you-need"></a>Mi szükséges

* Az oktatóanyag [beállítani eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely magában foglalja az alábbi követelményeknek:
  
  * Aktív Azure-előfizetés.
  * Az Azure IoT hub az előfizetéséhez.
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.

* Egy Power BI-fiók. ([Próbálja ki ingyenesen a Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Létrehozása, konfigurálása és a egy Stream Analytics-feladat futtatása

Először hozzon létre egy Stream Analytics-feladatot. Miután létrehozta a feladat, meghatározhatja a bemenetek, kimenetek és a lekérdezést, lekérheti az adatokat.

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Stream Analytics-feladat** elemre.

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: Használja ugyanazt a helyet az erőforráscsoportban.

   **Rögzítés az irányítópulton**: Ellenőrizze ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT hubhoz az irányítópultról.

   ![Stream Analytics-feladat létrehozása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

3. Kattintson a **Create** (Létrehozás) gombra.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics-feladat.

2. A **Feladattopológia** területen kattintson a **Bemenetek** elemre.

3. Az a **bemenetek** ablaktáblán kattintson a **streambemenet hozzáadása**, majd adja meg a következő információkat:

   **Bemeneti áljel**: A bemeneti, válassza ki az egyedi aliast **adja meg az IoT Hub beállításainak manuális** alatt.

   **forrás**: Válassza ki **az IoT hub**.
   
   **Végpont**: Kattintson a **üzenetkezelési**.

   **Fogyasztói csoport**: Válassza ki az imént létrehozott fogyasztói csoportot.

4. Kattintson a **Create** (Létrehozás) gombra.

   ![Az Azure Stream Analytics-feladat bemenete hozzáadása](./media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen kattintson a **Kimenetek** elemre.

2. Az a **kimenetek** ablaktáblán kattintson a **Hozzáadás** és **Power BI**, majd írja be a következő információkat:

   **Kimeneti alias**: A kimeneti egyedi aliast.

   **A csoport munkaterület**: Válassza ki a cél csoportos munkaterületre.

   **Adatkészlet neve**: Adja meg az adatkészlet nevét.

   **Tábla neve**: Adja meg egy tábla nevét.

3. Kattintson a **engedélyezés**, majd jelentkezzen be a Power BI-fiók.

4. Kattintson a **Create** (Létrehozás) gombra.

   ![Kimenet hozzáadása az Azure Stream Analytics-feladat](./media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen kattintson a **Lekérdezés** elemre.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére.

4. Kattintson a **Save** (Mentés) gombra.

   ![A lekérdezés hozzáadása az Azure Stream Analytics-feladat](./media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics-feladat területen kattintson az **Indítás** > **Most** > **Indítás** elemre. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladat futtatása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Hozzon létre, és az adatok megjelenítése Power BI-jelentés közzététele

1. Győződjön meg arról, a mintaalkalmazás fut az eszközön. Ha nem, olvassa el a oktatóanyagok alatt [beállítani eszközét](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/en-us/)-fiókjába.

3. Kattintson a használt, a munkaterület **saját munkaterület**.

4. Kattintson az **Adatkészletek** elemre.

   Az adatkészlet, a kimenet a Stream Analytics-feladat létrehozásakor megadott kell megjelennie.

5. Kattintson a létrehozott adatkészlethez, **adja hozzá a jelentés** (az első ikon a jobb oldalon az adathalmaz neve).

   ![A Microsoft Power BI-jelentés létrehozása](./media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

6. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   1. Adja hozzá a jelentésoldal létrehozása egy vonaldiagramot.

   2. A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát.
   
   3. Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.
   
   4. Húzza a **hőmérséklet** elemet az **Értékek** helyre.

      Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

      ![Egy vonaldiagramot a hőmérséklet hozzáadása a Microsoft Power BI-jelentés](./media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

7. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. Ehhez kövesse a fenti lépéseket, és helyezze **EventEnqueuedUtcTime** az x tengelyen és **páratartalom** az y tengelyen.

   ![Egy vonaldiagramot páratartalom hozzáadása a Microsoft Power BI-jelentés](./media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

8. Kattintson a **Mentés** elemre a jelentés mentéséhez.

9. Kattintson a **jelentések** elemre a bal oldali panelen, majd kattintson a jelentésben, hogy nemrég létrehozott.

10. Kattintson a **fájl** > **webes közzététel**.

11. Kattintson a **beágyazási kód létrehozása**, és kattintson a **közzététel**.

A jelentés hivatkozása már a megadott jelentés-hozzáférés és a egy kódrészletet a jelentés integrálása saját blogba vagy webhelyre bárkivel megoszthatja.

![A Microsoft Power BI-jelentés közzététele](./media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft által a [Power BI-mobilalkalmazásokban](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) megtekintését és a Power BI-irányítópultok és jelentések esetében a mobileszközén.

## <a name="next-steps"></a>További lépések

Az Azure IoT hub valós idejű érzékelői adatainak megjelenítése Power bi-ban sikeresen felhasználta.

Nincs más módja az Azure IoT Hub adatainak megjelenítése. Lásd: [használata az Azure Web Apps az Azure IoT Hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
