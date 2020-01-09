---
title: A IoT csatlakoztatása Plug and Play minta-eszköz kódja a IoT Hubhoz (Linux) | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play előzetes minta-eszköz kódját Linux rendszeren, amely egy IoT hubhoz csatlakozik. Az Azure CLI használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531250"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Gyors útmutató: a Linuxon futó minta IoT Plug and Play előnézeti alkalmazás csatlakoztatása IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy minta IoT Plug and Play Linux rendszeren, hogyan csatlakoztathatja a IoT-bub, és az Azure CLI használatával megtekintheti az általa az adott hubhoz küldött adatokat. A minta alkalmazás C nyelven íródott, amely a C Azure IoT Device SDK-ban szerepel. A megoldás fejlesztője az Azure CLI használatával megismerheti egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy a Ubuntu Linux használja. Az oktatóanyag lépései az Ubuntu 18,04 használatával lettek tesztelve.

A rövid útmutató elvégzéséhez telepítenie kell a következő szoftvereket a helyi linuxos gépre:

Telepítse a **GCC**, a **git**, a **CMAK**és az összes függőséget a `apt-get` parancs használatával:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ellenőrizze, hogy a `cmake` verziója meghaladja-e a **2.8.12** , és a **GCC** verziója meghaladja a **4.4.7**-t.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban olyan fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT Hub Device C SDK-t.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa az alábbi parancsot az [Azure IoT C SDK-k és könyvtárak GitHub-](https://github.com/Azure/azure-iot-sdk-c) tárházának klónozásához a következő helyre:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Ez a művelet várhatóan több percig is eltarthat.

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz SDK-val felépítheti a mellékelt mintakód-kódot. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon létre egy `cmake` alkönyvtárat az eszköz SDK gyökérkönyvtárában, és navigáljon a következő mappába:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. A következő parancsok futtatásával hozza létre az eszköz SDK-t és a generált kódot:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>A modell adattárának frissítése

A minta futtatása előtt adja hozzá az eszköz képességeinek modelljét és az illesztőfelület-definíciókat a vállalati modell adattárához:

1. Jelentkezzen be az [Azure Certified for IoT portál](https://preview.catalog.azureiotsolutions.com) portálra a Microsoft munkahelyi vagy iskolai fiókjával vagy a Microsoft partner-azonosítójával, ha rendelkezik ilyennel.

1. Válassza a **vállalati tárház** , majd a **képesség modellek**lehetőséget.

1. Válassza az **új** , majd a **feltöltés**lehetőséget.

1. Válassza ki az eszköz SDK-gyökérkönyvtárának `digitaltwin_client/samples` mappájában `SampleDevice.capabilitymodel.json` fájlt. Válassza a **Megnyitás** , majd a **Mentés** lehetőséget a modell fájljának a tárházba való feltöltéséhez.

1. Válassza a **vállalati tárház** , majd a **felületek**elemet.

1. Válassza az **új** , majd a **feltöltés**lehetőséget.

1. Válassza ki az eszköz SDK-gyökérkönyvtárának `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` mappájában `EnvironmentalSensor.interface.json` fájlt. Válassza a **Megnyitás** , majd a **Mentés** lehetőséget, hogy feltöltse a csatoló fájlt a tárházba.

1. Válassza a **vállalati tárház** , majd a **kapcsolatok karakterláncok**lehetőséget. Jegyezze fel az első _vállalati modell-adattárhoz_tartozó, a rövid útmutató későbbi részében használatos karakterláncot.

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

Futtasson egy minta alkalmazást az SDK-ban egy IoT Plug and Play-eszköz szimulálásához, amely telemetria küld az IoT hubhoz. A minta alkalmazás futtatása:

1. A `cmake` mappában navigáljon a végrehajtható fájlt tartalmazó mappához:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Futtassa a végrehajtható fájlt:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>A kód érvényesítése az Azure IoT CLI használatával

Az ügyfél mintájának elindítása után ellenőrizze, hogy működik-e az Azure CLI-vel.

A következő parancs használatával megtekintheti a telemetria küldő eszközét. Előfordulhat, hogy várnia kell egy percet vagy kettőt, mielőtt bármilyen telemetria lát a kimenetben:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

A következő parancs használatával tekintheti meg az eszköz által eljuttatott tulajdonságokat:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](howto-develop-solution.md)
