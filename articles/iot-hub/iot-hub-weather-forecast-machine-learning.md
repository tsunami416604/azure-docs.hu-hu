---
title: Időjárás-előrejelzés az Azure Machine Learning és az IoT Hub adatai használatával
description: Az Azure Machine Learning használatával előre jelezheti az eső esélyét az IoT hub által egy érzékelőből gyűjtött hőmérséklet- és páratartalom-adatok alapján.
author: robinsh
manager: philmea
keywords: időjárás-előrejelzés gépi tanulás
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122160"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Időjárás-előrejelzés az IoT-központ érzékelőadatainak használatával az Azure Machine Learningben

![Végpontok között diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

A gépi tanulás az adatelemzés olyan technikája, amely segít a számítógépeknek a meglévő adatokból való tanulásban a jövőbeli viselkedések, eredmények és trendek előrejelzéséhez. Az Azure Machine Learning egy felhőalapú prediktív elemzési szolgáltatás, amely lehetővé teszi elemzési megoldásként használható prediktív modellek gyors létrehozását és üzembe helyezését.

## <a name="what-you-learn"></a>Ismertetett témák

Megtudhatja, hogyan használhatja az Azure Machine Learning segítségével az időjárás-előrejelzést (eső esése) az Azure IoT hub hőmérséklet- és páratartalom-adatainak használatával. Az eső esélye az előkészített időjárás-előrejelzési modell eredménye. A modell a történelmi adatokra épül, hogy előre jelezz az eső esélyét a hőmérséklet és a páratartalom alapján.

## <a name="what-you-do"></a>Mit csinálsz

- Telepítse az időjárás-előrejelzési modellt webszolgáltatásként.
- Készítse elő az IoT hubot az adatelérésre egy fogyasztói csoport hozzáadásával.
- Hozzon létre egy Stream Analytics-feladatot, és konfigurálja a feladatot a következőkre:
  - A hőmérsékletre és a páratartalomra vonatkozó adatokat az IoT-központból olvashatja le.
  - Hívja a webszolgáltatást, hogy esőesélye legyen.
  - Mentse az eredményt egy Azure blob storage.Save the result to a Azure blob storage.
- Az időjárás-előrejelzés megtekintéséhez használja a Microsoft Azure Storage Exploreralkalmazást.

## <a name="what-you-need"></a>Mi szükséges

- Töltse ki a [Raspberry Pi online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz oktatóanyagait; például [a Raspberry Pi a node.js-szel.](iot-hub-raspberry-pi-kit-node-get-started.md) Ezek a következő követelményekre vonatkoznak:
  - Aktív Azure-előfizetés.
  - Egy Azure IoT hub az előfizetés alatt.
  - Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub.
- Egy [Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net/) fiók.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Az időjárás-előrejelzési modell telepítése webszolgáltatásként

Ebben a szakaszban az időjárás-előrejelzési modell az Azure AI-könyvtár. Ezután egy R-script modult ad hozzá a modellhez a hőmérséklet- és páratartalom-adatok tisztításához. Végül a modellt prediktív webszolgáltatásként telepíti.

### <a name="get-the-weather-prediction-model"></a>Az időjárás-előrejelzési modell beszereznie

Ebben a szakaszban az időjárás-előrejelzési modell az Azure AI-galériából származik, és nyissa meg az Azure Machine Learning Studio (klasszikus) ismerteti.

1. Nyissa meg az [időjárás-előrejelzési modell oldalát.](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)

   ![Az időjárás-előrejelzési modell lap megnyitása az Azure AI-galériában](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Kattintson **a Megnyitás a stúdióban (klasszikus)** gombra a modell megnyitásához a Microsoft Azure Machine Learning Studio (klasszikus) alkalmazásban.

   ![Nyissa meg az időjárás-előrejelzési modellt az Azure Machine Learning Studio-ban (klasszikus)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>R-script modul hozzáadása a hőmérséklet- és páratartalom-adatok tisztításához

Ahhoz, hogy a modell megfelelően viselkedjen, a hőmérsékletre és a páratartalomra vonatkozó adatoknak átválthatónak kell lenniük a numerikus adatokra. Ebben a szakaszban egy R-script modult ad hozzá az időjárás-előrejelzési modellhez, amely eltávolítja azokat a sorokat, amelyek olyan hőmérséklet- vagy páratartalom-adatértékekkel rendelkeznek, amelyek nem konvertálhatók numerikus értékekké.

1. Az Azure Machine Learning Studio ablak bal oldalán kattintson a nyílra az eszközök panel kibontásához. Írja be a "Végrehajtás" szót a keresőmezőbe. Válassza az **R-parancsfájl végrehajtása** modult.

   ![Válassza az R-parancsfájl végrehajtása modult](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Húzza az **R-parancsfájl végrehajtása** modult a **Hiányzó adatok tisztítása** modul és a diagramon lévő **R-parancsfájl végrehajtása** modul közelében. Törölje a kapcsolatot a **Tiszta hiányzó adatok** és az **R-parancsfájl végrehajtása** modulok között, majd csatlakoztassa az új modul bemeneteit és kimeneteit az ábrán látható módon.

   ![Végrehajtási R parancsfájl hozzáadása modul](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Az új **R-parancsfájl végrehajtása** modul kiválasztásával nyissa meg a tulajdonságablakot. Másolja és illessze be a következő kódot az **R Script** mezőbe.

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Ha elkészült, a tulajdonságok ablakának az alábbihoz hasonlóan kell kinéznie:

   ![Kód hozzáadása az R-parancsfájl végrehajtásához modulhoz](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Prediktív webszolgáltatás üzembe helyezése

Ebben a szakaszban érvényesítheti a modellt, beállít egy prediktív webszolgáltatást a modell alapján, majd üzembe helyezi a webszolgáltatást.

1. Kattintson a **Futtatás** gombra a modell lépéseinek érvényesítéséhez. Ez a lépés eltarthat néhány percig.

   ![Futtassa a kísérletet a lépések érvényesítéséhez](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Kattintson **a Webszolgáltatás** > **prediktív webszolgáltatás beállítása gombra.** Megnyílik a prediktív kísérletdiagram.

   ![Az időjárás-előrejelzési modell üzembe helyezése az Azure Machine Learning Studio-ban (klasszikus)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. A prediktív kísérlet diagram törölje a kapcsolatot a **webszolgáltatás bemeneti** modul és az **időjárás-adatkészlet** tetején. Ezután húzza a **webszolgáltatás beviteli** modulját valahol a **Score Model** modul közelében, és csatlakoztassa az ábrán látható módon:

   ![Két modul csatlakoztatása az Azure Machine Learning Studio-ban (klasszikus)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Kattintson a **FUTTATÁS** gombra a modell lépéseinek érvényesítéséhez.

1. Kattintson **a WEBSZOLGÁLTATÁS KÖZPONTI TELEPÍTÉSÉRE** a modell webszolgáltatásként való központi telepítéséhez.

1. A modell irányítópultján töltse le az **Excel 2010-es vagy korábbi munkafüzetet** **a KÉRÉSEK/VÁLASZ**programhoz.

   > [!Note]
   > Győződjön meg arról, hogy az **Excel 2010-es vagy korábbi munkafüzetet** akkor is letöltötte, ha az Excel újabb verzióját futtatja a számítógépen.

   ![Az Excel letöltése a REQUEST RESPONSE végponthoz](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Nyissa meg az Excel-munkafüzetet, jegyezze fel a **WEBSZOLGÁLTATÁS URL-címét** és **az ACCESS BILLENTYŰT**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása, konfigurálása és futtatása

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. Az [Azure Portalon](https://portal.azure.com/) kattintson az **Erőforrás létrehozása** > **Eszközök internetes hálózata** > **Stream Analytics-feladat** elemre.
1. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport:** Használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely:** Használja ugyanazt a helyet, mint az erőforráscsoport.

   **Rögzítés az irányítópulton**: Ezt a lehetőséget kiválasztva könnyen hozzáférhet az IoT Hubhoz az irányítópultról.

   ![Stream Analytics-feladat létrehozása az Azure-ban](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Kattintson **a Létrehozás gombra.**

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics feladatot.
1. A **Feladattopológia** területen kattintson a **Bemenetek** elemre.
1. A **Bemenetek** ablaktáblán kattintson a **Hozzáadás**gombra, majd adja meg a következő adatokat:

   **Bemeneti alias**: A bemenet egyedi aliasa.

   **Forrás**: Válassza az **IoT hub**lehetőséget.

   **Fogyasztói csoport**: Válassza ki a létrehozott fogyasztói csoportot.

   ![Bemenet hozzáadása a Stream Analytics-feladathoz az Azure-ban](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Kattintson **a Létrehozás gombra.**

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen kattintson a **Kimenetek** elemre.
1. A **Kimenetek** ablaktáblán kattintson a **Hozzáadás**gombra, majd adja meg a következő adatokat:

   **Kimeneti áljel**: A kimenet egyedi áljele.

   **Fogadó**: Válassza a **Blob Storage lehetőséget.**

   **Tárfiók:** A blob storage tárfiók. Létrehozhat egy tárfiókot, vagy használhat egy meglévőt.

   **Tároló:** Az a tároló, ahová a blob mentésre kerül. Létrehozhat egy tárolót, vagy használhat egy meglévőt.

   **Eseményszerializálási formátum**: Válassza a **CSV**lehetőséget.

   ![Kimenet hozzáadása az Azure-beli Stream Analytics-feladathoz](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Kattintson **a Létrehozás gombra.**

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Függvény hozzáadása a Stream Analytics feladathoz a telepített webszolgáltatás hívásához

1. A **Feladattopológia csoportban**kattintson a **Funkciók** > **hozzáadása**gombra.
1. Adja meg a következő információkat:

   **Függvény aliasa**: Írja be a értéket. `machinelearning`

   **Függvénytípusa**: Válassza az **Azure ML**lehetőséget.

   **Importálási lehetőség**: Válassza **az Importálás másik előfizetésből**lehetőséget.

   **URL**: Adja meg az Excel-munkafüzetből feljegyzett WEBSZOLGÁLTATÁS URL-címét.

   **Kulcs**: Adja meg az Excel-munkafüzetből feljegyzett HOZZÁFÉRÉSI KULCSOT.

   ![Függvény hozzáadása az Azure-beli Stream Analytics-feladathoz](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Kattintson **a Létrehozás gombra.**

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

1. Kattintson a **Mentés** gombra.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics-feladatban kattintson a **Start** > **now** > **Start**gombra. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladat futtatása](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Az időjárás-előrejelzés megtekintése a Microsoft Azure Storage Explorer segítségével

Futtassa az ügyfélalkalmazást a hőmérséklet- és páratartalom-adatok gyűjtésének és elküldésének megkezdéséhez az IoT hubba. Az IoT-központ által fogadott minden egyes üzenet esetében a Stream Analytics-feladat meghívja az időjárás-előrejelzési webszolgáltatást, hogy eső süljön el. Az eredmény ezután menti az Azure blob storage. Az Azure Storage Explorer egy olyan eszköz, amely segítségével megtekintheti az eredményt.

1. [Töltse le és telepítse a Microsoft Azure Storage Explorer](https://storageexplorer.com/)alkalmazást.
1. Nyissa meg az Azure Storage Explorert.
1. Jelentkezzen be Azure-fiókjába.
1. Válassza ki előfizetését.
1. Kattintson az előfizetési > **tárfiókok** > a storage-fiók > **blob tárolók** > a tárolóba.
1. Az eredmény megtekintéséhez töltsön le egy .csv fájlt. Az utolsó oszlop rögzíti az eső esélyét.

   ![Időjárás-előrejelzés eredménye az Azure Machine Learning segítségével](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Összefoglalás

Sikeresen használta az Azure Machine Learninget az IoT-központ által kapott hőmérséklet- és páratartalom-adatok alapján esőesély létrehozásához.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]