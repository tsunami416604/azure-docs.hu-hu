---
title: Kommunikáció az eszközalkalmazással a Node.js alkalmazásban az Azure IoT Hub-eszközadatfolyamokkal
description: Ebben a rövid útmutatóban egy Node.js szolgáltatásoldali alkalmazást fog futtatni, amely egy iot-eszközzel kommunikál egy eszközadatfolyamon keresztül.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 0757c5eb8639e4a864b049adc92c97a7cf69adba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675511"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Rövid útmutató: Kommunikáció egy eszközalkalmazással a Node.js-ben az IoT Hub-eszközadatfolyamokon keresztül (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Ebben a rövid útmutatóban egy szolgáltatásoldali alkalmazást futtat, és eszközadatfolyamok használatával beállítja az eszköz és a szolgáltatás közötti kommunikációt. Az Azure IoT Hub eszközstreamek lehetővé teszik a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát kommunikációját. A nyilvános előzetes verzió során a Node.js SDK csak a szolgáltatás oldalon támogatja az eszközstreameket. Ennek eredményeképpen ez a rövid útmutató csak a szolgáltatásoldali alkalmazás futtatásához adott utasításokat tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

* A [C-ben lévő eszközalkalmazásokkal való kommunikáció befejezése az IoT Hub-eszközadatfolyamokon keresztül,](./quickstart-device-streams-echo-c.md) vagy [kommunikáció a C# eszközalkalmazásokkal az IoT Hub-eszközadatfolyamokon keresztül.](./quickstart-device-streams-echo-csharp.md)

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

* [Egy minta Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

A Microsoft Azure IoT Hub jelenleg támogatja az eszközstreamelést [előnézeti funkcióként.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!IMPORTANT]
> Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT Hubok számára támogatott:
>
> * USA középső régiója
> * USA középső régiója
> * Észak-Európa
> * Délkelet-Ázsia

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

Futtassa a következő parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a Cloud Shell-példányhoz. Az IoT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS) parancsait az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md) című előző útmutatót, kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

   **MyDevice**: Ez a regisztráló eszköz neve. Javasoljuk, hogy használja a **MyDevice-t az** ábrán látható módon. Ha más nevet választ az eszközhöz, akkor ezt a nevet is használnia kell a cikkben, és frissítenie kell az eszköz nevét a mintaalkalmazásokban, mielőtt futtatna őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Szüksége van egy *szolgáltatáskapcsolati sztringre* is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

    **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Vegye figyelembe a visszaadott szolgáltatás kapcsolati karakterláncot későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunikáció az eszköz és a szolgáltatás között az eszközadatfolyamokon keresztül

Ebben a szakaszban futtatja az eszközoldali alkalmazást és a szolgáltatásoldali alkalmazást, és kommunikál a kettő között.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Ahogy korábban említettük, az IoT Hub Node.js SDK csak a szolgáltatás oldalon támogatja az eszközstreameléseket. Eszközoldali alkalmazások esetén használja az alábbi rövid útmutatókban elérhető, kapcsolódó eszközprogramok egyikét:

* [Kommunikáció a C-ben lévő eszközalkalmazásokkal az IoT Hub-eszközadatfolyamokon keresztül](./quickstart-device-streams-echo-c.md)

* [Kommunikáció c# eszközalkalmazásokkal az IoT Hub-eszközadatfolyamokon keresztül](./quickstart-device-streams-echo-csharp.md)

A következő lépés előtt győződjön meg arról, hogy az eszközoldali alkalmazás fut.

### <a name="run-the-service-side-application"></a>A szolgáltatásoldali alkalmazás futtatása

A szolgáltatásoldali Node.js alkalmazás ebben a rövid útmutatóban a következő funkciókkal rendelkezik:

* Létrehoz egy eszközadatfolyamot egy IoT-eszközre.
* Beolvassa a bemenetet a parancssorból, és elküldi az eszközalkalmazásnak, amely visszafogja a bemenetet.

A kód bemutatja az eszközadatfolyam kezdeményezési folyamatát, valamint azt, hogy hogyan kell azt adatok küldésére és fogadására használni.

Feltételezve, hogy az eszközoldali alkalmazás fut, kövesse az alábbi lépéseket a helyi terminálablakban a szolgáltatásoldali alkalmazás Node.js-ben való futtatásához:

* Adja meg a szolgáltatás hitelesítő adatait és az eszközazonosítót környezeti változókként.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Módosítsa a ServiceConnectionString helyőrzőt úgy, hogy megfeleljen a szolgáltatás kapcsolati karakterláncának, és a **MyDevice-t,** hogy megfeleljen az eszközazonosítójának, ha a sajátjának más nevet adott meg.

* Keresse `Quickstarts/device-streams-service` meg a kicsomagolt projektmappában, és futtassa a mintát a csomópont segítségével.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Az utolsó lépés végén a szolgáltatásoldali program adatfolyamot kezdeményez az eszközre, és a létrehozás után egy karakterlánc-puffert küld a szolgáltatásnak az adatfolyamon keresztül. Ebben a mintában a szolgáltatásoldali `stdin` program egyszerűen beolvassa a terminálon lévőt, és elküldi azt az eszköznek, amely ezután visszafogja. Ez bizonyítja a két alkalmazás közötti sikeres kétirányú kommunikációt.

![Szolgáltatásoldali konzol kimenete](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Ezután a programot az Enter billentyű ismételt megnyomásával szakíthatja meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállít egy IoT-központot, regisztrált egy eszközt, létrehozott egy eszközadatfolyamot az eszközök és a szolgáltatás oldalán lévő alkalmazások között, és az adatfolyamot használta az adatok oda-vissza küldésére az alkalmazások között.

Az alábbi hivatkozásokra kattintva többet is megtudhat az eszközstreamekről:

> [!div class="nextstepaction"]
> [Eszközadatfolyamok – áttekintés](./iot-hub-device-streams-overview.md) 