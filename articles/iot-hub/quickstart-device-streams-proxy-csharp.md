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
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 7a6a9a96bbde24fa8340714a8078e015a83d8cfb
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830237"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-applications-preview"></a>Gyors útmutató: IoT Hub device feletti SSH vagy RDP adatfolyamok használatával C# proxy-alkalmazások (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. Ez a rövid útmutató magában foglalja a két C# programok, amelyek lehetővé teszik az SSH és az RDP forgalom küldését az IoT hubon keresztül létrehozott eszköz adatfolyam felett. Lásd: [ezt oldal](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) áttekintését, a telepítés.

Először ismertetünk a telepítő az SSH (22-es port használatával). Ezután ismertetünk RDP számára beállított port a telepítés módosítása. Mivel az eszköz Streamek alkalmazás és a protokoll független, egyazon mintából módosíthatják (általában a kommunikációs portok módosítása) más típusú alkalmazás forgalmának befogadásához.

## <a name="how-it-works"></a>Hogyan működik?

Az alábbi ábra bemutatja, hogyan az eszköz és szolgáltatás helyi proxy programok ebben a példában SSH-ügyfél és az SSH démon között teljes körű kapcsolat lehetővé teszi a telepítés. Itt feltételezzük, hogy a démon fut-e az eszköz helyi proxyként ugyanazon az eszközön.

![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "helyi proxy telepítése")

1. Szolgáltatás helyi proxy csatlakozik az IoT hubhoz, és kezdeményezi a céleszköz eszköz adatfolyam.

2. Eszköz helyi proxy a stream kezdeményezés kézfogás befejeződött, és az IoT Hub a Szolgáltatásoldali streamvégponton keresztül egy teljes körű streamelési alagutat létesít.

