---
title: Egy eszköz alkalmazás a C Azure IoT Hub eszköz adatfolyamok (előzetes verzió) keresztül kommunikáljon |} A Microsoft Docs
description: Ebben a rövid útmutatóban egy C eszközoldali alkalmazást futtat, amely kommunikál egy IoT-eszközzel rendelkező eszköz adatfolyam-n keresztül.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 4b6f987c68f9fe3ef95c82017b7d8be1d83083ea
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446129"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Gyors útmutató: Egy eszköz alkalmazás a C IoT Hub eszköz adatfolyamok (előzetes verzió) használatával való kommunikáció során

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Az Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. A nyilvános előzetes verzióban az C SDK-t csak az eszköz oldalán eszköz Streamek támogatja. Ennek eredményeképpen az ebben a rövid útmutatóban csak az eszközoldali alkalmazás futtatásához útmutatást ismerteti. Kísérő Szolgáltatásoldali alkalmazás futtatásához, tekintse meg:
 
   * [Az eszköz Apps kommunikációhoz C# keresztül az IoT Hub eszköz Streamek](./quickstart-device-streams-echo-csharp.md)
   * [Az IoT Hub eszköz Streamek eszköz-alkalmazások a node.js-ben való kommunikáció során](./quickstart-device-streams-echo-nodejs.md)

Ebben a rövid útmutatóban az eszközoldali C alkalmazás van a következő funkciókat:

* Egy eszköz stream IoT-eszközökön való létrehozásához.
* A Szolgáltatásoldali alkalmazáshoz, és vissza echo küldött adatok fogadására.

A kód bemutatja, egy eszköz stream, valamint hogyan használható a adatokat küldeni és fogadni a kezdeményezés folyamatán.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Előzetes verziójának eszköz Streamek jelenleg csak az IoT hub, az alábbi régiókban létrehozott támogatott:

  * USA középső régiója
  * USA középső RÉGIÓJA – EUAP

* Telepítés [Visual Studio 2017](https://www.visualstudio.com/vs/) az a [asztali fejlesztés C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) számítási feladat engedélyezve van.

* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

* Futtassa a következő parancsot az Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadja az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) – az Azure CLI parancsok.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban használni a [Azure IoT eszközoldali SDK-t a c nyelvhez készült](iot-hub-device-sdk-c-intro.md). A fejlesztési környezet klónozása és felépítéséhez használt előkészíti a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a Githubról. Az SDK a Githubon ebben a rövid útmutatóban használt példakód tartalmazza.

1. Töltse le a [CMake buildelési rendszert](https://cmake.org/download/).

    A CMake telepítése előtt fontos, hogy a Visual Studio Előfeltételek (Visual Studio és a *asztali fejlesztés C++*  számítási feladat) vannak telepítve a gépen. Miután az előfeltételek teljesülnek, és a letöltés ellenőrzése, a CMake buildelési rendszert is telepítheti.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Ez a művelet néhány percet vesz igénybe.

3. Hozzon létre egy *cmake* alkönyvtárat, módon az a következő parancsot, majd lépjen abba a Git-tárház gyökérkönyvtárában található.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Futtassa a következő parancsokat a *cmake* könyvtár az SDK fejlesztési ügyfélplatform adott verzióját.

   * A Linux:

      ```bash
      cmake ..
      make -j
      ```

   * A Windows a következő parancsokat a fejlesztői parancssort a Visual Studio 2015 vagy 2017. A Visual Studio-megoldást a szimulált eszköz jönnek a *cmake* könyvtár.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Regisztrálnia kell egy eszközt az IoT hubbal, hogy csatlakozhasson. Ebben a szakaszban az Azure Cloud Shellben használja az [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) regisztrálni egy szimulált eszközt.

1. Hozza létre az eszközidentitást, a Cloud Shellben futtassa az alábbi parancsot:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.
   > * Használat *Sajáteszköz*látható módon. A regisztrált eszköz a megadott név legyen. Ha úgy dönt, hogy az eszköz egy másik nevet, használja során ez a cikk ezt a nevet, és az eszköz nevére a mintaalkalmazásból ahhoz, hogy futtatni őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Az első a *eszköz kapcsolati karakterláncának* az imént regisztrált eszközhöz, futtassa az alábbi parancsokat a Cloud Shellben:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Megjegyzés: az eszköz kapcsolati karakterláncának későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Az eszköz és az eszköz Streamek keresztül a szolgáltatás közötti kommunikációhoz

Ebben a szakaszban az eszközoldali alkalmazás, mind a Szolgáltatásoldali alkalmazás futtatásához, és a kettő közötti kommunikációhoz.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Az eszközoldali alkalmazás futtatásához, tegye a következőket:

1. Az eszköz hitelesítő adatok szerkesztésével a *iothub_client_c2d_streaming_sample.c* a fájlt a *iothub_client/samples/iothub_client_c2d_streaming_sample* mappát, és ezután biztosítása az eszköz kapcsolati karakterláncát.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. A kód fordításához a következőképpen:

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

3. A lefordított program futtatása:

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

### <a name="run-the-service-side-application"></a>A Szolgáltatásoldali alkalmazás futtatása

Ahogy korábban említettük, az IoT Hub C SDK eszköz Streamek csak az eszköz oldalán támogatja. Alkalmazás összeállítása és futtatása a Szolgáltatásoldali, kövesse az utasításokat az alábbi rövid útmutatók egyikében:

* [Egy eszköz alkalmazáshoz a kommunikációhoz C# keresztül az IoT Hub eszköz Streamek](./quickstart-device-streams-echo-csharp.md)
* [Egy eszköz alkalmazás Node.js-ben az IoT Hub eszköz Streamek keresztül való kommunikáció során](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, már állítsa be az IoT hub, regisztrált egy eszközt, egy eszköz stream a C alkalmazás az eszközön és a egy másik alkalmazás Szolgáltatásoldali között létrehozott és a stream használt oda-vissza az alkalmazások közötti adatküldéshez.

Eszköz Streamek kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
