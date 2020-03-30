---
title: Az adatok valós idejű adatvizualizációja az Azure IoT Hubon – Power BI
description: A Power BI segítségével vizualizálhatja az érzékelőről gyűjtött és az Azure IoT-központba küldött hőmérséklet- és páratartalom-adatokat.
author: robinsh
keywords: valós idejű adatvizualizáció, élő adatmegjelenítés, érzékelő adatok megjelenítése
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954627"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Valós idejű érzékelőadatok megjelenítése az Azure IoT Hubból a Power BI használatával

![Végpontok között diagram](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Megtudhatja, hogyan jelenítheti meg az Azure IoT-központ által a Power BI használatával fogadott valós idejű érzékelőadatokat. Ha meg szeretné próbálni az adatokat az IoT hub egy webalkalmazással, [lásd: Egy webalkalmazás megjelenítése valós idejű érzékelő adatok az Azure IoT Hub.](iot-hub-live-data-visualization-in-web-apps.md)

## <a name="what-you-do"></a>Mit csinálsz

* Készítse elő az IoT hubot az adatelérésre egy fogyasztói csoport hozzáadásával.

* Hozzon létre, konfiguráljon és futtasson egy Stream Analytics-feladatot az IoT hubról a Power BI-fiókba történő adatátvitelhez.

* Az adatok megjelenítéséhez Hozzon létre és tegyen közzé Egy Power BI-jelentést.

## <a name="what-you-need"></a>Mi szükséges

* Töltse ki a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz oktatóanyagait; például [a Raspberry Pi a node.js-szel.](iot-hub-raspberry-pi-kit-node-get-started.md) Ezek a cikkek a következő követelményekre vonatkoznak:
  
  * Aktív Azure-előfizetés.
  * Egy Azure IoT hub az előfizetés alatt.
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.

* Egy Power BI-fiók. ([Próbálja ki ingyen a Power BI-t](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása, konfigurálása és futtatása

Kezdjük egy Stream Analytics-feladat létrehozásával. A feladat létrehozása után megadhatja a bemeneteket, a kimeneteket és az adatok beolvasásához használt lekérdezést.

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com)válassza az > **Erőforrás-internet-adatfolyam-elemzési** > **feladat** **létrehozása**lehetőséget.

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport:** Használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely:** Használja ugyanazt a helyet, mint az erőforráscsoport.

   ![Stream Analytics-feladat létrehozása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Kattintson a **Létrehozás** gombra.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics feladatot.

2. A **Feladattopológia csoportban**válassza **a Bemenetek**lehetőséget.

3. A **Bemenetek** ablaktáblán válassza az **Adatfolyam-bevitel hozzáadása**lehetőséget, majd válassza az **IoT Hub** lehetőséget a legördülő listából. Az új beviteli ablaktáblán adja meg a következő adatokat:

   **Bemeneti alias**: Adjon meg egy egyedi aliast a bemenethez.

   **IoT Hub biztosítása az előfizetésből:** Válassza ezt a választógombot.

   **Előfizetés**: Válassza ki az oktatóanyaghoz használt Azure-előfizetést.

   **IoT Hub:** Válassza ki az ehhez az oktatóanyaghoz használt IoT Hubot.

   **Végpont**: Válassza az **Üzenetkezelés** lehetőséget.

   **Megosztott hozzáférésű házirend neve:** Válassza ki a nevét a megosztott hozzáférési szabályzat, amelyet a Stream Analytics-feladat használni az IoT hub. Ebben az oktatóanyagban kiválaszthatja a *szolgáltatást.* A *szolgáltatásszabályzat* alapértelmezés szerint az új IoT-központok, és engedélyt ad a felhőalapú végpontok küldése és fogadása az IoT hub által elérhetővé tett felhőalapú végpontok. További információ: [Hozzáférés-vezérlés és engedélyek](iot-hub-devguide-security.md#access-control-and-permissions).

   **Megosztott hozzáférésű házirendkulcs**: Ez a mező automatikusan ki van töltve a megosztott hozzáférési házirend nevének kiválasztása alapján.

   **Fogyasztói csoport**: Válassza ki a korábban létrehozott fogyasztói csoportot.

   Hagyja az összes többi mezőt az alapértelmezett értékükön.

   ![Bemenet hozzáadása egy Stream Analytics-feladathoz az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia**csoportban válassza **a Kimenetek**lehetőséget.

2. A **Kimenetek** ablaktáblán válassza a **Hozzáadás** és **a Power BI**lehetőséget.

3. A **Power BI – Új kimenetablakban** válassza az **Engedélyezés lehetőséget,** és kövesse az utasításokat a Power BI-fiókba való bejelentkezéshez.

4. Miután bejelentkezett a Power BI-ba, adja meg a következő adatokat:

   **Kimeneti alias**: A kimenet egyedi aliasa.

   **Csoportmunkaterület:** Válassza ki a célcsoport-munkaterületet.

   **Adatkészlet neve**: Adjon meg egy adatkészlet nevet.

   **Tábla neve**: Írjon be egy táblanevet.

   ![Kimenet hozzáadása streamanalytics-feladathoz az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére.

   ![Lekérdezés hozzáadása egy Stream Analytics-feladathoz az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Kattintson a **Mentés** gombra.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics-feladatban válassza az **Áttekintés**lehetőséget, majd válassza a Start now Start **(Indítsa** > **el)** > **lehetőséget.** Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladat futtatása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Power BI-jelentés létrehozása és közzététele az adatok megjelenítéséhez

1. Győződjön meg arról, hogy a mintaalkalmazás fut az eszközön. Ha nem, akkor olvassa el az oktatóanyagok at [Beállítása a készülék](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/en-us/)-fiókjába.

3. Válassza ki a használt munkaterületet, **a Saját munkaterület**et.

4. Válassza **az Adatkészletek**lehetőséget .

   Meg kell jelennie a megadott adatkészletet, amikor létrehozta a kimenetet a Stream Analytics-feladathoz.

5. A létrehozott adatkészlethez válassza a **Jelentés hozzáadása** lehetőséget (az adatkészlet nevétől jobbra található első ikon).

   ![Microsoft Power BI-jelentés létrehozása](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   1. A jelentés létrehozási lapjának **Képi megjelenítések** ablaktábláján jelölje ki a vonaldiagram ikonját vonaldiagram hozzáadásához.

   2. A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát.

   3. Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   4. Húzza a **hőmérséklet** elemet az **Értékek** helyre.

      Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

      ![Hőmérséklet-diagram hozzáadása A Microsoft Power BI-jelentéshez](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. Ehhez kövesse a fenti lépéseket, és helyezze **az EventEnqueuedUtcTime-ot** az x tengelyre és a **páratartalmat** az y tengelyre.

   ![Páratartalom-vonaldiagram hozzáadása Microsoft Power BI-jelentéshez](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. A jelentés mentéséhez válassza a **Mentés** gombot.

9. Válassza a **Jelentések** lehetőséget a bal oldali ablaktáblán, majd jelölje ki az imént létrehozott jelentést.

10. Válassza **a Fájlközzététel** > **webes közzététel**lehetőséget.

11. Válassza **a Beágyazási kód létrehozása**lehetőséget, majd a **Közzététel**lehetőséget.

Megadhatja a jelentéshivatkozást, amelyet bárkivel megoszthat a jelentés eléréséhez, valamint egy kódrészletet, amelynek segítségével integrálhatja a jelentést a blogjába vagy a webhelyébe.

![Microsoft Power BI-jelentés közzététele](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

A Microsoft a [Power BI mobilalkalmazásokat](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) is kínálja a Power BI-irányítópultok és -jelentések megtekintéséhez és kezeléséhez mobileszközén.

## <a name="next-steps"></a>További lépések

Sikeresen használta a Power BI-t az Azure IoT-központvalós idejű érzékelőadatainak megjelenítésére.

Az Azure IoT Hub ból származó adatok megjelenítésének másik módjáról a [Webalkalmazás használata az Azure IoT Hub valós idejű érzékelőadatainak megjelenítéséhez.](iot-hub-live-data-visualization-in-web-apps.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
