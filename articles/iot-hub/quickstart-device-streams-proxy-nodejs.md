---
title: Azure IoT Hub Device Streams Node. js gyors útmutató SSH-hoz és RDP-hez (előzetes verzió) | Microsoft Docs
description: Ebben a rövid útmutatóban egy minta Node. js-alkalmazást futtat, amely proxyként szolgál az SSH-és RDP-forgatókönyvek engedélyezéséhez IoT Hub eszköz streamen.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 83339273d9161c3947df191d10e788980db39b28
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "67446025"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Gyors útmutató: Az SSH és az RDP engedélyezése IoT Hub eszköz streamen egy Node. js-alkalmazásproxy használatával (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

A Microsoft Azure IoT Hub jelenleg [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verziójú szolgáltatásként támogatja az eszközök adatfolyamait.

[IoT hub](./iot-hub-device-streams-overview.md) az eszközökön elérhető streamek lehetővé teszik a szolgáltatás-és eszköz-alkalmazások számára a biztonságos és tűzfalon alapuló kommunikációt. 

Ez a rövid útmutató azt ismerteti, hogyan hajtható végre a szolgáltatás oldalán futó Node. js-alkalmazásproxy, hogy engedélyezze a Secure Shell (SSH) és a RDP protokoll (RDP) forgalmat az eszközre az eszközön. A telepítés áttekintését lásd: [helyi proxy minta](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

A nyilvános előzetes verzióban a Node. js SDK csak a szolgáltatási oldalon található eszköz-adatfolyamokat támogatja. Ennek eredményeképpen ez a rövid útmutató a csak a szolgáltatás helyi proxybeállításait futtató alkalmazások futtatására vonatkozó utasításokat tartalmazza. Az eszközön belüli alkalmazásproxy futtatásához tekintse meg a következőt:  

   * [Az SSH és az RDP engedélyezése IoT Hub eszköz streamen C proxy alkalmazás használatával](./quickstart-device-streams-proxy-c.md)
   * [SSH és RDP engedélyezése IoT Hub eszközön lévő streamek számára C# egy proxy alkalmazás használatával](./quickstart-device-streams-proxy-csharp.md)

Ez a cikk az SSH beállítását ismerteti (a 22-es port használatával), majd leírja, hogyan lehet módosítani az RDP-t (amely a 3389-es portot használja). Mivel az eszköz-adatfolyamok alkalmazás-és protokoll-függetlenek, ugyanezt a mintát úgy módosíthatja, hogy az ügyfél-kiszolgáló alkalmazások más típusú forgalmát is kielégítse, általában a kommunikációs port módosításával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az adatfolyamok előnézete jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:

  * USA középső régiója
  * USA középső régiója – EUAP

* A szolgáltatás helyi alkalmazásának ebben a rövid útmutatóban való futtatásához a fejlesztői gépen a Node. js v10. x. x vagy újabb verziójára van szükség.
  * Töltse le a [Node. js](https://nodejs.org) -t több platformra.
  * A következő parancs használatával ellenőrizze a Node. js aktuális verzióját a fejlesztői gépen:

   ```
   node --version
   ```

* A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Azure IoT-bővítményt a Cloud Shell-példányához. Az IOT bővítmény hozzáadja az Azure CLI-hez IoT Hub, IoT Edge és IoT eszközök kiépítési szolgáltatásának (DPS) specifikus parancsait.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Ha még nem tette meg, [töltse le a minta Node. js-projektet](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) , és bontsa ki a zip-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha végrehajtotta az előző [rövid útmutatót: Telemetria küldése az eszközről egy IoT-hubhoz](quickstart-send-telemetry-node.md), ezt a lépést kihagyhatja.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha befejezte [a gyors üzembe helyezést: Telemetria küldése az eszközről egy IoT-hubhoz](quickstart-send-telemetry-node.md), ezt a lépést kihagyhatja.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban a Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Az eszköz identitásának létrehozásához futtassa a következő parancsot Cloud Shellban:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.
   > * Használja az *MyDevice*-t az ábrán látható módon. Ez a regisztrált eszköz nevét adja meg. Ha más nevet választ az eszköznek, ezt a nevet használja ebben a cikkben, és a futtatásuk előtt frissítse az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Ahhoz, hogy a háttérbeli alkalmazás csatlakozhasson az IoT hubhoz, és lekérje az üzeneteket, szüksége lesz egy *szolgáltatás kapcsolati karakterláncára*is. A következő parancs lekérdezi az IoT hub karakterláncát:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Jegyezze fel a visszaadott értéket az ebben a rövid útmutatóban később történő használathoz. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH-eszköz az eszközön keresztül streamek használatával

Ebben a szakaszban egy végpontok közötti streamet hoz létre az SSH-forgalom bújtatásához.

### <a name="run-the-device-local-proxy-application"></a>Az eszköz helyi proxy alkalmazásának futtatása

Ahogy korábban említettük, a IoT Hub Node. js SDK csak az eszközön lévő adatfolyamokat támogatja a szolgáltatás oldalán. Az eszköz helyi alkalmazásához használja az alábbi rövid útmutatók egyikében elérhető alkalmazásproxy-alkalmazást:

   * [Az SSH és az RDP engedélyezése IoT Hub eszköz streamen C proxy alkalmazás használatával](./quickstart-device-streams-proxy-c.md)
   * [SSH és RDP engedélyezése IoT Hub eszközön lévő streamek számára C# egy proxy alkalmazás használatával](./quickstart-device-streams-proxy-csharp.md) 

Mielőtt továbblép a következő lépésre, győződjön meg arról, hogy az eszközön helyi alkalmazásproxy fut.

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxy alkalmazásának futtatása

A-t futtató helyi alkalmazásproxy-alkalmazás futtatásával futtassa a Node. js-ben írt szolgáltatás-helyi proxykiszolgálót a következő módon:

1. Környezeti változók esetén adja meg a szolgáltatás hitelesítő adatait, a megcélzott eszköz AZONOSÍTÓját, ahol az SSH démon fut, az eszközön futó proxy portszáma pedig.

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

   Módosítsa az előző értékeket úgy, hogy az megfeleljen az eszköz AZONOSÍTÓjának és a kapcsolódási karakterláncnak.

1. Nyissa meg a kibontott projekt mappában található gyors útmutató */eszköz-adatfolyamok-szolgáltatás* könyvtárat, és futtassa a Service-local alkalmazásproxy alkalmazást.

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH-n keresztül az eszközön

Linux rendszerben futtassa az SSH-t `ssh $USER@localhost -p 2222` egy terminálon keresztül. A Windowsban használja kedvenc SSH-ügyfelét (például a PuTTY-t).

Konzol kimenete a szolgáltatásban – helyi SSH-munkamenet létrehozása után (a szolgáltatás helyi proxy alkalmazás figyeli a 2222-es portot):

![SSH-terminál kimenete](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Az SSH-ügyfélalkalmazás konzoljának kimenete (az SSH-ügyfél a 22-es porthoz való csatlakozással kommunikál az SSH démonsal):

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP az eszközön az eszköz streamen keresztül

Most használja az RDP-ügyfélalkalmazás használatát, és kapcsolódjon a Service proxyhoz a 2222-as porton, egy tetszőleges portot, amelyet korábban választott.

> [!NOTE]
> Győződjön meg arról, hogy az eszköz proxyja megfelelően van konfigurálva az RDP-hez, és konfigurálja a 3389-es RDP-portot.

![Az RDP-ügyfél a szolgáltatás helyi alkalmazásproxy-alkalmazásához csatlakozik](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT hubot állított be, regisztrált egy eszközt, és üzembe helyezett egy Service proxy-alkalmazást, amely lehetővé teszi az RDP és az SSH IoT-eszközön való engedélyezését. Az RDP-és SSH-forgalom az IoT hub-on keresztül egy eszköz streamen keresztül lesz átirányítva. Ez a folyamat nem teszi szükségessé az eszközhöz való közvetlen csatlakozást.

További információ az eszközök streamekről:

> [!div class="nextstepaction"]
> [Az eszközök adatfolyamának áttekintése](./iot-hub-device-streams-overview.md)
