---
title: "Az Azure Machine Learning Azure IoT oldala telepítése |} Microsoft Docs"
description: "Azure Machine Learning peremhálózati eszköz egy modul telepítése"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/06/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e2314f589456f604c8c008e10fb8084e0524575d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Azure Machine Learning egy IoT peremhálózati modulként telepítése – előzetes

Az IoT-Edge modulok, amely megvalósítja az üzleti logikát, közvetlenül az IoT peremhálózati eszköz a kód telepítésére használhatja. Ez az oktatóanyag bemutatja, hogyan üzembe helyezése az Azure Machine Learning modulban, ha egy eszköz sikertelen a szimulált IoT peremhálózati eszközön létrehozott érzékelőadatait alapján előrejelzi a [telepítése Azure IoT Edge a szimulált eszköz Windows] [ lnk-tutorial1-win] vagy [Linux] [ lnk-tutorial1-lin] oktatóanyagok. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 

> [!div class="checklist"]
> * Hozzon létre egy Azure Machine Learning-modul
> * A modul tároló leküldése egy Azure-tárolót beállításjegyzék
> * Az IoT-peremhálózati eszköz egy Azure Machine Learning-modul telepítése
> * Adatok generált megtekintése

Az Azure Machine Learning modul, amely ebben az oktatóanyagban létrehozhat az eszköz állítja elő a környezeti adatokat olvas, és a feliratok rendellenes tevékenységként ismeri az üzeneteket, vagy nem. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure IoT peremhálózati eszköz, a gyors üzembe helyezés vagy első oktatóanyaga, amely létrehozta.
* Az IoT hub, amely az IoT-peremhálózati eszköz csatlakozik az IoT-központ kapcsolati karakterláncát.
* Egy Azure Machine Learning-fiók. Fiók létrehozásához kövesse a [létrehozása az Azure Machine Learning fiókok, és telepítse az Azure Machine Learning-munkaterület](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-services-accounts). Nem kell telepíteni a munkaterületet üzemeltető alkalmazás ehhez az oktatóanyaghoz. 
* Modul kezelése az Azure ML a számítógépen. Állítsa be a környezetet, és hozzon létre egy fiókot, kövesse az utasításokat a [modell kezelésének beállítása](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration).

## <a name="create-the-azure-ml-container"></a>Az Azure ML-tároló létrehozása
Ebben a szakaszban a betanított modell fájlok letöltéséhez, és alakíthatja át őket az Azure ML-tárolóba.  

Azon a számítógépen, amelyen a modul kezelése az Azure ML, töltse le és mentse [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) és [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) az az Azure ML IoT eszközkészlet a Githubon. Ezek a fájlok határozza meg azt a betanított gépi tanulási modellt az Iot-peremhálózati eszközön telepíteni. 

A betanított modell segítségével hozzon létre olyan tároló, amely IoT peremeszközök telepíthetők.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```
A szolgáltatásnév *machinelearningmodule* ebben a példában a docker-tároló kép neve lesz.

### <a name="view-the-container-repository"></a>A tároló tárház megtekintése

Ellenőrizze, hogy a tároló lemezkép sikeresen létrejött, de a machine learning környezet társított Azure-tárolót összetevőtárházat tárolja.

1. A a [Azure-portálon](https://portal.azure.com), és **minden szolgáltatás** válassza **tároló nyilvántartó**.
2. Válassza ki a beállításjegyzékben. A névnek kell kezdődnie **mlcr** és az erőforráscsoport, a helyre, és a modul kezelő használt előfizetés tartozik.
3. Válassza ki **hívóbetűk**
4. Másolás a **bejelentkezési kiszolgáló**, **felhasználónév**, és **jelszó**.  Szükség van ezekre a peremhálózati eszköz a beállításjegyzék eléréséhez.
5. Válassza ki **adattárak**
6. Válassza ki **machinelearningmodule**
7. Most már rendelkezik a teljes lemezképet a tároló elérési útját. Jegyezze fel a következő szakasz kép elérési út. Akkor kell kinéznie: **< registry_name >.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Adja hozzá a peremhálózati eszköz beállításjegyzék hitelesítő adatokat

A peremhálózati futásidejű a peremhálózati eszköz futtató számítógépen adja hozzá a rendszerleíró adatbázis hitelesítő adatait. Ez a parancs hozzáférést a futásidejű való lekérésére a tárolót.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>A megoldás futtatása

1. Az a [Azure-portálon](https://portal.azure.com), keresse meg az IoT hub.
1. Lépjen az **IoT Edge (előzetes verzió)** részhez, és válassza ki az IoT Edge-eszközt.
1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.
1. Ha korábban már telepítette a tempSensor modul az IoT-peremhálózati eszközön, akkor előfordulhat, hogy adatokkal való automatikus feltöltés. Ha még nem szerepel a modulok listáján, adja hozzá.
    1. Válassza ki **IoT peremhálózati modul hozzá lesz adva**.
    2. Az a **neve** mezőbe írja be `tempSensor`.
    3. Az a **lemezkép URI** mezőbe írja be `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Kattintson a **Mentés** gombra.
