---
title: A IoT csatlakoztatása Plug and Play a Python-eszköz kódjának megtekintése az Azure-ban IoT Hub | Microsoft Docs
description: A Python használatával hozzon létre és futtasson IoT Plug and Play előzetes minta-eszköz kódját, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352921"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>Gyors útmutató: minta IoT csatlakoztatása Plug and Play Preview-eszköz alkalmazásának beIoT Hub (Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A minta alkalmazás a Pythonhoz készült, és megtalálható a Pythonhoz készült Azure IoT Hub Device SDK-ban. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a fejlesztői gépen a Python 3,7 szükséges. A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót. A Python-verziót a következő paranccsal tekintheti meg:  

```cmd/sh
python --version
```

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Ha a rövid útmutató második részében a minta eszközzel szeretne kommunikálni, használja az **Azure IoT Explorer** eszközt. [Töltse le és telepítse az Azure IoT Explorer legújabb kiadását](./howto-use-iot-explorer.md) az operációs rendszeréhez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez. Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később a rövid útmutatóban fog használni:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Az Azure IoT Explorer eszközzel is megkeresheti az IoT hub kapcsolódási karakterláncát.

A következő parancs futtatásával lekérheti a hubhoz felvett eszközhöz tartozó _eszköz-kapcsolódási karakterláncot_ . Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később a rövid útmutatóban fog használni:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>A környezet kialakítása

Ez a csomag a nyilvános előzetes verzió frissítésének PIP-ként van közzétéve. A csomag verziószámának legújabbnak kell lennie, vagy`2.1.4`

A helyi Python-környezetben a következő módon telepítse a fájlt:

```cmd/sh
pip install azure-iot-device
```

A Python SDK IoT-tárház klónozása és a **Master**:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

A *Azure-IOT-SDK-python\azure-IOT-device\samples\pnp* mappa tartalmazza a IOT Plug and Play eszközhöz tartozó mintakód kódját. Ez a rövid útmutató a *pnp_thermostat.* a fájlt használja. Ez a mintakód egy IoT Plug and Play kompatibilis eszközt valósít meg, és az Azure IoT Python-eszköz ügyféloldali függvénytárát használja.

Hozzon létre egy **IOTHUB_DEVICE_CONNECTION_STRING** nevű környezeti változót, amely a korábban jegyzett eszköz-kapcsolódási karakterláncot tárolja.

Nyissa meg a **pnp_thermostat.** a fájlt egy szövegszerkesztőben. Figyelje meg, hogyan:

1. Meghatározza a [termosztátot](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)egyedileg jelölő, egyetlen eszközből álló Twin Model-azonosítót (DTMI). A felhasználónak ismernie kell a DTMI, és az eszköz implementációjának típusától függően változhat. Az aktuális minta esetében a modell egy olyan termosztátot jelöl, amely a figyelési hőmérséklettel társított telemetria, tulajdonságokkal és parancsokkal rendelkezik.

1. A a parancssori implementációk definiálásához használható függvényeket tartalmaz. Ezeket a kezelőket úgy írhatja be, hogy meghatározza, hogyan válaszol az eszköz a parancs kéréseire.

1. Függvényt ad meg a parancs válaszának definiálásához. A parancs-válasz függvények segítségével visszaküldhet egy választ az IoT hub-ra.

1. Definiál egy bemeneti billentyűzet-figyelő függvényt, amely lehetővé teszi az alkalmazásból való kilépést.

1. **Fő** funkciója van. A **fő** függvény:

    1. Az eszközoldali SDK használatával hozza létre az eszköz ügyfelet, és csatlakozik az IoT hubhoz.

    1. Frissítések tulajdonságai. Az általunk használt modell, a **termosztát**, valamint `targetTemperature` `maxTempSinceLastReboot` a termosztát két tulajdonsága, hogy mi lesz a használata. A tulajdonságok a `patch_twin_reported_properties` on definiált metódus használatával frissülnek `device_client` .

    1. A **execute_command_listener** függvény használatával elindítja a parancsokra vonatkozó kérelmek figyelését. A függvény egy "figyelőt" állít be a szolgáltatásból érkező parancsok figyelésére. A figyelő beállításakor a, a `method_name` `user_command_handler` és a `create_user_response_handler` . 
        - A `user_command_handler` függvény határozza meg, hogy az eszköz mit tegyen, ha parancsot kap. Ha például a riasztás leáll, a parancs fogadásának hatása felébred. Gondolja át ezt a parancsot a meghívott parancs hatására.
        - A `create_user_response_handler` függvény létrehoz egy választ, amelyet a rendszer a IoT hub számára fog elküldeni, ha a parancs végrehajtása sikeresen befejeződött. Ha például a riasztás leáll, a Szundi késleltetéssel válaszol, amely a szolgáltatásnak küldött visszajelzés. Erre a szolgáltatásra adott válaszként gondoljon. Ez a válasz a portálon tekinthető meg.

    1. Elindítja a telemetria küldését. A **pnp_send_telemetry** a pnp_methods. file. reszelő fájlban van definiálva. A mintakód egy hurok használatával hívja meg ezt a függvényt nyolc másodpercenként.

    1. Letiltja az összes figyelőt és feladatot, és a **q** vagy a **q**gomb megnyomásakor a hurok is létezik.

Most, hogy már látta a kódot, a következő paranccsal futtathatja a mintát:

```cmd/sh
python pnp_thermostat.py
```

A következő kimenet jelenik meg, amely azt jelzi, hogy az eszköz telemetria küld a központba, és most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására:

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A megoldáshoz csatlakoztatott IoT Plug and Play előnézeti eszköz használata](quickstart-service-python.md)
