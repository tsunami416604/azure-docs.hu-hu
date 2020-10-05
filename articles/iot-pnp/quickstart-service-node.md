---
title: Az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play-eszköz (Node.js) használata | Microsoft Docs
description: A Node.js használatával csatlakozhat az Azure IoT-megoldáshoz csatlakoztatott IoT-Plug and Play-eszközhöz, és együttműködhet velük.
author: elhorton
ms.author: elhorton
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 6ad6e48642e7b7df4b93b37b5ef66381833d8bbc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91574993"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-nodejs"></a>Gyors útmutató: interakció a megoldáshoz csatlakoztatott IoT Plug and Play eszközzel (Node.js)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

A IoT Plug and Play leegyszerűsíti a IoT azáltal, hogy az eszköz képességeinek ismerete nélkül teszi lehetővé az eszközök képességeit. Ez a rövid útmutató azt ismerteti, hogyan használható a Node.js a megoldáshoz csatlakoztatott IoT Plug and Play eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

A rövid útmutató elvégzéséhez Node.jsra van szükség a fejlesztői gépen. A [NodeJS.org](https://nodejs.org)több platformon is letöltheti a legújabb ajánlott verziót.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Az SDK-tárház klónozása a mintakód használatával

A minták klónozása a [Node SDK-tárházból](https://github.com/Azure/azure-iot-sdk-node). Nyisson meg egy terminál-ablakot tetszőleges mappában. Futtassa a következő parancsot a Node.jsGitHub-adattárhoz [tartozó Microsoft Azure IOT SDK ](https://github.com/Azure/azure-iot-sdk-node) klónozásához:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)információit.

Ebben a rövid útmutatóban a IoT Plug and Play eszközként Node.jsban írt minta termosztát-eszközt használhat. A minta eszköz futtatása:

1. Nyisson meg egy terminál-ablakot, és navigáljon ahhoz a helyi mappához, amely a GitHubról klónozott Node.js adattárhoz tartozó Microsoft Azure IoT SDK-t tartalmazza.

1. A rendszer ezt a terminált használja az **eszköz** -terminálként. Lépjen a klónozott adattár mappájába, és lépjen a */Azure-IOT-SDK-Node/Device/Samples/PnP* mappára. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

1. Futtassa a minta termosztátos eszközt a következő paranccsal:

    ```cmd/sh
    node simple_thermostat.js
    ```

1. Üzenet jelenik meg, amely közli, hogy az eszköz elküldött néhány információt, és online jelentett. Ezek az üzenetek azt jelzik, hogy az eszköz megkezdte a telemetria-adatok küldését a központba, és készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárjuk be ezt a terminált, hogy erősítse meg, hogy a szolgáltatási minta működik-e.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

A [környezet beállítása a IoT Plug and Play rövid útmutatók és oktatóanyagok](set-up-environment.md) létrehozott két környezeti változót a minta konfigurálásához a IoT hub és az eszközhöz való kapcsolódáshoz:

* **IOTHUB_CONNECTION_STRING**: a IoT hub-kapcsolatok karakterlánca korábban már jegyzett készített.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Ebben a rövid útmutatóban egy minta IoT megoldást használ a Node.jsban, hogy együttműködjön az imént beállított eszközzel.

1. Nyisson meg egy másik Terminálablak **szolgáltatást a szolgáltatás** -terminálként való használatra.

1. A klónozott Node SDK-adattárban navigáljon a */Azure-IOT-SDK-Node/Service/Samples/JavaScript* mappára. Telepítse az összes függőséget a következő parancs futtatásával:

    ```cmd/sh
    npm install
    ```

### <a name="read-a-property"></a>Tulajdonság beolvasása

1. Amikor futtatta a minta termosztátot az **eszköz** -terminálon, a következő üzenetek láthatók az online állapotáról:

    ```cmd/sh
    properties have been reported for component
    sending telemetry message 0...
    ```

1. Nyissa meg a **szolgáltatás** terminálját, és a következő paranccsal futtassa az eszköz adatainak olvasásához használt mintát:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. A **szolgáltatás** -terminál kimenetében figyelje meg a digitális iker válaszát. Megjelenik az eszköz modell-azonosítója és a hozzá tartozó tulajdonságok jelentés:

    ```json
    "$dtId": "mySimpleThermostat",
    "serialNumber": "123abc",
    "maxTempSinceLastReboot": 51.96167432818655,
    "$metadata": {
      "$model": "dtmi:com:example:Thermostat;1",
      "serialNumber": { "lastUpdateTime": "2020-07-09T14:04:00.6845182Z" },
      "maxTempSinceLastReboot": { "lastUpdateTime": "2020-07-09T14:04:00.6845182" }
    }
    ```

1. A következő kódrészlet a *get_digital_twin.js* kódot mutatja be, amely az eszköz Twin modell-azonosítóját kérdezi le:

    ```javascript
    console.log("Model Id: " + inspect(digitalTwin.$metadata.$model))
    ```

Ebben az esetben a kimenete `Model Id: dtmi:com:example:Thermostat;1` .

### <a name="update-a-writable-property"></a>Írható tulajdonság frissítése

1. Nyissa meg a *update_digital_twin.js* fájlt a kódszerkesztő programban.

1. Tekintse át a mintakód. Megtudhatja, hogyan hozhat létre JSON-javítást az eszköz digitális Twin-fájljának frissítéséhez. Ebben a példában a kód a termosztát hőmérsékletét a 42 értékkel helyettesíti:

    ```javascript
    const patch = [{
        op: 'add',
        path: '/targetTemperature',
        value: '42'
      }]
    ```

1. A **szolgáltatás** -terminálon futtassa a következő parancsot a minta futtatásához a tulajdonság frissítéséhez:

    ```cmd/sh
    node update_digital_twin.js
    ```

1. Az **eszköz** -terminálon láthatja, hogy az eszköz a következő frissítést kapta:

    ```cmd/sh
    The following properties will be updated for the default component:
    {
      targetTemperature: {
        value: 42,
        ac: 200,
        ad: 'Successfully executed patch for targetTemperature',
        av: 2
      }
    }
    updated the property
    Properties have been reported for component
    ```

1. A **szolgáltatás** -terminálban futtassa a következő parancsot a tulajdonság frissítésének megerősítéséhez:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. A **szolgáltatás** -terminál kimenetében, az összetevő alatt a digitális dupla válaszban a `thermostat1` frissített célként jelzett hőmérséklet látható. Eltarthat egy ideig, amíg az eszköz befejezi a frissítést. Ismételje meg ezt a lépést, amíg az eszköz fel nem dolgozza a tulajdonság frissítését:

    ```json
    targetTemperature: 42,
    ```

### <a name="invoke-a-command"></a>Parancs meghívása

1. Nyissa meg *invoke_command.js* fájlt, és tekintse át a kódot.

1. Nyissa meg a **szolgáltatás** terminálját. A következő parancs használatával futtassa a mintát a parancs meghívásához:

    ```cmd/sh
    set IOTHUB_COMMAND_NAME=getMaxMinReport
    set IOTHUB_COMMAND_PAYLOAD=commandpayload
    node invoke_command.js
    ```

1. A **szolgáltatás** -terminál kimenete a következő megerősítést jeleníti meg:

    ```cmd/sh
    {
        xMsCommandStatuscode: 200,  
        xMsRequestId: 'ee9dd3d7-4405-4983-8cee-48b4801fdce2',  
        connection: 'close',  'content-length': '18',  
        'content-type': 'application/json; charset=utf-8',  
        date: 'Thu, 09 Jul 2020 15:05:14 GMT',  
        server: 'Microsoft-HTTPAPI/2.0',  vary: 'Origin',  
        body: 'min/max response'
    }
    ```

1. Az **eszköz** -terminálon láthatja, hogy a parancs meg van-e ismerve:

    ```cmd/sh
    MaxMinReport commandpayload
    Response to method 'getMaxMinReport' sent successfully.
    ```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play modellezési fejlesztői útmutató](concepts-developer-guide-device-csharp.md)
