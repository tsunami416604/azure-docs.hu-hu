---
title: Időjárás-előrejelzés az IoT Hub adatainak az Azure Machine Learning segítségével |} A Microsoft Docs
description: Használja az Azure Machine Learning előre jelezni az esélye, hogy esőfelhő érzékelő gyűjti össze az IoT hub hőmérséklettel és páratartalommal kapcsolatos adatok alapján.
author: robinsh
manager: philmea
keywords: időjárás-előrejelzés gépi tanulás
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: a203018cb1a27c17b9c7162b50a380f9edae2e60
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273192"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Időjárás-előrejelzés az IoT hub érzékelői adatainak használatával az Azure Machine Learningben

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning olyan adatelemzési módszer, amelynek segítségével a számítógépek, ismerje meg, a meglévő adatokból jelezheti előre a jövőbeni viselkedéseket, kimeneteket és trendeket. Az Azure Machine Learning egy felhőalapú prediktív elemzési szolgáltatás, amely lehetővé teszi elemzési megoldásként használható prediktív modellek gyors létrehozását és üzembe helyezését.

## <a name="what-you-learn"></a>Ismertetett témák

Megismerheti, hogyan használhatja az Azure Machine Learning időjárás előrejelzéseket (esőfelhő esélyét) hőmérséklettel és páratartalommal kapcsolatos adatok az Azure IoT hub használatával. Az esélye, hogy esőfelhő egy előkészített időjárás-előrejelzési modell kimenetét. A modell előrejelzése alapján hőmérséklettel és páratartalommal kapcsolatos esőfelhő esélyét előzményadatok épül.

## <a name="what-you-do"></a>TEENDŐ

- Az időjárás-előrejelzési modellt üzembe webszolgáltatásként.
- Készüljön fel az IoT hub az adatok eléréséhez egy fogyasztói csoport hozzáadásával.
- Stream Analytics-feladat létrehozása, és adja meg a feladatot:
  - Hőmérséklettel és páratartalommal kapcsolatos adatokat olvasni az IoT hubnak.
  - Hívja meg a webszolgáltatás esőfelhő annak a lehetősége lekérni.
  - Egy Azure blob Storage-tárolóba mentheti az eredményt.
- A Microsoft Azure Storage Explorer használatával megtekinteni időjárás.

## <a name="what-you-need"></a>Mi szükséges

- Az oktatóanyag [beállítani eszközét](iot-hub-raspberry-pi-kit-node-get-started.md) fejeződött be, amely magában foglalja az alábbi követelményeknek:
  - Aktív Azure-előfizetés.
  - Az Azure IoT hub az előfizetéséhez.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.
