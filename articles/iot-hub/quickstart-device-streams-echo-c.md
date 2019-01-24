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
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 149d3e3515c8bbc10798ad26e58dcefbe830f9bc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830221"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Gyors útmutató: Egy eszköz alkalmazás a C IoT Hub eszköz adatfolyamok (előzetes verzió) használatával való kommunikáció során

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. A nyilvános előzetes verzióban az C SDK-t csak támogatja eszköz Streamek eszköz oldalán. Ez a rövid útmutató ennek eredményeképpen csak az eszközoldali alkalmazás futtatásához útmutatást terjed ki. Futtatnia kell a hozzájuk tartozó Szolgáltatásoldali alkalmazás, amely [ C# rövid](./quickstart-device-streams-echo-csharp.md) vagy [Node.js rövid](./quickstart-device-streams-echo-nodejs.md) útmutatók.

Ebben a rövid útmutatóban az eszközoldali C alkalmazás van a következő funkciókat:

* Egy eszköz stream IoT-eszközökön való létrehozásához.

* A Szolgáltatásoldali és vissza echo által küldött adatok fogadására.

Bemutatjuk, hogy a kód a kezdeményezés folyamatát egy eszköz stream, valamint hogyan küldhet és fogadhat adatokat használhatja.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/vs/)-et, amelyben engedélyezve van az [„Asztali fejlesztés C++ használatával”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) tevékenységprofil.
* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

A rövid útmutató céljaira a [C nyelvhez készült Azure IoT eszközoldali SDK-t](iot-hub-device-sdk-c-intro.md) fogjuk használni. A fejlesztési környezet klónozása és felépítéséhez használt előkészítésével foglalkozunk a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a Githubról. A rövid útmutató mintakódokat az SDK tartalmazza a GitHubon. 


1. 3.11.4 verzióját töltse le a [CMake buildelési rendszert](https://cmake.org/download/). Ellenőrizze a letöltött bináris fájlt a megfelelő titkosítási kivonat értékének használatával. Az alábbi példa a Windows PowerShell használatával ellenőrizte az x64 MSI disztribúció 3.11.4-es verziójának titkosítási kivonatát:

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

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Az adattár mérete jelenleg körülbelül 220 MB.

3. Hozzon létre egy `cmake` alkönyvtárat a Git-adattár gyökérkönyvtárában, és lépjen erre a mappára. 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. Futtassa az alábbi parancsot, amely létrehozza az SDK fejlesztői ügyfélplatformra szabott verzióját. A Windows, a Visual Studio-megoldást a szimulált eszköz jönnek a `cmake` könyvtár. 

```
    # In Linux
    cmake -Denable_streaming=ON ..
    make -j
```

A Windows a következő parancsokat a Developer-parancssorból a Visual Studio 2015 vagy 2017-es üzenet számára:

```
    # In Windows
    # For VS2015
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2017

    # Then build the project
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
- A fejlesztési környezet beállítása a jelen szakaszban szerint [eszköz Streamek ismertető cikkben](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c).

- Adja meg az eszköz hitelesítő adatokat a forrás fájl szerkesztésével `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` , és adja meg az eszköz kapcsolati karakterláncát.
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- A kód fordításához a következőképpen:

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- A lefordított program futtatása:

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>A Szolgáltatásoldali alkalmazás futtatása

Ahogy korábban említettük, IoT Hub C SDK-t csak támogatja az eszköz Streamek eszköz oldalán. A Szolgáltatásoldali alkalmazás használja a accompaying szolgáltatás-programokat elérhető [ C# rövid](./quickstart-device-streams-echo-csharp.md) vagy [Node.js rövid](./quickstart-device-streams-echo-nodejs.md) útmutatók.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban rendelkezik beállítása egy IoT hubot, regisztrált egy eszközt, szimulált telemetriát küldött a hubra a C alkalmazás használatával és a telemetriai adatok olvasása a hubról, az Azure Cloud Shell használatával.

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)