---
title: Rövid útmutató – Telemetria küldése az Azure IoT Hubra (C) | Microsoft Docs
description: Ebben a rövid útmutatóban két C-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria olvasásához az IoT Hubról a felhőben történő feldolgozás érdekében.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 46dc5298337b2ee0d16d22ec6ab24cb9d751fc38
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769178"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Rövid útmutató: Telemetriai adatok küldése egy eszközről egy IoT-központba, és olvassa el egy háttéralkalmazással (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

A rövid útmutató a [C nyelvhez készült Azure IoT eszközoldali SDK](iot-hub-device-sdk-c-intro.md) egy C-mintaalkalmazását használja telemetriai adatoknak az IoT hubra küldésére. Az Azure IoT eszközoldali SDK-t [ANSI C-ben (C99)](https://wikipedia.org/wiki/C99) írták a hordozhatóságot és a széles körű platformokon történő használhatóságot szem előtt tartva. A mintakód futtatása előtt létrehozza az IoT hubot és regisztrálja rajta a szimulált eszközt.

Ez a cikk windowsos, de ezt a rövid útmutatót Linuxon is befejezheti.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2019-et](https://www.visualstudio.com/vs/) az ["Asztali fejlesztés C++"-os munkaterheléssel.](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)

* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A rövid útmutatóban szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.


* Futtassa a következő parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a Cloud Shell-példányhoz. Az IoT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS) adott parancsokat az Azure CLI-hez.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ehhez a rövid útmutatóhoz az [Azure IoT-eszköz C-hez sdk-ját](iot-hub-device-sdk-c-intro.md)fogja használni. 

A következő környezetekben az SDK-t a csomagok és tárak telepítésével használhatja:

* **Linux**: az apt-get csomagok az Ubuntu 16.04 és 18.04 számára érhetők el a következő CPU architektúrák használatával: amd64, arm64, armhf és i386. További információkért lásd: [Az apt-get használata C eszköz ügyfél-projekt létrehozására az Ubuntu rendszeren](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed:** Az mbed platformon eszközalkalmazásokat létrehozó fejlesztők számára közzétettünk egy könyvtárat és mintákat, amelyek percek alatt megkezdik a witH Azure IoT Hub-ot. További információk: [Az mbed könyvtár használata](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino:** Ha az Arduino-n fejleszt, kihasználhatja az Arduino IDE könyvtárkezelőben elérhető Azure IoT-könyvtárat. Bővebb információk: [Az Azure IoT Hub könyvtár – Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: Mac és iOS rendszerű eszközfejlesztéshez az IoT Hub eszközoldali SDK mint CocoaPods érhető el. További információ: [iOS példák Microsoft Azure IoT számára](https://cocoapods.org/pods/AzureIoTHubClient).

Ebben a rövid útmutatóban azonban előkészíti az [Azure IoT C SDK-t](https://github.com/Azure/azure-iot-sdk-c) a GitHubról az Azure IoT C SDK klónozásához és létrehozásához használt fejlesztői környezetet. A rövid útmutató mintakódokat az SDK tartalmazza a GitHubon.

1. Töltse le a [CMake build rendszert](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Keresse meg az SDK [legújabb kiadásának](https://github.com/Azure/azure-iot-sdk-c/releases/latest) címkenevét.

3. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa a következő parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben található címkét `-b` a paraméter értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

4. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. Futtassa a `azure-iot-sdk-c` következő parancsokat a könyvtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Futtassa a következő parancsot a fejlesztői ügyfélplatformra jellemző SDK-verzió létrehozásához. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre.

    ```cmd
    cmake ..
    ```

    Ha `cmake` nem találja a C++ fordítót, a fenti parancs futtatása közben buildhibákat kaphat. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    A sikeres létrehozást követően a kimenet utolsó sorai a következőhöz hasonlóan néznek majd ki:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban az Azure Cloud Shell t az [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) használatával regisztrálhat egy szimulált eszközt.

1. Futtassa a következő parancsot az Azure Cloud Shellben az eszközidentitás létrehozásához.

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

   **MyCDevice**: A regisztráló eszköz neve. Javasoljuk, hogy használja **a MyCDevice-t** az ábrán látható módon. Ha más nevet választ az eszközhöz, akkor ezt a nevet is használnia kell a cikkben, és frissítenie kell az eszköz nevét a mintaalkalmazásokban, mielőtt futtatna őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Futtassa a következő parancsot az Azure Cloud Shellben az _eszköz kapcsolati karakterláncának_ lekérni az imént regisztrált eszközhöz:

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz választott névvel.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket később fogja használni a rövid útmutatóban.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és sztringet küld szimulált telemetriaként.

1. Szövegszerkesztővel nyissa meg az iothub_convenience_sample.c forrásfájlt, és tekintse át a telemetriai adatok küldését végző mintakódot. A fájl a következő helyen található a munkakönyvtár alatt, ahol klónozta az Azure IoT C SDK-t:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Keresse meg a `connectionString` konstans deklarációját:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Cserélje le az `connectionString` állandó értékét arra az eszközkapcsolati karakterláncra, amelyet korábban írt fel. Utána mentse el az **iothub_convenience_sample.c** módosításait.

3. Egy helyi terminálablakban keresse meg az *iothub_convenience_sample* projektkönyvtárat az Azure IoT C SDK-ban létrehozott CMake könyvtárban. Írja be a következő parancsot a munkakönyvtárból:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Futtassa a CMake parancsot a helyi terminálablakban a minta frissített `connectionString` értékkel való buildeléséhez:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. A helyi terminálablakban futtassa a következő parancsot a szimulált eszközalkalmazás futtatásához:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról

Ebben a szakaszban az Azure Cloud Shell t az [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) használatával figyelheti a szimulált eszköz által küldött eszközüzeneteket.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName:** Cserélje le ezt a helyőrzőt az IoT hubhoz kiválasztott névvel.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT-központot hoz létre, regisztrál egy eszközt, szimulált telemetriai adatokat küld a hubnak egy C alkalmazás használatával, és olvassa el a telemetriai adatokat a hubból az Azure Cloud Shell használatával.

Az Azure IoT Hub C SDK-val történő fejlesztéssel kapcsolatos további információkért folytassa a következő útmutatóval:

> [!div class="nextstepaction"]
> [Fejlesztés az Azure IoT Hub C SDK használatával](iot-hub-devguide-develop-for-constrained-devices.md)
