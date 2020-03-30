---
title: Az Azure IoT Hub-eszköz streameli a Node.js rövid útmutatót az SSH-hoz és az RDP-hez
description: Ebben a rövid útmutatóban futtat egy minta Node.js alkalmazást, amely proxyként működik az SSH és RDP-forgatókönyvek IoT Hub-eszközstreameken való engedélyezéséhez.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c372a0a09fd3143f570aa4b316c9191e617c69e2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675460"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Rövid útmutató: SSH és RDP engedélyezése Egy IoT Hub-eszközadatfolyamon egy Node.js proxyalkalmazás használatával (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Ebben a rövid útmutatóban engedélyezi a Secure Shell (SSH) és a Remote Desktop Protocol (RDP) forgalmat az eszközre egy eszközadatfolyamon keresztül. Az Azure IoT Hub eszközstreamek lehetővé teszik a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát kommunikációját. Ez a rövid útmutató a szolgáltatásoldalon futó Node.js proxyalkalmazás végrehajtását ismerteti. A nyilvános előzetes verzió során a Node.js SDK csak a szolgáltatásoldalon támogatja az eszközstreameket. Ennek eredményeképpen ez a rövid útmutató csak a szolgáltatás-helyi proxyalkalmazás futtatására vonatkozó utasításokat ismerteti.

## <a name="prerequisites"></a>Előfeltételek

