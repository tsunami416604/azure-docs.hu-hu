---
title: Az érzékelők adatstreamének Azure IoT Hub – Power bi-ban a valós idejű adatok vizuális |} Microsoft Docs
description: A Power BI használatával, amely az érzékelő gyűjtése történik, és az Azure IoT hub küldött hőmérséklet és a páratartalom adatainak megjelenítése.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: valós idejű adatok vizuális, az élő adatok vizuális érzékelő adatábrázolási
ms.assetid: e67c9c09-6219-4f0f-ad42-58edaaa74f61
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 12ac0596d70ae068ba17713d1251fbf117824f67
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Az Azure IoT Hub Power BI használatával valós idejű érzékelőadatok megjelenítése

![Végpontok közötti diagramja](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti, hogyan valós idejű érzékelőadatok, amely az Azure IoT hub megkapja a Power BI által megjelenítéséhez. Ha ki szeretné próbálni a a webalkalmazásokkal az IoT hub adatok megjelenítéséhez, olvassa el a [használata Azure Web Apps valós idejű érzékelőadatok Azure IoT hubról megjelenítéséhez](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Mit

- Felkészülés az IoT hub adatelérés egy felhasználói csoport hozzáadásával.
- Létrehozása, konfigurálása, és futtassa a Stream Analytics-feladat az adatok átvitele az IoT hub Power BI-fiókjába.
- Hozzon létre, és tegye közzé a Power BI-jelentés adatok megjelenítéséhez.

## <a name="what-you-need"></a>Mi szükséges

- Az oktatóanyag [beállítani az eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely hozzá van rendelve az alábbi követelményeknek:
  - Aktív Azure-előfizetés.
  - Az előfizetéshez tartozó Azure IoT hub.
  - Egy ügyfélalkalmazást, amely üzeneteket küld az Azure IoT hub.
- Power BI-fiókkal. ([Próbálja ingyenesen a Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Létrehozására, konfigurálására és a Stream Analytics-feladat futtatása

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **hozzon létre egy erőforrást** > **az eszközök internetes hálózatát** > **Stream Analytics-feladat**.
1. Adja meg a feladat a következő információkat.

   **Feladat neve**: a feladat nevét. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: ugyanazt a helyet használja a erőforráscsoportként működnek.

   **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

   ![A Stream Analytics-feladat létrehozása az Azure-ban](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="add-an-input-to-the-stream-analytics-job"></a>A Stream Analytics-feladat bemenete hozzáadása

1. Nyissa meg a Stream Analytics-feladat.
1. A **feladat topológia**, kattintson a **bemenetek**.
1. Az a **bemenetek** ablaktáblában kattintson **Hozzáadás**, és írja be a következő információkat:

   **A bemeneti alias**: a bemeneti egyedi alias.

   **Forrás**: válasszon **IoT-központ**.

   **Felhasználói csoport**: válassza ki az imént létrehozott fogyasztói csoportot.
1. Kattintson a **Create** (Létrehozás) gombra.

   ![A Stream Analytics-feladat bemenete hozzáadása az Azure-ban](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adja hozzá egy kimeneti a Stream Analytics-feladat

1. A **feladat topológia**, kattintson a **kimenetek**.
1. Az a **kimenetek** ablaktáblán kattintson a **Hozzáadás**, és írja be a következő információkat:

   **A kimeneti alias**: az egyedi alias a kimeneti oldal számára.

   **Gyűjtése**: válasszon **a Power BI**.
1. Kattintson a **engedélyezés**, és jelentkezzen be a Power BI-fiókjába.
1. Ha engedélyezett, adja meg a következő adatokat:

   **Munkaterület csoport**: válassza ki a cél csoport munkaterülettől.

   **A DataSet neve**: Adja meg a DataSet adatkészlet nevét.

   **Tábla neve**: Adjon meg egy tábla nevét.
1. Kattintson a **Create** (Létrehozás) gombra.

   ![Adja hozzá egy kimeneti Stream Analytics-feladat az Azure-ban](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A lekérdezést a Stream Analytics-feladat konfigurálása

1. A **feladat topológia**, kattintson a **lekérdezés**.
1. Cserélje le `[YourInputAlias]` a bemeneti áljel a feladat.
1. Cserélje le `[YourOutputAlias]` a kimeneti aliasnév a feladat.
1. Kattintson a **Save** (Mentés) gombra.

   ![A lekérdezés felvétele a Stream Analytics-feladat az Azure-ban](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>A Stream Analytics-feladat futtatása

Kattintson a Stream Analytics-feladat **Start** > **most** > **Start**. Ha a feladat sikeresen elindul, a feladat állapota a **leállítva** való **futtató**.

![Az Azure Stream Analytics-feladat futtatása](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Hozzon létre, és tegye közzé az adatok ábrázolása a Power BI-jelentés

1. Ellenőrizze, hogy a mintaalkalmazás fut-e az eszközön. Ha nem, olvassa el az oktatóprogramok [beállítani az eszközét](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/en-us/) fiók.
1. Nyissa meg a csoport munkaterületet, amely a Stream Analytics-feladat kimenetének létrehozása után.
1. Kattintson a **adatkészletek Streaming**.

   Meg kell jelenniük a felsorolt adatkészlet a kimenetet a Stream Analytics-feladat létrehozásakor megadott.
1. A **műveletek**, az első ikonra kattintva hozzon létre egy jelentést.

   ![Microsoft Power BI-jelentés létrehozása](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Hozzon létre egy vonaldiagramot időbeli valós idejű hőmérséklet megjelenítéséhez.
   1. A jelentés létrehozása lapon adja hozzá a grafikont.
   1. Az a **mezők** ablaktáblában bontsa ki a Stream Analytics-feladat kimeneti létrehozásakor megadott táblázat.
   1. A csomóponthúzási **EventEnqueuedUtcTime** való **tengely** a a **képi megjelenítések** ablaktáblán.
   1. A csomóponthúzási **hőmérséklet** való **értékek**.

      Most egy vonaldiagramot jön létre. Az x tengely dátum és idő az UTC időzóna jeleníti meg. Az y tengelyen jeleníti meg az érzékelő hőmérséklet.

      ![Egy vonaldiagramot hőmérséklet hozzáadása a Microsoft Power BI-jelentés](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Hozzon létre egy másik vonaldiagram időbeli valós idejű páratartalom megjelenítéséhez. Ehhez hajtsa végre a fenti lépéseket, és helyezze **EventEnqueuedUtcTime** az x tengelyen és **páratartalom** az y tengelyen.

   ![Egy vonaldiagramot páratartalom hozzáadása a Microsoft Power BI-jelentés](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Kattintson a **mentése** a jelentést.
1. Kattintson a **fájl** > **webes közzététel**.
1. Kattintson a **hozzon létre beágyazási kódot**, és kattintson a **közzététel**.

A jelentés hivatkozása van megadva, hogy egy kódrészletet a jelentés integrálja a saját blogba vagy webhelyre és jelentés hozzáférési bárkivel megoszthatja.

![Microsoft Power BI-jelentés közzététele](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

A Microsoft is kínál a [Power BI mobilalkalmazásokkal](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) megtekintéséhez és a Power BI-irányítópult és jelentések való interakció a mobileszközön.

## <a name="next-steps"></a>További lépések

A valós idejű érzékelőadatok jelenítheti meg az Azure IoT hub a Power bi-ban sikeresen használt.
Nincs megadva is az Azure IoT Hub-adatok ábrázolása. Lásd: [használata Azure Web Apps valós idejű érzékelőadatok Azure IoT hubról megjelenítéséhez](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
