---
title: Kommunikáció a Node. js-ben lévő eszköz-alkalmazásokkal IoT Hub eszköz-adatfolyamok használatával (előzetes verzió) | Microsoft Docs
description: Ebben a rövid útmutatóban egy Node. js-alapú szolgáltatás-oldali alkalmazást fog futtatni, amely egy IoT eszközzel kommunikál egy eszköz-adatfolyamon keresztül.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: e85f2ea849aca9deeb92da7d7b2381d6c2b1b725
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802447"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Gyors útmutató: Kommunikáció a Node. js-ben lévő eszköz-alkalmazásokkal IoT Hub eszköz-adatfolyamok használatával (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

A Microsoft Azure IoT Hub jelenleg [előzetes verziójú szolgáltatásként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)támogatja az eszközök adatfolyamait.

[IoT hub az eszközökön elérhető streamek](./iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás-és eszköz-alkalmazások számára a biztonságos és tűzfalon alapuló kommunikációt. A nyilvános előzetes verzióban a Node. js SDK csak a szolgáltatási oldalon található eszköz-adatfolyamokat támogatja. Ennek eredményeképpen ez a rövid útmutató csak a szolgáltatási oldali alkalmazások futtatására vonatkozó utasításokat tartalmazza. A következő rövid útmutatók egyikéről kell futtatnia egy csatolt eszköz-előfizetést:

* [Kommunikáció a C eszköz alkalmazásaival IoT Hub eszköz streamen keresztül](./quickstart-device-streams-echo-c.md)

* [Kommunikáció az eszköz alkalmazásaival C# IoT hub eszköz streameken keresztül](./quickstart-device-streams-echo-csharp.md).

Az ebben a rövid útmutatóban található kiszolgálóoldali Node. js-alkalmazás a következő funkciókat nyújtja:

* Egy eszköz streamet hoz létre egy IoT-eszközhöz.

* Beolvassa a bemenetet a parancssorból, és elküldi az eszköz alkalmazásának, amely visszaadja a visszalépést.

A kód bemutatja egy eszköz adatfolyamának kezdeményezési folyamatát, valamint azt, hogy hogyan használható az adatküldés és az Adatfogadás.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az adatfolyamok előnézete jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:

*  **USA középső régiója**

*  **USA középső – EUAP**

A szolgáltatás-oldali alkalmazás ebben a rövid útmutatóban való futtatásához a fejlesztői gépen a Node. js v10. x. x vagy újabb verziójára van szükség.

A Node. js-t a [NodeJS.org](https://nodejs.org)több platformján is letöltheti.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ha még nem tette meg, töltse le a Node.js-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip címről, és csomagolja ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha végrehajtotta az előző [rövid útmutatót: Telemetria küldése az eszközről egy IoT-hubhoz](quickstart-send-telemetry-node.md), ezt a lépést kihagyhatja.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha végrehajtotta az előző [rövid útmutatót: Telemetria küldése az eszközről egy IoT-hubhoz](quickstart-send-telemetry-node.md), ezt a lépést kihagyhatja.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyDevice**: Ez a regisztrált eszköz nevét adja meg. Használja a MyDevice az ábrán látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Szüksége van egy *szolgáltatáskapcsolati sztringre* is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

    **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Jegyezze fel a visszaadott értéket, amely a következőképpen néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Kommunikáció az eszköz és a szolgáltatás között az eszköz streamen keresztül

Ebben a szakaszban az eszköz-és a kiszolgálóoldali alkalmazást is futtatja, és kommunikál a kettő között.

### <a name="run-the-device-side-application"></a>Az eszköz oldali alkalmazás futtatása

Ahogy korábban említettük, IoT Hub Node. js SDK csak a szolgáltatás oldalán lévő eszközökön futó adatfolyamokat támogatja. Az eszközökön elérhető alkalmazások esetében használja az alábbi rövid útmutatók egyikét:

   * [Kommunikáció a C eszköz alkalmazásaival IoT Hub eszköz streamen keresztül](./quickstart-device-streams-echo-c.md)

   * [Kommunikáció az eszköz alkalmazásaival C# IoT hub eszköz streamen keresztül](./quickstart-device-streams-echo-csharp.md)

Mielőtt továbblép a következő lépésre, győződjön meg arról, hogy az eszközön futó alkalmazás fut.

### <a name="run-the-service-side-application"></a>A szolgáltatás-oldali alkalmazás futtatása

Feltéve, hogy az eszközön futó alkalmazás fut, kövesse az alábbi lépéseket a kiszolgálóoldali alkalmazás a Node. js-ben való futtatásához:

* Adja meg a szolgáltatás hitelesítő adatait és az eszköz AZONOSÍTÓját környezeti változókként.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   Váltson `MyDevice` át az eszközhöz kiválasztott eszköz-azonosítóra.

* `Quickstarts/device-streams-service` Navigáljon a kibontott projekt mappájába, és futtassa a mintát a csomópont használatával.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Az utolsó lépés végén a szolgáltatási oldali program elindít egy streamet az eszközön, és a létrehozás után karakterlánc-puffert küld a szolgáltatásnak az adatfolyamon keresztül. Ebben a példában a szolgáltatás-oldali program egyszerűen beolvassa `stdin` a terminálon, és elküldi azt az eszköznek, amely ezt követően visszhangot ad vissza. Ez a két alkalmazás közötti sikeres kétirányú kommunikációt mutatja be.

![Szolgáltatás oldali konzol kimenete](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Ezután leállíthatja a programot az ENTER billentyű lenyomásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT hub-t állított be, regisztrált egy eszközt, létrehozott egy eszközt az alkalmazások között az eszközön és a szolgáltatás oldalán, és a stream használatával visszaküldheti az adatátvitelt az alkalmazások között.

Az alábbi hivatkozásokat követve további információkat tudhat meg az eszközök streamekről:

> [!div class="nextstepaction"]
> [Az eszközök adatfolyamának áttekintése](./iot-hub-device-streams-overview.md) 