---
title: A IoT csatlakoztatása Plug and Play előnézet minta Node.js összetevő-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play Preview minta Node.js eszköz kódját, amely több összetevőt használ, és csatlakozik egy IoT hubhoz. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js
ms.openlocfilehash: 24bfbf4199671da497844444a57e566e66eb8c90
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308214"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>Oktatóanyag: minta-IoT csatlakoztatása Plug and Play előzetes verzió több összetevő-eszköz alkalmazása IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy minta IoT Plug and Play eszköz-alkalmazást az összetevőkkel és a gyökérszintű felülettel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa az adott hubhoz küldött adatokat. A minta alkalmazás a Node.jshoz van írva, és a Node.js Azure IoT Hub Device SDK része. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez Node.jsra van szükség a fejlesztői gépen. A [NodeJS.org](https://nodejs.org)több platformon is letöltheti a legújabb ajánlott verziót.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

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

## <a name="download-the-code"></a>A kód letöltése

Ebben az oktatóanyagban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti a Node.jshez készült Azure IoT Hub eszközoldali SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa a következő parancsot a Node.jsGitHub-adattárhoz [tartozó Microsoft Azure IOT SDK ](https://github.com/Azure/azure-iot-sdk-node) ezen a helyen történő klónozásához:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

A művelet végrehajtása több percet is igénybe vehet.

## <a name="install-required-libraries"></a>A szükséges kódtárak telepítése

Az eszköz SDK-val felépítheti a mellékelt mintakód-kódot. Az Ön által létrehozott alkalmazás szimulál egy Plug and Play eszközt több, a IoT hubhoz csatlakozó összetevővel és gyökér interfésszel. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. A helyi terminál ablakban lépjen a klónozott adattár mappájába, és keresse meg a */Azure-IOT-SDK-Node/Device/Samples/PnP* mappát. Ezután futtassa a következő parancsot a szükséges kódtárak telepítéséhez:

```cmd/sh
npm install
```

Ezzel telepíti a megfelelő NPM-fájlokat, amelyek a mintáknak a mappában való futtatásához szükségesek.

1. Konfigurálja a környezeti változót a korábban megjegyzett eszköz-csatlakoztatási karakterlánccal:

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>A kód áttekintése

Navigáljon a *Azure-IOT-SDK-node\device\samples\pnp* mappára.

A *Azure-IOT-SDK-node\device\samples\pnp* mappa tartalmazza a IOT Plug and Play hőmérséklet-vezérlő eszköz mintájának kódját.

A *pnpTemperatureController.js* fájlban található kód egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. A minta által megvalósított modell [több összetevőt](concepts-components.md)használ. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Nyissa meg a *pnpTemperatureController.js* fájlt egy tetszőleges szerkesztőprogramban. A mintakód a következőket mutatja be:

1. Határozza meg, `modelId` hogy melyik a megvalósított eszköz DTMI. Ez a DTMI felhasználó által definiált, és meg kell egyeznie a [hőmérséklet-vezérlő DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)DTMI.

2. Implementálja a hőmérséklet-vezérlő DTDL-modellben meghatározott összetevőket. A valós hőmérséklet-vezérlő összetevőinek meg kell valósítaniuk ezt a két felületet. Ez a két csatoló már közzé van téve egy központi tárházban. Ebben a példában a két csatoló a következő:
  - Okostelefonok
  - Az Azure által fejlesztett eszközök adatai

3. Adja meg az összetevők nevét. Ez a minta két termosztáttal és egy eszköz-információs összetevővel rendelkezik.

4. Adja meg a parancs nevét. Ezek azok a parancsok, amelyekre az eszköz válaszol.

5. Adja meg az `serialNumber` állandót. A `serialNumber` rögzített egy adott eszközt.

6. Adja meg a parancs-kezelőket.

7. Adja meg a függvényeket a parancs válaszainak elküldéséhez.

8. Segítő függvények definiálása a parancsok naplózásához.

9. Adjon meg egy segítő függvényt a tulajdonságok létrehozásához.

10. Adjon meg egy figyelőt a tulajdonságok frissítéseihez.

11. Definiáljon egy függvényt, amely telemetria küld az eszközről. Mindkét termosztát és a gyökérszintű összetevő telemetria küld. Ez a függvény paraméterként fogadja az összetevő nevét.

12. Definiáljon egy `main` függvényt, amely:

    1. Az eszközoldali SDK használatával hozza létre az eszköz ügyfelet, és csatlakozik az IoT hubhoz. Az eszköz biztosítja, `modelId` hogy a IoT hub azonosítani tudja az eszközt IoT Plug and Play eszközként.

    1. A függvény használatával elindítja a parancsokra vonatkozó kérelmek figyelését `onDeviceMethod` . A függvény egy figyelőt állít be a szolgáltatástól érkező parancsokhoz:
        - Az eszköz DTDL határozza meg a `reboot` és a `getMaxMinReport` parancsokat.
        - A `commandHandler` függvény határozza meg, hogy az eszköz hogyan válaszol a parancsra.

    1. Elindítja a telemetria küldését a és a használatával `setInterval` `sendTelemetry` .

    1. A `helperCreateReportedPropertiesPatch` függvény használatával hozza létre a tulajdonságokat, és `updateComponentReportedProperties` frissíti a tulajdonságokat.

    1. A használatával `desiredPropertyPatchListener` figyeli a tulajdonságok frissítéseit.

    1. Letiltja az összes figyelőt és feladatot, és a **q** vagy a **q**gomb megnyomásakor kilép a hurokból.

Most, hogy már látta a kódot, a következő paranccsal futtathatja a mintát:

```cmd\sh
node pnpTemperatureController.js
```

A következő kimenet jelenik meg, amely azt jelzi, hogy az eszköz megkezdte a telemetria adatok küldését a központba, és most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására.

![Eszköz megerősítő üzenetei](media/tutorial-multiple-components-node/multiple-component.png)

A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt az összetevőkkel egy IoT hubhoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play előzetes verzió modellezése – fejlesztői útmutató](concepts-developer-guide.md)
