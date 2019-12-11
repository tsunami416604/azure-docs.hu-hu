---
title: Időjárás-előrejelzés a Azure Machine Learning IoT Hub adataival
description: A Azure Machine Learning segítségével előre megjósolhatja, hogy az IoT hub milyen hőmérséklet-és páratartalom-adatok alapján gyűjti az eső esélyét.
author: robinsh
manager: philmea
keywords: Időjárás-előrejelzés gépi tanulás
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: d26ccd47ada4f1f1fd87f315e05f822bb2463114
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976179"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Időjárás-előrejelzés az IoT hub Sensor-adatainak használatával Azure Machine Learning

![Végpontok közötti diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

A gépi tanulás olyan adatelemzési módszer, amely segít a számítógépeknek megtanulni a meglévő adatokból a jövőbeli viselkedések, eredmények és trendek előrejelzését. Az Azure Machine Learning egy felhőalapú prediktív elemzési szolgáltatás, amely lehetővé teszi elemzési megoldásként használható prediktív modellek gyors létrehozását és üzembe helyezését.

## <a name="what-you-learn"></a>Ismertetett témák

Az Azure IoT hub hőmérséklet-és páratartalom-adatainak használatával megtudhatja, hogyan végezheti el az időjárás-előrejelzés (az eső esélye) Azure Machine Learning használatát. Az eső esélye az előkészített időjárási előrejelzési modell kimenete. A modell a múltbeli adatmennyiségre épül, hogy a hőmérséklet és a páratartalom alapján megbecsülje az eső esélyét.

## <a name="what-you-do"></a>Teendők

- Az időjárási előrejelző modell üzembe helyezése webszolgáltatásként.
- Felhasználói csoport hozzáadásával az IoT hub készen áll az adathozzáférésre.
- Hozzon létre egy Stream Analytics feladatot, és konfigurálja a következő feladatot:
  - Az IoT hub hőmérséklet-és páratartalom-adatainak olvasása.
  - Hívja meg a webszolgáltatást az eső véletlen eléréséhez.
  - Mentse az eredményt egy Azure Blob Storage-tárolóba.
- A Microsoft Azure Storage Explorer használatával megtekintheti az időjárás-előrejelzést.

## <a name="what-you-need"></a>Mi szükséges

- Fejezze be a [málna PI online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz egyik oktatóanyagát; például: [málna PI és Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Ezek az alábbi követelményekre vonatkoznak:
  - Aktív Azure-előfizetés.
  - Az előfizetéshez tartozó Azure IoT hub.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub-nak.
- Egy [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net/) fiók.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Az időjárási előrejelzési modell üzembe helyezése webszolgáltatásként

1. Ugrás az [időjárás-előrejelző modell oldalára](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Kattintson a **Megnyitás a studióban** Microsoft Azure Machine learning Studio (klasszikus) lehetőségre.
   ![nyissa meg Cortana Intelligence Gallery időjárás-előrejelzési modell lapját](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Kattintson a **Futtatás** gombra a modell lépéseinek ellenőrzéséhez. Ez a lépés 2 percet is igénybe vehet.
   ![nyissa meg Azure Machine Learning Studio (klasszikus) időjárási előrejelzési modelljét](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Kattintson a WEBSZOLGÁLTATÁS > **prediktív webszolgáltatás** **beállítása** elemre.
   ![az időjárás-előrejelző modell üzembe helyezését Azure Machine Learning Studio (klasszikus)](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. A diagramon húzza a **webszolgáltatás bemeneti** modulját valahol a **pontszám modell** modul közelében.
1. Kapcsolódjon a **webszolgáltatáshoz tartozó bemeneti** modulhoz a **pontszám modell** modulhoz.
   ![két modul összekapcsolását Azure Machine Learning Studio (klasszikus)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Kattintson a **Futtatás** gombra a modell lépéseinek ellenőrzéséhez.
1. Kattintson a **webszolgáltatás telepítése** lehetőségre a modell webszolgáltatásként való üzembe helyezéséhez.
1. A modell irányítópultján töltse le a **kérelem/válasz** **Excel 2010 vagy korábbi munkafüzetét** .

   > [!Note]
   > Ügyeljen arra, hogy az **excel 2010-as vagy korábbi munkafüzetét** akkor is letöltse, ha az Excel újabb verzióját futtatja a számítógépen.

   ![Az Excel letöltése a kérelem VÁLASZának végpontja számára](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Nyissa meg az Excel-munkafüzetet, jegyezze fel a **webszolgáltatás URL-címét** és a **hozzáférési kulcsot**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics-feladatok létrehozása, konfigurálása és futtatása

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com/) kattintson az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Stream Analytics-feladat** elemre.
1. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely**: használja ugyanazt a helyet, mint az erőforráscsoport.

   **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

   ![Stream Analytics-feladatok létrehozása az Azure-ban](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics feladatot.
1. A **Feladattopológia** területen kattintson a **Bemenetek** elemre.
1. A **bemenetek** ablaktáblán kattintson a **Hozzáadás**elemre, majd adja meg a következő adatokat:

   **Bemeneti alias**: a bemenet egyedi aliasa.

   **Forrás**: válassza az **IoT hub**elemet.

   **Fogyasztói csoport**: válassza ki a létrehozott fogyasztói csoportot.

   ![Bemenet hozzáadása a Stream Analytics feladathoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen kattintson a **Kimenetek** elemre.
1. A **kimenetek** ablaktáblán kattintson a **Hozzáadás**elemre, majd adja meg a következő adatokat:

   **Kimeneti áljel**: A kimenet egyedi áljele.

   Fogadó **: válassza**a **blob Storage**lehetőséget.

   **Storage-fiók**: a blob Storage-hoz tartozó Storage-fiók. Hozzon létre egy Storage-fiókot, vagy használjon egy meglévőt.

   **Container (tároló**): az a tároló, amelyben a blob mentve van. Létrehozhat egy tárolót, vagy használhat egy meglévőt is.

   **Esemény szerializálási formátuma**: válassza a **CSV**lehetőséget.

   ![Kimenet hozzáadása a Stream Analytics feladatokhoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Függvény hozzáadása a Stream Analytics feladathoz a telepített webszolgáltatás meghívásához

1. A **feladatok topológiája**területen kattintson a **függvények** > **Hozzáadás**elemre.
1. Adja meg a következő információkat:

   **Függvény aliasa**: írja be a `machinelearning`.

   **Függvény típusa**: válassza az **Azure ml**lehetőséget.

   **Importálási lehetőség**: válassza az **Importálás másik előfizetésből**lehetőséget.

   **URL**: adja meg az Excel-munkafüzetből lejegyzett webszolgáltatás URL-címét.

   **Kulcs**: adja meg az Excel-munkafüzetből lejegyzett hozzáférési kulcsot.

   ![Függvény hozzáadása a Stream Analytics feladathoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen kattintson a **Lekérdezés** elemre.
1. Cserélje le a meglévő kódot a következő kódra:

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

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>A Microsoft Azure Storage Explorer használatával megtekintheti az időjárás-előrejelzést

Futtassa az ügyfélalkalmazás a hőmérséklet és a páratartalom adatok IoT hubhoz való gyűjtésének és küldésének megkezdéséhez. Az IoT hub által fogadott összes üzenet esetében a Stream Analytics-feladatokban meghívja az időjárás-előrejelzési webszolgáltatást, hogy a rendszer az eső esélyt hozza létre. Az eredmény ezután az Azure Blob Storage-ba lesz mentve. Azure Storage Explorer egy eszköz, amellyel megtekintheti az eredményt.

1. [Microsoft Azure Storage Explorer letöltése és telepítése](https://storageexplorer.com/).
1. Nyissa meg Azure Storage Explorer.
1. Jelentkezzen be Azure-fiókjába.
1. Válassza ki előfizetését.
1. Kattintson az előfizetésre > **Storage-fiókok** > a Storage-fiókja > **blob-tárolók** > a tárolót.
1. Nyisson meg egy. csv-fájlt, és tekintse meg az eredményt. Az utolsó oszlop az eső esélyét rögzíti.

   ![Időjárás-előrejelzési eredmény beolvasása Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Összefoglalás

Sikeresen használta a Azure Machine Learningt, hogy az IoT hub hőmérséklet-és páratartalom-adatai alapján előkészítse az eső esélyét.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]