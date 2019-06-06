---
title: 'Az Azure IoT Hub eszköz Streamek C# gyors útmutató: az SSH és az RDP-t (előzetes verzió) |} A Microsoft Docs'
description: Ebben a rövid útmutatóban a két minta futtatása C# alkalmazásokat, amelyek SSH- és RDP-forgatókönyvek engedélyezése egy IoT Hub eszköz adatfolyam felett.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1d5fbb410a61419f6f6d2e80cdb1a16c07672fe9
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733342"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Gyors útmutató: SSH és az RDP engedélyezése egy IoT Hub eszköz adatfolyam felett használatával egy C# proxyalkalmazást (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. A rövid útmutató magában foglalja a két C# alkalmazásokat, amelyek lehetővé teszik az ügyfél és kiszolgáló alkalmazási forgalmat (például a Secure Shell [SSH] és Remote Desktop Protocol [RDP] az IoT hubon keresztül létrehozott eszköz adatfolyam lesz elküldve. A telepítő áttekintését lásd: [mintaalkalmazás helyi proxy SSH vagy RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Ez a cikk először a témakör ismerteti a telepítés az SSH (22-es port használatával), és ezután ismerteti, hogyan lehet módosítani a telepítő port RDP-hez. Mivel az eszköz Streamek alkalmazás - és a protokoll-független, egyazon mintából módosítható, hogy más típusú alkalmazás forgalom kezelésére. Ezt a módosítást általában csak módosítása, hogy a kívánt alkalmazásra használja a kommunikációhoz használt portot is hozzátartozik.

## <a name="how-it-works"></a>Működés

A következő ábra azt szemlélteti, hogyan lehetővé a helyi eszköz és szolgáltatás helyi proxyalkalmazásokba ebben a példában a végpontok közötti kapcsolat az SSH-ügyfél és az SSH démon folyamatok közötti. Itt feltételezzük, hogy a démon fut-e az eszköz helyi proxy alkalmazásként ugyanazon az eszközön.

