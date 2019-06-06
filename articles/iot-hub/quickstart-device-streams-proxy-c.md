---
title: Az Azure IoT Hub eszköz C adatfolyamok rövid SSH és az RDP (előzetes verzió) |} A Microsoft Docs
description: Ebben a rövid útmutatóban futtat egy minta C alkalmazás, amely proxyként való SSH- és RDP-forgatókönyvek az IoT Hub eszköz adatfolyamokat.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: e21d7381831553f8d82b00d9ed3be0b03f13bca9
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735124"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Gyors útmutató: SSH és az RDP engedélyezése egy IoT Hub eszköz stream felett a C-proxyalkalmazást (előzetes verzió) használatával

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Az Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. A telepítő áttekintését lásd: [a helyi Proxy mintaoldalt](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Ez a rövid útmutató azt ismerteti, a Secure Shell (SSH) forgalmat (22-es port használatával) keresztül eszköz Streamek bújtatás beállítása. A telepítő a távoli asztal protokoll (RDP) forgalmat hasonló, és egyszerű konfigurációmódosítás szükséges. Mivel az eszköz Streamek alkalmazás - és a protokoll-független, ez a rövid útmutató más típusú alkalmazás forgalmának befogadásához módosíthatja.

## <a name="how-it-works"></a>Működés

A következő ábra azt szemlélteti, hogyan lehetővé az eszköz és szolgáltatás helyi proxy programok, végpontok közötti kapcsolat az SSH-ügyfél és az SSH démon folyamatok között. A nyilvános előzetes verzióban az C SDK-t csak az eszköz oldalán eszköz Streamek támogatja. Ennek eredményeképpen az ebben a rövid útmutatóban csak az eszköz helyi proxy alkalmazás futtatásához útmutatást ismerteti. Futtassa az alábbi Szolgáltatásoldali rövid útmutatókat egyikét:

* [IoT Hub device feletti SSH vagy RDP adatfolyamok használatával C# proxy](./quickstart-device-streams-proxy-csharp.md)
* [NodeJS-proxy használata az IoT Hub eszköz Streamek feletti SSH vagy RDP](./quickstart-device-streams-proxy-nodejs.md).

![Helyi proxy telepítése](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. A szolgáltatás helyi proxy csatlakozik az IoT hubhoz, és elindítja a céleszköz eszköz adatfolyam.

2. Az eszköz helyi proxy a stream kezdeményezés kézfogás befejeződött, és az IoT hub a Szolgáltatásoldali streamvégponton keresztül egy teljes körű streamelési alagutat létesít.

3. Az eszköz helyi proxy csatlakozik az SSH démon, amely az eszköz a 22-es porton figyel. Ez a beállítás nem konfigurálható, a "Futtatása az eszköz helyi proxyalkalmazást" szakaszban leírtak szerint.

4. A szolgáltatás helyi proxy megvárja a felhasználó új SSH-kapcsolatokat, a kijelölt porton, amely ebben az esetben 2222-es port. Ez a beállítás nem konfigurálható, a "Futtatása az eszköz helyi proxyalkalmazást" szakaszban leírtak szerint. Amikor a felhasználó keresztül az SSH-ügyfél kapcsolódik, az alagút lehetővé teszi, hogy SSH-alkalmazás forgalmat át kell helyezni az SSH-ügyfél és kiszolgáló programok között.

> [!NOTE]
> SSH-forgalmat, egy eszköz stream keresztül küldött az IoT hub folyamatos átviteli végponton keresztül bújtatott helyett közvetlenül a szolgáltatás és az eszközök között küldött. További információkért lásd: a [az Iot Hub eszköz Streamek előnyei](iot-hub-device-streams-overview.md#benefits). Ezenkívül az ábra az SSH démon, amelyen fut az ugyanazon az eszközön (vagy a gép), az eszköz helyi proxyként. Az ebben a rövid útmutatóban az SSH démon IP-cím megadása lehetővé teszi az eszköz helyi proxy- és a démont, valamint a különböző gépeken futhatnak.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Előzetes verziójának eszköz Streamek jelenleg csak az IoT hub, az alábbi régiókban létrehozott támogatott:

  * USA középső régiója
  * USA középső RÉGIÓJA – EUAP

* Telepítse [Visual Studio 2019](https://www.visualstudio.com/vs/) az a [asztali fejlesztés C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) számítási feladat engedélyezve van.
* Telepítse a [Git](https://git-scm.com/download/) legújabb verzióját.

* Futtassa a következő parancsot az Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadja az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) – az Azure CLI parancsok.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban használni a [Azure IoT eszközoldali SDK-t a c nyelvhez készült](iot-hub-device-sdk-c-intro.md). A fejlesztési környezet klónozása és felépítéséhez használt előkészíti a [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) a Githubról. Az SDK a Githubon ebben a rövid útmutatóban használt példakód tartalmazza.

1. Töltse le a [CMake buildelési rendszert](https://cmake.org/download/).

    Fontos, hogy a Visual Studio Előfeltételek (Visual Studio és a *asztali fejlesztés C++*  számítási feladat) vannak telepítve a gépén, *előtt* a CMake telepítésének megkezdése. Miután az előfeltételek teljesülnek, és a letöltés ellenőrzése, a CMake buildelési rendszert is telepítheti.

1. Nyisson meg egy parancssort vagy a Git Bash-felületet. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Ez a művelet néhány percet vesz igénybe számíthat.

1. Hozzon létre egy *cmake* a Git-adattár, ahogyan az a következő parancs a gyökérkönyvtár alkönyvtára, és navigáljon ahhoz a mappához.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a *cmake* könyvtár az SDK fejlesztési ügyfélplatform adott verzióját.

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

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban az Azure Cloud Shellben használja az [IoT-bővítmény](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) regisztrálni egy szimulált eszközt.

1. Hozza létre az eszközidentitást, a Cloud Shellben futtassa az alábbi parancsot:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.
   > * Használat *Sajáteszköz*látható módon. A regisztrált eszköz a megadott név legyen. Ha úgy dönt, hogy az eszköz egy másik nevet, használja során ez a cikk ezt a nevet, és az eszköz nevére a mintaalkalmazásból ahhoz, hogy futtatni őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Az első a *eszköz kapcsolati karakterláncának* az imént regisztrált eszközhöz, futtassa az alábbi parancsokat a Cloud Shellben:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Megjegyzés: az eszköz kapcsolati karakterláncának későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Ssh-n keresztül az eszköz Streamek eszköz

Ebben a szakaszban kapcsolatot hoz létre az SSH-forgalom egy teljes körű stream.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxy-alkalmazások futtatása

1. A forrásfájl szerkesztése *iothub_client_c2d_streaming_sample.c* mappában *iothub_client/samples/iothub_client_c2d_streaming_sample*, és adja meg az eszköz kapcsolati karakterláncát, cél az eszköz IP/állomásnév, és a 22-es SSH-port:

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Fordítsa le a mintát:

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

1. Az eszközön a lefordított program futtatása:

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

A "Működését" szakaszban leírtaknak egy helyi proxykiszolgáló mindkét végén (az a szolgáltatás és az eszköz oldalsó) egy teljes körű stream való SSH-forgalmat létrehozó igényel. A nyilvános előzetes verzióban az IoT Hub C SDK támogatja a Streamek eszköz csak az eszköz oldalán. Hozza létre, és futtassa a szolgáltatás helyi proxy, kövesse az utasításokat az alábbi rövid útmutatók egyikében:

   * [IoT Hub device feletti SSH vagy RDP adatfolyamok használatával C# proxy-alkalmazások](./quickstart-device-streams-proxy-csharp.md)
   * [Az IoT Hub eszköz adatfolyamok használata a Node.js alkalmazások feletti SSH/RDP](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>SSH-munkamenetet létesít

Ha mindkét az eszköz és szolgáltatás helyi proxyk futnak, az SSH-ügyfél programot használ, és csatlakozzon a 2222-es port (és nem az SSH démon közvetlenül) a helyi szolgáltatási proxyt.

```cmd/sh
ssh <username>@localhost -p 2222
```

Ezen a ponton az SSH bejelentkezési ablak kéri, hogy adja meg hitelesítő adatait.

Az alábbi képen látható a konzol kimenete az eszköz helyi proxy, amelyhez csatlakozik, az SSH démon a `IP_address:22`:

![Eszköz helyi proxy kimenet](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Az alábbi képen látható a konzol kimenete az SSH-ügyfél program. Az SSH démon csatlakozik a 22-es portot figyeli a szolgáltatás helyi proxy, amely kommunikál az SSH-ügyfél:

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, már beállítása egy IoT hubot, regisztrált egy eszközt, eszköz - és szolgáltatás helyi proxy program létesíteni egy eszköz stream IoT hubon keresztül telepített és a proxyk használt SSH-forgalom.

Eszköz Streamek kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
