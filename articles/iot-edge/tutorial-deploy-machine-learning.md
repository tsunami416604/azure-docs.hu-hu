---
title: Az Azure Machine Learning üzembe helyezése az Azure IoT Edge használatával | Microsoft Docs
description: Az Azure Machine Learning üzembe helyezése modulként Edge-eszközre
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 248bc97c214c013d10f1839201ce2f572cb854ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631173"
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Az Azure Machine Learning üzembe helyezése IoT Edge-modulként – előzetes verzió

Az IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti egy olyan Azure Machine Learning-modul üzembe helyezésén, amely előrejelzi az eszközök meghibásodását az [Azure IoT Edge üzembe helyezése szimulált eszközön Windows][lnk-tutorial1-win] vagy [Linux][lnk-tutorial1-lin] rendszeren című oktatóanyagban létrehozott szimulált IoT Edge-eszközök érzékelőadatai alapján.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Machine Learning-modul létrehozása
> * Modultároló leküldése egy Azure-beli tárolóregisztrációs adatbázisba
> * Azure Machine Learning-modul üzembe helyezése az IoT Edge-eszközön
> * A létrejött adatok megtekintése

Az ebben az oktatóanyagban létrehozott Azure Machine Learning-modul kiolvassa az eszköz által előállított környezeti adatokat, illetve rendellenesként vagy nem rendellenesként jelöli meg az üzeneteket.

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutatóban vagy az első oktatóanyagban létrehozott Azure IoT Edge-eszköz.
* Az IoT Hubhoz tartozó IoT Hub kapcsolati sztring, amelyhez az IoT Edge-eszköz csatlakozik.
* Egy Azure Machine Learning-fiók. Fiók létrehozásához kövesse az [Azure Machine Learning-fiókok létrehozása és az Azure Machine Learning Workbench telepítése](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts) szakaszban megadott utasításokat. Az oktatóanyag elvégzéséhez nincs szükség a Workbench alkalmazás telepítésére. 
* Az Azure ML moduljainak kezelése a gépén. A környezet beállításához és fiók létrehozásához kövesse [A modellkezelés beállítása](../machine-learning/desktop-workbench/deployment-setup-configuration.md) szakaszban megadott utasításokat.

Az Azure Machine Learning-modul nem támogatja az ARM processzorokat.

## <a name="create-the-azure-ml-container"></a>Azure ML-tároló létrehozása
Ebben a szakaszban letölti a betanított modell fájljait, és Azure ML-tárolóvá konvertálja azokat.

Az Azure ML modulkezelési alkalmazását futtató számítógépen töltse le és mentse a GitHub Azure ML IoT-eszközkészletéből származó [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) és a [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) fájlokat. Ezek a fájlok határozzák meg azt a betanított Machine Learning-modellt, amelyet az IoT Edge-eszközön üzembe fog helyezni.

A betanított modell használatával hozzon létre egy, az IoT Edge-eszközökön üzembe helyezhető tárolót. Az alábbi paranccsal végezze el a következőket:

   * Regisztrálja a modellt.
   * Hozzon létre egy jegyzéket.
   * Hozzon létre egy Docker-tárolórendszerképet *machinelearningmodule* néven.
   * Helyezze üzembe a rendszerképet az Azure Kubernetes Service- (AKS-) fürtön.

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>A tárolóadattár megtekintése

Ellenőrizze, hogy a tárolórendszerkép sikeresen létrejött-e és a Machine Learning-környezethez tartozó Azure-tárolóadattárba lett-e mentve.

