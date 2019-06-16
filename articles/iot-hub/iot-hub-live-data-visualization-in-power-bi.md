---
title: Az Azure IoT Hub – Power BI érzékelőktől kapott adatok valós idejű adatok vizualizációját |} A Microsoft Docs
description: A Power BI használatával jelenítheti meg az érzékelő összegyűjtött és az Azure IoT hubra küldött hőmérséklettel és páratartalommal kapcsolatos adatokat.
author: robinsh
keywords: valós idejű adatvizualizáció, élő adatvizualizáció, érzékelő adatmegjelenítés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: 7deb1b501d30c8af0cb190f4722d46435afa9b8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065937"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Azure IoT Hub használata a Power BI valós idejű érzékelői adatainak megjelenítése

![Végpontok közötti diagram](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ismerteti az Azure IoT hub által fogadott Power BI használatával valós idejű érzékelői adatainak megjelenítése. Ha ki szeretné próbálni megjelenítheti az adatokat az IoT hub egy olyan webalkalmazással, lásd: [webalkalmazás használata Azure IoT Hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>TEENDŐ

* Készüljön fel az IoT hub az adatok eléréséhez egy fogyasztói csoport hozzáadásával.

* Létrehozása, konfigurálása és az adatátvitelhez a Stream Analytics-feladat futtatása az IoT hub a Power BI-fiókjába.

* Hozzon létre, és az adatok megjelenítése Power BI-jelentés közzététele.

## <a name="what-you-need"></a>Mi szükséges

* Végezze el a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyag vagy az eszköz oktatóanyagokban; például [Raspberry Pi és node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ezek a cikkek terjed ki a következő követelményeknek:
  
  * Aktív Azure-előfizetés.
  * Az Azure IoT hub az előfizetéséhez.
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.

* Egy Power BI-fiók. ([Próbálja ki ingyenesen a Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Létrehozása, konfigurálása és a egy Stream Analytics-feladat futtatása

Először hozzon létre egy Stream Analytics-feladatot. Miután létrehozta a feladat, meghatározhatja a bemenetek, kimenetek és a lekérdezést, lekérheti az adatokat.

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az a [az Azure portal](https://portal.azure.com)válassza **erőforrás létrehozása** > **IOT-**  > **Stream Analytics-feladat**.

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: Használja ugyanazt a helyet az erőforráscsoportban.

   ![Stream Analytics-feladat létrehozása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Kattintson a **Létrehozás** gombra.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics-feladat.

2. A **Feladattopológia**válassza **bemenetek**.

3. Az a **bemenetek** ablaktáblán válassza **streambemenet hozzáadása**, majd **az IoT Hub** a legördülő listából. A bemeneti új panelen adja meg a következőket:

   **Bemeneti áljel**: Adja meg egy egyedi nevet a bemenet.

   **Adja meg az IoT Hub-előfizetéséből**: Válassza ki ezt a választógombot.

   **Előfizetés**: Válassza ki az Azure-előfizetés ehhez az oktatóanyaghoz használja.

   **Az IoT Hub**: Válassza ki az IoT Hub, az oktatóanyaghoz használja.

   **Végpont**: Válassza ki **üzenetkezelési**.

   **Megosztott elérési házirend neve**: Válassza ki a megosztott elérési házirend azt szeretné, hogy az IoT hub használata a Stream Analytics-feladat nevét. A jelen oktatóanyag esetében válassza *szolgáltatás*. A *szolgáltatás* szabályzat jön létre alapértelmezés szerint az új IoT-központok és engedélyt ad a küldése és fogadása az IoT hub által elérhetővé tett felhőoldali végpontokon. További tudnivalókért lásd: [hozzáférés-vezérlési és engedélyek](iot-hub-devguide-security.md#access-control-and-permissions).

   **Megosztott elérési házirend kulcsa**: Ezt a mezőt a rendszer automatikusan kitölti a megosztott elérési házirend neve a választott beállítás alapján.

   **Fogyasztói csoport**: Válassza ki a korábban létrehozott fogyasztói csoportot.

   Az összes többi mező alapértelmezett értéken hagyja.

   ![Az Azure Stream Analytics-feladat bemenete hozzáadása](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia**válassza **kimenetek**.

2. Az a **kimenetek** ablaktáblán válassza **Hozzáadás** és **Power BI**.

3. Az a **Power BI – új kimenet** ablaktáblán válassza **engedélyezés** , és kövesse az utasításokat követve jelentkezzen be a Power BI-fiókjába.

4. Miután Power bi-bA bejelentkezett, adja meg a következőket:

   **Kimeneti alias**: A kimeneti egyedi aliast.

   **A csoport munkaterület**: Válassza ki a cél csoportos munkaterületre.

   **Adatkészlet neve**: Adja meg az adatkészlet nevét.

   **Tábla neve**: Adja meg egy tábla nevét.

   ![Kimenet hozzáadása az Azure Stream Analytics-feladat](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére.

   ![A lekérdezés hozzáadása az Azure Stream Analytics-feladat](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Kattintson a **Mentés** gombra.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

Válassza ki a Stream Analytics-feladat **áttekintése**, majd válassza **Start** > **most** > **Start**. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladat futtatása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Hozzon létre, és az adatok megjelenítése Power BI-jelentés közzététele

1. Győződjön meg arról, a mintaalkalmazás fut az eszközön. Ha nem, olvassa el a oktatóanyagok alatt [beállítani eszközét](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/en-us/)-fiókjába.

3. Válassza ki a munkaterületet használja, **saját munkaterület**.

4. Válassza ki **adatkészletek**.

   Az adatkészlet, a kimenet a Stream Analytics-feladat létrehozásakor megadott kell megjelennie.

5. Válassza ki a létrehozott adatkészlethez, **adja hozzá a jelentés** (az első ikon a jobb oldalon az adathalmaz neve).

   ![A Microsoft Power BI-jelentés létrehozása](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   1. Az a **Vizualizációk** a jelentésoldal létrehozása panelen válassza a vonaldiagram ikonját hozzáadása egy vonaldiagramot.

   2. A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát.

   3. Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   4. Húzza a **hőmérséklet** elemet az **Értékek** helyre.

      Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

      ![Egy vonaldiagramot a hőmérséklet hozzáadása a Microsoft Power BI-jelentés](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. Ehhez kövesse a fenti lépéseket, és helyezze **EventEnqueuedUtcTime** az x tengelyen és **páratartalom** az y tengelyen.

   ![Egy vonaldiagramot páratartalom hozzáadása a Microsoft Power BI-jelentés](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Válassza ki **mentése** a jelentés mentéséhez.

9. Válassza ki **jelentések** a bal oldali panelen, és válassza ki, hogy csak a jelentés létrehozása.

10. Válassza ki **fájl** > **webes közzététel**.

11. Válassza ki **beágyazási kód létrehozása**, majd válassza ki **közzététel**.

A megadott Ön a jelentés hivatkozást, amely a jelentés hozzáféréshez bárkivel megoszthatja és a egy kódrészletet, amely segítségével a jelentés beágyazása saját blogba vagy webhelyre.

![A Microsoft Power BI-jelentés közzététele](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft által a [Power BI-mobilalkalmazásokban](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) megtekintését és a Power BI-irányítópultok és jelentések esetében a mobileszközén.

## <a name="next-steps"></a>További lépések

Az Azure IoT hub valós idejű érzékelői adatainak megjelenítése Power bi-ban sikeresen felhasználta.

A másik lehetőség az Azure IoT Hub adatainak megjelenítése, lásd: [webalkalmazás használata Azure IoT Hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
