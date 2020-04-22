---
title: 'Rövid útmutató: Telemetriai adatok küldése az Azure IoT Hubba Java-val'
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
ms.date: 06/21/2019
ms.openlocfilehash: 954fe6f92b68d011aaff0fff7925b3409ee628cb
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769112"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Rövid útmutató: Telemetriai adatok küldése egy Azure IoT-központba, és egy Java-alkalmazással való olvasása

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Ebben a rövid útmutatóban telemetriai adatokat küld az Azure IoT Hubnak, és egy Java-alkalmazással olvassa el. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ez a rövid útmutató két előre megírt Java-alkalmazást használ: az egyik a telemetriai adatok küldéséhez, a másik pedig a telemetriai adatok a hubról történő olvasásához. Mielőtt futtatja ezt a két alkalmazást, hozzon létre egy IoT Hubot, és regisztráljon egy eszközt a hubon.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* Java SE Fejlesztői Készlet 8. A [Java hosszú távú támogatása az Azure és az Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)hosszú távú támogatása a **Hosszú távú támogatás**csoportban válassza a **Java 8**lehetőséget.

* [Apache Maven 3](https://maven.apache.org/download.cgi).

* [A minta Java projekt](https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip).

* A 8883-as port megnyílik a tűzfalon. A rövid útmutatóban szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

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

   **MyJavaDevice**: Ez a regisztráló eszköz neve. Javasoljuk, hogy használja **a MyJavaDevice-t az** ábrán látható módon. Ha más nevet választ az eszközhöz, akkor ezt a nevet is használnia kell a cikkben, és frissítenie kell az eszköz nevét a mintaalkalmazásokban, mielőtt futtatna őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Futtassa a következő parancsot az Azure Cloud Shellben az _eszköz kapcsolati karakterláncának_ lekérni az imént regisztrált eszközhöz:

    **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később fogja használni a rövid útmutatóban.

3. Az Event _Hubs-kompatibilis végpontra_, az _Event Hubs-kompatibilis elérési útra_és az IoT hubszolgáltatás _elsődleges kulcsára_ is szüksége van ahhoz, hogy a háttéralkalmazás csatlakozzon az IoT-központhoz, és lekérje az üzeneteket. Ezeket az értékeket a következő parancsok kérdezik le az IoT Hubhoz:

     **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Jegyezze fel ezt a három értéket, amelyeket később a rövid útmutatóban fog használni.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és hőmérséklettel és páratartalommal kapcsolatos szimulált telemetriát küld.

1. Egy helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a `connString` változó értékét arra az eszközkapcsolati karakterláncra, amelyről korábban feljegyzést készített. Ezután mentse a módosításokat **SimulatedDevice.java**.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![Az eszköz által az IoT hubra küldött telemetriai adatok kimenete](media/quickstart-send-telemetry-java/iot-hub-simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

A háttéralkalmazás a szolgáltatásoldali **Események** végponthoz csatlakozik az IoT Hubon. Az alkalmazás fogadja az eszközről a felhőbe irányuló üzeneteket, amelyeket a rendszer a szimulált eszközről küld. Az IoT Hub-háttéralkalmazások általában a felhőben futnak, hogy fogadják és feldolgozzák az eszközről a felhőbe küldött üzeneteket.

1. Egy másik helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\read-d2c-messages** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** fájlt egy tetszőleges szövegszerkesztőben. Frissítse a következő változókat, és mentse a fájlon végrehajtott módosításait.

    | Változó | Érték |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Cserélje le a változó értékét az Event Hubs-kompatibilis végpontra, amelyet korábban feljegyezte. |
    | `eventHubsCompatiblePath`     | Cserélje le a változó értékét az Event Hubs-kompatibilis elérési útra, amelyet korábban feljegyezte. |
    | `iotHubSasKey`                | Cserélje le a változó értékét arra a szolgáltatás elsődleges kulcsára, amelyről korábban feljegyzést készített. |

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás futtatásához:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a háttéralkalmazás fogadja a szimulálteszköz-alkalmazás által az IoT Hubhoz küldött telemetriát:

    ![Kimenet háttéralkalmazás ként az IoT hubnak küldött telemetriai adatokat kap](media/quickstart-send-telemetry-java/iot-hub-read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT hubot hoz létre, regisztrált egy eszközt, szimulált telemetriai adatokat küldött a hubnak egy Java-alkalmazás használatával, és egy egyszerű háttéralkalmazás használatával elolvassa a telemetriai adatokat a hubról.

Ha meg szeretné tudni, hogyan vezérelheti a szimulált eszközt egy háttéralkalmazáson keresztül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése](quickstart-control-device-java.md)
