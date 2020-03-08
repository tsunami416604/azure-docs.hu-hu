---
title: 'Gyors útmutató: telemetria küldése az Azure IoT (node. js)'
description: Ebben a rövid útmutatóban két Node.js-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria olvasásához az IoT Hubról a felhőben történő feldolgozás érdekében.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 8b99cf08e0e47ab99deb443a0fefee6ba0d61a88
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675390"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Gyors útmutató: telemetria küldése egy eszközről egy IoT-hubhoz, és olvasása háttérbeli alkalmazással (node. js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 Ebben a rövid útmutatóban egy szimulált eszközről küld telemetria egy Azure IoT Hubon keresztül a feldolgozásra szolgáló háttérbeli alkalmazáshoz. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató két előre megírt Node. js-alkalmazást használ: az egyiket a telemetria elküldéséhez, és az egyiket, hogy beolvassa a telemetria a központból. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node. js 10 +](https://nodejs.org). Ha a Azure Cloud Shell használja, ne frissítse a Node. js telepített verzióját. A Azure Cloud Shell már rendelkezik a Node. js legújabb verziójával.

* [Egy példa Node. js-projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* A 8883-es port megnyitható a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IoT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyNodeDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyNodeDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni.

1. Szüksége van egy _szolgáltatáskapcsolati sztringre_ is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --policy-name service --output table
    ```

    Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni. Ez a szolgáltatási kapcsolatok karakterlánca különbözik az előző lépésben feljegyzett eszköz-összekapcsolási karakterlánctól.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Nyissa meg a helyi terminálablakot, és keresse meg a Node.js-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.js** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a `connectionString` változó értékét a korábban jegyzett eszköz-összekapcsolási sztringre. Ezután mentse a módosításokat a **SimulatedDevice. js**fájlba.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

A háttéralkalmazás a szolgáltatásoldali **Események** végponthoz csatlakozik az IoT Hubon. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

1. Nyisson meg a másik helyi terminálablakot, és keresse meg a Node.js-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\read-d2c-messages** mappába.

1. Nyissa meg a **ReadDeviceToCloudMessages.js** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a `connectionString` változó értékét arra a szolgáltatás-összekapcsolási sztringre, amelyet korábban jegyzett készített. Ezután mentse a módosításokat a **ReadDeviceToCloudMessages. js**fájlba.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás futtatásához:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    A következő képernyőképen az a kimenet látható, amikor a háttéralkalmazás fogadja a szimulálteszköz-alkalmazás által az IoT Hubhoz küldött telemetriát:

    ![A háttéralkalmazás futtatása](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, szimulált telemetria elküldve a hubhoz egy Node. js-alkalmazás használatával, valamint egy egyszerű háttérbeli alkalmazás használatával olvassa be a telemetria.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-node.md)
