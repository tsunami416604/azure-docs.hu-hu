---
title: Rövid útmutató – Telemetria küldése az Azure IoT Hubra (Python) | Microsoft Docs
description: Ebben a rövid útmutatóban egy Python-mintaalkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez, és egy segédprogrammal telemetriát olvas az IoT Hubról.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/07/2018
ms.author: dobett
ms.openlocfilehash: 2d851bc8d5af7f824512cc9f14e6b1120026dd07
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785155"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-python"></a>Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra, és a telemetria olvasása a háttéralkalmazással (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

A gyors útmutató egy előre megírt Python-alkalmazást használ a telemetria küldésére, és egy parancssori felületi segédprogrammal olvassa be a telemetriát a hubról. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban futtatott két mintaalkalmazás a Python használatával készült. A fejlesztői gépen szükség lesz a Python 2.7.x vagy a 3.5.x verziójára.

A Pythont a [Python.org](https://www.python.org/downloads/) oldalról töltheti le többféle platformra. Válassza az Ön által használt rendszer architektúrájának megfelelő Python-telepítőt. Amennyiben a rendszer CPU-architektúrája 32 bites, az alapértelmezett x86-os telepítőt töltse le a Python.org oldalról, 64 bites architektúra esetén pedig az x86-64 jelzésű telepítőt.

A Python aktuális verzióját a következő parancsok egyikével ellenőrizheti a fejlesztői gépen:

```python
python --version
```

```python
python3 --version
```

Töltse le a Python-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure CLI használatával regisztrál egy szimulált eszközt.

1. Adja hozzá az IoT Hub CLI-bővítményt, és hozza létre az eszközidentitást. A `{YourIoTHubName}` helyére írja be az IoT Hub Ön által választott nevét:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

    Ha úgy dönt, hogy eszközének egy másik nevet választ, a mintaalkalmazás futtatása előtt frissítse az eszköznevet benne.

1. Futtassa az alábbi parancsot az imént regisztrált eszköz _kapcsolati sztringjének_ lekéréséhez:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki: `Hostname=...=`. Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Egy terminálablakban keresse meg a Python-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.py** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a `CONNECTION_STRING` változó értékét az eszköz korábban lejegyzett kapcsolati sztringjére. Ezután mentse a **SimulatedDevice.py** fájl módosításait.

1. Futtassa az alábbi parancsokat a terminálablakban a szimulálteszköz-alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Futtassa az alábbi parancsokat a terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-python/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Az IoT Hub CLI-bővítmény csatlakozhat a szolgáltatásoldali **Események** végponthoz az IoT Hubon. A bővítmény fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

Futtassa a következő Azure CLI-parancsokat úgy, hogy a `{YourIoTHubName}` helyére az IoT Hub neve kerüljön:

```azurecli-interactive
az iot hub monitor-events --device-id MyPythonDevice --hub-name {YourIoTHubName}
```

A következő képernyőképen az a kimenet látható, amikor a bővítmény fogadja a szimulált eszköz által az IoT Hubnak küldött telemetriát:

![A háttéralkalmazás futtatása](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a Hubra egy Python-alkalmazással, és beolvasta a telemetriát a Hubról egy egyszerű háttéralkalmazással.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-python.md)