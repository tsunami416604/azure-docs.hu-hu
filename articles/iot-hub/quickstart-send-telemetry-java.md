---
title: 'Gyors útmutató: Telemetria küldése az Azure IoT Hub Javával'
description: Ebben a rövid útmutatóban két Java-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria az IoT Hubról, a felhőben történő feldolgozás érdekében való beolvasásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: cb115b8658850fc85f93fc7a9508a82ecee920d8
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166447"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Gyors útmutató: Telemetria küldése egy Azure IoT hub-ba, és beolvasása Java-alkalmazással

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

A rövid útmutató bemutatja, hogyan küldhet telemetria egy Azure IoT hub-ba, és hogyan olvashatja el egy Java-alkalmazással. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

Ez a cikk két előre megírt Java-alkalmazást használ a telemetria küldésére: egyet a telemetria elküldésére, egyet pedig a telemetria olvasásához a hubról. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban futtatott két mintaalkalmazás a Java használatával készült. A fejlesztői gépen Java SE 8 szükséges.

A Java SE Development Kit 8 letöltése több platformra is elvégezhető a [Java hosszú távú Azure-és Azure stack-támogatásával](https://docs.microsoft.com/en-us/java/azure/jdk/?view=azure-java-stable). Győződjön meg arról, hogy a **Java 8** lehetőséget választja a **hosszú távú támogatás** alatt a JDK 8 letöltéséhez.

A Java aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
java -version
```

A minták létrehozásához telepíteni kell a Maven 3-at. A Mavent az [Apache Maven](https://maven.apache.org/download.cgi) webhelyéről töltheti le többféle platformra.

A Maven aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
mvn --version
```

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Töltse le a Maven-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyJavaDevice**: A regisztrálni kívánt eszköz neve. Javasoljuk, hogy a **MyJavaDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Futtassa a következő parancsot a Azure Cloud Shellban az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

    **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később a gyors útmutatóban fogja használni.

3. A IoT hub _Event Hubs-kompatibilis végpontja_, _Event Hubs-kompatibilis útvonala_és a _szolgáltatás elsődleges kulcsa_ is szükséges ahhoz, hogy a háttér-alkalmazás csatlakozhasson az IoT hubhoz, és lekérje az üzeneteket. Ezeket az értékeket a következő parancsok kérdezik le az IoT Hubhoz:

     **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Jegyezze fel ezt a három értéket, amelyet később a rövid útmutatóban fog használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Egy helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a `connString` változó értékét a korábban jegyzett eszköz-összekapcsolási sztringre. Ezután mentse a módosításokat a **SimulatedDevice. Java**fájlba.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![Az eszköz által az IoT hubhoz elküldett telemetria kimenete](media/quickstart-send-telemetry-java/iot-hub-simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

A háttéralkalmazás a szolgáltatásoldali **Események** végponthoz csatlakozik az IoT Hubon. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

1. Egy másik helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\read-d2c-messages** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** fájlt egy tetszőleges szövegszerkesztőben. Frissítse a következő változókat, és mentse a fájlon végrehajtott módosításait.

    | Változó | Value |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Cserélje le a változó értékét a Event Hubs-kompatibilis végpontra, amelyet korábban jegyzett készített. |
    | `eventHubsCompatiblePath`     | Cserélje le a változó értékét arra a Event Hubs-kompatibilis elérési útra, amelyet korábban jegyzett készített. |
    | `iotHubSasKey`                | A változó értékét cserélje le a szolgáltatás elsődleges kulcsára, amelyet korábban jegyzett készített. |

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás futtatásához:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a háttéralkalmazás fogadja a szimulálteszköz-alkalmazás által az IoT Hubhoz küldött telemetriát:

    ![A kimeneti háttérbeli alkalmazás fogadja az IoT hub-ba küldött telemetria](media/quickstart-send-telemetry-java/iot-hub-read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, szimulált telemetria elküldve a hubhoz egy Java-alkalmazás használatával, valamint egy egyszerű háttérbeli alkalmazás használatával olvassa be a telemetria.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT hub-hoz csatlakoztatott eszköz vezérlése @ no__t-0