1. Adja hozzá a gépi tanulási modul, amely létrehozta.
    1. Válassza ki **IoT peremhálózati modul hozzá lesz adva**.
    1. Az a **neve** mezőbe írja be `machinelearningmodule`
    1. Az a **kép** mezőbe írja be a kép címét, például `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Kattintson a **Mentés** gombra.
1. Az **Add Modules** (Modulok hozzáadása) lépésben kattintson a **Next** (Tovább) gombra.
1. Az a **útvonalak megadása** . lépés:, másolja az alábbi JSON a szövegmezőbe. Az első útvonal szállításokkal a hőmérséklet-érzékelő üzeneteit a machine learning modulra "amlInput" végpont keresztül ez az a végpont, amelyek az összes Azure Machine Learning modulok használják. A második útvonal szállításokkal üzenetek IoT-központ a machine learning modulban. Ez az útvonal "amlOutput". a végpont, amelyek az összes Azure Machine Learning modulok kimeneti adatai, és "előtt$" jelöli az IoT-központ. 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. Kattintson a **Tovább** gombra. 
1. A **Sablon áttekintése** lépésben válassza a **Küldés** lehetőséget. 
1. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre.  Láthatja, hogy az új **machinelearningmodule** fut, valamint a **tempSensor** modul és a IoT peremhálózati futásidejű modult.

## <a name="view-generated-data"></a>Adatok generált megtekintése

Megtekintheti az eszközről a felhőbe üzeneteket, amelyek az IoT-peremhálózati eszköz használatával küld a [IoT-központ explorer](https://github.com/azure/iothub-explorer) vagy a Visual Studio Code Azure IoT eszközkészlet kiterjesztését. 

1. A Visual Studio Code, válassza ki **IoT Hub-eszközöknek**. 
2. Válassza ki **...**  válassza **IoT Hub kapcsolati karakterlánc beállítása** a menüből. 

   ![Az IoT Hub-eszközöknek további menü](./media/tutorial-deploy-machine-learning/set-connection.png)

3. A lap tetején megjelenik a szövegmezőben adjon meg az IoT Hub a iothubowner kapcsolati karakterláncot. Az IoT-peremhálózati eszköz meg kell jelennie az IoT Hub-eszközöknek listájában.
4. Válassza ki **...**  újra, majd válassza ki **D2C üzenet figyelni**.
5. Vizsgálja meg az üzeneteket, ötpercenként tempSensor érkező. Az üzenettörzs tartalmazza a tulajdonságot, **anomáliadetektálási** a machinelearningmodule biztosító igaz vagy hamis értékű. A **AzureMLResponse** tulajdonsága tartalmazza az "OK" értéket, ha a modell már sikeresen futott. 

   ![Az üzenet törzse Azure ML válasz](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Machine Learning technológiával IoT peremhálózati modul telepítette. Bármely más módszereket, amelyek segítségével Azure IoT peremhálózati tájékozódhat az adatok az üzleti elemzések készítése a peremhálózaton kapcsolja be az egyéb oktatóanyagok be tovább.

> [!div class="nextstepaction"]
> [Egy Azure-függvény modul telepítése](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
