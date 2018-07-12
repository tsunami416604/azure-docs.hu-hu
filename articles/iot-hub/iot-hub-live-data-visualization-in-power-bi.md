---
title: Az Azure IoT Hub – Power BI érzékelőktől kapott adatok valós idejű adatok vizualizációját |} A Microsoft Docs
description: A Power BI használatával jelenítheti meg az érzékelő összegyűjtött és az Azure IoT hubra küldött hőmérséklettel és páratartalommal kapcsolatos adatokat.
author: rangv
manager: ''
keywords: valós idejű adatvizualizáció, élő adatvizualizáció, érzékelő adatmegjelenítés
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: a3c54fe635fe0f8988c321684a815e9896922587
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38235505"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Azure IoT Hub használata a Power BI valós idejű érzékelői adatainak megjelenítése

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti, hogyan jelenítheti meg a valós idejű érzékelői adatokat az Azure IoT hub által fogadott a Power BI által. Ha azt szeretné, próbálkozhat megjelenítheti az adatokat az IoT hub, a Web Apps, olvassa el a [használata az Azure Web Apps az Azure IoT Hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>TEENDŐ

- Készüljön fel az IoT hub az adatok eléréséhez egy fogyasztói csoport hozzáadásával.
- Létrehozása, konfigurálása és az adatátvitelhez a Stream Analytics-feladat futtatása az IoT hub a Power BI-fiókjába.
- Hozzon létre, és az adatok megjelenítése Power BI-jelentés közzététele.

## <a name="what-you-need"></a>Mi szükséges

- Az oktatóanyag [beállítani eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely magában foglalja az alábbi követelményeknek:
  - Aktív Azure-előfizetés.
  - Az Azure IoT hub az előfizetéséhez.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.
- A Power BI-fiókra. ([Próbálja ki ingyenesen a Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Létrehozása, konfigurálása és a egy Stream Analytics-feladat futtatása

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com) kattintson az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Stream Analytics-feladat** elemre.
1. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: ugyanazt a helyet használja, az erőforráscsoportot.

   **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

   ![Stream Analytics-feladat létrehozása az Azure-ban](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics-feladat.
1. A **Feladattopológia** területen kattintson a **Bemenetek** elemre.
1. A a **bemenetek** ablaktáblán kattintson a **hozzáadása**, majd adja meg a következő információkat:

   **Bemeneti áljel**: a bemeneti az egyedi aliast.

   **Forrás**: válasszon **az IoT hub**.

   **Fogyasztói csoport**: válassza ki az imént létrehozott fogyasztói csoportot.
1. Kattintson a **Create** (Létrehozás) gombra.

   ![Az Azure Stream Analytics-feladat bemenete hozzáadása](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen kattintson a **Kimenetek** elemre.
1. Az a **kimenetek** ablaktáblán kattintson a **Hozzáadás**, majd adja meg a következő információkat:

   **Kimeneti áljel**: A kimenet egyedi áljele.

   **Fogadó**: válasszon **Power bi-ban**.
1. Kattintson a **engedélyezés**, majd jelentkezzen be a Power BI-fiók.
1. Ha jogosult, adja meg a következőket:

   **A csoport munkaterület**: válassza ki a cél csoportos munkaterületre.

   **Adatkészlet neve**: Adja meg az adatkészlet nevét.

   **Tábla neve**: Adja meg a tábla nevét.
1. Kattintson a **Create** (Létrehozás) gombra.

   ![Kimenet hozzáadása az Azure Stream Analytics-feladat](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen kattintson a **Lekérdezés** elemre.
1. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére.
1. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére.
1. Kattintson a **Save** (Mentés) gombra.

   ![A lekérdezés hozzáadása az Azure Stream Analytics-feladat](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics-feladat területen kattintson az **Indítás** > **Most** > **Indítás** elemre. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladat futtatása az Azure-ban](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Hozzon létre, és az adatok megjelenítése Power BI-jelentés közzététele

1. Győződjön meg arról, a mintaalkalmazás fut az eszközön. Ha nem, olvassa el a oktatóanyagok alatt [beállítani eszközét](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/en-us/)-fiókjába.
1. Nyissa meg a csoportos munkaterület állíthat be a kimenet a Stream Analytics-feladat létrehozásakor.
1. Kattintson a **Streamelési adatkészletek**.

   Megjelenik az adatkészlet, amelyet a Stream Analytics-feladat kimenetének létrehozásakor adott meg.
1. A **MŰVELETEK** területen kattintson az első ikonra egy jelentés létrehozásához.

   ![A Microsoft Power BI-jelentés létrehozása](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.
   1. Adja hozzá a jelentésoldal létrehozása egy vonaldiagramot.
   1. A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát.
   1. Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.
   1. Húzza a **hőmérséklet** elemet az **Értékek** helyre.

      Most jön létre egy vonaldiagramot. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

      ![Egy vonaldiagramot a hőmérséklet hozzáadása a Microsoft Power BI-jelentés](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. Ehhez kövesse a fenti lépéseket, és helyezze **EventEnqueuedUtcTime** az x tengelyen és **páratartalom** az y tengelyen.

   ![Egy vonaldiagramot páratartalom hozzáadása a Microsoft Power BI-jelentés](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Kattintson a **Mentés** elemre a jelentés mentéséhez.
1. Kattintson a **fájl** > **webes közzététel**.
1. Kattintson a **beágyazási kód létrehozása**, és kattintson a **közzététel**.

A jelentés hivatkozása már a megadott jelentés-hozzáférés és a egy kódrészletet a jelentés integrálása saját blogba vagy webhelyre bárkivel megoszthatja.

![A Microsoft Power BI-jelentés közzététele](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft által a [Power BI-mobilalkalmazásokban](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) megtekintését és a Power BI-irányítópultok és jelentések esetében a mobileszközén.

## <a name="next-steps"></a>További lépések

Az Azure IoT hub valós idejű érzékelői adatainak megjelenítése Power bi-ban sikeresen felhasználta.
Nincs más módja az Azure IoT Hub adatainak megjelenítése. Lásd: [használata az Azure Web Apps az Azure IoT Hub valós idejű érzékelői adatainak megjelenítése](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
