---
title: Az Azure IoT Hub eszköz C adatfolyamok rövid SSH vagy RDP (előzetes verzió) |} A Microsoft Docs
description: Ebben a rövid útmutatóban két minta futtatása lesz C# alkalmazásokat, amelyek az RDP/SSH-forgatókönyvek engedélyezése egy IoT Hub eszköz adatfolyam felett.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 514c2e0ea1ef33406c6633064434239d8bdd0e3f
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832991"
---
# <a name="quickstart-sshrdp-over-an-iot-hub-device-stream-using-a-c-proxy-application-preview"></a>Gyors útmutató: Az IoT Hub eszköz stream használatához képest SSH vagy RDP- C# proxyalkalmazást (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. A rövid útmutató magában foglalja a két C# programok, amelyek lehetővé teszik az ügyfél-kiszolgáló alkalmazási forgalmat (például az SSH és az RDP-t) az IoT hubon keresztül létrehozott eszköz adatfolyam lesz elküldve. Lásd: [helyi Proxy minta SSH vagy RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) áttekintését, a telepítés.

Először ismertetünk a telepítő az SSH (22-es port használatával). Azt majd ismertetjük, hogyan módosíthatja a telepítő port RDP-hez. Mivel az eszköz Streamek alkalmazás és a protokoll független, egyazon mintából módosítható, hogy más típusú alkalmazás forgalom kezelésére. Ez általában csak módosítása is hozzátartozik a kommunikációhoz használt portot, az importálni kívánt alkalmazás által használt.

## <a name="how-it-works"></a>A szolgáltatás működése

Az alábbi ábra bemutatja, hogyan az eszköz és szolgáltatás helyi proxy programok ebben a példában SSH-ügyfél és az SSH démon között teljes körű kapcsolat lehetővé teszi a telepítés. Itt feltételezzük, hogy a démon fut-e az eszköz helyi proxyként ugyanazon az eszközön.

