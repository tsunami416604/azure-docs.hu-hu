---
title: Egy eszköz alkalmazáshoz a kommunikációhoz C# keresztül az Azure IoT Hub eszköz adatfolyamok (előzetes verzió) |} A Microsoft Docs
description: Ebben a rövid útmutatóban a két minta futtatása C# IoT hubon keresztül létrehozott eszköz adatfolyam-n keresztül kommunikáló alkalmazásokat.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 74a8fc40cff12070f7cea99981eb4e8321d7c1ef
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735138"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Gyors útmutató: Egy eszköz alkalmazás a kommunikációhoz C# keresztül az IoT Hub eszköz adatfolyamok (előzetes verzió)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Az Azure IoT Hub jelenleg támogatja az eszköz adatfolyamok, mint egy [előzetes verziójú funkció](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az IoT Hub eszköz Streamek](./iot-hub-device-streams-overview.md) szolgáltatás és eszköz alkalmazások biztonságos és tűzfalbarát módon kommunikálnak. Ez a rövid útmutató magában foglalja a két C# alkalmazásokat, amelyek az eszköz streameket is küldhet adatokat oda-vissza (echo) előnyeit.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Előzetes verziójának eszköz Streamek jelenleg csak az IoT hub, az alábbi régiókban létrehozott támogatott:
  * USA középső régiója
  * USA középső RÉGIÓJA – EUAP

* Ebben a rövid útmutatóban futtató két mintaalkalmazásból használatával írt C#. A .NET Core SDK 2.1.0, vagy később a fejlesztői gépén van szüksége.
  * Töltse le a [.NET Core SDK a .NET használatával több platformon](https://www.microsoft.com/net/download/all).
  * Ellenőrizze a jelenlegi verziója C# a fejlesztői gépen, a következő paranccsal:

   ```
   dotnet --version
   ```

* Adja hozzá az Azure IoT-bővítmény az Azure CLI-vel a Cloud Shell-példányhoz a következő parancs futtatásával. Az IOT-bővítmény hozzáadja az IoT Hub, IoT Edge és IoT Device Provisioning Service (DPS) – az Azure CLI parancsok.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Töltse le a mintát C# projekt](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) , és bontsa ki a ZIP-archívumot. Az eszköz oldalán és a Szolgáltatásoldali van szükség.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a szakaszban használhatja az Azure Cloud Shell regisztrál egy szimulált eszközt.

1. Hozza létre az eszközidentitást, a Cloud Shellben futtassa az alábbi parancsot:

   > [!NOTE]
   > * Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.
   > * Használat *Sajáteszköz*látható módon. A regisztrált eszköz a megadott név legyen. Ha úgy dönt, hogy az eszköz egy másik nevet, használja során ez a cikk ezt a nevet, és az eszköz nevére a mintaalkalmazásból ahhoz, hogy futtatni őket.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Az első a *eszköz kapcsolati karakterláncának* az imént regisztrált eszközhöz, futtassa a következő parancsot a Cloud Shellben:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Megjegyzés: az eszköz kapcsolati karakterláncának későbbi használatra ebben a rövid útmutatóban. Az alábbi példához hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Emellett a *szolgáltatáskapcsolati karakterláncra* az IoT hub a Szolgáltatásoldali alkalmazás csatlakoztatása az IoT hubhoz, és létrehozza a eszköz stream engedélyezése. Az alábbi parancs lekéri az IoT hub ezt az értéket:

   > [!NOTE]
   > Cserélje le a *YourIoTHubName* helyőrzőt az IoT hub választott név.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Megjegyzés: a visszaadott érték az ebben a rövid útmutató későbbi használatra. Az alábbi példához hasonlóan néz ki:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Az eszköz és az eszköz Streamek keresztül a szolgáltatás közötti kommunikációhoz

Ebben a szakaszban az eszközoldali alkalmazás, mind a Szolgáltatásoldali alkalmazás futtatásához, és a kettő közötti kommunikációhoz.

### <a name="run-the-service-side-application"></a>A Szolgáltatásoldali alkalmazás futtatása

Nyissa meg a *iot-hub/rövid útmutatók és-adatfolyamok-echo/szolgáltatás* könyvtárat a kicsomagolt projektet tartalmazó mappában. Tartsa praktikus a következő információkat:

| Paraméter neve | Hodnota parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Adja meg az IoT hub szolgáltatáskapcsolati karakterláncát. |
| `DeviceId` | Adja meg a korábban létrehozott az eszköz Azonosítóját (például *Sajáteszköz*). |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Időtúllépés történik, ha az eszköz ügyféloldali alkalmazás időben nem válaszol.

### <a name="run-the-device-side-application"></a>Az eszközoldali alkalmazás futtatása

Nyissa meg a *iot-hub/rövid útmutatók/eszköz-adatfolyamok-echo/eszköz* könyvtárat a kicsomagolt projektet tartalmazó mappában. Tartsa praktikus a következő információkat:

| Paraméter neve | Hodnota parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Adja meg az IoT hub, az eszköz kapcsolati karakterláncát. |

Fordítsa le és futtassa a kódot az alábbiak szerint:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

Az utolsó lépés végén a Szolgáltatásoldali alkalmazás elindítja az eszköz egy stream. A stream létrejötte után az alkalmazás egy karakterláncpuffert a szolgáltatás az adatfolyam keresztül küld. Ebben a példában a Szolgáltatásoldali alkalmazás egyszerűen ad vissza az eszközön, amely bemutatja egy sikeres kétirányú kommunikációt a két alkalmazás közötti ugyanazokat az adatokat.

A konzol kimenetét, az eszköz oldalán:

![Az eszköz oldalán a konzol kimenete](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

A konzol kimenetét, a szolgáltatás oldalon:

![A szolgáltatás oldalán a konzol kimenete](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

A forgalom, a stream keresztül küld az IoT hubon keresztül bújtatott ahelyett, küldi. A aktiválásáért részletesen ismertetett [eszköz streameli előnyöket](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid, már beállítása egy IoT hubot, regisztrált egy eszközt, egy eszköz stream között létrehozott C# alkalmazások az eszköz és szolgáltatás oldalán, és a stream oda-vissza az alkalmazások közötti adatküldéshez.

Eszköz Streamek kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Eszköz Streamek áttekintése](./iot-hub-device-streams-overview.md)
