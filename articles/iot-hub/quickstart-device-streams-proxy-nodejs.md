---
title: Az Azure IoT Hub device Node.js rövid adatfolyamok az SSH és az RDP-t (előzetes verzió) |} A Microsoft Docs
description: Ebben a rövid útmutatóban futtat egy mintául szolgáló Node.js-alkalmazás, amely proxyként való SSH- és RDP-forgatókönyvek az IoT Hub eszköz adatfolyamokat.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1d77f3a10e286246e23ae522e95aa23c487cca2f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735111"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Gyors útmutató: SSH és az RDP engedélyezése egy IoT Hub eszköz adatfolyam felett egy Node.js-proxyalkalmazást (előzetes verzió) használatával

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. 

Ebben a rövid útmutatóban egy Node.js-proxyalkalmazást engedélyezheti a Secure Shell (SSH) és a távoli asztal protokoll (RDP) forgalmat egy eszköz-adatfolyam felett az eszközre küldendő Szolgáltatásoldali futtató végrehajtását ismerteti. A telepítő áttekintését lásd: [helyi Proxy minta](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

A nyilvános előzetes verzióban a Node.js SDK csak Szolgáltatásoldali eszköz Streamek támogatja. Ennek eredményeképpen Ez a rövid útmutató ismerteti a utasítások csak a helyi szolgáltatás proxy-alkalmazások futtatásához. Az eszköz helyi proxy alkalmazás futtatásához, tekintse meg:  

   * [SSH és az RDP engedélyezése az IoT Hub eszköz Streamek felett C proxy-alkalmazás használatával](./quickstart-device-streams-proxy-c.md)
   * [SSH és az RDP engedélyezése az IoT Hub eszköz Streamek keresztül használatával egy C# proxy-alkalmazások](./quickstart-device-streams-proxy-csharp.md)

Ez a cikk ismerteti a telepítés az SSH (22-es port használatával), és ezután ismerteti, hogyan lehet RDP (3389-es portot használó) a telepítés módosításához. Mivel az eszköz Streamek alkalmazás - és a protokoll-független, igazodik más típusú ügyfél és kiszolgáló alkalmazásforgalomba, általában a kommunikációs port módosításával egyazon mintából módosíthatja.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Előzetes verziójának eszköz Streamek jelenleg csak az IoT hub, az alábbi régiókban létrehozott támogatott:

  * USA középső régiója
  * USA középső RÉGIÓJA – EUAP

* Ebben a rövid útmutatóban a szolgáltatás helyi alkalmazás futtatásához szüksége Node.js v10.x.x vagy annál újabb verzió a fejlesztői gépére.
  * Töltse le [Node.js](https://nodejs.org) több platformon.
  * Ellenőrizze a fejlesztői gépen Node.js jelenlegi verziója a következő parancsot:

   ```
   node --version
   ```

* Adja hozzá az Azure IoT-bővítmény az Azure CLI-vel a Cloud Shell-példányhoz a következő parancs futtatásával. Az IOT-bővítmény hozzáadja az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) – az Azure CLI parancsok.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Ha ezt még nem tette meg, [töltse le a Node.js mintaprojektet](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) , és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha elvégezte az előző [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-node.md), kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha végrehajtotta [a rövid útmutató: Telemetria küldése egy eszközről IoT hubra](quickstart-send-telemetry-node.md), kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban használhatja az Azure Cloud Shell regisztrál egy szimulált eszközt.

1. Hozza létre az eszközidentitást, a Cloud Shellben futtassa az alábbi parancsot:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.
   > * Használat *Sajáteszköz*látható módon. A regisztrált eszköz a megadott név legyen. Ha úgy dönt, hogy az eszköz egy másik nevet, használja során ez a cikk ezt a nevet, és az eszköz nevére a mintaalkalmazásból ahhoz, hogy futtatni őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Engedélyezi a háttér-alkalmazás csatlakoztatása az IoT hubhoz, és az üzenetek beolvasására, is szüksége van egy *szolgáltatáskapcsolati karakterláncra*. Az alábbi parancs lekéri az IoT hub a karakterlánc:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Megjegyzés: a visszaadott érték az ebben a rövid útmutató későbbi használatra. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Ssh-n keresztül az eszköz Streamek eszköz

Ebben a szakaszban kapcsolatot hoz létre az SSH-forgalom egy teljes körű stream.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxy-alkalmazások futtatása

Ahogy korábban említettük, az IoT Hub Node.js SDK eszköz Streamek csak Szolgáltatásoldali támogatja. Az eszköz helyi alkalmazás használja a proxy alkalmazást, amely érhető el az alábbi rövid útmutatók egyikében:

   * [SSH és az RDP engedélyezése az IoT Hub eszköz Streamek felett C proxy-alkalmazás használatával](./quickstart-device-streams-proxy-c.md)
   * [SSH és az RDP engedélyezése az IoT Hub eszköz Streamek keresztül használatával egy C# proxy-alkalmazások](./quickstart-device-streams-proxy-csharp.md) 

Mielőtt a következő lépéssel folytatja, győződjön meg arról, hogy fut-e az eszköz helyi proxyalkalmazást.

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxy-alkalmazások futtatása

Az eszköz helyi proxy alkalmazás fut futtassa a szolgáltatás helyi proxy alkalmazás, amely a Node.js-ben írt, az alábbiak szerint:

1. A környezeti változók adja meg a szolgáltatás hitelesítő adatait, a cél eszköz azonosítója, ahol az SSH démon fut, és a portnak a számát a proxyt, amelyen fut az eszközön.

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

   Módosítsa az előző értékeit, hogy megfeleljenek az eszköz azonosítója és a kapcsolati karakterláncot.

1. Nyissa meg a *rövid útmutatók/eszköz-adatfolyam-szolgáltatás* könyvtárat a a kicsomagolt projektet mappát, majd futtassa a helyi szolgáltatás proxy-alkalmazások.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Ssh-n keresztül az eszköz adatfolyamok az eszköz

Linux rendszeren, SSH használatával futtassa `ssh $USER@localhost -p 2222` a terminálon. Windows a kedvenc SSH-ügyfél (például a putty-t) használja.

Konzol SSH-munkamenet létrehozása után a szolgáltatás helyi kimeneti (a szolgáltatás helyi proxy alkalmazás figyeli a 2222-es port):

![A Terminálszolgáltatások SSH-kimenet](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Az SSH-ügyfélalkalmazás konzolkimenetet (az SSH démon, amely a proxy szolgáltatás helyi alkalmazás figyel a következőn: csatlakozik a 22-es portot SSH-ügyfél kommunikál):

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Az eszköz keresztül eszköz Streamek RDP-vel

Most már az RDP-ügyfélalkalmazás használata és a proxy 2222-es, porton csatlakozhat egy tetszőleges portját, amely a korábban kiválasztott.

> [!NOTE]
> Győződjön meg arról, hogy az eszköz proxy megfelelően konfigurálva az RDP és konfigurálva van a 3389-es RDP-portra.

![Az RDP-ügyfél csatlakozik a helyi szolgáltatás proxy-alkalmazások](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban már üzembe helyezett egy szolgáltatás proxy-alkalmazást az RDP és SSH-IoT-eszköz, regisztrált egy eszközt és állítsa be az IoT hub el. Az RDP és SSH-forgalom lesz bújtatni keresztül egy eszköz streamet az IoT hubon keresztül. Ez a folyamat kiküszöböli a közvetlen kapcsolat az eszközön.

Eszköz Streamek kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
