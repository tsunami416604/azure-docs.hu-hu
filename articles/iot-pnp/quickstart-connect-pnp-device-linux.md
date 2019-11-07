---
title: A IoT csatlakoztatása Plug and Play minta-eszköz kódja a IoT Hubhoz (Linux) | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play előzetes minta-eszköz kódját Linux rendszeren, amely egy IoT hubhoz csatlakozik. Az Azure CLI használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 38a6deb8d021c5e6a20d765cc7aee5b86042f557
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586669"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub"></a>Rövid útmutató: a Linuxon futó minta IoT Plug and Play előnézeti eszköz csatlakoztatása IoT Hub

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

## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!IMPORTANT]
> A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

Ha helyileg használja az Azure CLI-t, a `az` verziójának **2.0.73** vagy újabbnak kell lennie, a Azure Cloud Shell a legújabb verziót használja. A `az --version` parancs használatával keresse meg a számítógépen telepített verziót.

Adja hozzá a Microsoft Azure IoT bővítményt az Azure CLI-hez:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Az ebben a rövid útmutatóban szereplő lépések a bővítmény **0.8.5** vagy újabb verzióját igénylik. A `az extension list` parancs használatával ellenőrizze a telepített verziót, és szükség esetén frissítse a `az extension update` parancsot.

Ha helyileg használja a CLI-t, jelentkezzen be az Azure-előfizetésbe a következő paranccsal:

```bash
az login
```

Ha Azure Cloud Shell használ, már automatikusan bejelentkezett.

Ha nem rendelkezik IoT Hubval, [az alábbi utasításokat követve hozhat létre egyet](../iot-hub/iot-hub-create-using-cli.md). A nyilvános előzetes verzióban a IoT Plug and Play az Észak-Európában, az USA középső régiójában és a Kelet-japán régióban érhető el. Győződjön meg arról, hogy az egyik régióban hozza létre a hubot.

A következő parancs futtatásával hozza létre az eszköz identitását az IoT hub-ban. Cserélje le a **YourIoTHubName** helyőrzőt a tényleges IoT hub-névre:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id mydevice
```

Futtassa az alábbi parancsokat az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id mydevice --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT C Device SDK-t.

Nyisson meg egy parancssort. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

A parancs végrehajtása több percet is igénybe vehet.

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

1. Válassza a **vállalati tárház** , majd a **kapcsolatok karakterláncok**lehetőséget. Jegyezze fel az első vállalati modell adattár-összekapcsolási karakterláncát, amelyet később a rövid útmutatóban fog használni.

## <a name="run-the-sample"></a>Minta futtatása

Futtasson egy minta alkalmazást az SDK-ban egy IoT Plug and Play-eszköz szimulálásához, amely telemetria küld az IoT hubhoz. A minta alkalmazás futtatása:

1. A `cmake` mappában navigáljon a végrehajtható fájlt tartalmazó mappához:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Futtassa a végrehajtható fájlt:

    ```bash
    ./digitaltwin_sample_device "{your device connection string}"
    ```

A szimulált eszköz megkezdi a telemetria küldését, a parancsok figyelését és a tulajdonságok frissítésének figyelését.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>A kód érvényesítése az Azure IoT CLI használatával

Az ügyfél mintájának elindítása után ellenőrizze, hogy működik-e az Azure CLI-vel.

A következő parancs használatával megtekintheti a telemetria küldő eszközét. Előfordulhat, hogy várnia kell egy percet vagy kettőt, mielőtt bármilyen telemetria lát a kimenetben:

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mydevice
```

A következő parancs használatával tekintheti meg az eszköz által eljuttatott tulajdonságokat:

```azurecli-interactive
az iot dt list-properties --hub-name {your IoT hub} --device-id mydevice --interface sensor --source private --repo-login "{your company model repository connection string}"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](howto-develop-solution.md)
