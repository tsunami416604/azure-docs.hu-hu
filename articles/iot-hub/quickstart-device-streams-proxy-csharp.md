---
title: Az Azure IoT Hub-eszköz c# rövid útmutatót streamel az SSH-hoz és az RDP-hez
description: Ebben a rövid útmutatóban két c#-os mintaalkalmazást futtat, amelyek sSH- és RDP-forgatókönyveket engedélyeznek egy IoT Hub-eszközadatfolyamon.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c8ef6a87e839e6d8dfb296e7b24f3d0d95843d1c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675465"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Rövid útmutató: SSH és RDP engedélyezése egy IoT Hub-eszközadatfolyamon egy C# proxyalkalmazás használatával (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszközstreamelést [előnézeti funkcióként.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Az IoT Hub eszközadatfolyamok](iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát kommunikációját. Ez a rövid útmutató két C# alkalmazást tartalmaz, amelyek lehetővé teszik az ügyfél-kiszolgáló alkalmazásforgalmát (például a Secure Shell [SSH] és a Remote Desktop Protocol [RDP] ioT hubon keresztül létrehozott eszközön keresztül történő küldését. A telepítés áttekintése az [SSH vagy RDP helyi proxyalkalmazás-minta](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)című témakörben olvashat.

Ez a cikk először ismerteti az SSH beállítását (a 22-es porthasználatával), majd ismerteti, hogyan módosíthatja a telepítő RDP-portját. Mivel az eszközadatfolyamok alkalmazás- és protokollfüggetlenek, ugyanaz a minta módosítható más típusú alkalmazásforgalom befogadására. Ez a módosítás általában csak a kommunikációs port ot a kívánt alkalmazás által használt portra módosítja.

## <a name="how-it-works"></a>Működés

Az alábbi ábra bemutatja, hogy az eszköz-helyi és szolgáltatás-helyi proxyalkalmazások ebben a mintában lehetővé teszik az SSH-ügyfél és az SSH démonfolyamatok végpontok közötti kapcsolatot. Itt feltételezzük, hogy a démon ugyanazon az eszközön fut, mint az eszköz-helyi proxyalkalmazás.

