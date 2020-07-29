---
title: Eszköz vezérlése Azure IoT Hubról – rövid útmutató (.NET) | Microsoft Docs
description: Ebben a rövid útmutatóban két C# mintaalkalmazást fog futtatni. Az egyik egy háttéralkalmazás, amely a hubhoz csatlakoztatott eszközök távoli vezérlését teszi lehetővé. A másik alkalmazás a hubhoz csatlakoztatott eszközt szimulál, amelyet távolról lehet irányítani.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
ms.date: 03/04/2020
ms.openlocfilehash: 1b3b8382c81015e3278954dd0443ba44520e2e3b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87315138"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>Rövid útmutató: IoT Hubhoz csatlakozó eszköz vezérlése (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

A IoT Hub egy olyan Azure-szolgáltatás, amely lehetővé teszi a IoT-eszközök Felhőbeli kezelését, valamint a felhőbe irányuló nagy mennyiségű eszköz telemetria történő tárolását és feldolgozását. Ebben a rövid útmutatóban egy *közvetlen metódussal* fogja vezérelni az IoT Hubhoz csatlakoztatott szimulált eszközt. A közvetlen metódusok használatával távolról módosíthatja az IoT Hubhoz csatlakoztatott eszköz működését.

Ez a rövid útmutató két előre megírt .NET-alkalmazást használ:

* Egy szimulálteszköz-alkalmazás, amely válaszol a háttéralkalmazásokból meghívott közvetlen metódusokra. A közvetlen metódusok meghívásának fogadásához ez az alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik.

* Egy háttéralkalmazás, amely meghívja a közvetlen metódusokat a szimulált eszközre. A közvetlen metódus egy eszközre való meghívásához ez az alkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutatóban futtatott két mintaalkalmazás a C# használatával készült. A fejlesztői gépen szükség lesz a .NET Core SDK 2.1.0-s vagy újabb változatára.

