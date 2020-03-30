---
title: Az Azure IoT Hub-eszköz c-gyorsindítást indít ssh-hoz és RDP-hez
description: Ebben a rövid útmutatóban futtat egy C-mintaalkalmazást, amely proxyként működik az SSH- és RDP-forgatókönyvek IoT Hub-eszközstreameken való engedélyezéséhez.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: af303928490d2570fa9430bd4b9d30c4a49df304
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675490"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Rövid útmutató: SSH és RDP engedélyezése Egy IoT Hub-eszközadatfolyamon egy C proxyalkalmazás használatával (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Az Azure IoT Hub jelenleg támogatja az eszközstreamelési [funkciót előnézeti funkcióként.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Az IoT Hub eszközadatfolyamok](./iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát kommunikációját. A beállítás áttekintését a [Helyi proxyminta lapon találja.](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)

Ez a rövid útmutató ismerteti a secure shell (SSH) forgalom bújtatásának beállítását (a 22-es port használatával) az eszközadatfolyamokon keresztül. Az RDP-forgalom beállítása hasonló, és egyszerű konfigurációmódosítást igényel. Mivel az eszközfolyamok alkalmazás- és protokollfüggetlenek, módosíthatja ezt a rövid útmutatót más típusú alkalmazásforgalom nak megfelelően.

## <a name="how-it-works"></a>Működés

Az alábbi ábra azt mutatja be, hogy az eszköz- és szolgáltatás-helyi proxyprogramok hogyan engedélyezik az SSH-ügyfél és az SSH démonfolyamatok végpontok közötti kapcsolatát. A nyilvános előzetes verzió során a C SDK csak az eszköz oldalán támogatja az eszközstreameléseket. Ennek eredményeképpen ez a rövid útmutató csak az eszköz-helyi proxyalkalmazás futtatására vonatkozó utasításokat ismerteti. A kapcsolódó szolgáltatásoldali alkalmazás létrehozásához és futtatásához kövesse az alábbi rövid útmutatók egyikének utasításait:

