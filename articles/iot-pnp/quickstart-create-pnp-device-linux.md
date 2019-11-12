---
title: Azure IoT-Plug and Play előnézeti eszköz létrehozása (Linux) | Microsoft Docs
description: Eszköz-képesség modell használata az eszköz kódjának létrehozásához. Ezután futtassa az eszköz kódját, és tekintse meg az eszközt a IoT Hubhoz való kapcsolódáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 087f1d76aaab4b05425262e0c1fb87b168c99b95
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931224"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Gyors útmutató: eszköz-képesség modell használata IoT Plug and Play Preview-eszköz (Linux) létrehozásához

Az _eszköz képességi modellje_ (DCM) ismerteti a IoT Plug and Play eszköz képességeit. A DCM gyakran társítva van egy termék SKU-hoz. A DCM-ben meghatározott képességek újrafelhasználható felületekbe vannak rendezve. A DCM-eszköz kódját létrehozhatja a DCM-ből. Ez a rövid útmutató bemutatja, hogyan használható a VS code on Ubuntu Linux egy IoT Plug and Play-eszköz a DCM használatával történő létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy a Ubuntu Linux asztali környezettel használja. Az oktatóanyag lépései az Ubuntu 18,04 használatával lettek tesztelve.

A rövid útmutató elvégzéséhez telepítenie kell a következő szoftvereket a helyi linuxos gépre:

* Telepítse a **GCC**, a **git**, a **CMAK**és az összes függőséget a `apt-get` parancs használatával:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ellenőrizze, hogy a `cmake` verziója meghaladja-e a **2.8.12** , és a **GCC** verziója meghaladja a **4.4.7**-t.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Az Azure IoT-eszközök telepítése

Az alábbi lépéseket követve telepítheti a VS Code bővítmény-csomaghoz készült [Azure IoT-eszközöket](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) :

1. A VS Code-ban válassza a **kiterjesztések** lapot.
1. Keresse meg az **Azure IoT-eszközöket**.
1. Válassza az **Install** (Telepítés) lehetőséget.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>A vállalati modell adattárához tartozó kapcsolatok karakterláncának beolvasása

