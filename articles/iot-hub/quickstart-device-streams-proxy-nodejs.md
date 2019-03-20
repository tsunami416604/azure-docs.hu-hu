---
title: Az Azure IoT Hub device Node.js rövid adatfolyamok SSH vagy RDP (előzetes verzió) |} A Microsoft Docs
description: Ebben a rövid útmutatóban egy mintául szolgáló Node.js-alkalmazás, amely proxyként működik az RDP/SSH-forgatókönyvek engedélyezése az IoT Hub eszköz adatfolyam felett fog futtatni.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: a737413f6692b4ee811d0590351a385552cc9a8f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085575"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-nodejs-proxy-application-preview"></a>Gyors útmutató: Az SSH vagy RDP keresztül az IoT Hub eszköz adatfolyamok használata a Node.js-proxyalkalmazást (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. A rövid útmutató azt ismerteti, egy proxy futó Node.js alkalmazást Szolgáltatásoldali SSH és az RDP-t egy eszköz-adatfolyam felett az eszközre küldendő forgalom engedélyezéséhez végrehajtását. Lásd: [Itt](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) áttekintését, a telepítés. A nyilvános előzetes verzióban Node.js SDK csak támogatja eszköz Streamek Szolgáltatásoldali. A rövid útmutató ennek eredményeképpen csak utasítások futtatásához a szolgáltatás helyi proxy terjed ki. Egy kísérő eszköz helyi proxy, amely kell futtatásakor [C rövid](./quickstart-device-streams-proxy-c.md) vagy [ C# rövid](./quickstart-device-streams-proxy-csharp.md) útmutatók.

Először ismertetünk a telepítő az SSH (22-es port használatával). Ezután ismertetünk RDP (3389-es portot használó) a telepítés módosítása. Mivel az eszköz Streamek alkalmazás és a protokoll független, egyazon mintából módosítható (általában a a kommunikációs port módosításával) ügyfél-kiszolgáló alkalmazásforgalomba más típusú befogadásához.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

Előzetes verziójának eszköz Streamek jelenleg csak a az IoT-központok létrehozni a következő régiókban támogatott:

  - **USA középső RÉGIÓJA**
  - **USA középső RÉGIÓJA – EUAP**

Ebben a rövid útmutatóban a szolgáltatás helyi alkalmazás futtatásához szükséges a Node.js 4.x.x vagy újabb verzióját a fejlesztői gépére.

A Node.js-t a [nodejs.org](https://nodejs.org) oldalról töltheti le többféle platformra.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```
node --version
```

Ha még nem tette meg, töltse le a Node.js-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip címről, és csomagolja ki a ZIP-archívumot.


## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-node.md), kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]


## <a name="register-a-device"></a>Eszköz regisztrálása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-node.md), kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsokat az Azure Cloud Shellben az IoT Hub CLI-bővítmény hozzáadásához és az eszközidentitás létrehozásához. 

   **YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

   **Sajáteszköz**: Ez az eszköz a megadott név. Használjon Sajáteszköz látható módon. Ha úgy dönt, hogy eszközének egy másik nevet választ, akkor az egész cikkben azt a nevet kell használnia, és a mintaalkalmazások futtatása előtt frissítenie kell bennük az eszköznevet.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Szüksége van egy _szolgáltatáskapcsolati sztringre_ is azért, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

    **YourIoTHubName**: Alább a helyőrzőt cserélje le az IoT hub számára is választott nevét.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Jegyezze fel, a visszaadott érték, amely a következőhöz hasonló:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`


## <a name="ssh-to-a-device-via-device-streams"></a>Ssh-n keresztül az eszköz Streamek eszköz

### <a name="run-the-device-local-proxy"></a>Az eszköz helyi proxy futtatása

Ahogy korábban említettük, IoT Hub Node.js SDK-t csak támogatja az eszköz Streamek Szolgáltatásoldali. A helyi eszközre alkalmazás esetében használja a kísérő eszköz proxy programok elérhető [C rövid](./quickstart-device-streams-proxy-c.md) vagy [ C# rövid](./quickstart-device-streams-proxy-csharp.md) útmutatók. Győződjön meg róla, az eszköz helyi proxy fut, a folytatás előtt a következő lépéssel.


### <a name="run-the-service-local-proxy"></a>Futtassa a helyi szolgáltatási proxy

Feltételezve, hogy a [eszköz helyi proxy](#run-the-device-local-proxy) van fut, az alábbi lépésekkel futtatása a Node.js-ben írt szolgáltatás helyi proxy.

- A proxy, az eszközön futó környezeti változókként adja meg a szolgáltatás hitelesítő adatai, a cél eszköz azonosítója, ahol az SSH démon fut és a port számát.
  ```
  # In Linux
  export IOTHUB_CONNECTION_STRING="<provide_your_service_connection_string>"
  export STREAMING_TARGET_DEVICE="MyDevice"
  export PROXY_PORT=2222

  # In Windows
  SET IOTHUB_CONNECTION_STRING=<provide_your_service_connection_string>
  SET STREAMING_TARGET_DEVICE=MyDevice
  SET PROXY_PORT=2222
  ```
  Az eszköz Azonosítóját és a kapcsolati karakterláncot a fenti értékek módosításához.

- Navigáljon a `Quickstarts/device-streams-service` a kicsomagolt projektet mappában, és futtassa a helyi szolgáltatási proxy.
  ```
  cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

  # Install the preview service SDK, and other dependencies
  npm install azure-iothub@streams-preview
  npm install

  # Run the service-local proxy application
  node proxy.js
  ```

### <a name="ssh-to-your-device-via-device-streams"></a>Ssh-n keresztül az eszköz adatfolyamok az eszköz
Linux rendszeren, SSH használatával futtassa `ssh $USER@localhost -p 2222` a terminálon. A Windows, használja a kedvenc SSH-ügyfél (például a putty. lépése).

Konzol SSH-munkamenet létrehozása után a szolgáltatás helyi kimeneti (a szolgáltatás helyi proxy figyeli a 2222-es port): ![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-nodejs/service-console-output.PNG "terminál SSH-kimenet")


Az SSH-ügyfél program konzolkimenetet (SSH-ügyfél kommunikál az SSH démon ahol szolgáltatás helyi proxy figyeli a 22-es porton csatlakozik): ![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.PNG "SSH-ügyfél kimenete")


### <a name="rdp-to-your-device-via-device-streams"></a>Az eszköz keresztül eszköz Streamek RDP-vel

Most már használhatja az RDP-ügyfélprogram, és csatlakozzon a szolgáltatási proxy 2222-es porton (Ez a korábban kiválasztott egy tetszőleges szabad portot volt).

> [!NOTE]
> Győződjön meg arról, hogy az eszköz proxy megfelelően konfigurálva az RDP és konfigurálva van a 3389-es RDP-portra.

![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.PNG "RDP-ügyfél csatlakozik a helyi szolgáltatási proxy.")


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid hogy állítsa be az IoT hub, regisztrált egy eszközt, és telepített egy szolgáltatási proxy program RDP és SSH-IoT-eszköz lehetővé teszi, hogy. Az RDP és SSH-forgalom lesz bújtatni IoT hubon keresztül eszköz adatfolyam keresztül. Ez kiküszöböli a közvetlen kapcsolat az eszközön.

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