* [SSH/RDP ioT Hub-eszközön c# proxy használatával](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP ioT Hub-eszközadatfolyamokon keresztül A NodeJS proxy használatával.](./quickstart-device-streams-proxy-nodejs.md)

![Helyi proxy beállítása](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. A szolgáltatás-helyi proxy csatlakozik az IoT hubhoz, és elindítja az eszköz adatfolyamot a céleszközre.

2. Az eszköz-helyi proxy befejezi az adatfolyam-kezdeményezési kézfogást, és létrehoz egy végpontok közötti streamelési alagutat az IoT hub streamelési végpontján keresztül a szolgáltatás oldalra.

3. Az eszköz-helyi proxy csatlakozik az SSH démon, amely figyeli a port 22 az eszközön. Ez a beállítás konfigurálható, az "Eszközhelyi proxyalkalmazás futtatása" című szakaszban leírtak szerint.

4. A szolgáltatás-helyi proxy vár az új SSH-kapcsolatok a felhasználó figyelegy kijelölt port, amely ebben az esetben a port 2222. Ez a beállítás konfigurálható, az "Eszközhelyi proxyalkalmazás futtatása" című szakaszban leírtak szerint. Amikor a felhasználó SSH-ügyfélen keresztül csatlakozik, a bújtatás lehetővé teszi az SSH alkalmazásforgalom átvitelét az SSH-ügyfél és a kiszolgálói programok között.

> [!NOTE]
> Az eszközadatfolyamon keresztül küldött SSH-forgalom az IoT hub streamelési végpontján keresztül bújtat, nem pedig közvetlenül a szolgáltatás és az eszköz között. További információt az [Iot Hub eszközadatfolyamok használatának előnyeiben](iot-hub-device-streams-overview.md#benefits)talál. Továbbá az ábra azt mutatja, hogy az SSH démon fut ugyanazon az eszközön (vagy gépen), mint az eszköz-helyi proxy. Ebben a rövid útmutatóban az SSH démon IP-cím ének megadása lehetővé teszi, hogy az eszközhelyi proxy és a démon különböző gépeken is fusson.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT-központok számára támogatott:

  * USA középső régiója
  * USA középső régiója
  * Észak-Európa
  * Délkelet-Ázsia

* Telepítse a [Visual Studio 2019-et](https://www.visualstudio.com/vs/) az Asztali fejlesztés funkcióval, ha a [C++](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) munkaterhelés engedélyezve van.
* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

* Futtassa a következő parancsot az Azure CLI-hez való Azure IoT-bővítmény hozzáadásához a Cloud Shell-példányhoz. Az IOT-bővítmény iot hub, IoT Edge és IoT-eszközlétesítési szolgáltatás (DPS)-specifikus parancsokat ad hozzá az Azure CLI-hez.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ehhez a rövid útmutatóhoz az [Azure IoT-eszköz SDK C-hez.](iot-hub-device-sdk-c-intro.md) Az [Azure IoT C SDK-t](https://github.com/Azure/azure-iot-sdk-c) a GitHubról klónozásra és létrehozásra használt fejlesztői környezetet készít. A GitHub SDK-ja tartalmazza az ebben a rövid útmutatóban használt mintakódot.

1. Töltse le a [CMake build rendszert](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az *asztali fejlesztés C++ munkaterheléssel)* telepítve legyenek a számítógépen, *mielőtt* elkezdené a CMake telepítését. Miután az előfeltételek a helyükön vannak, és a letöltés ellenőrzése megtörtént, telepítheti a CMake build rendszert.

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

   * Windows rendszerben futtassa a következő parancsokat a Visual Studio 2015 vagy 2017 Fejlesztői parancssorában. A szimulált eszköz höz visual studio-megoldás jön létre a *cmake* könyvtárban.

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

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban az Azure Cloud Shell t az [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) használatával regisztrálhat egy szimulált eszközt.

1. Az eszközidentitás létrehozásához futtassa a következő parancsot a Cloud Shell ben:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.
   > * A regisztrálandó eszköz nevéhez ajánlott a *MyDevice* használata az ábrán látható módon. Ha más nevet választ az eszközhöz, használja ezt a nevet a cikkben, és frissítse az eszköz nevét a mintaalkalmazásokban, mielőtt futtatja őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Az imént regisztrált eszköz *eszközkapcsolati karakterláncának* lekérnie, futtassa a következő parancsokat a Cloud Shellben:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Vegye figyelembe a visszaadott eszköz kapcsolati karakterláncot későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH készülékre eszközstreameken keresztül

Ebben a szakaszban hozzon létre egy végpontok között az SSH-forgalmat.

### <a name="run-the-device-local-proxy-application"></a>Az eszközhelyi proxyalkalmazás futtatása

1. A forrásfájl szerkesztése **iothub_client_c2d_streaming_proxy_sample.c** fájlt a mappában `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`, és adja meg az eszköz kapcsolati karakterláncát, a céleszköz IP/állomásnevét és a 22-es SSH-portot:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Állítsa össze a mintát:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Futtassa a lefordított programot az eszközön:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás-helyi proxyalkalmazás futtatása

Ahogy azt a "Hogyan működik" szakaszban tárgyalt, létrehozása egy végpontok közötti stream alagút SSH-forgalom hoz egy helyi proxy mindkét végén (mind a szolgáltatás és az eszköz oldalán). Nyilvános előzetes verzió során az IoT Hub C SDK csak az eszköz oldalán támogatja az eszközstreameléseket. A szolgáltatáshelyi proxy létrehozásához és futtatásához kövesse az alábbi rövid útmutatók utasításait:

   * [SSH/RDP az IoT Hub-eszközök streamjein C# proxyalkalmazások használatával](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP ioT Hub-eszközstreameken keresztül a Node.js proxyalkalmazások használatával](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>SSH-munkamenet létrehozása

Miután mind az eszköz- mind a szolgáltatás-helyi proxyk futnak, használja az SSH-ügyfélprogramot, és csatlakozzon a 2222-es port szolgáltatáshelyi proxyjához (közvetlenül az SSH démon helyett).

```cmd/sh
ssh {username}@localhost -p 2222
```

Ezen a ponton az SSH bejelentkezési ablak kéri, hogy adja meg a hitelesítő adatokat.

Az alábbi képen a konzol kimenete látható az eszköz helyi proxyján, amely `IP_address:22`az SSH démonhoz csatlakozik:

![Eszközhelyi proxykimenet](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Az alábbi képen az SSH-ügyfélprogram konzolkimenete látható. Az SSH-ügyfél a 22-es porthoz való csatlakozással kommunikál az SSH démonnal, amelyet a szolgáltatáshelyi proxy figyel:

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállít egy IoT hubot, regisztrált egy eszközt, telepített egy eszközt és egy szolgáltatás-helyi proxyt az IT Hubon keresztüli eszközadatfolyam létrehozásához, és a proxykat használta az SSH-forgalom alagttunneléhez.

Az eszközstreamekről az:

> [!div class="nextstepaction"]
> [Eszközadatfolyamok – áttekintés](./iot-hub-device-streams-overview.md)