![Helyi proxy telepítése](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Szolgáltatás helyi proxy csatlakozik az IoT hubhoz, és kezdeményezi a céleszköz, az eszköz azonosítójával eszköz adatfolyam

2. Eszköz helyi proxy a stream kezdeményezés kézfogás befejeződött, és az IoT Hub a Szolgáltatásoldali streamvégponton keresztül egy teljes körű streamelési alagutat létesít.

3. Eszköz helyi proxy csatlakozik-e az SSH démon (SSHD) az eszközön a 22-es porton (konfigurálható, a port leírtak szerint a [futtatása az eszköz helyi Proxy szakasz](#run-the-device-local-proxy).

4. Szolgáltatás helyi proxy várja az új SSH-kapcsolatok a felhasználó által a kijelölt porton, amely ebben az esetben 2222-es port (ez történik akkor is konfigurálható, leírtak szerint a [futtassa a szolgáltatás helyi Proxy szakasz](#run-the-service-local-proxy). SSH-ügyfél-n keresztül csatlakozó felhasználó, az alagút lehetővé teszi a forgalmát az SSH-ügyfél és a szolgáltatás programok között.

> [!NOTE]
> SSH-forgalmat küld a rendszer a stream keresztül fog bújtatni küld a rendszer közvetlenül a szolgáltatás és eszköz közötti helyett az IoT Hub streamvégponton keresztül. További információkért lásd a szakasz [eszköz streameli előnyöket](./iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Előzetes verziójának eszköz Streamek jelenleg csak a az IoT-központok létrehozni a következő régiókban támogatott:

*  **USA középső RÉGIÓJA**

*  **USA középső RÉGIÓJA – EUAP**

A rövid útmutatóban futtatott két mintaalkalmazás a C# használatával készült. A fejlesztői gépen szükség lesz a .NET Core SDK 2.1.0-s vagy újabb változatára.

Letöltheti a [.NET Core SDK a .NET használatával több platformon](https://www.microsoft.com/net/download/all).

A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```
dotnet --version
```

Futtassa a következő parancsot a Microsoft Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadása Azure CLI-vel az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) parancsok.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Töltse le a C#-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

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

3. Emellett a *szolgáltatáskapcsolati karakterláncra* az IoT hub a Szolgáltatásoldali alkalmazás csatlakoztatása az IoT hubhoz, és létrehozza a eszköz stream engedélyezése. Az alábbi parancs lekéri az IoT hub ezt az értéket:

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Jegyezze fel, a visszaadott érték, amely a következőhöz hasonló:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Ssh-n keresztül az eszköz Streamek eszköz

Ebben a szakaszban kapcsolatot hoz létre az SSH-forgalom egy teljes körű stream.

### <a name="run-the-device-local-proxy"></a>Az eszköz helyi proxy futtatása

Navigáljon a `device-streams-proxy/device` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Argumentum neve | Hodnota argumentu |
|----------------|-----------------|
| `deviceConnectionString` | A kapcsolati karakterláncot a korábban létrehozott eszköz. |
| `targetServiceHostName` | Az IP-címet, ahol az SSH-kiszolgáló figyel (Ez akkor lehet `localhost` Ha a futtató helyi eszköz proxy azonos IP-cím). |
| `targetServicePort` | A protokoll által használt port (alapértelmezés szerint ez lenne az SSH-hoz a 22-es port).  |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

### <a name="run-the-service-local-proxy"></a>Futtassa a helyi szolgáltatási proxy

Navigáljon a `device-streams-proxy/service` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméternév | Paraméter értéke |
|----------------|-----------------|
| `iotHubConnectionString` | Az IoT hub szolgáltatáskapcsolati karakterláncát. |
| `deviceId` | A korábban létrehozott eszköz azonosítóját. |
| `localPortNumber` | Eltérő helyi portokat, ha az SSH-ügyfél csatlakozni fognak. Ebben a példában 2222-es port használunk, de ezt más tetszőleges számokat módosítása. |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-ssh-client"></a>SSH-ügyfél futtatása

Most már az SSH-ügyfél programot használ, és csatlakozzon a 2222-es port (és nem az SSH démon közvetlenül) proxyt helyi szolgáltatás.

```
ssh <username>@localhost -p 2222
```

Ezen a ponton, megjelenik a hitelesítő adatait az SSH-bejelentkezési felület.

A konzol kimenetét, az a Szolgáltatásoldali (a szolgáltatás helyi proxy 2222-es portot figyeli):

![Helyi szolgáltatás proxy-kimenet](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Az eszköz helyi proxy, amelyhez csatlakozik, az SSH démon a konzolkimenetet `IP_address:22`:

![Eszköz helyi proxy kimenet](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Az SSH-ügyfél program konzolkimenetet (SSH-ügyfél kommunikál az SSH démon ahol szolgáltatás helyi proxy figyeli a 22-es porton csatlakozik):

![SSH-ügyfél program kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Egy eszköz eszköz Streamek keresztül RDP-vel

A telepítő RDP nagyon hasonlít az SSH (lásd fent). Alapvetően kell használjon az RDP cél IP-cím és a 3389-es és RDP-ügyfél (az SSH-ügyfél) helyett.

### <a name="run-the-device-local-proxy-rdp"></a>Futtassa az eszköz helyi proxy (RDP)

Navigáljon a `device-streams-proxy/device` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Argumentum neve | Hodnota argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | A kapcsolati karakterláncot a korábban létrehozott eszköz. |
| `targetServiceHostName` | Az állomásnév vagy IP-címet, ahol az RDP-kiszolgáló fut (Ez akkor lehet `localhost` Ha az eszköz helyi proxy futtató ugyanazon IP). |
| `targetServicePort` | A protokoll által használt port (alapértelmezés szerint ez lenne az RDP a 3389-es port).  |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux/MacOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

### <a name="run-the-service-local-proxy-rdp"></a>Futtassa a helyi szolgáltatási proxyt (RDP)

Navigáljon a `device-streams-proxy/service` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméternév | Paraméter értéke |
|----------------|-----------------|
| `iotHubConnectionString` | Az IoT hub szolgáltatáskapcsolati karakterláncát. |
| `deviceId` | A korábban létrehozott eszköz azonosítóját. |
| `localPortNumber` | Eltérő helyi portokat, ha az SSH-ügyfél csatlakozni fognak. Ebben a példában 2222-es port használunk, de ezt más tetszőleges számokat módosítása. |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-rdp-client"></a>RDP-ügyfél

Most már használhatja az RDP-ügyfélprogram, és csatlakozzon a helyi szolgáltatási proxy 2222-es porton (Ez a korábban kiválasztott egy tetszőleges szabad portot volt).

![RDP csatlakozik a helyi szolgáltatási proxy](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, rendelkezik beállítása egy IoT hubot, regisztrált egy eszközt, egy eszköz - és szolgáltatás helyi proxy program létesíteni egy eszköz stream IoT hubon keresztül telepített és alagutat az SSH vagy RDP-forgalmat a proxyk használt. Az azonos paradigmát más ügyfél/kiszolgáló irányú protokollok (ahol kiszolgálón futtatja az eszközön, például az SSH démon) képes kezelni.

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)