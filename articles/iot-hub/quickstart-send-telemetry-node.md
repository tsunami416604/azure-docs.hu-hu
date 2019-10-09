---
title: 'Gyors útmutató: Telemetria küldése az Azure IoT (node. js)'
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
ms.openlocfilehash: 92d6af41e55429f1b788de68940bc9b033c51ad6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167034"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Gyors útmutató: Telemetria küldése egy eszközről egy IoT-hubhoz, és olvasása háttérbeli alkalmazással (node. js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

Ez a cikk két előre megírt Node.js-alkalmazást használ a telemetria küldésére: egyet a telemetria elküldésére, egyet pedig a telemetria olvasásához a hubról. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az ebben a rövid útmutatóban futtatott két minta alkalmazás a Node. js-ben van megírva. A fejlesztői gépen a Node. js v10. x. x vagy újabb verziója szükséges.

A Node.js-t a [nodejs.org](https://nodejs.org) oldalról töltheti le többféle platformra.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Töltse le a Node.js-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyNodeDevice**: A regisztrálni kívánt eszköz neve. Javasoljuk, hogy a **MyNodeDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni.

1. Szüksége van egy _szolgáltatáskapcsolati sztringre_ is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

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

    Cserélje le a `connectionString` változó értékét a szolgáltatás-összekapcsolási sztringre, amelyet korábban jegyzett készített. Ezután mentse a módosításokat a **ReadDeviceToCloudMessages. js**fájlba.

1. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás futtatásához:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    A következő képernyőképen az a kimenet látható, amikor a háttéralkalmazás fogadja a szimulálteszköz-alkalmazás által az IoT Hubhoz küldött telemetriát:

    ![A háttéralkalmazás futtatása](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, szimulált telemetria elküldve a hubhoz egy Node. js-alkalmazás használatával, valamint egy egyszerű háttérbeli alkalmazás használatával olvassa be a telemetria.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT hub-hoz csatlakoztatott eszköz vezérlése @ no__t-0
