---
title: A IoT csatlakoztatása Plug and Play a Python összetevő-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play Preview minta Python-eszköz kódját, amely több összetevőt használ, és csatlakozik egy IoT hubhoz. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 571f0e0ceff0adfbf1814abc627fcab6b23acbe1
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905856"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-python"></a>Oktatóanyag: minta IoT csatlakoztatása Plug and Play előzetes verzió több összetevő-eszköz alkalmazása IoT Hub (Python)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy minta IoT Plug and Play eszköz-alkalmazást az összetevőkkel és a gyökérszintű felülettel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa az adott hubhoz küldött adatokat. A minta alkalmazás Pythonban van megírva, és része a Pythonhoz készült Azure IoT Device SDK-nak. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez Python 3,7 szükséges a fejlesztői gépen. A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót. A Python-verziót a következő paranccsal tekintheti meg:  

```cmd/sh
python --version
```

A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót.

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Az oktatóanyag második részében az **Azure IoT Explorer** eszköz használatával kommunikálhat a minta eszközzel. [Töltse le és telepítse az Azure IoT Explorer legújabb kiadását](./howto-use-iot-explorer.md) az operációs rendszeréhez.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez. Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később az oktatóanyagban használ:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Az Azure IoT Explorer eszközzel is megkeresheti az IoT hub kapcsolódási karakterláncát.

A következő parancs futtatásával lekérheti a hubhoz felvett eszközhöz tartozó _eszköz-kapcsolódási karakterláncot_ . Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később az oktatóanyagban használ:

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

A Python SDK IoT-tárház klónozása és a **PnP-előnézet-frissítés**kivétele:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. A minta által megvalósított modell [több összetevőt](concepts-components.md)használ. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

A *Azure-IOT-SDK-python\azure-IOT-device\samples\pnp* mappa tartalmazza a IOT Plug and Play eszközhöz tartozó mintakód kódját. A hőmérséklet-vezérlő mintájának fájljai a következők:

- pnp_temp_controller_with_thermostats.
- pnp_helper_preview_refresh.

A hőmérséklet-vezérlő több összetevővel és egy gyökérszintű interfésszel rendelkezik, a hőmérséklet-vezérlő DTDL modellje alapján.

Nyissa meg a *pnp_temp_controller_with_thermostats.* a fájlt egy tetszőleges szerkesztőben. A fájlban található kód:

1. Importálás `pnp_helper_preview_refresh.py` a segítő módszerekhez való hozzáféréshez.

2. A két digitális kettős modell azonosítóját (DTMIs) határozza meg, amelyek egyedileg jelölik a DTDL modellben definiált két különböző felületet. A valós hőmérséklet-vezérlő összetevőinek meg kell valósítaniuk ezt a két felületet. Ez a két csatoló már közzé van téve egy központi tárházban. Ezeknek a DTMIs ismerniük kell a felhasználó számára, és az eszközök megvalósítási forgatókönyvtől függően változhatnak. Az aktuális minta esetében ez a két csatoló a következőket képviseli:

  - Egy termosztát
  - Az Azure által fejlesztett eszközök adatai.

3. A `model_id` megvalósított eszköz DTMI határozza meg. A DTMI felhasználó által definiált, és meg kell egyeznie a DTMI a DTDL-modell fájljában.

4. Meghatározza a DTDL-fájlban található összetevők nevét. Két termosztát található a DTDL és egy eszköz-információs összetevőben. A rendszer a `serial_number` gyökérszintű felületen is definiált konstanst. Egy `serial_number` eszköz nem módosítható.

5. A parancs-kezelő implementációit határozza meg. Ezek határozzák meg, hogy az eszköz mikor kapja meg a parancs kéréseit.

6. Függvények definiálása a parancs válaszának létrehozásához. Ezek határozzák meg, hogy az eszköz hogyan válaszol a parancs kéréseire. Ha egy parancsnak egyéni választ kell küldenie az IoT hubhoz, a parancs-válasz függvényeket kell létrehoznia. Ha nincs megadva egy parancshoz tartozó Response függvény, a rendszer általános választ kap. Ebben a mintában csak a **getMaxMinReport** parancs egyéni választ tartalmaz.

7. Definiál egy függvényt, amely telemetria küld az eszközről. A Termosztátok és a legfelső szintű interfész telemetria is küldenek. Ez a függvény egy opcionális összetevő-Name paramétert használ, amely lehetővé teszi annak azonosítását, hogy a telemetria melyik összetevőt küldték.

8. Egy figyelőt határoz meg a parancsok kéréséhez.

9. Egy figyelőt határoz meg a kívánt tulajdonságok frissítéseihez.

10. A funkciója a következőket tartalmazhatja `main` :

    1. Az eszközoldali SDK használatával hozza létre az eszköz ügyfelet, és csatlakozik az IoT hubhoz. Az eszköz elküldi `model_id` azt, hogy az IoT hub azonosítani tudja az eszközt IoT Plug and Play eszközként.

    1. A a `create_reported_properties` segítő fájl függvényét használja a tulajdonságok létrehozásához. Adja át az összetevő nevét és a tulajdonságokat kulcs érték párokként ehhez a függvényhez.

    1. A meghívásával frissíti az összetevők olvasható tulajdonságait `patch_twin_reported_properties` .

    1. A függvény használatával elindítja a parancsokra vonatkozó kérelmek figyelését `execute_command_listener` . A függvény egy figyelőt állít be a szolgáltatástól érkező parancsokhoz. A figyelő beállításakor a, a `method_name` `user_command_handler` és az opcionális `create_user_response_handler` paramétert adja meg.
        - Az `method_name` határozza meg a parancs kérését. Ebben a példában a modell meghatározza a parancsok **újraindítását**és a **getMaxMinReport**.
        - A `user_command_handler` függvény határozza meg, hogy az eszköz mit tegyen, ha parancsot kap.
        - A `create_user_response_handler` függvény létrehoz egy választ, amelyet a rendszer a IoT hub számára fog elküldeni, ha a parancs végrehajtása sikeresen befejeződött. Ez a válasz a portálon tekinthető meg. Ha ez a függvény nincs megadva, a rendszer általános választ kap a szolgáltatásnak.

    1. A használatával `execute_property_listener` figyeli a tulajdonságok frissítéseit.

    1. Megkezdi a telemetria küldését a használatával `send_telemetry` . A mintakód egy hurok használatával hívja meg a három telemetria küldési függvényt. Mindegyiket nyolc másodpercenként hívja meg

    1. Letiltja az összes figyelőt és feladatot, és a **q** vagy a **q**gomb megnyomásakor kilép a hurokból.

Most, hogy megtekintette a kódot, hozzon létre egy **IOTHUB_DEVICE_CONNECTION_STRING** nevű környezeti változót, amely a korábban jegyzett eszköz-kapcsolódási karakterláncot tárolja. A minta futtatásához használja a következő parancsot:

```cmd/sh
python pnp_temp_controller_with_thermostats.py
```

A mintául szolgáló eszköz néhány másodpercenként telemetria üzenetet küld az IoT hubhoz.

A következő kimenet jelenik meg, amely azt jelzi, hogy az eszköz telemetria küld a központba, és most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására.

![Eszköz megerősítő üzenetei](media/tutorial-multiple-components-python/multiple-component.png)

A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt az összetevőkkel egy IoT hubhoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play előzetes verzió modellezése – fejlesztői útmutató](concepts-developer-guide.md)
