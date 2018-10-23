---
title: Eszköz vezérlése Azure IoT Hubról – rövid útmutató (Python) | Microsoft Docs
description: Ebben a rövid útmutatóban két Python-mintaalkalmazást fog futtatni. Az egyik egy háttéralkalmazás, amely a hubhoz csatlakoztatott eszközök távoli vezérlését teszi lehetővé. A másik alkalmazás a hubhoz csatlakoztatott eszközt szimulál, amelyet távolról lehet irányítani.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: 06ca8269fc41807dd6cb27bab22d10e45f025ee2
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363860"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését az IoT-eszközökről a felhőbe, valamint az eszközök kezelését a felhőből. Ebben a rövid útmutatóban egy *közvetlen metódussal* fogja vezérelni az IoT Hubhoz csatlakoztatott szimulált eszközt. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését.

Ez a rövid útmutató két előre megírt Python-alkalmazást használ:

* Egy szimulálteszköz-alkalmazás, amely válaszol a háttéralkalmazásokból meghívott közvetlen metódusokra. A közvetlen metódusok meghívásának fogadásához ez az alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik.
* Egy háttéralkalmazás, amely meghívja a közvetlen metódusokat a szimulált eszközre. A közvetlen metódus egy eszközre való meghívásához ez az alkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban futtatott két mintaalkalmazás a Python használatával készült. A fejlesztői gépen szükség lesz a Python 2.7.x vagy a 3.5.x verziójára.

A Pythont a [Python.org](https://www.python.org/downloads/) oldalról töltheti le többféle platformra.

A Python aktuális verzióját a következő parancsok egyikével ellenőrizheti a fejlesztői gépen:

```python
python --version
```

```python
python3 --version
```

Ha még nem tette meg, töltse le a Python-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip címről, és csomagolja ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-python.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-python.md) című előző útmutatót, kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához. 

    **YourIoTHubName**: A helyőrző helyére írja be az IoT Hubjához választott nevet.

    **MyPythonDevice**: Ezt a nevet kapja a regisztrált eszköz. A MyPythonDevice nevet használja a bemutatott módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

    **YourIoTHubName**: A helyőrző helyére írja be az IoT Hubjához választott nevet.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

1. Szüksége van egy _szolgáltatáskapcsolati sztringre_ is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

    **YourIoTHubName**: A helyőrző helyére írja be az IoT Hubjához választott nevet.

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name YourIoTHubName --output table
    ```

    Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében. A szolgáltatáskapcsolati sztring nem azonos az eszközkapcsolati sztringgel.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A szimulálteszköz-alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik, szimulált telemetriát küld, és figyeli a hubról érkező közvetlenmetódus-hívásokat. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz nyugtázást küld vissza a hubra a közvetlen metódus végrehajtása után.

1. Egy helyi terminálablakban keresse meg a Python-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device-2** mappába.

1. Nyissa meg a **SimulatedDevice.py** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a `CONNECTION_STRING` változó értékét az eszköz korábban lejegyzett kapcsolati sztringjére. Ezután mentse a **SimulatedDevice.py** fájl módosításait.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A háttéralkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik. Az alkalmazás közvetlen metódusokat hív meg egy eszközre az IoT Hubon keresztül, és figyeli a nyugtázásokat. Az IoT Hub-háttéralkalmazások általában a felhőben futnak.

1. Egy másik helyi terminálablakban keresse meg a Python-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\back-end-application** mappába.

1. Nyissa meg a **BackEndApplication.py** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le az `CONNECTION_STRING` változó értéket a szolgáltatás korábban lejegyzett kapcsolati sztringjére. Mentse a **BackEndApplication.py** fájl módosításait.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás futtatásához:

    ```cmd/sh
    python BackEndApplication.py
    ```

    A következő képernyőképen az a kimenet látható, amelyben az alkalmazás közvetlen metódust hív meg az eszközre, és megkapja a nyugtázást:

    ![A háttéralkalmazás futtatása](media/quickstart-control-device-python/BackEndApplication.png)

    A háttéralkalmazás futtatása után megjelenik egy üzenet a szimulált eszközt futtató konzolablakban, és megváltozik az üzenetküldések gyakorisága:

    ![Változás a szimulált ügyfélben](media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban közvetlen metódust hívott meg egy eszközre egy háttéralkalmazásból, és válaszolt a közvetlenmetódus-hívásra egy szimulálteszköz-alkalmazásban.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria irányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)