* Az [SSH és az RDP engedélyezése IoT Hub-eszközön egy C proxyalkalmazás vagy](./quickstart-device-streams-proxy-c.md) [Az SSH és az RDP engedélyezése IoT Hub-eszközön egy C# proxyalkalmazás használatával.](./quickstart-device-streams-proxy-csharp.md)

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Node.js 10+](https://nodejs.org).

* [Egy minta Node.js projekt](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

A node.js aktuális verzióját a fejlesztőgépen a következő paranccsal ellenőrizheti:

```cmd/sh
node --version
```

A Microsoft Azure IoT Hub jelenleg támogatja az eszközstreamelést [előnézeti funkcióként.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!IMPORTANT]
> Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT Hubok számára támogatott:
>
> * USA középső régiója
> * USA középső régiója
> * Észak-Európa
> * Délkelet-Ázsia
  
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="add-azure-iot-extension"></a>Azure IoT-bővítmény hozzáadása

Adja hozzá az Azure CLI-hez való Azure IoT-bővítményt a következő parancs futtatásával a Cloud Shell-példányhoz. Az IoT-bővítmény hozzáadja az IoT Hub, az IoT Edge és az IoT-eszközlétesítési szolgáltatás (DPS)-specifikus parancsokat az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-node.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha befejezte [a rövid útmutató: Telemetriai adatok küldése egy eszközről egy IoT hub,](quickstart-send-telemetry-node.md)kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban az Azure Cloud Shell használatával regisztrálhat egy szimulált eszközt.

1. Az eszközidentitás létrehozásához futtassa a következő parancsot a Cloud Shell ben:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.
   > * A regisztrálandó eszköz nevéhez ajánlott a *MyDevice* használata az ábrán látható módon. Ha más nevet választ az eszközhöz, használja ezt a nevet a cikkben, és frissítse az eszköz nevét a mintaalkalmazásokban, mielőtt futtatja őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Ahhoz, hogy a háttéralkalmazás csatlakozzon az IoT hubhoz, és az üzenetek beolvasása, akkor is szükség van egy *szolgáltatás kapcsolati karakterlánc.* A következő parancs lekéri az IoT hub karakterláncát:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Vegye figyelembe a visszaadott szolgáltatás kapcsolati karakterláncot későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>SSH készülékre eszközstreameken keresztül

Ebben a szakaszban hozzon létre egy végpontok között az SSH-forgalmat.

### <a name="run-the-device-local-proxy-application"></a>Az eszközhelyi proxyalkalmazás futtatása

Ahogy korábban említettük, az IoT Hub Node.js SDK csak a szolgáltatás oldalon támogatja az eszközstreameléseket. Az eszközhelyi alkalmazás hoz egy eszközproxy-alkalmazást, amely az alábbi rövid útmutatók egyikében érhető el:

   * [SSH és RDP engedélyezése IoT Hub-eszközstreameken C proxyalkalmazás használatával](./quickstart-device-streams-proxy-c.md)
   * [SSH és RDP engedélyezése IoT Hub-eszközstreameken C# proxyalkalmazás használatával](./quickstart-device-streams-proxy-csharp.md) 

Mielőtt továbblépne a következő lépésre, győződjön meg arról, hogy az eszköz-helyi proxyalkalmazás fut. A beállítás áttekintését a [Helyi proxyminta című témakörben](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)találja.

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás-helyi proxyalkalmazás futtatása

Ez a cikk az SSH beállítását ismerteti (a 22-es port használatával), majd ismerteti, hogyan módosíthatja az RDP (a 3389-es portot használó) beállítását. Mivel az eszközfolyamok alkalmazás- és protokollfüggetlenek, ugyanazt a mintát módosíthatja az ügyfél-kiszolgáló alkalmazásforgalom más típusainak megfelelően, általában a kommunikációs port módosításával.

Az eszköz-helyi proxyalkalmazás futásával futtassa a Node.js nyelven írt szolgáltatás-helyi proxyalkalmazást a helyi terminálablakban az alábbi módon:

1. Környezeti változók esetén adja meg a szolgáltatás hitelesítő adatait, a céleszköz-azonosítót, ahol az SSH démon fut, és az eszközön futó proxy portszámát.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   Módosítsa a ServiceConnectionString helyőrzőt úgy, hogy megfeleljen a szolgáltatás kapcsolati karakterláncának, és a **MyDevice-t,** hogy megfeleljen az eszközazonosítójának, ha a sajátjának más nevet adott meg.

1. Keresse meg `Quickstarts/device-streams-service` a projekt kibontott mappájában lévő könyvtárat. A szolgáltatás-helyi proxyalkalmazás futtatásához használja a következő kódot:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>SSH készülékére eszközstreameken keresztül

Linux alatt futtassa `ssh $USER@localhost -p 2222` az SSH-t egy terminálon. A Windows rendszerben használja kedvenc SSH-ügyfélprogramját (például putty).

A konzol kimenete a szolgáltatás-helyi SSH-munkamenet létrehozása után (a szolgáltatás-helyi proxyalkalmazás figyel a 2222-es porton):

![SSH terminál kimenet](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

Az SSH-ügyfélalkalmazás konzolkimenete (az SSH-ügyfél a 22-es porthoz való csatlakozással kommunikál az SSH démonnal, ahol a szolgáltatáshelyi proxyalkalmazás figyel):

![SSH-ügyfél kimenete](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>RDP a készülékre eszközstreameken keresztül

Most használja az RDP-ügyfélalkalmazást, és csatlakozzon a 2222-es portszolgáltatás-proxyhoz, amely egy korábban kiválasztott tetszőleges port.

> [!NOTE]
> Győződjön meg arról, hogy az eszközproxy megfelelően van konfigurálva az RDP-hez, és a 3389-es RDP-porttal van konfigurálva.

![Az RDP-ügyfél csatlakozik a szolgáltatás-helyi proxyalkalmazáshoz](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy IoT-központot hoz létre, regisztrál egy eszközt, és üzembe helyezett egy szolgáltatásproxy-alkalmazást az RDP és az SSH ioT-eszközön való engedélyezéséhez. Az RDP- és SSH-forgalom bújtatása az IoT-központon keresztül egy eszközadatfolyamon keresztül lesz. Ez a folyamat szükségtelenné teszi az eszközhöz való közvetlen csatlakozást.

Az eszközstreamekről az:

> [!div class="nextstepaction"]
> [Eszközadatfolyamok – áttekintés](./iot-hub-device-streams-overview.md)
