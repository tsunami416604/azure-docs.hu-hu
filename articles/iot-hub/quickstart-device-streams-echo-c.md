---
title: Kommunikáció az eszközalkalmazással C-ben az Azure IoT Hub-eszközadatfolyamokkal
description: Ebben a rövid útmutatóban egy C-eszközoldali alkalmazást futtat, amely egy IOt-eszközzel kommunikál egy eszközadatfolyamon keresztül.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 52f9e6529329c5bb1abb176082294dc26e64baa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675543"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rövid útmutató: Kommunikáció egy eszközalkalmazással C-ben az IoT Hub-eszközadatfolyamokon keresztül (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Az Azure IoT Hub jelenleg támogatja az eszközstreamelési [funkciót előnézeti funkcióként.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Az IoT Hub eszközadatfolyamok](iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát kommunikációját. A nyilvános előzetes verzió során a C SDK csak az eszköz oldalán támogatja az eszközstreameléseket. Ennek eredményeképpen ez a rövid útmutató csak az eszközoldali alkalmazás futtatására vonatkozó utasításokat ismerteti. A megfelelő szolgáltatásoldali alkalmazás futtatásához tekintse meg az alábbi cikkeket:

* [Kommunikáció c# eszközalkalmazásokkal az IoT Hub-eszközadatfolyamokon keresztül](./quickstart-device-streams-echo-csharp.md)

* [Kommunikáció az eszközalkalmazásokkal a Node.js-ben az IoT Hub-eszközadatfolyamokon keresztül](./quickstart-device-streams-echo-nodejs.md)

Az eszközoldali C alkalmazás ebben a rövid útmutatóban a következő funkciókkal rendelkezik:

* Hozzon létre egy eszközadatfolyamot egy IoT-eszközre.

* A szolgáltatásoldali alkalmazásból küldött adatok fogadása és visszaküldése.

A kód bemutatja az eszközadatfolyam kezdeményezési folyamatát, valamint azt, hogy hogyan használhatja azt adatok küldésére és fogadására.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A következő előfeltételekre van szüksége:

* Telepítse a [Visual Studio 2019-et](https://www.visualstudio.com/vs/) az Asztali fejlesztés funkcióval, ha a **C++** munkaterhelés engedélyezve van.

* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

* Futtassa a következő parancsot az Azure CLI-hez való Azure IoT-bővítmény hozzáadásához a Cloud Shell-példányhoz. Az IOT-bővítmény iot hub, IoT Edge és IoT-eszközlétesítési szolgáltatás (DPS)-specifikus parancsokat ad hozzá az Azure CLI-hez.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT-központok számára támogatott:

  * USA középső régiója
  * USA középső régiója
  * Észak-Európa
  * Délkelet-Ázsia

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ehhez a rövid útmutatóhoz az [Azure IoT-eszköz SDK C-hez.](iot-hub-device-sdk-c-intro.md) Az [Azure IoT C SDK-t](https://github.com/Azure/azure-iot-sdk-c) a GitHubról klónozásra és létrehozásra használt fejlesztői környezetet készít. A GitHub SDK-ja tartalmazza az ebben a rövid útmutatóban használt mintakódot.

   > [!NOTE]
   > Az eljárás megkezdése előtt győződjön meg arról, hogy a Visual Studio **c++ munkaterheléssel** rendelkező asztali fejlesztéssel van telepítve.

1. Telepítse a [CMake build rendszert](https://cmake.org/download/) a letöltési oldalon leírtak szerint.

1. Nyisson meg egy parancssort vagy a Git Bash-felületet. Futtassa a következő parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház klónozásához:

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet néhány percet vesz igénybe.

1. Hozzon létre egy *cmake* alkönyvtárat a git-tárház gyökérkönyvtárában, és keresse meg azt a mappát. Futtassa a következő parancsokat az *azure-iot-sdk-c* könyvtárból:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a *cmake* könyvtárból az SDK fejlesztői ügyfélplatformra jellemző verziójának létrehozásához.

   * Linux alatt:

      ```bash
      cmake ..
      make -j
      ```

   * A Windows rendszerben nyisson meg egy [Fejlesztői parancssort a Visual Studio programhoz.](/dotnet/framework/tools/developer-command-prompt-for-vs) Futtassa a Visual Studio verziójának parancsát. Ez a rövid útmutató a Visual Studio 2019-et használja. Ezek a parancsok visual studio-megoldást hoznak létre a szimulált eszközhöz a *cmake* könyvtárban.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

A csatlakozás előtt regisztrálnia kell egy eszközt az IoT-központtal. Ebben a szakaszban az Azure Cloud Shell az [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) segítségével egy szimulált eszköz regisztrálásához.

1. Az eszközidentitás létrehozásához futtassa a következő parancsot a Cloud Shell ben:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.
   > * A regisztrálandó eszköz nevéhez ajánlott a *MyDevice* használata az ábrán látható módon. Ha más nevet választ az eszközhöz, használja ezt a nevet a cikkben, és frissítse az eszköz nevét a mintaalkalmazásokban, mielőtt futtatja őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Az imént regisztrált eszköz *eszközkapcsolati karakterláncának* lekérnie, futtassa a következő parancsot a Cloud Shellben:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Vegye figyelembe a visszaadott eszköz kapcsolati karakterláncot későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunikáció az eszköz és a szolgáltatás között az eszközadatfolyamokon keresztül

Ebben a szakaszban futtatja az eszközoldali alkalmazást és a szolgáltatásoldali alkalmazást, és kommunikál a kettő között.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Az eszközoldali alkalmazás futtatásához hajtsa végre az alábbi lépéseket:

1. Adja meg az eszköz hitelesítő adatait a mappában lévő **iothub_client_c2d_streaming_sample.c** forrásfájl szerkesztésével és az `iothub_client/samples/iothub_client_c2d_streaming_sample` eszköz kapcsolati karakterláncának hozzáadásával.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Fordítsa le a kódot a következő parancsokkal:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. A lefordított program futtatása:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>A szolgáltatásoldali alkalmazás futtatása

Ahogy korábban említettük, az IoT Hub C SDK csak az eszköz oldalán támogatja az eszközstreameléseket. A kapcsolódó szolgáltatásoldali alkalmazás létrehozásához és futtatásához kövesse az alábbi rövid útmutatók egyikének utasításait:

* [Kommunikáció c# eszközalkalmazással az IoT Hub-eszközadatfolyamokon keresztül](./quickstart-device-streams-echo-csharp.md)

* [Kommunikáció egy eszközalkalmazással a Node.js-ben az IoT Hub-eszközadatfolyamokon keresztül](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállít egy IoT hubot, regisztrált egy eszközt, létrehozott egy eszközadatfolyamot az eszközön lévő C-alkalmazás és egy másik alkalmazás között a szolgáltatási oldalon, és az adatfolyamot használta az adatok oda-vissza küldésére az alkalmazások között.

Az eszközstreamekről az:

> [!div class="nextstepaction"]
> [Eszközadatfolyamok – áttekintés](./iot-hub-device-streams-overview.md)
