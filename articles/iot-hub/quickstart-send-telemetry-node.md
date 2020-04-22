---
title: 'Rövid útmutató: Telemetriai adatok küldése az Azure IoT-nek (Node.js)'
description: Ebben a rövid útmutatóban két Node.js-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria olvasásához az IoT Hubról a felhőben történő feldolgozás érdekében.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom:
- mvc
- seo-javascript-september2019
- mqtt
ms.date: 06/21/2019
ms.openlocfilehash: 24b6d2eca2eaa12e3e04647d403a015bdbf24ec6
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770030"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Rövid útmutató: Telemetriai adatok küldése egy eszközről egy IoT-központba, és olvassa el egy háttéralkalmazással (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 Ebben a rövid útmutatóban telemetriai adatokat küld egy szimulált eszközalkalmazásból az Azure IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató két előre megírt Node.js alkalmazást használ: az egyik a telemetriai adatok, a másik pedig a telemetriai adatok a hubról történő olvasásához. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org). Ha az Azure Cloud Shellt használja, ne frissítse a Node.js telepített verzióját. Az Azure Cloud Shell már rendelkezik a legújabb Node.js verzióval.

* [Egy minta Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip).

* A 8883-as port megnyílik a tűzfalon. A rövid útmutatóban szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

Futtassa a következő parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a Cloud Shell-példányhoz. Az IoT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS) adott parancsokat az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

   **MyNodeDevice**: Ez a regisztráló eszköz neve. Javasoljuk, hogy használja **a MyNodeDevice-t** az ábrán látható módon. Ha más nevet választ az eszközhöz, akkor ezt a nevet is használnia kell a cikkben, és frissítenie kell az eszköz nevét a mintaalkalmazásokban, mielőtt futtatna őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Futtassa a következő parancsot az Azure Cloud Shellben az _eszköz kapcsolati karakterláncának_ lekérni az imént regisztrált eszközhöz:

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később fogja használni a rövid útmutatóban.

1. Szüksége van egy _szolgáltatáskapcsolati sztringre_ is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --policy-name service --output table
    ```

    Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később fogja használni a rövid útmutatóban. Ez a szolgáltatáskapcsolati karakterlánc eltér az előző lépésben megjegyzett eszközkapcsolati karakterlánctól.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Nyissa meg a helyi terminálablakot, és keresse meg a Node.js-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

1. Nyissa meg a **SimulatedDevice.js** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a `connectionString` változó értékét arra az eszközkapcsolati karakterláncra, amelyről korábban feljegyzést készített. Ezután mentse a módosításokat **SimulatedDevice.js**.

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

    Cserélje le a `connectionString` változó értékét arra a szolgáltatáskapcsolati karakterláncra, amelyről korábban feljegyzést készített. Ezután mentse el a módosításokat **ReadDeviceToCloudMessages.js**.

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

Ebben a rövid útmutatóban egy IoT hubot hoz létre, regisztrált egy eszközt, szimulált telemetriai adatokat küldött a hubnak egy Node.js alkalmazás használatával, és egy egyszerű háttéralkalmazás használatával elolvassa a telemetriai adatokat a hubról.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-node.md)