![Helyi proxy alkalmazás telepítése](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. A szolgáltatás helyi proxyalkalmazást csatlakozik az IoT hubhoz, és kezdeményezi a céleszköz eszköz adatfolyam.

1. Az eszköz helyi proxyalkalmazást a stream kezdeményezés kézfogás befejeződött, és az IoT hub a Szolgáltatásoldali streamvégponton keresztül egy teljes körű streamelési alagutat létesít.

1. Az eszköz helyi proxyalkalmazást csatlakozik az SSH démon, amely az eszköz a 22-es porton figyel. Ez a beállítás nem konfigurálható, a "Futtatása az eszköz helyi proxyalkalmazást" szakaszban leírtak szerint.

1. A szolgáltatás helyi proxyalkalmazást megvárja a felhasználó új SSH-kapcsolatokat, a kijelölt porton, amely ebben az esetben 2222-es port. Ez a beállítás nem konfigurálható, az "A szolgáltatás helyi proxy-alkalmazások futtatása" szakaszban leírtak szerint. Amikor a felhasználó az SSH-ügyfél-n keresztül csatlakozik, az alagút lehetővé teszi, hogy SSH-alkalmazás forgalmat az SSH-ügyfél és kiszolgáló alkalmazás közötti adatátvitelhez.

> [!NOTE]
> SSH-forgalmat, egy eszköz stream keresztül küldött az IoT hub folyamatos átviteli végponton keresztül bújtatott helyett közvetlenül a szolgáltatás és az eszközök között küldött. További információkért lásd: a [az Iot Hub eszköz Streamek előnyei](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Előzetes verziójának eszköz Streamek jelenleg csak az IoT hub, az alábbi régiókban létrehozott támogatott:

  * USA középső régiója
  * USA középső RÉGIÓJA – EUAP

* Ebben a rövid útmutatóban futtató két mintaalkalmazásból használatával írt C#. A .NET Core SDK 2.1.0, vagy később a fejlesztői gépén van szüksége.

  Letöltheti a [.NET Core SDK a .NET használatával több platformon](https://www.microsoft.com/net/download/all).

* Ellenőrizze a jelenlegi verziója C# a fejlesztői gépen, a következő paranccsal:

    ```
    dotnet --version
    ```

* Futtassa a következő parancsot az Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadja az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) – az Azure CLI parancsok.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [Töltse le a mintát C# projekt](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip), és bontsa ki a ZIP-archívumot.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shellben használhatja egy szimulált eszköz regisztrálásához.

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

1. Csatlakoztatása az IoT hubhoz, és a egy eszköz stream létesít, is szüksége van a *szolgáltatáskapcsolati karakterláncra* az IoT hub a Szolgáltatásoldali kérelem engedélyezéséhez. Az alábbi parancs lekéri az IoT hub ezt az értéket:

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

Nyissa meg a *eszköz-adatfolyamok-proxy/eszköz* könyvtárat a kicsomagolt projektet tartalmazó mappában. Tartsa praktikus a következő információkat:

| Argumentum neve | Hodnota argumentu |
|----------------|-----------------|
| `deviceConnectionString` | A kapcsolati karakterláncot, az eszköz, amelyet korábban hozott létre. |
| `targetServiceHostName` | Az IP-cím, ahol az SSH-kiszolgáló figyel. A cím lenne `localhost` , mintha a hol futnak az eszköz helyi proxyalkalmazást azonos IP-cím. |
| `targetServicePort` | Az alkalmazás protokoll által használt port (az ssh-t, alapértelmezés szerint ez lenne 22-es port).  |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás helyi proxy-alkalmazások futtatása

Navigáljon a `device-streams-proxy/service` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméter neve | Hodnota parametru |
|----------------|-----------------|
| `iotHubConnectionString` | Az IoT hub szolgáltatáskapcsolati karakterláncát. |
| `deviceId` | A korábban létrehozott eszköz azonosítóját. |
| `localPortNumber` | Helyi port, amely az SSH-ügyfél csatlakozni fog. Ebben a példában 2222-es port használunk, de használhat más tetszőleges számát. |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Az SSH-ügyfél

Most már az SSH-ügyfélalkalmazás használata, és csatlakozzon a helyi szolgáltatás-proxyalkalmazást (helyett az SSH démon közvetlenül) 2222-es porton.

```
ssh <username>@localhost -p 2222
```

Ezen a ponton az SSH bejelentkezési ablak kéri, hogy adja meg hitelesítő adatait.

Konzolkimenet Szolgáltatásoldali (2222-es portot figyeli a szolgáltatás helyi proxy-alkalmazások):

![Proxy szolgáltatás helyi alkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Az eszköz helyi proxy alkalmazásra, amelyhez csatlakozik, az SSH démon konzolkimenetet *IP_address:22*:

![Eszköz helyi proxy alkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Az SSH-ügyfélalkalmazás konzol kimenetét. Az SSH démon 22-es, porton csatlakozik a helyi szolgáltatási proxy alkalmazás figyel, amely kommunikál az SSH-ügyfél:

![SSH-ügyfél alkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Egy eszköz eszköz Streamek keresztül RDP-vel

RDP-beállítások nagyon hasonlít a telepítő az SSH (fentebb leírt) számára. Az RDP-cél IP-cím használata, és helyette a 3389-es port és az RDP-ügyfél (helyett az SSH-ügyfél) használata.

### <a name="run-the-device-local-proxy-application-rdp"></a>Futtassa az eszköz helyi-proxyalkalmazást (RDP)

Nyissa meg a *eszköz-adatfolyamok-proxy/eszköz* könyvtárat a kicsomagolt projektet tartalmazó mappában. Tartsa praktikus a következő információkat:

| Argumentum neve | Hodnota argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | A kapcsolati karakterláncot, az eszköz, amelyet korábban hozott létre. |
| `targetServiceHostName` | Az állomásnév vagy IP-címet, ahol RDP-kiszolgáló fut-e. A cím lenne `localhost` , mintha a hol futnak az eszköz helyi proxyalkalmazást azonos IP-cím. |
| `targetServicePort` | A portot használják a protokoll (RDP, alapértelmezés szerint ez lenne 3389-es port).  |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>A szolgáltatás helyi proxyalkalmazást (RDP) futtatása

Navigáljon a `device-streams-proxy/service` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméter neve | Hodnota parametru |
|----------------|-----------------|
| `iotHubConnectionString` | Az IoT hub szolgáltatáskapcsolati karakterláncát. |
| `deviceId` | A korábban létrehozott eszköz azonosítóját. |
| `localPortNumber` | Helyi port, amely az SSH-ügyfél csatlakozni fog. Ebben a példában 2222-es port használunk, de ezt más tetszőleges számokat módosítása. |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-rdp-client"></a>RDP-ügyfél

Most már az RDP-ügyfélalkalmazás használata, és csatlakozzon a helyi szolgáltatás proxy-alkalmazások, a 2222-es porton (ez volt egy tetszőleges szabad portot, amely a korábban kiválasztott).

![RDP csatlakozik a helyi szolgáltatás proxy-alkalmazások](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, már beállítása egy IoT hubot, regisztrált egy eszközt, telepített helyi eszköz és szolgáltatás helyi proxy alkalmazásai létrehozására, az IoT hubon keresztül eszköz adatfolyam és SSH vagy RDP-forgalom a proxy-alkalmazások segítségével. Az azonos paradigmát más ügyfél – kiszolgáló irányú protokollok, ahol a kiszolgáló fut-e az eszközön (például az SSH démon) képes kezelni.

Eszköz Streamek kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
