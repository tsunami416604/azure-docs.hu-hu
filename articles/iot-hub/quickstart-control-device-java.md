---
title: 'Gyors útmutató: Eszköz vezérlése az Azure IoT Hub és Javával'
description: Ebben a rövid útmutatóban két Java-mintaalkalmazást fog futtatni. Az egyik egy háttéralkalmazás, amely a hubhoz csatlakoztatott eszközök távoli vezérlését teszi lehetővé. A másik alkalmazás a hubhoz csatlakoztatott eszközt szimulál, amelyet távolról lehet irányítani.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 9fb110eff1d498b2b20952048759c76a2dac39f2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306488"
---
# <a name="quickstart-control-a-device-connected-to-an-azure-iot-hub-with-java"></a>Gyors útmutató: Azure IoT hub-hoz csatlakoztatott eszköz kezelése Javával

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

Ebben a rövid útmutatóban egy *közvetlen módszert* használ az Azure IoT hub-hoz kapcsolódó szimulált eszköz egy Java-alkalmazással való vezérlésére. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését. Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését az IoT-eszközökről a felhőbe, valamint az eszközök kezelését a felhőből. 

Ez a rövid útmutató két előre megírt Java-alkalmazást használ:

* Egy szimulálteszköz-alkalmazás, amely válaszol a háttéralkalmazásokból meghívott közvetlen metódusokra. A közvetlen metódusok meghívásának fogadásához ez az alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik.

* Egy háttéralkalmazás, amely meghívja a közvetlen metódusokat a szimulált eszközre. A közvetlen metódus egy eszközre való meghívásához ez az alkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik.

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

Ha még nem tette meg, töltse le a Java-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha végrehajtotta az előző [rövid útmutatót: Telemetria küldése az eszközről egy IoT-hubhoz](quickstart-send-telemetry-java.md), ezt a lépést kihagyhatja.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha végrehajtotta az előző [rövid útmutatót: Telemetria küldése az eszközről egy IoT-hubhoz](quickstart-send-telemetry-java.md), ezt a lépést kihagyhatja.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyJavaDevice**: Annak az eszköznek a neve, amelyhez regisztrálva van. Használja a **MyJavaDevice** az ábrán látható módon. Ha más nevet választ az eszközének, ezt a nevet kell használnia ebben a cikkben, és frissítenie kell az eszköz nevét a minta alkalmazásokban a futtatása előtt.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyJavaDevice \
      --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="retrieve-the-service-connection-string"></a>A szolgáltatás kapcsolati sztringjének lekérése

Szüksége van egy _szolgáltatáskapcsolati sztringre_ is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

**YourIoTHubName**: Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --policy-name service --output table
```

Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Ezt az értéket használni fogja a rövid útmutató későbbi részében. A szolgáltatáskapcsolati sztring nem azonos az eszközkapcsolati sztringgel.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A szimulálteszköz-alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik, szimulált telemetriát küld, és figyeli a hubról érkező közvetlenmetódus-hívásokat. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz nyugtázást küld vissza a hubra a közvetlen metódus végrehajtása után.

1. Egy helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device-2** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** fájlt egy tetszőleges szövegszerkesztőben.

    Cserélje le a `connString` változó értékét az eszköz korábban lejegyzett kapcsolati sztringjére. Ezután mentse a **SimulatedDevice.java** fájl módosításait.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](./media/quickstart-control-device-java/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A háttéralkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik. Az alkalmazás közvetlen metódusokat hív meg egy eszközre az IoT Hubon keresztül, és figyeli a nyugtázásokat. Az IoT Hub-háttéralkalmazások általában a felhőben futnak.

1. Egy másik helyi terminálablakban keresse meg a Java-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\back-end-application** mappába.

2. Nyissa meg az **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le az `iotHubConnectionString` változó értéket a szolgáltatás korábban lejegyzett kapcsolati sztringjére. Mentse a **BackEndApplication.java** fájl módosításait.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szükséges kódtárak telepítéséhez és a háttéralkalmazás létrehozásához:

    ```cmd/sh
    mvn clean package
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás futtatásához:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    A következő képernyőképen az a kimenet látható, amelyben az alkalmazás közvetlen metódust hív meg az eszközre, és megkapja a nyugtázást:

    ![A háttéralkalmazás futtatása](./media/quickstart-control-device-java/BackEndApplication.png)

    A háttéralkalmazás futtatása után megjelenik egy üzenet a szimulált eszközt futtató konzolablakban, és megváltozik az üzenetküldések gyakorisága:

    ![Változás a szimulált ügyfélben](./media/quickstart-control-device-java/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban közvetlen metódust hívott egy eszközön egy háttérbeli alkalmazásból, és a közvetlen metódus hívására válaszolt egy szimulált eszköz alkalmazásban.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria átirányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)