- An Azure Machine Learning Studio account. ([Ingyenesen kipróbálhatja a Machine Learning Studio](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Az időjárás-előrejelzési modellt üzembe webszolgáltatásként

1. Nyissa meg a [időjárás-előrejelzési modell oldalán](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Kattintson a **Megnyitás a Studióban** a Microsoft Azure Machine Learning Studióban.
   ![Az időjárási előrejelzések Rétegmodellek oldalán nyissa meg a Cortana Intelligence-katalógusban](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Kattintson a **futtatása** ellenőrzése a lépéseket a modellben. Ebben a lépésben 2 percet is igénybe vehet.
   ![Az Azure Machine Learning Studióban nyissa meg az időjárás-előrejelzési modell](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Kattintson a **ÁLLÍTSA be a WEB SERVICE** > **prediktív webszolgáltatás**.
   ![Az Azure Machine Learning studióban időjárás-előrejelzési modell üzembe helyezése](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. A diagramon, húzza a **bemenet webes** modul valahol közelében a **Score Model** modul.
1. Csatlakozás a **bemenet webes** modult a **Score Model** modul.
   ![Csatlakozás az Azure Machine Learning Studióban két modul](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Kattintson a **futtatása** ellenőrzése a lépéseket a modellben.
1. Kattintson a **WEBSZOLGÁLTATÁS üzembe helyezése** a modellt webszolgáltatásként üzembe helyezéséhez.
1. A modell az irányítópulton, töltse le a **Excel 2010 vagy korábbi munkafüzet** a **KÉRÉS/válasz**.

   > [!Note]
   > Győződjön meg arról, le kell tölteni a **Excel 2010 vagy korábbi munkafüzet** még akkor is, ha az Excel egy újabb verziójában a számítógépen futtatja.

   ![Töltse le az Excel, a kérelem-válasz-végpont](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Nyissa meg az Excel-munkafüzet, jegyezze fel a **WEB SERVICE URL-cím** és **HÍVÓBETŰ**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Létrehozása, konfigurálása és a egy Stream Analytics-feladat futtatása

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com/) kattintson az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Stream Analytics-feladat** elemre.
1. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, amely az IoT hub használja.

   **Hely**: Használja ugyanazt a helyet az erőforráscsoportban.

   **Rögzítés az irányítópulton**: Ellenőrizze ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT hubhoz az irányítópultról.

   ![Stream Analytics-feladat létrehozása az Azure-ban](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics-feladat.
1. A **Feladattopológia** területen kattintson a **Bemenetek** elemre.
1. A a **bemenetek** ablaktáblán kattintson a **hozzáadása**, majd adja meg a következő információkat:

   **Bemeneti áljel**: A bevitelhez egyedi aliasneve.

   **forrás**: Válassza ki **az IoT hub**.

   **Fogyasztói csoport**: Válassza ki a fogyasztói csoportot hozott létre.

   ![A Stream Analytics-feladat bemenete hozzáadása az Azure-ban](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen kattintson a **Kimenetek** elemre.
1. Az a **kimenetek** ablaktáblán kattintson a **Hozzáadás**, majd adja meg a következő információkat:

   **Kimeneti alias**: A kimeneti egyedi aliast.

   **Fogadó**: Válassza ki **a Blob Storage-**.

   **Storage-fiók**: A tárfiók a blob storage. Hozzon létre egy tárfiókot, vagy használjon egy meglévőt.

   **tároló**: A tároló, blob mentési helye. Hozzon létre egy tárolót, vagy használjon egy meglévőt.

   **Eseményszerializációs formátum**: Válassza ki **CSV**.

   ![Kimenet hozzáadása a Stream Analytics-feladat az Azure-ban](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>A Stream Analytics-feladat a telepített webes szolgáltatás hívása egy függvény hozzáadása

1. A **Feladattopológia**, kattintson a **funkciók** > **Hozzáadás**.
1. Adja meg a következő információkat:

   **Függvény aliasa**: Írja be a `machinelearning` (igen) kifejezést.

   **Függvénytípus**: Válassza ki **Azure gépi tanulás**.

   **Importálási beállítás**: Válassza ki **importálás másik előfizetésből**.

   **URL-CÍM**: Adja meg a WEB SERVICE URL-cím útmutatóban lejegyzett az Excel-munkafüzetből.

   **kulcs**: Adja meg a hozzáférési kulcs útmutatóban lejegyzett az Excel-munkafüzetből.

   ![Függvény hozzáadása a Stream Analytics-feladat az Azure-ban](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen kattintson a **Lekérdezés** elemre.
1. Cserélje le a meglévő kódot az alábbira:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére.

   A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére.

1. Kattintson a **Save** (Mentés) gombra.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics-feladat területen kattintson az **Indítás** > **Most** > **Indítás** elemre. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladat futtatása](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>A Microsoft Azure Storage Explorer használatával megtekinteni időjárás-előrejelzés

Futtassa az ügyfélalkalmazást, összegyűjtése és hőmérséklettel és páratartalommal kapcsolatos adatok küldését az IoT hub elindításához. Az IoT hub által fogadott üzenetek a Stream Analytics-feladat meghívja az időjárás-előrejelzés webszolgáltatás esőfelhő esélyét előállításához. Az Azure blob storage majd menti az eredményt. Az Azure Storage Explorer egy olyan eszköz, az eredmény megtekintéséhez használhatja.

1. [Töltse le és telepítse a Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Nyissa meg az Azure Storage Explorerben.
1. Jelentkezzen be Azure-fiókjába.
1. Válassza ki előfizetését.
1. Kattintson az előfizetésre > **Tárfiókok** > a tárfiók > **Blobtárolók** > a tárolót.
1. Nyisson meg egy .csv-fájlba, az eredmény megjelenítéséhez. Az utolsó oszlopban az esélye, hogy esőfelhő rögzíti.

   ![Az Azure Machine Learning időjárás-előrejelzés eredményének beolvasása](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Összegzés

Az Azure Machine Learning sikeresen felhasználta az esélye, hogy az IoT hub által fogadott hőmérséklettel és páratartalommal kapcsolatos adatok alapján esőfelhő előállításához.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]