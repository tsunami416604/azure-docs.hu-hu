---
title: Rövid útmutató – Telemetria küldése az Azure IoT Hubra (Java) | Microsoft Docs
description: Ebben a rövid útmutatóban két Java-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria az IoT Hubról, a felhőben történő feldolgozás érdekében való beolvasásához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/22/2019
ms.openlocfilehash: f5ec6a16de46a7902f7ab739922dc756c96702e0
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010973"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-java"></a>Gyors útmutató: Telemetriát küldjön az eszközről az IoT hub és a egy háttér-alkalmazással (Java), annak olvasása

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

Ez a cikk két előre megírt Java-alkalmazást használ a telemetria küldésére: egyet a telemetria elküldésére, egyet pedig a telemetria olvasásához a hubról. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban futtatott két mintaalkalmazás a Java használatával készült. A fejlesztői gépen szükség lesz a Java SE 8-as vagy újabb verziójára.

A Javát az [Oracle](https://aka.ms/azure-jdks) webhelyéről töltheti le többféle platformra.

A Java aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
java -version
```

A minták létrehozásához telepíteni kell a Maven 3-at. A Mavent az [Apache Maven](https://maven.apache.org/download.cgi) webhelyéről töltheti le többféle platformra.

A Maven aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
mvn --version
```

Töltse le a Maven-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához.

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

   **MyJavaDevice**: Ön regisztrálja az eszköz neve. Használat **MyJavaDevice** látható módon. Ha úgy dönt, hogy az eszköz egy másik nevet, meg kell során ez a cikk ezt a nevet használja, és az eszköz neve a mintaalkalmazások őket futtatása előtt.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben beolvasni a _eszköz kapcsolati karakterláncának_ az imént regisztrált eszköz: ** YourIoTHubName: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyJavaDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

3. Emellett szükség lesz az _Event Hubs-kompatibilis végpontra_, az _Event Hubs-kompatibilis elérési útra_ és az _iothubowner elsődleges_ kulcsra az IoT Hubról, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Ezeket az értékeket a következő parancsok kérdezik le az IoT Hubhoz:

     ** YourIoTHubName: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name YourIoTHubName

    az iot hub show --query properties.eventHubEndpoints.events.path --name YourIoTHubName

    az iot hub policy show --name iothubowner --query primaryKey --hub-name YourIoTHubName
    ```

    Jegyezze fel ezt a három értéket, mert ebben a rövid útmutatóban később használni fogja őket.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Egy helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a `connString` változó értékét az eszköz korábban lejegyzett kapcsolati sztringjére. Ezután mentse a **SimulatedDevice.java** fájl módosításait.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

A háttéralkalmazás a szolgáltatásoldali **Események** végponthoz csatlakozik az IoT Hubon. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

1. Egy másik helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\read-d2c-messages** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** fájlt egy tetszőleges szövegszerkesztőben. Frissítse a következő változókat, és mentse a fájlon végrehajtott módosításait.

    | Változó | Érték |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Cserélje le a változó értékét a korábban feljegyzett Event Hubs-kompatibilis végpontra. |
    | `eventHubsCompatiblePath`     | Cserélje le a változó értékét a korábban feljegyzett Event Hubs-kompatibilis elérési útra. |
    | `iotHubSasKey`                | Cserélje le a változó értékét a korábban feljegyzett iothubowner elsődleges kulcsra. |


3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás futtatásához:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a háttéralkalmazás fogadja a szimulálteszköz-alkalmazás által az IoT Hubhoz küldött telemetriát:

    ![A háttéralkalmazás futtatása](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a Hubra egy Java-alkalmazással, és beolvasta a telemetriát a Hubról egy egyszerű háttéralkalmazással.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Csatlakozik az IoT hub eszköz vezérlése](quickstart-control-device-java.md)