---
title: 'Gyors útmutató: telemetria küldése az Azure IoT Hub Javával'
description: Ebben a rövid útmutatóban két Java-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria az IoT Hubról, a felhőben történő feldolgozás érdekében való beolvasásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom:
- mvc
- seo-java-august2019
- seo-java-september2019
- mqtt
- devx-track-java
ms.date: 05/26/2020
ms.openlocfilehash: 757e0892469e52b1f2716d837d8423c5940322e2
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87317926"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Gyors útmutató: telemetria küldése egy Azure IoT hub-ba, és beolvasása Java-alkalmazással

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Ebben a rövid útmutatóban telemetria küld az Azure IoT Hubnek, és elolvashatja azt egy Java-alkalmazással. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató két előre megírt Java-alkalmazást használ: egyet a telemetria elküldéséhez, és az egyiket, hogy beolvassa a telemetria a központból. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Java SE Development Kit 8. A [Java hosszú távú Azure-és Azure stack-támogatásában](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)a **hosszú távú támogatás**alatt válassza a **Java 8**lehetőséget.

* [Apache Maven 3](https://maven.apache.org/download.cgi).

* [Egy minta Java-projekt](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* A 8883-es port megnyitható a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

A Java aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
java -version
```

A Maven aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
mvn --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IoT-bővítmény az IoT Hubhoz, az IoT Edge-hez és az IoT Device Provisioning Service-hez (DPS) használható parancsokkal bővíti az Azure CLI-t.

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

   **MyJavaDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyJavaDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

    **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni.

3. A IoT hub _Event Hubs-kompatibilis végpontja_, _Event Hubs-kompatibilis útvonala_és a _szolgáltatás elsődleges kulcsa_ is szükséges ahhoz, hogy a háttér-alkalmazás csatlakozhasson az IoT hubhoz, és lekérje az üzeneteket. Ezeket az értékeket a következő parancsok kérdezik le az IoT Hubhoz:

     **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Jegyezze fel ezt a három értéket, amelyet később a rövid útmutatóban fog használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetriai adatok küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Egy helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a változó értékét `connString` a korábban megjegyzett eszköz-összekapcsolási sztringre. Ezután mentse a módosításokat a **SimulatedDevice. Java**fájlba.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![Az eszköz által az IoT hubhoz elküldett telemetria kimenete](media/quickstart-send-telemetry-java/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetriai adatok kiolvasása a központból

A háttéralkalmazás a szolgáltatásoldali **Események** végponthoz csatlakozik az IoT Hubon. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

1. Egy másik helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\read-d2c-messages** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** fájlt egy tetszőleges szövegszerkesztőben. Frissítse a következő változókat, és mentse a fájlon végrehajtott módosításait.

    | Változó | Érték |
    | -------- | ----------- |
    | `EVENT_HUBS_COMPATIBLE_ENDPOINT` | Cserélje le a változó értékét a Event Hubs-kompatibilis végpontra, amelyet korábban jegyzett készített. |
    | `EVENT_HUBS_COMPATIBLE_PATH`     | Cserélje le a változó értékét arra a Event Hubs-kompatibilis elérési útra, amelyet korábban jegyzett készített. |
    | `IOT_HUB_SAS_KEY`                | A változó értékét cserélje le a szolgáltatás elsődleges kulcsára, amelyet korábban jegyzett készített. |

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás futtatásához:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a háttéralkalmazás fogadja a szimulálteszköz-alkalmazás által az IoT Hubhoz küldött telemetriát:

    ![A kimeneti háttérbeli alkalmazás fogadja az IoT hub-ba küldött telemetria](media/quickstart-send-telemetry-java/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, szimulált telemetria elküldve a hubhoz egy Java-alkalmazás használatával, valamint egy egyszerű háttérbeli alkalmazás használatával olvassa be a telemetria.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-java.md)