3. Eszköz helyi proxy csatlakozik-e az SSH démon (SSHD) az eszközön a 22-es porton (leírt módon konfigurálható, azt [alábbi](#run-the-device-side-application)).

4. Szolgáltatás helyi proxy várja az új SSH-kapcsolatok a felhasználó által a kijelölt porton, amely ebben az esetben ez a port `2222` (ez történik akkor is konfigurálható, amint [alábbi](#run-the-service-side-application)). SSH-ügyfél-n keresztül csatlakozó felhasználó, az alagút lehetővé teszi a forgalmát az SSH-ügyfél és kiszolgáló programok között.

> [!NOTE]
> SSH-forgalmat küld a rendszer a stream keresztül fog bújtatni küld a rendszer közvetlenül a szolgáltatás és eszköz közötti helyett az IoT Hub streamvégponton keresztül. Ez [értékelemeket](./iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban futtatott két mintaalkalmazás a C# használatával készült. A fejlesztői gépen szükség lesz a .NET Core SDK 2.1.0-s vagy újabb változatára.

A .NET Core SDK-t többféle platformra a [.NET](https://www.microsoft.com/net/download/all) oldaláról töltheti le.

A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```
dotnet --version
```

Töltse le a C#-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip címről, és bontsa ki a ZIP-archívumot.


## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

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

3. Emellett a _szolgáltatáskapcsolati karakterláncra_ az IoT hub a Szolgáltatásoldali alkalmazás csatlakoztatása az IoT hubhoz, és létrehozza a eszköz stream engedélyezése. Az alábbi parancs lekéri az IoT hub ezt az értéket:

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Jegyezze fel, a visszaadott érték, amely a következőhöz hasonló:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`
    

## <a name="ssh-to-a-device-via-device-streams"></a>Ssh-n keresztül az eszköz Streamek eszköz

### <a name="run-the-service-side-proxy"></a>Futtassa a Szolgáltatásoldali proxy

Navigáljon a `device-streams-proxy/service` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `IotHubConnectionString` | Az IoT hub szolgáltatáskapcsolati karakterláncát. |
| `DeviceId` | A korábban létrehozott eszköz azonosítóját. |
| `Port` | Eltérő helyi portokat, ha az SSH-ügyfél csatlakozni fognak. Port használjuk `2222` a jelen minta, de sikerült módosítani a más tetszőleges számmá. |

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

### <a name="run-the-device-local-proxy"></a>Az eszköz helyi proxy futtatása

Navigáljon a `device-streams-proxy/device` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A kapcsolati karakterláncot a korábban létrehozott eszköz. |
| `RemoteHostName` | Az IP-cím, ahol SSH kiszolgáló (Ez akkor lehet `localhost` Ha a futtató helyi eszköz proxy azonos IP-cím). |
| `RemotePort` | A protokoll által használt port (alapértelmezés szerint ez lenne az SSH-hoz a 22-es port).  |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $DeviceConnectionString localhost 22

# In Windows
dotnet run %DeviceConnectionString% localhost 22
```

Most már használja az SSH-ügyfélprogram, és csatlakozzon a helyi szolgáltatási proxy port `2222` (helyett az SSH démon közvetlenül). 

```
ssh <username>@localhost -p 2222
```

Ezen a ponton, megjelenik a hitelesítő adatait az SSH-bejelentkezési felület.

A konzol kimenetét, az a Szolgáltatásoldali (a szolgáltatás helyi proxy 2222-es portot figyeli): ![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-csharp/service-console-output.png "szolgáltatás helyi proxy kimenet")

Az eszköz helyi proxy, amelyhez csatlakozik, az SSH démon a konzolkimenetet <code>IP_address:22</code>: ![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-csharp/device-console-output.png "eszköz helyi proxy kimenet")

Az SSH-ügyfél program konzolkimenetet (SSH-ügyfél kommunikál az SSH démon ahol szolgáltatás helyi proxy figyeli a 22-es porton csatlakozik): ![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "SSH-ügyfél program kimenete")

## <a name="rdp-to-a-device-via-device-streams"></a>Egy eszköz eszköz Streamek keresztül RDP-vel

A telepítő RDP nagyon hasonlít az SSH (lásd fent). Alapvetően kell használjon az RDP cél IP-cím és a 3389-es és RDP-ügyfél (az SSH-ügyfél) helyett.

### <a name="run-the-service-side-application"></a>A Szolgáltatásoldali alkalmazás futtatása

Navigáljon a `device-streams-proxy/service` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `IotHubConnectionString` | Az IoT hub szolgáltatáskapcsolati karakterláncát. |
| `DeviceId` | A korábban létrehozott eszköz azonosítóját. |
| `Port` | Eltérő helyi portokat, ha az SSH-ügyfél csatlakozni fognak. Port használjuk `2222` a jelen minta, de sikerült módosítani a más tetszőleges számmá. |

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

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Navigáljon a `device-streams-proxy/device` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A kapcsolati karakterláncot a korábban létrehozott eszköz. |
| `RemoteHostName` | Az IP-cím where RDP-kiszolgáló (Ez akkor lehet `localhost` Ha az eszköz helyi proxy futtató ugyanazon IP). |
| `RemotePort` | A protokoll által használt port (alapértelmezés szerint ez lenne az RDP a 3389-es port).  |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux/MacOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

Most már használhatja az RDP-ügyfélprogram, és csatlakozzon a helyi szolgáltatási proxy port `2222` (Ez a korábbi választotta egy tetszőleges szabad portot volt).

![Helyettesítő szöveg](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.PNG "RDP csatlakozik a helyi szolgáltatás teljesítményszámláló proxy")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, rendelkezik beállítása egy IoT hubot, regisztrált egy eszközt, egy eszköz - és szolgáltatás helyi proxy program létesíteni egy eszköz stream IoT hubon keresztül telepített és alagutat az SSH vagy RDP-forgalmat a proxyk használt. Az azonos paradigmát más ügyfél/kiszolgáló irányú protokollok (ahol kiszolgálón futtatja az eszközön, például az SSH démon) képes kezelni.

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