![Helyi proxyalkalmazás beállítása](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. A szolgáltatás-helyi proxyalkalmazás csatlakozik az IoT hubhoz, és kezdeményez egy eszközadatfolyamot a céleszközre.

1. Az eszköz-helyi proxyalkalmazás befejezi az adatfolyam-kezdeményezési kézfogást, és létrehoz egy végpontok közötti streamelési alagutat az IoT hub streamelési végpontján keresztül a szolgáltatás oldalra.

1. Az eszköz-helyi proxyalkalmazás csatlakozik az SSH démon, amely figyeli a port 22 az eszközön. Ez a beállítás konfigurálható, az "Eszközhelyi proxyalkalmazás futtatása" című szakaszban leírtak szerint.

1. A szolgáltatás-helyi proxyalkalmazás a felhasználó új SSH-kapcsolataira vár egy kijelölt port figyelésével, amely ebben az esetben a 2222-es port. Ez a beállítás konfigurálható, a "Futtatása a szolgáltatás-helyi proxyalkalmazás" című szakaszban leírtak szerint. Amikor a felhasználó az SSH-ügyfélen keresztül csatlakozik, a bújtatás lehetővé teszi az SSH alkalmazásforgalom átvitelét az SSH-ügyfél és a kiszolgálóalkalmazás között.

> [!NOTE]
> Az eszközadatfolyamon keresztül küldött SSH-forgalom az IoT hub streamelési végpontján keresztül bújtat, nem pedig közvetlenül a szolgáltatás és az eszköz között. További információt az [Iot Hub eszközadatfolyamok használatának előnyeiben](iot-hub-device-streams-overview.md#benefits)talál.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT-központok számára támogatott:

  * USA középső régiója
  * USA középső régiója
  * Délkelet-Ázsia
  * Észak-Európa

* A rövid útmutatóban futtatott két mintaalkalmazás C#-ban íródott. A .NET Core SDK 2.1.0 vagy újabb verzióra van szüksége a fejlesztői gépen.

  A [.NET Core SDK több platformra](https://www.microsoft.com/net/download/all)is letölthető a .NET -ből.

* Ellenőrizze a C# aktuális verzióját a fejlesztőgépen a következő paranccsal:

    ```
    dotnet --version
    ```

* Futtassa a következő parancsot az Azure CLI-hez való Azure IoT-bővítmény hozzáadásához a Cloud Shell-példányhoz. Az IOT-bővítmény iot hub, IoT Edge és IoT-eszközlétesítési szolgáltatás (DPS)-specifikus parancsokat ad hozzá az Azure CLI-hez.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   ```azurecli-interactive
   az extension add --name azure-iot
   ```
[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Töltse le az Azure IoT C# mintákat,](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)és bontsa ki a ZIP-archívumot.

* Érvényes felhasználói fiók és hitelesítő adat az eszközön (Windows vagy Linux), amely a felhasználó hitelesítésére szolgál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrálhat egy szimulált eszközt.

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

1. Az IoT hubhoz való csatlakozáshoz és egy eszközadatfolyam létrehozásához az IoT-hubszolgáltatásból származó *szolgáltatáskapcsolati karakterláncra* is szüksége van a szolgáltatásoldali alkalmazás engedélyezéséhez. A következő parancs lekéri ezt az értéket az IoT hub:

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

A helyi terminálablakban keresse `device-streams-proxy/device` meg a projektkibontott mappában lévő könyvtárat. A következő információkat tartsa kéznél:

| Argumentum neve | Argumentum értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A korábban létrehozott eszköz eszközkapcsolati karakterlánca. |
| `targetServiceHostName` | Az az IP-cím, amelyet az SSH-kiszolgáló figyel. A cím `localhost` az lenne, ha ugyanaz az IP-cím lenne, ahol az eszközhelyi proxyalkalmazás fut. |
| `targetServicePort` | Az alkalmazásprotokoll által használt port (az SSH esetében alapértelmezés szerint ez a 22-es port).  |

A kód fordítása és futtatása a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>A szolgáltatás-helyi proxyalkalmazás futtatása

Egy másik helyi terminálablakban keresse meg `iot-hub/quickstarts/device-streams-proxy/service` a kibontott projektmappában. A következő információkat tartsa kéznél:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | Az IoT Hub szolgáltatáskapcsolati karakterlánca. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |
| `localPortNumber` | Egy helyi port, amelyhez az SSH-ügyfél csatlakozni fog. Ebben a mintában a 2222-es portot használjuk, de más tetszőleges számokat is használhat. |

A kód fordítása és futtatása a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Az SSH-ügyfél futtatása

Most használja az SSH-ügyfélalkalmazást, és csatlakozzon a szolgáltatás-helyi proxyalkalmazáshoz a 2222-es porton (közvetlenül az SSH démon helyett).

```
ssh {username}@localhost -p 2222
```

Ezen a ponton az SSH bejelentkezési ablak kéri, hogy adja meg a hitelesítő adatokat.

A szolgáltatás oldalon lévő konzolkimenet (a szolgáltatás-helyi proxyalkalmazás a 2222-es porton figyel):

![Szolgáltatás-helyi proxyalkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Konzolkimenet az eszközhelyi proxyalkalmazáson, amely *IP_address:22-kor*csatlakozik az SSH démonhoz:

![Eszközhelyi proxyalkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Az SSH-ügyfélalkalmazás konzolkimenete. Az SSH-ügyfél a 22-es porthoz való csatlakozással kommunikál az SSH démonnal, amelyet a szolgáltatáshelyi proxyalkalmazás figyel:

![SSH ügyfélalkalmazás kimenete](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>RDP eszközre eszközadatfolyamokon keresztül

Az RDP beállítása hasonló az SSH beállításához (lásd fent). Ehelyett az RDP cél IP-címét és a 3389-es portot használja, és az RDP-ügyfelet használja (az SSH-ügyfél helyett).

### <a name="run-the-device-local-proxy-application-rdp"></a>Az eszközhelyi proxyalkalmazás (RDP) futtatása

A helyi terminálablakban keresse `device-streams-proxy/device` meg a projektkibontott mappában lévő könyvtárat. A következő információkat tartsa kéznél:

| Argumentum neve | Argumentum értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A korábban létrehozott eszköz eszközkapcsolati karakterlánca. |
| `targetServiceHostName` | Az a állomásnév vagy IP-cím, amelyen az RDP-kiszolgáló fut. A cím `localhost` az lenne, ha ugyanaz az IP-cím lenne, ahol az eszközhelyi proxyalkalmazás fut. |
| `targetServicePort` | Az alkalmazásprotokoll által használt port (az RDP esetében alapértelmezés szerint ez a 3389-es port).  |

A kód fordítása és futtatása a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>A szolgáltatáshelyi proxyalkalmazás (RDP) futtatása

Egy másik helyi terminálablakban keresse meg `device-streams-proxy/service` a kibontott projektmappában. A következő információkat tartsa kéznél:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | Az IoT Hub szolgáltatáskapcsolati karakterlánca. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |
| `localPortNumber` | Egy helyi port, amelyhez az SSH-ügyfél csatlakozni fog. Ebben a mintában a 2222-es portot használjuk, de ezt más tetszőleges számokra módosíthatja. |

A kód fordítása és futtatása a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>RdP-ügyfél futtatása

Most használja az RDP-ügyfélalkalmazást, és csatlakozzon a szolgáltatás-helyi proxyalkalmazáshoz a 2222-es porton (ez egy korábban kiválasztott tetszőleges elérhető port volt).

![Az RDP a szolgáltatáshelyi proxyalkalmazáshoz csatlakozik](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállít egy IoT-központot, regisztrál egy eszközt, telepíti az eszköz-helyi és szolgáltatás-helyi proxyalkalmazásokat az IT hubon keresztüli eszközadatfolyam létrehozásához, és a proxyalkalmazásokat használja az SSH vagy RDP-forgalom alagtakiépítéséhez. Ugyanez a paradigma más ügyfél-kiszolgáló protokollokat is képes befogadni, ahol a kiszolgáló fut az eszközön (például az SSH démon).

Az eszközstreamekről az:

> [!div class="nextstepaction"]
> [Eszközadatfolyamok – áttekintés](./iot-hub-device-streams-overview.md)
