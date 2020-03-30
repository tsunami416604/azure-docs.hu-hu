---
title: Kommunikáció az eszközalkalmazással C# nyelven az Azure IoT Hub-eszközadatfolyamokkal
description: Ebben a rövid útmutatóban két c#-os mintaalkalmazást futtat, amelyek az IoT Hubon keresztül létrehozott eszközadatfolyamon keresztül kommunikálnak.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 64af62cb6c2c56ca8c7e67e2f1467d4a7e8335a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675521"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Rövid útmutató: Kommunikáció egy eszközalkalmazással C# nyelven az IoT Hub-eszközadatfolyamokon keresztül (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Az Azure IoT Hub jelenleg támogatja az eszközstreamelési [funkciót előnézeti funkcióként.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Az IoT Hub eszközadatfolyamok](./iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás- és eszközalkalmazások biztonságos és tűzfalbarát kommunikációját. Ez a rövid útmutató két C# alkalmazást foglal magában, amelyek kihasználják az eszközadatfolyamok előnyeit az adatok oda-vissza küldéséhez (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Az eszközstreamek előzetes verziója jelenleg csak a következő régiókban létrehozott IoT-központok számára támogatott:
  * USA középső régiója
  * USA középső régiója
  * Észak-Európa
  * Délkelet-Ázsia

* A rövid útmutatóban futtatott két mintaalkalmazás C#-ban íródott. A .NET Core SDK 2.1.0 vagy újabb verzióra van szüksége a fejlesztői gépen.
  * Töltse le a [.NET Core SDK-t több platformra a .NET-ről.](https://www.microsoft.com/net/download/all)
  * Ellenőrizze a C# aktuális verzióját a fejlesztőgépen a következő paranccsal:

   ```
   dotnet --version
   ```

* Adja hozzá az Azure CLI-hez való Azure IoT-bővítményt a következő parancs futtatásával a Cloud Shell-példányhoz. Az IOT-bővítmény iot hub, IoT Edge és IoT-eszközlétesítési szolgáltatás (DPS)-specifikus parancsokat ad hozzá az Azure CLI-hez.

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Töltse le az Azure IoT C# mintákat,](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) és bontsa ki a ZIP-archívumot. Szüksége van rá mind a készülék oldalán, mind a szervizoldalon.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban az Azure Cloud Shell használatával regisztrálhat egy szimulált eszközt.

1. Az eszközidentitás létrehozásához futtassa a következő parancsot a Cloud Shell ben:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.
   > * A regisztrálandó eszköz nevéhez ajánlott a *MyDevice* használata az ábrán látható módon. Ha más nevet választ az eszközhöz, használja ezt a nevet a cikkben, és frissítse az eszköz nevét a mintaalkalmazásokban, mielőtt futtatja őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Az imént regisztrált eszköz *eszközkapcsolati karakterláncának* lekérnie, futtassa a következő parancsot a Cloud Shellben:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Vegye figyelembe a visszaadott eszköz kapcsolati karakterláncot későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Az IoT hubról a *szolgáltatáskapcsolati karakterláncra* is szüksége van ahhoz, hogy a szolgáltatásoldali alkalmazás csatlakozzon az IoT hubhoz, és hozzon létre egy eszközadatfolyamot. A következő parancs lekéri ezt az értéket az IoT hub:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hubhoz választott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Vegye figyelembe a visszaadott szolgáltatás kapcsolati karakterláncot későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunikáció az eszköz és a szolgáltatás között az eszközadatfolyamokon keresztül

Ebben a szakaszban futtatja az eszközoldali alkalmazást és a szolgáltatásoldali alkalmazást, és kommunikál a kettő között.

### <a name="run-the-service-side-application"></a>A szolgáltatásoldali alkalmazás futtatása

A helyi terminálablakban keresse `iot-hub/Quickstarts/device-streams-echo/service` meg a projektkibontott mappában lévő könyvtárat. A következő információkat tartsa kéznél:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | Az IoT hub szolgáltatáskapcsolati karakterlánca. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |

A kód fordítása és futtatása a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Az alkalmazás megvárja, amíg az eszközalkalmazás elérhetővé válik.

> [!NOTE]
> Időtúljelentkezés következik be, ha az eszközoldali alkalmazás nem válaszol időben.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Egy másik helyi terminálablakban `iot-hub/Quickstarts/device-streams-echo/device` keresse meg a projektkibontott mappában lévő könyvtárat. A következő információkat tartsa kéznél:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `DeviceConnectionString` | Az IOt Hub eszközkapcsolati karakterlánca. |

A kód fordítása és futtatása a következő parancsokkal:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

Az utolsó lépés végén a szolgáltatásoldali alkalmazás adatfolyamot kezdeményez az eszközre. Az adatfolyam létrehozása után az alkalmazás egy karakterlánc-puffert küld a szolgáltatásnak az adatfolyamon keresztül. Ebben a példában a szolgáltatásoldali alkalmazás egyszerűen ugyanazokat az adatokat visszhangozza vissza az eszközre, amely bizonyítja a két alkalmazás közötti sikeres kétirányú kommunikációt.

Konzol kimenet a készülék oldalán:

![Konzol kimenet e készülék oldalán](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konzol kimenet a szolgáltatási oldalon:

![Konzol kimenete a szolgáltatási oldalon](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Az adatfolyamon keresztül küldött forgalom az IoT hubon keresztül bújtat, nem pedig közvetlenül. A nyújtott előnyöket az [Eszközfolyamok előnyei részletezik.](./iot-hub-device-streams-overview.md#benefits)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban beállít egy IoT hubot, regisztrált egy eszközt, létrehozott egy eszközadatfolyamot az eszközön és a szolgáltatás oldalán a C# alkalmazások között, és az adatfolyamot használta az adatok oda-vissza küldésére az alkalmazások között.

Az eszközstreamekről az:

> [!div class="nextstepaction"]
> [Eszközadatfolyamok – áttekintés](./iot-hub-device-streams-overview.md)
