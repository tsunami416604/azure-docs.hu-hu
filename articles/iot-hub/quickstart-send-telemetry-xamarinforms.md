---
title: Telemetria küldése az Azure IoT Hub rövid útmutatóba (Xamarin. Forms)
description: Futtasson egy minta Xamarin. Forms-alkalmazást, amely szimulált telemetria küld egy IoT hubhoz, és beolvassa a telemetria az IoT hub-ból a felhőben történő feldolgozásra.
author: cmaneu
manager: philmea
ms.author: chmaneu
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 07/01/2020
ms.openlocfilehash: 962dd35bd07e3ab542bdb8616b1cc2fda1441b36
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061540"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-xamarinforms"></a>Gyors útmutató: telemetria küldése az eszközről egy IoT-hubhoz (Xamarin. Forms)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a cikkben telemetriát küld egy szimulált eszközalkalmazástól az IoT Hubra. Ezután megtekintheti az adatokat egy háttéralkalmazásból.

Ez a cikk egy előre megírt Xamarin. Forms alkalmazást használ a telemetria és egy CLI-segédprogram elküldéséhez, hogy beolvassa a telemetria a IoT Hubból.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="prerequisites"></a>Előfeltételek

- A mintakód letöltése az [Azure-minták](https://github.com/Azure-Samples/azure-iot-samples-xamarin/archive/master.zip) oldalról

- A [Visual Studio 2019](https://visualstudio.microsoft.com/) legújabb verziója vagy [Visual Studio for Mac](https://visualstudio.microsoft.com/) a Xamarin. Forms eszközzel. Ez a rövid útmutató a Visual Studio 16.6.0 lett tesztelve.

- Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

- A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IoT-bővítmény az IoT Hubhoz, az IoT Edge-hez és az IoT Device Provisioning Service-hez (DPS) használható parancsokkal bővíti az Azure CLI-t.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT-központ létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **myXamarinDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **myXamarinDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {YourIoTHubName} --device-id myXamarinDevice
   ```

1. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id myXamarinDevice --output table
   ```

   Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=myXamarinDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

A minta alkalmazás Windows rendszeren fut – egy iOS-eszközön vagy szimulátoron, illetve az Android-eszközön vagy a szimulátoron, amely a IoT hub eszköz-specifikus UWP csatlakozik, és szimulált hőmérséklet-és páratartalom-telemetria küld. 

1. Nyissa meg a minta munkaterületet a Visual Studióban vagy Visual Studio for Macban.
2. Bontsa ki a **SimulatedDevice** projektet.  
3. Nyissa meg a **IoTHubService.cs** a Visual Studióban való szerkesztéshez. 
4. Keresse meg a **_iotHubConnectionString** változót, és frissítse az értéket a korábban jegyzett eszköz-összekapcsolási karakterlánccal.
5. Mentse a módosításokat. 
6. Futtassa a projektet az eszköz-emulátorban vagy egy valódi eszközön a **Build és a Futtatás** gombbal, vagy az **F5** billentyűt a Windows vagy a **Command + r** billentyűkombinációval a Mac számítógépen. 

   ![A projekt futtatása](media/quickstart-send-telemetry-xamarinforms/run-sample.png)

7. Amikor az emulátor megnyílt, válassza a mintaalkalmazás **Indítás** lehetőségét.

Az alábbi képernyőképen néhány példa kimenet jelenik meg, mivel az alkalmazás szimulált telemetria küld a IoT hubhoz: ![ futtassa a szimulált eszközt.](media/quickstart-send-telemetry-xamarinforms/view-d2c.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

Az XCode-emulátorban futtatott mintaalkalmazás megjeleníti az eszközről küldött üzenetek adatait. Az IoT Hub segítségével is megtekintheti az adatokat a kapott formában. Az IoT Hub CLI-bővítmény csatlakozhat a szolgáltatásoldali **Események** végponthoz az IoT Hubon. A bővítmény fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

Futtassa a következő parancsokat az Azure Cloud Shellben úgy, hogy a `YourIoTHubName` helyére az IoT Hub neve kerüljön:

```azurecli-interactive
az iot hub monitor-events --device-id myXamarinDevice --hub-name {YourIoTHubName}
```

A következő képernyőképen az a kimenet látható, amikor a bővítmény fogadja a szimulált eszköz által az IoT Hubnak küldött telemetriát:

A következő képernyőfelvételen látható, hogy milyen típusú telemetria láthat a helyi terminál ablakban: ![ telemetria megtekintése](media/quickstart-send-telemetry-ios/view-telemetry.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT hubot állít be, regisztrált egy eszközt, szimulált telemetria küldtünk a központba egy Xamarin. Forms alkalmazásból, és beolvasta a telemetria a központból. 

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-node.md)
