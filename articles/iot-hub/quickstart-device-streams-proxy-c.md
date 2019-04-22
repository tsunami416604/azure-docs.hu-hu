---
title: Az Azure IoT Hub eszköz C adatfolyamok rövid SSH vagy RDP (előzetes verzió) |} A Microsoft Docs
description: Ebben a rövid útmutatóban egy minta C alkalmazás, amely proxyként működik az RDP/SSH-forgatókönyvek engedélyezése az IoT Hub eszköz adatfolyam felett fog futtatni.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1468268e407eeac6196c8e8e4db0fc5a52ca09c7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501569"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Gyors útmutató: Az IoT Hub eszköz Streamek C-proxyalkalmazást (előzetes verzió) használatával feletti SSH/RDP

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. Lásd: [ezt oldal](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) áttekintését, a telepítés.

Ez a dokumentum ismerteti a telepítés SSH-forgalom (22-es port használatával) keresztül eszköz Streamek bújtatás. A telepítő az RDP-forgalmat hasonló, és egyszerű konfigurációmódosítás szükséges. Mivel az eszköz Streamek alkalmazás és a protokoll független, a jelen rövid útmutatóban módosíthatják (a kommunikációs portok módosítása) más típusú alkalmazás forgalmának befogadásához.

## <a name="how-it-works"></a>Hogyan működik?

Az alábbi ábra bemutatja, hogyan az eszköz és szolgáltatás helyi proxy programok engedélyezi az SSH-ügyfél és az SSH démon folyamatok között teljes körű kapcsolat beállítása. A nyilvános előzetes verzióban az C SDK-t csak támogatja eszköz Streamek eszköz oldalán. Ez a rövid útmutató ennek eredményeképpen csak az eszköz helyi proxy alkalmazás futtatásához útmutatást terjed ki. Kísérő proxy szolgáltatás helyi alkalmazások, amelyek érhető el kell futtatásakor [ C# rövid](./quickstart-device-streams-proxy-csharp.md) vagy [Node.js rövid](./quickstart-device-streams-proxy-nodejs.md) útmutatók.

![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "helyi proxy telepítése")

1. Szolgáltatás helyi proxy csatlakozik az IoT hubhoz, és kezdeményezi a céleszköz eszköz adatfolyam.

2. Eszköz helyi proxy a stream kezdeményezés kézfogás befejeződött, és az IoT Hub a Szolgáltatásoldali streamvégponton keresztül egy teljes körű streamelési alagutat létesít.

3. Eszköz helyi proxy csatlakozik-e az SSH démon (SSHD) az eszközön a 22-es porton (leírt módon konfigurálható, azt [alábbi](#run-the device-local-proxy-application)).

4. Szolgáltatás helyi proxy várja az új SSH-kapcsolatok a felhasználó által a kijelölt porton, amely ebben az esetben 2222-es port (ez történik akkor is konfigurálható, amint [alábbi](#run-the-device-local-proxy-application)). SSH-ügyfél-n keresztül csatlakozó felhasználó, az alagút lehetővé teszi, hogy SSH-alkalmazás forgalmat át kell helyezni az SSH-ügyfél és kiszolgáló programok között.

> [!NOTE]
> SSH-forgalmat küld egy eszköz stream keresztül fog bújtatni küld a rendszer közvetlenül a szolgáltatás és eszköz közötti helyett az IoT Hub streamvégponton keresztül. Ez [értékelemeket](./iot-hub-device-streams-overview.md#benefits). Ezenkívül az ábra az SSH démon az ugyanazon az eszközön (vagy a gépen), az eszköz helyi proxy. Ebben a rövid útmutatóban az IP-címét az SSH démon lehetővé teszi, hogy eszköz helyi proxy és a démont, valamint a különböző gépeken futhatnak.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Előzetes verziójának eszköz Streamek jelenleg csak a az IoT-központok létrehozni a következő régiókban támogatott:

  * **USA középső RÉGIÓJA**
  * **USA középső RÉGIÓJA – EUAP**

* Telepítse a [Visual Studio 2017](https://www.visualstudio.com/vs/)-et, amelyben engedélyezve van az [„Asztali fejlesztés C++ használatával”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) tevékenységprofil.
* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.
* Futtassa a következő parancsot a Microsoft Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadása Azure CLI-vel az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) parancsok.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

A rövid útmutató céljaira a [C nyelvhez készült Azure IoT eszközoldali SDK-t](iot-hub-device-sdk-c-intro.md) fogjuk használni. A fejlesztési környezet klónozása és felépítéséhez használt előkészítésével foglalkozunk a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a Githubról. A rövid útmutató mintakódokat az SDK tartalmazza a GitHubon. 

1. Töltse le a [CMake buildelési rendszert](https://cmake.org/download/).

    Fontos, hogy a Visual Studio előfeltételei (Visual Studio és az „Asztali fejlesztés C++ használatával” számítási feladat) telepítve legyenek a gépen, **mielőtt** megkezdené a `CMake` telepítését. Ha az előfeltételek telepítve vannak, és ellenőrizte a letöltött fájlt, telepítse a CMake buildelési rendszert.

2. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    Ez a művelet várhatóan több percig is eltarthat.

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

1. Futtassa a következő parancsot az Azure Cloud Shellben, hozza létre az eszközidentitást.

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

   **Sajáteszköz**: Ez az eszköz a megadott név. Használjon Sajáteszköz látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
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

## <a name="ssh-to-a-device-via-device-streams"></a>Ssh-n keresztül az eszköz Streamek eszköz

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxy-alkalmazások futtatása

1. A forrásfájl szerkesztése `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` , és adja meg az eszköz kapcsolati karakterláncát, a céleszközön IP/állomásnév és a 22-es SSH-port:

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. Fordítsa le a mintát:

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

3. Az eszközön a lefordított program futtatása:

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

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxy-alkalmazások futtatása

Írtaknak [korábban](#how-it-works), egy teljes körű stream való SSH-forgalmat létrehozó megköveteli egy helyi proxykiszolgáló mindkét végén (mind a szolgáltatás és az eszköz). A nyilvános előzetes verzióban IoT Hub C SDK-t csak támogatja eszköz Streamek eszköz oldalán. Hozza létre, és futtassa a szolgáltatás helyi proxy, lépésekkel érhető el a [ C# rövid](./quickstart-device-streams-proxy-csharp.md) vagy a [Node.js rövid](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>SSH-munkamenetet létesít

Ha mindkét az eszköz és szolgáltatás helyi proxyk futnak, az SSH-ügyfél programot használ, és csatlakozzon a 2222-es port (és nem az SSH démon közvetlenül) a helyi szolgáltatási proxyt.

```cmd/sh
ssh <username>@localhost -p 2222
```

Ezen a ponton, megjelenik a hitelesítő adatait az SSH-bejelentkezési felület.

Az eszköz helyi proxy, amelyhez csatlakozik, az SSH démon a konzolkimenetet `IP_address:22`: ![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "eszköz helyi proxy kimenet")

Az SSH-ügyfél program konzolkimenetet (az SSH démon, amely a szolgáltatás helyi proxy figyel a következőn: csatlakozik a 22-es portot SSH-ügyfél kommunikál): ![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "SSH-ügyfél kimenete")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, rendelkezik beállítása egy IoT hubot, regisztrált egy eszközt, egy eszköz - és szolgáltatás helyi proxy program létesíteni egy eszköz stream IoT hubon keresztül telepített és a proxyk használt SSH-forgalom.

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
