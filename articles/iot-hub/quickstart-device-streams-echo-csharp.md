---
title: Kommunikáció egy eszköz alkalmazásával az C# Azure IoT hub-eszközökön keresztül (előzetes verzió) | Microsoft Docs
description: Ebben a rövid útmutatóban két minta C# alkalmazást futtat, amelyek egy IoT hub használatával létrehozott eszköz-adatfolyamon keresztül kommunikálnak.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c994b77105fe94eef418c0befc4c135ec09ada14
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900934"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Gyors útmutató: IoT Hub- C# adatfolyamon keresztüli kommunikáció egy eszköz alkalmazásával (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Az Azure IoT Hub jelenleg [előzetes verziójú szolgáltatásként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)támogatja az eszközök streamjét.

[IoT hub az eszközökön elérhető streamek](./iot-hub-device-streams-overview.md) lehetővé teszik a szolgáltatás-és eszköz-alkalmazások számára a biztonságos és tűzfalon alapuló kommunikációt. Ez a rövid útmutató C# két olyan alkalmazást foglal magába, amelyek kihasználják az adatfolyamok számára az adatküldés (ECHO) által nyújtott előnyöket.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az adatfolyamok előnézete jelenleg csak a következő régiókban létrehozott IoT hubok esetében támogatott:
  * USA középső régiója
  * USA középső – EUAP
  * Észak-Európa
  * Délkelet-Ázsia

* Az ebben a rövid útmutatóban futtatott két minta alkalmazás beírása folyamatban van C#. A fejlesztői gépen szükség van a .NET Core SDK 2.1.0 vagy újabb verzióra.
  * Töltse le a [.net Core SDKt több platformra a .net-ről](https://www.microsoft.com/net/download/all).
  * A következő parancs használatával ellenőrizze C# a fejlesztői gépen a jelenlegi verzióját:

   ```
   dotnet --version
   ```

* A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Azure IoT-bővítményt a Cloud Shell-példányához. Az IOT bővítmény hozzáadja az Azure CLI-hez IoT Hub, IoT Edge és IoT eszközök kiépítési szolgáltatásának (DPS) specifikus parancsait.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Töltse le a C# minta projektet](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) , és CSOMAGOLja ki a zip-archívumot. Szüksége lesz rá az eszköz és a szolgáltatás oldalán is.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban a Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Az eszköz identitásának létrehozásához futtassa a következő parancsot Cloud Shellban:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.
   > * Annak az eszköznek a nevére, amelyet regisztrál, javasolt a *MyDevice* használata az ábrán látható módon. Ha más nevet választ az eszköznek, ezt a nevet használja ebben a cikkben, és a futtatásuk előtt frissítse az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Az imént regisztrált eszközhöz tartozó *eszköz-kapcsolódási karakterlánc* lekéréséhez futtassa a következő parancsot a Cloud Shellban:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Jegyezze fel a visszaadott eszköz csatlakoztatási karakterláncát a rövid útmutató későbbi verzióihoz. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Emellett az IoT hub *szolgáltatás kapcsolati karakterláncára* is szüksége lesz ahhoz, hogy a kiszolgálóoldali alkalmazás kapcsolódjon az IoT hubhoz, és hozzon létre egy adatfolyamot. A következő parancs lekérdezi ezt az értéket az IoT hub esetében:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Jegyezze fel a visszaadott szolgáltatás-kapcsolatok karakterláncát a rövid útmutató későbbi verzióihoz. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Kommunikáció az eszköz és a szolgáltatás között az eszköz streamen keresztül

Ebben a szakaszban az eszköz-és a kiszolgálóoldali alkalmazást is futtatja, és kommunikál a kettő között.

### <a name="run-the-service-side-application"></a>A szolgáltatás-oldali alkalmazás futtatása

A helyi terminál ablakban navigáljon a kibontott projekt mappájában található `iot-hub/Quickstarts/device-streams-echo/service` könyvtárhoz. A következő információk hasznosak maradnak:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | Az IoT hub szolgáltatási kapcsolatok karakterlánca. |
| `MyDevice` | A korábban létrehozott eszköz azonosítója. |

Fordítsa le és futtassa a kódot az alábbi parancsokkal:

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
Az alkalmazás megvárja, amíg az alkalmazás elérhetővé válik.

> [!NOTE]
> Időtúllépés történik, ha az eszköz oldali alkalmazás nem válaszol időben.

### <a name="run-the-device-side-application"></a>Az eszköz oldali alkalmazás futtatása

Egy másik helyi terminál ablakban navigáljon a kibontott projekt mappájában található `iot-hub/Quickstarts/device-streams-echo/device` könyvtárhoz. A következő információk hasznosak maradnak:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `DeviceConnectionString` | A IoT Hub eszköz-csatlakoztatási karakterlánca. |

Fordítsa le és futtassa a kódot az alábbi parancsokkal:

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

Az utolsó lépés végén a szolgáltatás-oldali alkalmazás elindít egy streamet az eszközre. A stream létrehozása után az alkalmazás karakterlánc-puffert küld a szolgáltatásnak az adatfolyamon keresztül. Ebben a példában a szolgáltatás oldali alkalmazás egyszerűen visszaigazolja ugyanazokat az eszközöket az eszközre, amely sikeres kétirányú kommunikációt mutat be a két alkalmazás között.

Konzol kimenete az eszköz oldalán:

![Konzol kimenete az eszköz oldalán](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konzol kimenete a szolgáltatás oldalán:

![Konzol kimenete a szolgáltatás oldalán](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Az adatfolyamon keresztül küldött forgalom a IoT hub használatával lesz átirányítva ahelyett, hogy közvetlenül elküldjék. A megadott előnyök az [eszközök stream-előnyeiben](./iot-hub-device-streams-overview.md#benefits)vannak részletezve.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy IoT hub, egy eszköz regisztrálása, az eszköz és a szolgáltatás oldalán C# lévő alkalmazások közötti adatfolyamot állított be, és a stream használatával visszaküldheti az alkalmazásokat az alkalmazások között.

További információ az eszközök streamekről:

> [!div class="nextstepaction"]
> [Az eszközök adatfolyamának áttekintése](./iot-hub-device-streams-overview.md)
