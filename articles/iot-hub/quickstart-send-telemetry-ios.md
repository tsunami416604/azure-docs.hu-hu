---
title: Rövid útmutató – Telemetria küldése az Azure IoT Hubra | Microsoft Docs
description: Ebben a rövid útmutatóban egy iOS-mintaalkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria olvasásához az IoT Hubról, a felhőben történő feldolgozás érdekében.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/20/2018
ms.author: kgremban
ms.openlocfilehash: aecb9a1819060e0da6338e8e16bf681fad42dd22
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161917"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-ios"></a>Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra (iOS)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a cikkben telemetriát küld egy szimulált eszközalkalmazástól az IoT Hubra. Ezután megtekintheti az adatokat egy háttéralkalmazásból. 

Ez a cikk előre megírt Swift-alkalmazást használ a telemetria küldésére, és egy CLI-segédprogramot a telemetria beolvasására az IoT Hubról. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- A mintakód letöltése az [Azure-minták](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip) oldalról 
- Az iOS SDK legújabb verzióját futtató [XCode](https://developer.apple.com/xcode/) legújabb verziója. A rövid útmutató tesztelése az XCode 9.3-as és az iOS 11.3-as verziójával történt.
- A [CocoaPods](https://guides.cocoapods.org/using/getting-started.html) legújabb verziója.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure CLI használatával regisztrál egy szimulált eszközt.

1. Adja hozzá az IoT Hub CLI-bővítményt, és hozza létre az eszközidentitást. A `{YourIoTHubName}` elemet cserélje le az IoT Hub nevére:

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myiOSdevice
   ```

    Ha úgy dönt, hogy eszközének egy másik nevet választ, a mintaalkalmazások futtatása előtt frissítse az eszköznevet bennük.

1. Futtassa az alábbi parancsot az imént regisztrált eszköz _kapcsolati sztringjének_ lekéréséhez:

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myiOSdevice --output table
   ```

   Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki: `Hostname=...=`. Ezt az értéket a cikk későbbi részében fogja használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A mintaalkalmazás az olyan iOS-eszközön fut, amely az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld. 

### <a name="install-cocoapods"></a>A CocoaPods telepítése

A CocoaPods a külső gyártótól származó kódtárakat használó iOS-projektek függőségeit kezeli.

Egy terminálablakban lépjen az Azure-IoT-Samples-iOS mappához, amelyet az előfeltételek részben töltött le. Ezután lépjen a mintaprojekthez:

```sh
cd quickstart/sample-device
```

Győződjön meg arról, hogy az XCode be van zárva, majd futtassa az alábbi parancsot a **podfile** fájlban szereplő CocoaPods telepítéséhez:

```sh
pod install
```

A projekthez szükséges podok telepítésén kívül a telepítési parancs egy XCode-munkaterületfájlt is létrehozott, amely már konfigurálva van a podok függőségekhez való használatára. 

### <a name="run-the-sample-application"></a>A mintaalkalmazás futtatása 

1. Nyissa meg a minta munkaterületet az XCode-ban.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Bontsa ki az **MQTT-ügyfélminta** projektet majd bontsa ki az azonos nevű mappát is.  
3. Nyissa meg **ViewController.swift** fájlt az XCode-ban való szerkesztéshez. 
4. Keresse meg a **connectionString** változót, és frissítse az értéket az eszköz korábban lejegyzett kapcsolati sztringjével.
5. Mentse a módosításokat. 
6. Futtassa a projektet az eszközemulátorban a **Létrehozás és futtatás** gombbal vagy a **command + r** billentyűkombinációval. 

   ![A projekt futtatása](media/quickstart-send-telemetry-ios/run-sample.png)

7. Amikor az emulátor megnyílt, válassza a mintaalkalmazás **Indítás** lehetőségét.

A következő képernyőképen az a példakimenet látható, amikor az alkalmazás szimulált telemetriát küld az IoT Hubhoz:

   ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-ios/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Az XCode-emulátorban futtatott mintaalkalmazás megjeleníti az eszközről küldött üzenetek adatait. Az IoT Hub segítségével is megtekintheti az adatokat a kapott formában. Az IoT Hub CLI-bővítmény csatlakozhat a szolgáltatásoldali **Események** végponthoz az IoT Hubon. A bővítmény fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

Futtassa a következő Azure CLI-parancsokat úgy, hogy a `{YourIoTHubName}` helyére az IoT Hub neve kerüljön:

```azurecli-interactive
az iot hub monitor-events --device-id myiOSdevice --hub-name {YourIoTHubName}
```

A következő képernyőképen az a kimenet látható, amikor a bővítmény fogadja a szimulálteszköz-alkalmazás által az IoT Hubnak küldött telemetriát:

Az alábbi képernyőképen a terminálablakban megjelenő telemetriatípus látható:

![Telemetria megtekintése](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a cikkben beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a Hubra egy iOS-eszközről, és beolvasta a telemetriát a Hubról. 

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-node.md)

<!-- Links -->
[lnk-process-d2c-tutorial]: tutorial-routing.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
