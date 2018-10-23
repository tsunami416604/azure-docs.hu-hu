---
title: Rövid útmutató – Telemetria küldése az Azure IoT Hubra (C) | Microsoft Docs
description: Ebben a rövid útmutatóban két C-alkalmazást fog futtatni szimulált telemetria egy IoT Hubra való küldéséhez és telemetria olvasásához az IoT Hubról a felhőben történő feldolgozás érdekében.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/27/2018
ms.author: wesmc
ms.openlocfilehash: 3fa4c536313375ed88f6f0223218a663d4be3eb3
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364775"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-the-telemetry-from-the-hub-with-a-back-end-application-c"></a>Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra, és a telemetria olvasása a háttéralkalmazással (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban telemetriát küld egy szimulálteszköz-alkalmazástól az IoT Hubon keresztül egy háttéralkalmazásba feldolgozásra.

A rövid útmutató a [C nyelvhez készült Azure IoT eszközoldali SDK](iot-hub-device-sdk-c-intro.md) egy C-mintaalkalmazását használja telemetriai adatoknak az IoT hubra küldésére. Az Azure IoT eszközoldali SDK-t [ANSI C-ben (C99)](https://wikipedia.org/wiki/C99) írták a hordozhatóságot és a széles körű platformokon történő használhatóságot szem előtt tartva. A mintakód futtatása előtt létrehozza az IoT hubot és regisztrálja rajta a szimulált eszközt.

A cikk Windowsra íródott, de a rövid útmutató Linuxon is elvégezhető.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/vs/)-et, amelyben engedélyezve van az [„Asztali fejlesztés C++ használatával”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) tevékenységprofil.
* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

A rövid útmutató céljaira a [C nyelvhez készült Azure IoT eszközoldali SDK-t](iot-hub-device-sdk-c-intro.md) fogjuk használni. 

Az SDK használatához a következő környezetekhez tartozó csomagok és könyvtárak telepítése szükséges:

* **Linux**: apt-get-csomagok állnak rendelkezésre az Ubuntu 16.04 és 18.04 verzióhoz a következő CPU-architektúrákhoz: amd64, arm64, armhf és i386. További információkért lásd: [Az apt-get használata C eszköz ügyfél-projekt létrehozására az Ubuntu rendszeren](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: Az mbed platformon eszközalkalmazások fejlesztői számára közzétett könyvtárral és példákkal perceken belül elindulhat az Azure IoT Hub-bal. További információk: [Az mbed könyvtár használata](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Ha az Arduino-ra fejleszt, kihasználhatja az Arduino IDE kódtárkezelőben elérhető Azure IoT könyvtárat. Bővebb információk: [Az Azure IoT Hub könyvtár – Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: Mac és iOS rendszerű eszközfejlesztéshez az IoT Hub eszközoldali SDK mint CocoaPods érhető el. További információ: [iOS példák Microsoft Azure IoT számára](https://cocoapods.org/pods/AzureIoTHubClient).

Ebben a rövid útmutatóban azonban az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) fordítására és klónolására szolgáló fejlesztőkörnyezetet készítjük elő a GitHubról. A rövid útmutató mintakódokat az SDK tartalmazza a GitHubon. 


1. Töltse le a [CMake buildelési rendszer](https://cmake.org/download/) 3.11.4-es verzióját. Ellenőrizze a letöltött bináris fájlt a megfelelő titkosítási kivonat értékének használatával. Az alábbi példa a Windows PowerShell használatával ellenőrizte az x64 MSI disztribúció 3.11.4-es verziójának titkosítási kivonatát:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    A CMake webhelyén az írás időpontjában a következő kivonatolási értékek szerepelnek a 3.11.4-es verzióhoz:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Az adattár mérete jelenleg körülbelül 220 MB. Ez a művelet várhatóan több percig is eltarthat.


3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre. 

    ```cmd
    cmake ..
    ```
    
    Ha a `cmake` nem találja a C++ fordítóprogramot, a fenti parancs futtatása esetlegesen fordítási hibákat adhat vissza. Ilyen esetekben futtassa a parancsot a [Visual Studio parancssorából](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban az Azure Cloud Shellt fogja használni az [IoT-bővítménnyel](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) a szimulált eszköz regisztrálásához.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához. 

   **YourIoTHubName**: A helyőrző helyére írja be az IoT Hubjához választott nevet.

   **MyCDevice**: Ezt a nevet kapja a regisztrált eszköz. A MyCDevice eszközt használja a bemutatott módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: A helyőrző helyére írja be az IoT Hubjához választott nevet.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="send-simulated-telemetry"></a>Szimulált telemetria küldése

A szimulálteszköz-alkalmazás egy az IoT Hubon található eszközspecifikus végponthoz csatlakozik, és karakterláncot küld szimulált telemetriaként.

1. Szövegszerkesztővel nyissa meg az iothub_convenience_sample.c forrásfájlt, és tekintse át a telemetriai adatok küldését végző mintakódot. A fájl a következő helyen található:

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Keresse meg a `connectionString` konstans deklarációját:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    Cserélje le a `connectionString` konstans értékét az eszköz korábban feljegyzett kapcsolati karakterláncára. Utána mentse el az **iothub_convenience_sample.c** módosításait.

3. Egy helyi terminálablakban keresse meg az *iothub_convenience_sample* projektkönyvtárat az Azure IoT C SDK-ban létrehozott CMake könyvtárban.

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Futtassa a CMake parancsot a helyi terminálablakban a minta frissített `connectionString` értékkel való buildeléséhez:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Futtassa az alábbi parancsot egy helyi terminálablakban a szimulálteszköz-alkalmazás futtatásához:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Telemetria olvasása a Hubról


Ebben a szakaszban az Azure Cloud Shellt fogja használni az [IoT-bővítménnyel](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) a szimulált eszköz által küldött üzeneteket monitorozásához.

1. Az Azure Cloud Shell használatával futtassa a következő parancsot az IoT Hubhoz történő csatlakozáshoz és az üzenetek olvasásához:

   **YourIoTHubName**: A helyőrző helyére írja be az IoT Hubjához választott nevet.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Olvassa el az eszköz üzeneteit az Azure CLI használatával](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállított egy IoT Hubot, regisztrált egy eszközt, szimulált telemetriát küldött a Hubra egy C-alkalmazással, és beolvasta a telemetriát a Hubról az Azure Cloud Shell használatával.

Az Azure IoT Hub C SDK-val történő fejlesztéssel kapcsolatos további információkért folytassa a következő útmutatóval:

> [!div class="nextstepaction"]
> [Fejlesztés az Azure IoT Hub C SDK használatával](iot-hub-devguide-develop-for-constrained-devices.md)