A .NET Core SDK-t többféle platformra a [.NET](https://www.microsoft.com/net/download/all) oldaláról töltheti le.

A C# aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
dotnet --version
```

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Ha még nem tette meg, töltse le az Azure IoT C#-mintákat, https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip és bontsa ki a zip-archívumot.

Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az ebben a rövid útmutatóban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-dotnet.md) című előző útmutatót, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Eszköz regisztrálása

Ha már elvégezte a [Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra](quickstart-send-telemetry-dotnet.md) című előző útmutatót, kihagyhatja ezt a lépést.

Az eszköznek regisztrálva kell lennie az IoT Hubbal, hogy csatlakozhasson hozzá. Ebben a rövid útmutatóban az Azure Cloud Shell használatával regisztrál egy szimulált eszközt.

1. Futtassa az alábbi parancsot a Azure Cloud Shell az eszköz identitásának létrehozásához.

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

   **MyDotnetDevice**: a regisztrált eszköz neve. Javasoljuk, hogy a **MyDotnetDevice** használja az ábrán látható módon. Ha másik nevet választ az eszköznek, akkor a jelen cikkben is ezt a nevet kell használnia, és a futtatásuk előtt frissítenie kell az eszköz nevét a minta alkalmazásokban.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Futtassa az alábbi parancsokat az Azure Cloud Shellben az imént regisztrált eszköz _eszközkapcsolati sztringjének_ lekéréséhez:

   **YourIoTHubName**: az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name {YourIoTHubName} \
      --device-id MyDotnetDevice \
      --output table
    ```

    Jegyezze fel az eszközkapcsolati sztringet, amely az alábbihoz hasonlóan néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ezt az értéket használni fogja a rövid útmutató későbbi részében.

## <a name="retrieve-the-service-connection-string"></a>A szolgáltatás kapcsolati sztringjének lekérése

Szüksége van az IoT Hub _szolgáltatáskapcsolati sztringjére_ is, amellyel lehetővé teheti, hogy a háttéralkalmazás csatlakozhasson az IoT Hubhoz, és üzeneteket kérhessen le. Az alábbi parancs lekéri az IoT Hub szolgáltatáskapcsolati sztringjét:

```azurecli-interactive
az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
```

Jegyezze fel a szolgáltatáskapcsolati sztringet, amely a következőképpen néz ki:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Ezt az értéket használni fogja a rövid útmutató későbbi részében. Ez a szolgáltatási kapcsolatok karakterlánca különbözik az előző lépésben feljegyzett eszköz-összekapcsolási karakterlánctól.

## <a name="listen-for-direct-method-calls"></a>Közvetlen metódusok hívásának figyelése

A szimulálteszköz-alkalmazás az IoT Hubon található eszközspecifikus végponthoz csatlakozik, szimulált telemetriát küld, és figyeli a hubról érkező közvetlenmetódus-hívásokat. Ebben a rövid útmutatóban a hubról érkező közvetlenmetódus-hívás arra utasítja az eszközt, hogy módosítsa a telemetriaküldések közötti időintervallumot. A szimulált eszköz visszaigazolást küld a hubhoz a közvetlen metódus végrehajtása után.

1. Egy helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\simulated-device-2** mappába.

2. Nyissa meg a **SimulatedDevice.cs** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a változó értékét `s_connectionString` a korábban megjegyzett eszköz-összekapcsolási sztringre. Ezután mentse a módosításokat a **SimulatedDevice.cs**.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a szimulálteszköz-alkalmazáshoz szükséges csomagok telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

4. Futtassa az alábbi parancsot a helyi terminálablakban a szimulálteszköz-alkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    dotnet run
    ```

    A következő képernyőképen az a kimenet látható, amikor a szimulálteszköz-alkalmazás telemetriát küld az IoT Hubnak:

    ![A szimulált eszköz futtatása](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>A közvetlen metódus meghívása

A háttéralkalmazás az IoT Hubon található szolgáltatásoldali végponthoz csatlakozik. Az alkalmazás lehetővé teszi, hogy a közvetlen metódus hívásokat hajtson végre egy eszközön az IoT hub segítségével, és figyelje a nyugtákat. Az IoT Hub-háttéralkalmazások általában a felhőben futnak.

1. Egy másik helyi terminálablakban keresse meg a C#-mintaprojekt gyökérmappáját. Ezután lépjen az **iot-hub\Quickstarts\back-end-application** mappába.

2. Nyissa meg a **BackEndApplication.cs** fájlt egy Ön által választott szövegszerkesztőben.

    Cserélje le a változó értékét arra `s_connectionString` a szolgáltatás-összekapcsolási sztringre, amelyet korábban jegyzett készített. Ezután mentse a módosításokat a **BackEndApplication.cs**.

3. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazáshoz szükséges kódtárak telepítéséhez:

    ```cmd/sh
    dotnet restore
    ```

4. Futtassa az alábbi parancsokat a helyi terminálablakban a háttéralkalmazás létrehozásához és futtatásához:

    ```cmd/sh
    dotnet run
    ```

    Az alábbi képernyőfelvételen a kimenet látható, mivel az alkalmazás közvetlen metódust hív meg az eszköznek, és nyugtát kap:

    ![A háttéralkalmazás futtatása](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    A háttéralkalmazás futtatása után megjelenik egy üzenet a szimulált eszközt futtató konzolablakban, és megváltozik az üzenetküldések gyakorisága:

    ![Változás a szimulált ügyfélben](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban közvetlen metódust hívott egy eszközön egy háttérbeli alkalmazásból, és a közvetlen metódus hívására válaszolt egy szimulált eszköz alkalmazásban.

Ha szeretné megtudni, hogy hogyan irányíthatók az eszközről felhőbe irányuló üzenetek különböző felhőbeli célokhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Telemetria irányítása különböző végpontokra feldolgozás céljából](tutorial-routing.md)
