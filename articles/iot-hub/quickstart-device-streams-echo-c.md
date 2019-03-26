---
title: Azure IoT Hub eszköz C adatfolyamok rövid útmutató (előzetes verzió) |} A Microsoft Docs
description: Ebben a rövid útmutatóban fog futtatni, amely kommunikál, C Szolgáltatásoldali-alkalmazások egy IoT-eszközzel rendelkező eszköz adatfolyam-n keresztül.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 026e072d6e5ac65d957b272300898de3dc9099a9
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438274"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Gyors útmutató: Egy eszköz alkalmazás a C IoT Hub eszköz adatfolyamok (előzetes verzió) használatával való kommunikáció során

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. A nyilvános előzetes verzióban az C SDK-t csak támogatja eszköz Streamek eszköz oldalán. Ez a rövid útmutató ennek eredményeképpen csak az eszközoldali alkalmazás futtatásához útmutatást terjed ki. Futtatnia kell a hozzájuk tartozó Szolgáltatásoldali alkalmazás, amely a [ C# rövid](./quickstart-device-streams-echo-csharp.md) vagy [Node.js rövid](./quickstart-device-streams-echo-nodejs.md).

Ebben a rövid útmutatóban az eszközoldali C alkalmazás van a következő funkciókat:

* Egy eszköz stream IoT-eszközökön való létrehozásához.

* A Szolgáltatásoldali és vissza echo által küldött adatok fogadására.

Bemutatjuk, hogy a kód egy eszköz stream, valamint hogyan használható a adatokat küldeni és fogadni a kezdeményezés folyamatán.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Előzetes verziójának eszköz Streamek jelenleg csak a az IoT-központok létrehozni a következő régiókban támogatott:

  * **USA középső RÉGIÓJA**
  * **USA középső RÉGIÓJA – EUAP**

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/vs/)-et, amelyben engedélyezve van az [„Asztali fejlesztés C++ használatával”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) tevékenységprofil.
* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

A rövid útmutató céljaira a [C nyelvhez készült Azure IoT eszközoldali SDK-t](iot-hub-device-sdk-c-intro.md) fogjuk használni. A fejlesztési környezet klónozása és felépítéséhez használt előkészítésével foglalkozunk a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a Githubról. A rövid útmutató mintakódokat az SDK tartalmazza a GitHubon. 

1. Töltse le a [CMake buildelési rendszert](https://cmake.org/download/). Ellenőrizze a letöltött bináris fájlt, amely megfelel a verzió letöltése kriptográfiai kivonatokat értékének használatával. A kriptográfiai kivonatokat értékei is az adott CMake letöltési hivatkozás található.

    Az alábbi példa a kriptográfiai kivonatokat a x64 3.13.4 verziójának ellenőrzéséhez használja a Windows PowerShell MSI terjesztési:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.13.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "64AC7DD5411B48C2717E15738B83EA0D4347CD51B940487DFF7F99A870656C09"
    True
    ```

    A következő kivonatértékeket verzió 3.13.4 írásának időpontjában a CMake webhelyen felsorolt:

    ```
    563a39e0a7c7368f81bfa1c3aff8b590a0617cdfe51177ddc808f66cc0866c76  cmake-3.13.4-Linux-x86_64.tar.gz
    7c37235ece6ce85aab2ce169106e0e729504ad64707d56e4dbfc982cb4263847  cmake-3.13.4-win32-x86.msi
    64ac7dd5411b48c2717e15738b83ea0d4347cd51b940487dff7f99a870656c09  cmake-3.13.4-win64-x64.msi
    ```

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Az adattár mérete jelenleg körülbelül 220 MB.

3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Futtassa a következő parancsokat a `cmake` könyvtár az SDK-t a fejlesztési fejlesztésiügyfél-platformhoz konkrét verzióját.

   * A Linux:

      ```bash
      cmake ..
      make -j
      ```

   * A Windows a következő parancsokat a fejlesztői parancssort a Visual Studio 2015 vagy 2017. A szimulált eszközhöz tartozó Visual Studio-megoldás a `cmake` könyvtárban jön létre.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban az Azure Cloud Shellt fogja használni az [IoT-bővítménnyel](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) a szimulált eszköz regisztrálásához.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához. 

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

   **Sajáteszköz**: Ez az eszköz a megadott név. Használjon Sajáteszköz látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Jegyezze fel az eszköz kapcsolati karakterláncot, amely a következő példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="communicate-between-device-and-service-via-device-streams"></a>Eszköz és szolgáltatás keresztül eszköz adatfolyamok közötti kommunikáció

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Az eszközoldali alkalmazás futtatásához, meg kell hajtsa végre az alábbi lépéseket:

1. Adja meg az eszköz hitelesítő adatokat a forrás fájl szerkesztésével `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` és biztosítják, hogy az eszköz kapcsolati karakterláncát.

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

Amint azt korábban említettük, a IoT Hub C SDK-t csak az eszközoldalon támogatja a eszköz Streamek. Alkalmazás összeállítása és futtatása a Szolgáltatásoldali, lépésekkel érhető el a [ C# rövid](./quickstart-device-streams-echo-csharp.md) vagy a [Node.js rövid](./quickstart-device-streams-echo-nodejs.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, rendelkezik állítsa be az IoT hub, regisztrált egy eszközt, egy eszköz stream a C alkalmazás az eszközön és a egy másik alkalmazás Szolgáltatásoldali között létrehozott és a stream használt oda-vissza az alkalmazások közötti adatküldéshez.

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)