A _vállalati modell adattárának kapcsolati karakterláncát_ az [Azure Certified for IoT portál](https://preview.catalog.azureiotsolutions.com) portálon találja, ha Microsoft munkahelyi vagy iskolai fiókkal jelentkezik be, vagy ha rendelkezik Microsoft partner-azonosítóval. A bejelentkezést követően válassza a **vállalati tárház** , majd a **kapcsolatok karakterláncok**lehetőséget.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. Ha nem rendelkezik IoT hub-fiókkal, akkor a következő lépésekkel hozhat létre egyet.

Ha helyileg használja az Azure CLI-t, a `az` verziójának **2.0.75** vagy újabbnak kell lennie, a Azure Cloud Shell a legújabb verziót használja. A `az --version` parancs használatával keresse meg a számítógépen telepített verziót.

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Az ebben a rövid útmutatóban szereplő lépések a bővítmény **0.8.5** vagy újabb verzióját igénylik. A `az extension list` parancs használatával ellenőrizze a telepített verziót, és szükség esetén frissítse a `az extension update` parancsot.

Ha nem rendelkezik IoT-hubhoz, hozzon létre egyet az alábbi parancsokkal, és cserélje le a `<YourIoTHubName>`t a választott egyedi névre. Ha helyileg futtatja ezeket a parancsokat, először jelentkezzen be az Azure-előfizetésbe `az login`használatával. Ha ezeket a parancsokat az Azure Cloud shellben futtatja, a rendszer automatikusan bejelentkezett:

  ```azurecli-interactive
  az group create --name pnpquickstarts_rg --location centralus
  az iot hub create --name <YourIoTHubName> \
    --resource-group pnpquickstarts_rg --sku S1
  ```

Az előző parancsok létrehoznak egy `pnpquickstarts_rg` nevű erőforráscsoportot és egy IoT hubot az USA középső régiójában.

> [!IMPORTANT]
> A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

Futtassa a következő parancsot egy eszköz identitásának létrehozásához az IoT hub-ban. Cserélje le a **YourIoTHubName** és a **YourDevice** helyőrzőket a tényleges nevekre.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDevice>
```

Futtassa az alábbi parancsokat az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="author-your-model"></a>A modell szerzője

Ebben a rövid útmutatóban egy meglévő minta-eszköz képesség modellt és társított csatolókat használ.

1. Hozzon létre egy `pnp_app` könyvtárat a helyi meghajtón. Ezt a mappát kell használnia az eszköz modell fájljaihoz és az eszköz kódjához.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Töltse le az eszköz képességeinek modelljét és a felületi mintákat a `pnp_app` mappába.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Nyissa meg `pnp_app` mappát a VS Code-ban. A fájlokat az IntelliSense használatával tekintheti meg:

    ![Eszköz képességeinek modellje](media/quickstart-create-pnp-device-linux/dcm.png)

1. A letöltött fájlokban cserélje le a `<YOUR_COMPANY_NAME_HERE>`t a `@id` és a `schema` mezőkbe egy egyedi értékkel. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja. További információ: [digitális kettős azonosító formátuma](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>A C kód kiváltása

Most, hogy már rendelkezik DCM-rel és a hozzá tartozó csatolókkal, létrehozhatja a modellt megvalósító eszköz kódját. A C-kód a (z) VS Code-ban való létrehozásához:

1. A VS Code-ban megnyitott `pnp_app` mappában a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

    > [!NOTE]
    > Amikor első alkalommal használja a IoT Plug and Play Code Generator segédprogramot, az automatikusan letölti és telepíti a szolgáltatást.

1. Válassza ki az **SampleDevice. capabilitymodel. JSON** fájlt, amelyet az eszköz kódjának generálásához kíván használni.

1. Adja meg a projekt nevét **sample_device**. Ez lesz az eszköz-alkalmazás neve.

1. Válassza az **ANSI C** nyelvet.

1. Válassza a **IoT hub eszköz kapcsolatok karakterlánca** lehetőséget a csatlakoztatási módszerként.

1. A Project-sablon **alapján válassza a Linux** rendszerhez készült CMAK-projekt lehetőséget.

1. Válassza a **forráskódon keresztül** lehetőséget az eszköz SDK befoglalásához.

1. A rendszer egy **sample_device** nevű új mappát hoz létre a DCM-fájllal megegyező helyen, és ez a generált kódlap-fájlok. A VS Code egy új ablakot nyit meg, amely megjeleníti ezeket.
    ![eszköz kódja](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>A kód létrehozása és futtatása

Az eszköz SDK forráskódját a generált eszköz kódjának összeállításához használhatja. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. Futtassa az alábbi parancsokat az eszköz SDK-forráskódjának letöltéséhez:

    ```bash
    cd ~/pnp_app/sample_device
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

1. Hozzon létre egy **CMAK** -Build mappát a **sample_device** alkalmazáshoz:

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Futtassa a CMakt az alkalmazás az SDK-val való létrehozásához:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build .
    ```

1. A létrehozás sikeres befejezése után futtassa az alkalmazást a IoT hub Device-kapcsolatok karakterláncának paraméterként való átadásával.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<device connection string>"
    ```

1. Az eszköz megkezdi az adatok küldését a IoT Hubba.

    ![Eszközön futó alkalmazás](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>A kód ellenőrzése

### <a name="publish-device-model-files-to-model-repository"></a>Eszköz-modell fájljainak közzététele a Model repositoryban

Az eszköz kódjának az **az parancssori** felülettel való ellenőrzéséhez közzé kell tennie a fájlokat a modell adattárában.

1. A VS Code-ban megnyitott `pnp_app` mappában a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a parancsot, majd válassza a **IoT Plug & Play: fájlok elküldése a modell adattárba**lehetőséget.

1. Válassza ki `SampleDevice.capabilitymodel.json` és `EnvironmentalSensor.interface.json` fájlokat.

1. Adja meg a vállalati modell adattárához tartozó kapcsolatok sztringjét.

    > [!NOTE]
    > A kapcsolati karakterláncra csak akkor van szükség, amikor először csatlakozik az adattárhoz.

1. A VS Code output ablakban és az értesítésben megtekintheti, hogy a fájlok közzététele sikeresen megtörtént-e.

    > [!NOTE]
    > Ha hibaüzenet jelenik meg az eszköz-modell fájljainak közzétételekor, akkor próbálja meg használni a **IoT Plug and Play: Jelentkezzen ki a modell tárházában** a kijelentkezéshez, és ismételje meg a lépéseket.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>A kód érvényesítése az Azure IoT CLI használatával

Az eszköz-ügyfél mintájának elindítása után megtekintheti, hogy az Azure CLI-vel dolgozik-e.

A következő parancs használatával megtekintheti a telemetria küldő eszközét. Előfordulhat, hogy várnia kell egy percet vagy kettőt, mielőtt bármilyen telemetria lát a kimenetben:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDevice>
```

A következő parancs használatával tekintheti meg az eszköz által eljuttatott összes tulajdonságot:

```azurecli-interactive
az iot dt list-properties --device-id <YourDevice> --hub-name <YourIoTHubNme> --source private --repo-login "<Your company model repository connection string>"
```

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre IoT Plug and Play-eszközt DCM használatával.

Ha többet szeretne megtudni a DCMs és a saját modelljeinek létrehozásáról, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Oktatóanyag: eszköz-képesség modell létrehozása és tesztelése a Visual Studio Code használatával](tutorial-pnp-visual-studio-code.md)
