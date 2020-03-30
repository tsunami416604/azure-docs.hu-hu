---
title: IoT Plug and Play preview mintaeszköz-kód csatlakoztatása az IoT Hubhoz (Linux) | Microsoft dokumentumok
description: IoT Plug and Play preview mintaeszköz-kódot hozhat létre és futtatható Linuxon, amely egy IoT-központhoz csatlakozik. Az Azure CLI segítségével tekintse meg az eszköz által a hubra küldött információkat.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75531250"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Rövid útmutató: Csatlakoztasson egy Linuxon futó IoT Plug and Play előzetes eszközalkalmazást az IoT Hubhoz (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy minta IoT Plug and Play eszközalkalmazást Linuxon, hogyan csatlakoztathatja az IoT bub-hoz, és az Azure CLI segítségével megtekintheti a hubnak küldött információkat. The sample application is written in C and is included in the Azure IoT device SDK for C. A megoldásfejlesztő használhatja az Azure CLI-t az IoT Plug and Play eszközök képességeinek megismeréséhez anélkül, hogy bármilyen eszközkódot kellene megtekintenie.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy Ubuntu Linuxot használ. Az oktatóanyag lépéseit ubuntu 18.04-es tesztelték.

A rövid útmutató végrehajtásához a következő szoftvert kell telepítenie a helyi Linux-gépre:

Telepítse **a GCC,** **Git**, **cmake** `apt-get` és az összes függőséget a paranccsal:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ellenőrizze, hogy `cmake` a verzió meghaladja-e a **2.8.12-t,** és a **GCC** verziója **4.4.7**felett van- e.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban előkészíti a fejlesztői környezetet, amely az Azure IoT Hub-eszköz C SDK klónozásához és létrehozásához használható.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Az alábbi parancs végrehajtása az [Azure IoT C SDK-k és a GitHub-tárház](https://github.com/Azure/azure-iot-sdk-c) ezen a helyen történő klónozásához:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Ez a művelet várhatóan több percig is eltarthat.

## <a name="build-the-code"></a>A kód létrehozása

Az sdk eszköz segítségével hozhatja létre a mellékelt mintakódot. Az alkalmazást hoz létre szimulálja egy eszköz, amely csatlakozik egy IoT hub. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon `cmake` létre egy alkönyvtárat az eszköz SDK-gyökérmappájában, és keresse meg azt a mappát:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat az eszköz SDK és a létrehozott kódcsonk létrehozásához:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>A modelltárház frissítése

A minta futtatása előtt adja hozzá az eszközképességi modellt és a felületdefiníciókat a vállalati modelltárházhoz:

1. Jelentkezzen be az [Azure Certified for IoT portal](https://preview.catalog.azureiotsolutions.com) portálra microsoftos munkahelyi vagy iskolai fiókjával, vagy ha rendelkezik ilyennel, jelentkezzen be.

1. Válassza **a Vállalati tárházat,** majd **a Képességmodellek lehetőséget.**

1. Válassza az **Új** lehetőséget, majd **a Feltöltés lehetőséget.**

1. Jelölje ki `SampleDevice.capabilitymodel.json` a `digitaltwin_client/samples` fájlt az eszköz SDK gyökérmappájában lévő mappában. Válassza **a Megnyitás,** majd a **Mentés** lehetőséget, ha fel szeretné tölteni a modellfájlt a tárházba.

1. Válassza **a Vállalati tárházat,** majd **a Interfaces lehetőséget.**

1. Válassza az **Új** lehetőséget, majd **a Feltöltés lehetőséget.**

1. Jelölje ki `EnvironmentalSensor.interface.json` a `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` fájlt az eszköz SDK gyökérmappájában lévő mappában. Válassza **a Megnyitás,** majd a **Mentés** lehetőséget, ha fel szeretné tölteni a felületfájlt a tárházba.

1. Válassza **a Vállalati tárház,** majd **a Kapcsolati karakterláncok lehetőséget.** Jegyezze fel az első _vállalati modell tárház kapcsolati karakterláncát,_ ahogy azt a rövid útmutató későbbi részében használja.

## <a name="run-the-device-sample"></a>Az eszközminta futtatása

Futtasson egy mintaalkalmazást az SDK-ban egy IoT Plug and Play-eszköz szimulálására, amely telemetriát küld az IoT hubnak. A mintaalkalmazás futtatása:

1. A `cmake` mappából keresse meg a végrehajtható fájlt tartalmazó mappát:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Futtassa a végrehajtható fájlt:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Az eszköz most már készen áll a parancsok és a tulajdonságfrissítések fogadására, és megkezdte a telemetriai adatok küldését a hubra. A következő lépések végrehajtásával tartsa a mintát.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Az Azure IoT CLI segítségével érvényesítheti a kódot

Az eszközügyfél-minta indítása után ellenőrizze, hogy az Azure CLI-vel működik-e.

A következő paranccsal megtekintheti a mintaeszköz által küldött telemetriai adatokat. Előfordulhat, hogy várnia kell egy-két percet, mielőtt bármilyen telemetriai adatokat látna a kimenetben:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Az eszköz által küldött tulajdonságok megtekintéséhez használja a következő parancsot:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat ioT Plug and Play-eszközt egy IoT-központhoz. Ha többet szeretne tudni arról, hogyan hozhat létre olyan megoldást, amely kölcsönhatásba lép az IoT Plug and Play eszközeivel, olvassa el a következő témaköröket:

> [!div class="nextstepaction"]
> [Útmutató: Eszközhöz való csatlakozás és az eszközzel való interakció](howto-develop-solution.md)