1. Az [Azure Portalon](https://portal.azure.com) lépjen **Az összes szolgáltatás** elemre, és válassza a **Tárolóregisztrációs adatbázisok** lehetőséget.
2. Válassza ki a saját beállításjegyzékét. A névnek az **mlcr** karakterekkel kell kezdődnie, továbbá a modulkezelési alkalmazás beállításakor megadott erőforráscsoporthoz, helyhez és előfizetéshez kell tartoznia.
3. Válassza a **Hozzáférési kulcsok** elemet
4. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** mezők értékeit.  Ezek az adatok ahhoz kellenek, hogy az Edge-eszközeiről is hozzá tudjon férni a beállításjegyzékhez.
5. Válassza az **Adattárak** elemet
6. Válassza a **machinelearningmodule** elemet
7. Most már rendelkezésére áll a tároló rendszerképének teljes elérési útja. Jegyezze fel a rendszerkép elérési útját, mert szüksége lesz rá a következő szakaszban. Az elérési útnak a következőképpen kell kinéznie: **<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>Beállításjegyzékhez tartozó hitelesítő adatok hozzáadása az Edge-eszközhöz

Adja hozzá az Edge-futtatókörnyezethez a beállításjegyzék hitelesítő adatait azon a számítógépen, amelyen az Edge-eszközt futtatja. Ez a parancs hozzáférést nyújt a futtatókörnyezetnek a tároló lekéréséhez.

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password>
   ```

## <a name="run-the-solution"></a>A megoldás futtatása

1. Az [Azure Portalon](https://portal.azure.com) keresse meg az IoT-központot.
1. Lépjen az **IoT Edge (előzetes verzió)** részhez, és válassza ki az IoT Edge-eszközt.
1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.
1. Ha már üzembe helyezte a tempSensor modult az IoT Edge-eszközön, akkor lehet, hogy automatikusan ki van töltve. Ha a modul még nem szerepel a listában, akkor vegye fel.
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    2. A **Név** mezőbe írja a következőt: `tempSensor`.
    3. A **Rendszerkép URI** mezőbe írja be a következőt: `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Kattintson a **Mentés** gombra.
1. Adja hozzá az Ön által létrehozott Machine Learning-modult.
    1. Válassza az **IoT Edge-modul hozzáadása** lehetőséget.
    1. A **Név** mezőbe írja a következőt: `machinelearningmodule`
    1. A **Rendszerkép** mezőbe írja be a rendszerkép címét, például a következőt: `<registry_name>.azurecr.io/machinelearningmodule:1`.
    1. Kattintson a **Mentés** gombra.
1. Az **Add Modules** (Modulok hozzáadása) lépésben kattintson a **Next** (Tovább) gombra.
1. Az **Útvonalak megadása** lépésben másolja az alábbi JSON-t a szövegmezőbe. Az első útvonal a hőmérséklet-érzékelőből a Machine Learning-modulba szállítja az üzeneteket az összes Azure Machine Learning-modul által használt „amlInput” végponton keresztül. A második útvonal a Machine Learning-modulból az IoT Hubra szállítja az üzeneteket. Ebben az útvonalban az „amlOutput” az a végpont, amelyet az összes Azure Machine Learning-modul használ az adatok kimenetének küldéséhez, az „$upstream” pedig az IoT Hubot jelöli.

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
1. Térjen vissza az eszköz részleteit tartalmazó oldalra, és kattintson a **Frissítés** elemre.  Látható, hogy az új **machinelearningmodule** modul fut a **tempSensor** modullal és az IoT Edge-futtatókörnyezeti modulokkal együtt.

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Az IoT Edge-eszköz által az eszközről a felhőbe küldött üzeneteket az [IoT Hub Explorer](https://github.com/azure/iothub-explorer) eszközzel vagy a Visual Studio Code Azure IoT-eszközkészlet bővítményével tekintheti meg.

1. A Visual Studio Code-ban válassza az **IoT Hub-eszközök** elemet.
2. Ezt követően válassza a **...**, majd az **IoT Hub kapcsolati sztring beállítása** lehetőséget a menüben.

   ![IoT Hub-eszközök – Továbbiak menü](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Az oldal tetején megnyíló szövegmezőben adja meg a saját IoT Hubjához tartozó iothubowner kapcsolati sztringet. IoT Edge-eszközének ekkor meg kell jelennie az IoT Hub-eszközök listájában.
4. Ezt követően ismét válassza a **...**, majd a **D2C üzenet monitorozásának megkezdése** lehetőséget.
5. Figyelje meg a tempSensor felől öt másodpercenként érkező üzeneteket. Az üzenettörzs tartalmaz egy **anomaly** nevű tulajdonságot, amelyhez a machinelearningmodule igaz vagy hamis értéket rendel. Az **AzureMLResponse** tulajdonság „OK” értéket tartalmaz, ha a modell sikeresen lefutott.

   ![Azure ML-válasz az üzenettörzsben](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy, az Azure Machine Learning által működtetett IoT Edge-modult. Továbbléphet bármely másik oktatóanyagra, és megtudhatja, milyen más módokon alakíthatja jelentőséggel bíró üzleti információkká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Azure-függvény üzembe helyezése modulként](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
