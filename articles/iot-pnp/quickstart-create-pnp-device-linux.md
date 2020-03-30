---
title: Azure IoT Plug and Play előzetes verziójú eszköz (Linux) létrehozása | Microsoft dokumentumok
description: Eszközképességi modell használata eszközkód létrehozásához. Ezután futtassa az eszközkódot, és tekintse meg az eszköz csatlakozik az IoT Hubhoz.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d2cc440572d6f33480972c15f5c498cc384cb2e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550482"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Rövid útmutató: IoT Plug and Play előzetes verziójú eszköz (Linux) létrehozásához használjon eszközképességi modellt

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Az _eszközképességi modell_ (DCM) az IoT Plug and Play eszközök képességeit írja le. A DCM gyakran kapcsolódik egy termékváltozathoz. A DCM-ben definiált képességek újrafelhasználható felületekbe vannak rendezve. A DCM-ből létrehozott csontváz eszközkód. Ez a rövid útmutató bemutatja, hogyan használhatja a VS Code ubuntu Linux on IoT Plug and Play eszközt egy DCM használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy ubuntu Linuxot használ asztali környezetben. Az oktatóanyag lépéseit ubuntu 18.04-es tesztelték.

A rövid útmutató végrehajtásához a következő szoftvert kell telepítenie a helyi Linux-gépre:

