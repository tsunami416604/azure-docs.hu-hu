---
title: Az Azure IoT Hub eszköz Streamek C# rövid útmutató (előzetes verzió) |} A Microsoft Docs
description: Ebben a rövid útmutatóban két minta futtatása lesz C# IoT hubon keresztül létrehozott eszköz adatfolyam-n keresztül kommunikáló alkalmazásokat.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 2853bd5539a40e3b38927f619756fe37a4cec984
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006855"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>Gyors útmutató: Az eszköz alkalmazások kommunikálni C# keresztül az IoT Hub eszköz adatfolyamok (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

A Microsoft Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. Ez a rövid útmutató magában foglalja a két C# programok, az eszköz streameket is küldhet adatokat oda-vissza (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Előzetes verziójának eszköz Streamek jelenleg csak a az IoT-központok létrehozni a következő régiókban támogatott:

  - **USA középső régiója**
  - **USA középső régiója – EUAP**

A rövid útmutatóban futtatott két mintaalkalmazás a C# használatával készült. A fejlesztői gépen szükség lesz a .NET Core SDK 2.1.0-s vagy újabb változatára.

A .NET Core SDK-t többféle platformra a [.NET](https://www.microsoft.com/net/download/all) oldaláról töltheti le.

A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```
dotnet --version
```

Futtassa a következő parancsot a Microsoft Azure IoT-bővítmény hozzáadása a Cloud Shell-példány Azure CLI-hez. Az IOT-bővítmény hozzáadása Azure CLI-vel az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) parancsok.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Töltse le a C#-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip címről, és bontsa ki a ZIP-archívumot. Eszköz és a szolgáltatás oldalán kell.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

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

3. Emellett a _szolgáltatáskapcsolati karakterláncra_ az IoT hub a Szolgáltatásoldali alkalmazás csatlakoztatása az IoT hubhoz, és létrehozza a eszköz stream engedélyezése. Az alábbi parancs lekéri az IoT hub ezt az értéket:

   **YourIoTHubName**: Cserélje le a helyőrző alábbi úgy dönt, az IoT hub nevét.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Jegyezze fel, a visszaadott érték, amely a következőhöz hasonló:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Eszköz és szolgáltatás keresztül eszköz adatfolyamok közötti kommunikáció

### <a name="run-the-service-side-application"></a>A Szolgáltatásoldali alkalmazás futtatása

Navigáljon a `iot-hub/Quickstarts/device-streams-echo/service` a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `ServiceConnectionString` | Adja meg az IoT hub szolgáltatáskapcsolati karakterláncát. |
| `DeviceId` | Adja meg az eszköz azonosítója, a korábban létrehozott például Sajáteszköz. |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Időtúllépés történik, ha az eszköz ügyféloldali alkalmazás időben nem válaszol.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Navigáljon a `iot-hub/Quickstarts/device-streams-echo/device` könyvtárat a kicsomagolt projektet tartalmazó mappában. Szüksége lesz a következő információkat hasznos:

| Paraméter neve | Paraméter értéke |
|----------------|-----------------|
| `DeviceConnectionString` | Adja meg az IoT hub, az eszköz kapcsolati karakterláncát. |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

Az utolsó lépés végén a Szolgáltatásoldali program egy streamet, az eszközre, és lesz létrejöttét követően megkezdődik a szolgáltatás egy karakterláncpuffert küldenie a streamet. Ebben a példában a Szolgáltatásoldali program egyszerűen ad vissza az eszköz sikeres kétirányú kommunikációt a két alkalmazás közötti bemutatásához ugyanazokat az adatokat. Lásd az alábbi ábrát.

Az eszköz oldalán konzolkimenetet: ![helyettesítő szöveg](./media/quickstart-device-streams-echo-csharp/device-console-output.png "Konzolkimenetet eszköz-oldalán")

A szolgáltatás oldalon konzolkimenetet: ![helyettesítő szöveg](./media/quickstart-device-streams-echo-csharp/service-console-output.png "Konzolkimenetet Szolgáltatásoldali-")

A forgalmat küld a rendszer a stream keresztül fog bújtatni közvetlenül elküldött helyett az IoT Hub segítségével. Ez [értékelemeket](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, rendelkezik beállítása egy IoT hubot, regisztrált egy eszközt, egy eszköz stream között létrehozott C# eszköz és szolgáltatás oldalán, alkalmazások és a stream oda-vissza az alkalmazások közötti adatküldéshez.

További információ az eszköz adatfolyamok az alábbi hivatkozásokat:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
