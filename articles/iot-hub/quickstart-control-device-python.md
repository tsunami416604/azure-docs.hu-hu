---
title: Eszköz vezérlése Azure IoT Hubról – rövid útmutató (Python) | Microsoft Docs
description: Ebben a rövid útmutatóban két Python-mintaalkalmazást fog futtatni. Az egyik egy háttéralkalmazás, amely a hubhoz csatlakoztatott eszközök távoli vezérlését teszi lehetővé. A másik alkalmazás a hubhoz csatlakoztatott eszközt szimulál, amelyet távolról lehet irányítani.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/09/2020
ms.openlocfilehash: 23f9a88cd0accbf8716c706643e7b67f4ecaf05c
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110525"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

A IoT Hub egy olyan Azure-szolgáltatás, amely lehetővé teszi a IoT-eszközök Felhőbeli kezelését, valamint a felhőbe irányuló nagy mennyiségű eszköz telemetria történő tárolását és feldolgozását. Ebben a rövid útmutatóban egy *közvetlen metódussal* fogja vezérelni az IoT Hubhoz csatlakoztatott szimulált eszközt. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését.

Ez a rövid útmutató két előre megírt Python-alkalmazást használ:

* Egy szimulálteszköz-alkalmazás, amely válaszol a háttéralkalmazásokból meghívott közvetlen metódusokra. A közvetlen metódusok meghívásának fogadásához ez az alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik.

* Egy háttéralkalmazás, amely meghívja a közvetlen metódusokat a szimulált eszközre. A közvetlen metódus egy eszközre való meghívásához ez az alkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ha még nem tette meg, töltse le a Python-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip címről, és csomagolja ki a ZIP-archívumot.

A [Python 3,7-es vagy újabb verziója](https://www.python.org/downloads/) telepítve van a fejlesztői gépen. A Python egyéb verziói esetében lásd: az SDK dokumentációjának [Azure IoT-eszközök funkciói](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features) .

Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-python.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-python.md) című előző útmutatót, kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

    **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    **MyPythonDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyPythonDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

    **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

3. Szüksége van egy _szolgáltatáskapcsolati sztringre_ is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

    **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében. Ez a szolgáltatási kapcsolatok karakterlánca különbözik az előző lépésben feljegyzett eszköz-összekapcsolási karakterlánctól.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A szimulálteszköz-alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik, szimulált telemetriát küld, és figyeli a hubról érkező közvetlenmetódus-hívásokat. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz visszaigazolást küld a hubhoz a közvetlen metódus végrehajtása után.

1. Egy helyi terminálablakban keresse meg a Python-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device-2** mappába.

1. Nyissa meg a **SimulatedDevice.py** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a `CONNECTION_STRING` változó értékét a korábban jegyzett eszköz-összekapcsolási sztringre. Ezután mentse a módosításokat a **SimulatedDevice.py**.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A háttéralkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik. Az alkalmazás lehetővé teszi, hogy a közvetlen metódus hívásokat hajtson végre egy eszközön az IoT hub segítségével, és figyelje a nyugtákat. Az IoT Hub-háttéralkalmazások általában a felhőben futnak.

1. Egy másik helyi terminálablakban keresse meg a Python-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\back-end-application** mappába.

1. Nyissa meg a **BackEndApplication.py** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a `CONNECTION_STRING` változó értékét arra a szolgáltatás-összekapcsolási sztringre, amelyet korábban jegyzett készített. Ezután mentse a módosításokat a **BackEndApplication.py**.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás futtatásához:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Az alábbi képernyőfelvételen a kimenet látható, mivel az alkalmazás közvetlen metódust hív meg az eszköznek, és nyugtát kap:

    ![A háttéralkalmazás futtatása](./media/quickstart-control-device-python/backend-application.png)

    A háttéralkalmazás futtatása után megjelenik egy üzenet a szimulált eszközt futtató konzolablakban, és megváltozik az üzenetküldések gyakorisága:

    ![Változás a szimulált ügyfélben](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban közvetlen metódust hívott meg egy eszközre egy háttéralkalmazásból, és válaszolt a közvetlenmetódus-hívásra egy szimulálteszköz-alkalmazásban.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria irányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)