* Telepítse **a GCC,** **Git**, **cmake** `apt-get` és az összes függőséget a paranccsal:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ellenőrizze, hogy `cmake` a verzió meghaladja-e a **2.8.12-t,** és a **GCC** verziója **4.4.7**felett van- e.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio kód](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Az Azure IoT-eszközök telepítése

Az alábbi lépésekkel telepítheti az [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítménycsomagot:

1. A VS-kód ban válassza a **Bővítmények** lapot.
1. Keresse meg az **Azure IoT-eszközöket.**
1. Válassza az **Install** (Telepítés) lehetőséget.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>A vállalati modelltárház kapcsolati karakterláncának beszereznie

A vállalati _modelltárház kapcsolati karakterláncát_ az [Azure Certified for IoT portálportálon](https://preview.catalog.azureiotsolutions.com) találhatja meg, ha microsoftos munkahelyi vagy iskolai fiókkal, vagy ha rendelkezik ilyennel, a Microsoft-partnerazonosítójával jelentkezik be. Miután bejelentkezett, válassza **a Vállalati tárház** lehetőséget, majd **a Kapcsolati karakterláncokat.**

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a [Vcpkg-könyvtárkezelővel](https://github.com/microsoft/vcpkg) telepítheti az Azure IoT C-eszköz SDK-t a fejlesztői környezetben.

Nyisson meg egy rendszerhéjat. A Vcpkg telepítéséhez hajtsa végre a következő parancsot:

```bash
cd ~
git clone https://github.com/microsoft/vcpkg
cd vcpkg
./bootstrap-vcpkg.sh
./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
```

Ez a művelet várhatóan több percig is eltarthat.

## <a name="author-your-model"></a>A modell megkészítése

Ebben a rövid útmutatóban egy meglévő mintaeszköz-képességi modellt és a kapcsolódó felületeket használ.

1. Hozzon `pnp_app` létre egy könyvtárat a helyi meghajtón. Ezt a mappát az eszközmodellfájljaihoz és az eszközkódcsonkjához használhatja.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Töltse le az eszközképességi modellt és a felület mintafájljait a `pnp_app` mappába.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Nyissa `pnp_app` meg a mappát a VS-kóddal. Megtekintheti a fájlokat intelliSense:

    ![Eszközképességi modell](media/quickstart-create-pnp-device-linux/dcm.png)

1. A letöltött fájlokban `<YOUR_COMPANY_NAME_HERE>` cserélje `@id` le `schema` a és a mezőket egy egyedi értékre. Csak az a-z, A-Z, 0-9 és aláhúzáskaraktereket használja. További információ: [Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>A C-kódcsonk létrehozása

Most, hogy rendelkezik egy DCM és a hozzá tartozó felületek, létrehozhatja az eszköz kódját, amely megvalósítja a modellt. A C kódcsonk létrehozása a VS-kódban:

1. Ha `pnp_app` a mappa meg van nyitva a VS-kódban, a **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát, írja be az **IoT Plug and Play**parancsot, és válassza **az Eszközkód csonklétrehozása lehetőséget.**

    > [!NOTE]
    > Az IoT Plug and Play Kódgenerátor segédprogram első használatakor néhány másodpercet vesz igénybe az automatikus letöltés és telepítés.

1. Válassza ki a **SampleDevice.capabilitymodel.json** fájlt az eszközkód csonklétrehozásához.

1. Írja be a projekt nevét **sample_device**. Ez lesz az eszközalkalmazás neve.

1. Válassza az **ANSI C nyelvet.**

1. Válassza **a Via IoT Hub eszköz kapcsolati karakterlánc** csatlakozási módszerként.

1. Projektsablonként válassza **a CMake Project on Linux** lehetőséget.

1. Válassza a **Via Vcpkg** lehetőséget az eszköz SDK-jának felvételére.

1. Egy új mappa nevű **sample_device** jön létre ugyanazon a helyen, mint a DCM fájlt, és benne vannak a generált eszköz kód csonkfájlokat. A VS Code megnyit egy új ablakot ezek megjelenítéséhez.
    ![Eszköz kódja](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>A kód létrehozása és futtatása

Az eszköz SDK forráskódjával hozhatja létre a létrehozott eszközkódcsonkot. Az alkalmazást hoz létre szimulálja egy eszköz, amely csatlakozik egy IoT hub. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. **CMake** buildmappa létrehozása a **sample_device** alkalmazáshoz:

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Futtassa a CMake-et az alkalmazás SDK-val való létrehozásához. A következő parancs feltételezi, hogy **a vcpkg-ot** a saját mappájába telepítette:

    ```bash
    cmake .. -DCMAKE_TOOLCHAIN_FILE=~/vcpkg/scripts/buildsystems/vcpkg.cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build .
    ```

1. Miután a build sikeresen befejeződött, futtassa az alkalmazást az IoT hub eszköz kapcsolati karakterlánc paraméterként.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "<YourDeviceConnectionString>"
    ```

1. Az eszközalkalmazás megkezdi az adatok küldését az IoT Hubra.

    ![Futó eszközalkalmazás](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>A kód ellenőrzése

### <a name="publish-device-model-files-to-model-repository"></a>Eszközmodell-fájlok közzététele a modelltárházban

Az eszközkód ellenőrzése az **az** CLI-vel, közzé kell tennie a fájlokat a modell tárházban.

1. Ha `pnp_app` a mappa meg van nyitva vs-kódban, a **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát, írja be és válassza az **IoT Plug & Play: Fájlok küldése a modelltárházba**lehetőséget.

1. Kijelölés `SampleDevice.capabilitymodel.json` `EnvironmentalSensor.interface.json` és fájlok.

1. Adja meg a vállalati modell tárház kapcsolati karakterláncát.

    > [!NOTE]
    > A kapcsolati karakterlánc csak akkor szükséges, amikor először csatlakozik a tárházhoz.

1. A VS Code kimeneti ablakban és értesítésben ellenőrizheti, hogy a fájlok közzététele sikeresen megtörtént-e.

    > [!NOTE]
    > Ha hibákat tapasztal az eszközmodell-fájlok közzétételekor, megpróbálhatja az **IoT Plug and Play: Sign out Model Repository parancsot** a kijelentkezéshez, és újra végigmenni a lépéseken.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Az Azure IoT CLI segítségével érvényesítheti a kódot

Az eszközügyfél-minta indítása után ellenőrizheti, hogy az Azure CLI-vel működik-e.

A következő paranccsal megtekintheti a mintaeszköz által küldött telemetriai adatokat. Előfordulhat, hogy várnia kell egy-két percet, mielőtt bármilyen telemetriai adatokat látna a kimenetben:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubNme> --device-id <YourDeviceID>
```

Az eszköz által küldött összes tulajdonságot a következő paranccsal tekintheti meg:

```azurecli-interactive
az iot dt list-properties --device-id <YourDeviceID> --hub-name <YourIoTHubNme> --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre IoT Plug and Play eszközt DCM használatával.

Ha többet szeretne megtudni a DCM-ekről és a saját modellek létrehozásáról, folytassa az oktatóanyaggal:

> [!div class="nextstepaction"]
> [Oktatóanyag: Eszközképesség-modell létrehozása és tesztelése a Visual Studio-kód használatával](tutorial-pnp-visual-studio-code.md)
