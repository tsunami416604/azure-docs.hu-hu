---
title: IoT Plug and Play előzetes verziójú eszköz (Windows) létrehozása | Microsoft dokumentumok
description: Eszközképességi modell használata eszközkód létrehozásához. Ezután futtassa az eszközkódot, és tekintse meg az eszköz csatlakozik az IoT Hubhoz.
author: miagdp
ms.author: miag
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e80194f53a406b8b378d0fb787df627937125a27
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75867493"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Gyorsútmutató: IoT Plug and Play előzetes verziójú eszköz (Windows) létrehozásához használjon eszközképesség-modellt

[!INCLUDE [iot-pnp-quickstarts-1-selector.md](../../includes/iot-pnp-quickstarts-1-selector.md)]

Az _eszközképességi modell_ (DCM) az IoT Plug and Play eszközök képességeit írja le. A DCM gyakran kapcsolódik egy termékváltozathoz. A DCM-ben definiált képességek újrafelhasználható felületekbe vannak rendezve. A DCM-ből létrehozott csontváz eszközkód. Ez a rövid útmutató bemutatja, hogyan hozhat létre IoT Plug and Play eszközt a Windows rendszeren a VS Code használatával egy DCM használatával.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató végrehajtásához a következő szoftvert kell telepítenie a helyi számítógépre:

* **C++ buildeszközökkel** és **NuGet csomagkezelői összetevők** munkaterheléseivel [fejlesztőeszközök](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) kelhet létre a Visual Studio számára. Vagy ha már rendelkezik [visual studio (közösségi, professzionális vagy nagyvállalati)](https://visualstudio.microsoft.com/downloads/) 2019,2017 vagy 2015 azonos számítási feladatok telepítve.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio kód](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Az Azure IoT-eszközök telepítése

Az alábbi lépésekkel telepítheti az [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítménycsomagot:

1. A VS-kód ban válassza a **Bővítmények** lapot.
1. Keresse meg az **Azure IoT-eszközöket.**
1. Válassza az **Install** (Telepítés) lehetőséget.

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT-felfedező telepítése

Töltse le és telepítse az **Azure IoT Explorer** legújabb kiadását az eszköz [tárházlapjáról,](https://github.com/Azure/azure-iot-explorer/releases) válassza ki az .msi fájlt az "Eszközök" alatt a legújabb frissítéshez.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>A vállalati modelltárház kapcsolati karakterláncának beszereznie

A vállalati _modelltárház kapcsolati karakterláncát_ az [Azure Certified for IoT portálportálon](https://preview.catalog.azureiotsolutions.com) találhatja meg, ha microsoftos munkahelyi vagy iskolai fiókkal, vagy ha rendelkezik ilyennel, a Microsoft-partnerazonosítójával jelentkezik be. Miután bejelentkezett, válassza **a Vállalati tárház** lehetőséget, majd **a Kapcsolati karakterláncokat.**

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot az _IoT hub kapcsolati karakterláncának_ lekérnia a hubhoz (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben a rövid útmutatóban a [Vcpkg-könyvtárkezelővel](https://github.com/microsoft/vcpkg) telepítheti az Azure IoT C-eszköz SDK-t a fejlesztői környezetben.

1. Nyisson meg egy parancssort. A Vcpkg telepítéséhez hajtsa végre a következő parancsot:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Ezután a felhasználói szintű [integráció](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)csatlakoztatásához futtassa a következőket (megjegyzés: az első használatkor rendszergazdaszükséges):

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Telepítse az Azure IoT C-eszköz SDK Vcpkg:Install Azure IoT C device SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>A modell megkészítése

Ebben a rövid útmutatóban egy meglévő mintaeszköz-képességi modellt és a kapcsolódó felületeket használ.

1. Hozzon `pnp_app` létre egy mappát a helyi meghajtón. Ezt a mappát az eszközmodellfájljaihoz és az eszközkódcsonkjához használhatja.

1. Töltse le az [eszköz képességmodelljét és felületét, mintafájlokat](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) és [felületmintát,](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) és mentse a fájlokat a `pnp_app` mappába.

    > [!TIP]
    > Ha fájlt szeretne letölteni a GitHubról, keresse meg a fájlt, kattintson a jobb gombbal a **Raw**elemre, és válassza **a Hivatkozás mentése másként parancsot.**

1. Nyissa `pnp_app` meg a mappát a VS-kóddal. Megtekintheti a fájlokat intelliSense:

    ![Eszközképességi modell](media/quickstart-create-pnp-device/dcm.png)

1. A letöltött fájlokban `<YOUR_COMPANY_NAME_HERE>` cserélje `@id` le `schema` a és a mezőket egy egyedi értékre. Csak az a-z, A-Z, 0-9 és aláhúzáskaraktereket használja. További információ: [Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>A C-kódcsonk létrehozása

Most, hogy rendelkezik egy DCM és a hozzá tartozó felületek, létrehozhatja az eszköz kódját, amely megvalósítja a modellt. A C kódcsonk létrehozása a VS-kódban:

1. Ha `pnp_app` a mappa meg van nyitva a VS-kódban, a **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát, írja be az **IoT Plug and Play**parancsot, és válassza **az Eszközkód csonklétrehozása lehetőséget.**

    > [!NOTE]
    > Az IoT Plug and Play CodeGen CLI első használatakor néhány másodpercet vesz igénybe az automatikus letöltés és telepítés.

1. Válassza ki a **SampleDevice.capabilitymodel.json** fájlt az eszközkód csonklétrehozásához.

1. Írja be a projekt nevét **sample_device**. Ez az eszközalkalmazás neve.

1. Válassza az **ANSI C nyelvet.**

1. Válassza **a Via IoT Hub eszköz kapcsolati karakterlánc** csatlakozási módszerként.

1. Projektsablonként válassza **a CMake Project on Windows** lehetőséget.

1. Válassza a **Via Vcpkg** lehetőséget az eszköz SDK-jának felvételére.

1. Egy új mappa nevű **sample_device** jön létre ugyanazon a helyen, mint a DCM fájlt, és benne vannak a generált eszköz kód csonkfájlokat. A VS Code megnyit egy új ablakot ezek megjelenítéséhez.
    ![Eszköz kódja](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>A kód létrehozása és futtatása

A Vcpkg csomag segítségével hozza létre a generált eszközkód csonkot. Az alkalmazást hoz létre szimulálja egy eszköz, amely csatlakozik egy IoT hub. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon `cmake` létre egy `sample_device` alkönyvtárat a mappában, és keresse meg azt a mappát:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a létrehozott kódcsonk létrehozásához (a helyőrző helyett a Vcpkg tártár könyvtára):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Ha a Visual Studio 2017-et vagy 2015-öt használja, a CMake-generátort a használt buildeszközök alapján kell megadnia:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Ha a cmake nem találja a C++ fordítót, az előző parancs futtatásakor buildhibákat kap. Ebben az esetben próbálja meg futtatni ezt a parancsot a [Visual Studio parancssori parancssorból.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

1. Miután a build sikeresen befejeződött, futtassa az alkalmazást, paraméterként haladva az IoT hub eszköz kapcsolati karakterláncán.

    ```cmd\sh
    .\Debug\sample_device.exe "<YourDeviceConnectionString>"
    ```

1. Az eszközalkalmazás megkezdi az adatok küldését az IoT Hubra.

    ![Futó eszközalkalmazás](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>A kód ellenőrzése

### <a name="publish-device-model-files-to-model-repository"></a>Eszközmodell-fájlok közzététele a modelltárházban

Az eszközkód ellenőrzése az **Azure IoT Explorer,** közzé kell tennie a fájlokat a modell tárházban.

1. Ha `pnp_app` a mappa meg van nyitva a VS-kódban, a **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát, írja be és válassza az **IoT Plug & Play: Fájlok küldése a modelltárházba**lehetőséget.

1. Kijelölés `SampleDevice.capabilitymodel.json` `EnvironmentalSensor.interface.json` és fájlok.

1. Adja meg a vállalati modell tárház kapcsolati karakterláncát.

    > [!NOTE]
    > A kapcsolati karakterlánc csak akkor szükséges, amikor először csatlakozik a tárházhoz.

1. A VS Code kimeneti ablakban és értesítésben ellenőrizheti, hogy a fájlok közzététele sikeresen megtörtént-e.

    > [!NOTE]
    > Ha hibákat tapasztal az eszközmodell-fájlok közzétételekor, megpróbálhatja az **IoT Plug and Play: Sign out Model Repository parancsot** a kijelentkezéshez, és újra végigmenni a lépéseken.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítéséhez használja az Azure IoT-felfedezőt

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. A vállalati tárház hozzáadásához válassza a **Beállítások**lehetőséget, majd **a + Moduldefinícióforrás hozzáadása**lehetőséget, majd a Vállalati **tárházat.** Adja hozzá a vállalati modelltárkapcsolati karakterláncát, és válassza a **Mentés és csatlakozás**lehetőséget.

1. Az **Eszközök** áttekintése lapon keresse meg a korábban létrehozott eszközidentitást. Ha az eszközalkalmazás továbbra is fut a parancssorban, ellenőrizze, hogy az eszköz **kapcsolati állapota** az Azure IoT Explorer _ben csatlakoztatottként jelent-e_ (ha nem, nyomja meg a **Frissítés** gombot, amíg nincs). A további részletek megtekintéséhez válassza ki az eszközt.

1. Bontsa ki a felületet id **urn:<YOUR_INTERFACE_NAME>:EnvironmentalSensor:1** az IoT Plug and Play primitívek – tulajdonságok, parancsok és telemetriai adatok megjelenítéséhez. A felület neve jelenik meg a modell készítésekénél beírt név.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre IoT Plug and Play eszközt DCM használatával.

Ha többet szeretne megtudni a DCM-ekről és a saját modellek létrehozásáról, folytassa az oktatóanyaggal:

> [!div class="nextstepaction"]
> [Oktatóanyag: Eszközképesség-modell létrehozása és tesztelése a Visual Studio-kód használatával](tutorial-pnp-visual-studio-code.md)
