---
title: IoT-Plug and Play előnézeti eszköz létrehozása | Microsoft Docs
description: Eszköz-képesség modell használata az eszköz kódjának létrehozásához. Ezután futtassa az eszköz kódját, és tekintse meg az eszközt a IoT Hubhoz való kapcsolódáshoz.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 654ebc6f40e6c365e9abf406ff19cd7269539dd8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682221"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-windows"></a>Gyors útmutató: eszköz-képesség modell használata IoT Plug and Play előnézeti eszköz (Windows) létrehozásához

Az _eszköz képességi modellje_ (DCM) ismerteti a IoT Plug and Play eszköz képességeit. A DCM gyakran társítva van egy termék SKU-hoz. A DCM-ben meghatározott képességek újrafelhasználható felületekbe vannak rendezve. A DCM-eszköz kódját létrehozhatja a DCM-ből. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a VS Code-t a Windowson egy IoT Plug and Play-eszköz létrehozásához DCM használatával.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítenie kell a következő szoftvereket a helyi gépre:

* [Hozzon létre eszközöket a Visual studióhoz](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) ,  **C++ és hozzon létre eszközöket** és **NuGet csomagkezelő összetevő** -számítási feladatokat. Ha már rendelkezik a [Visual Studióval (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 vagy 2015-val, és ugyanazokat a számítási feladatokat telepítette.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Az Azure IoT-eszközök telepítése

Az alábbi lépéseket követve telepítheti a VS Code bővítmény-csomaghoz készült [Azure IoT-eszközöket](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) :

1. A VS Code-ban válassza a **kiterjesztések** lapot.
1. Keresse meg az **Azure IoT-eszközöket**.
1. Válassza az **Install** (Telepítés) lehetőséget.

### <a name="install-the-azure-iot-explorer"></a>Az Azure IoT Explorer telepítése

Töltse le és telepítse az Azure IoT Explorer eszközt a [legújabb kiadási](https://github.com/Azure/azure-iot-explorer/releases) oldalról.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>A vállalati modell adattárához tartozó kapcsolatok karakterláncának beolvasása

A _vállalati modell adattárának kapcsolati karakterláncát_ az [Azure Certified for IoT portál](https://preview.catalog.azureiotsolutions.com) portálon találja, ha Microsoft munkahelyi vagy iskolai fiókkal jelentkezik be, vagy ha rendelkezik Microsoft partner-azonosítóval. A bejelentkezést követően válassza a **vállalati tárház** , majd a **kapcsolatok karakterláncok**lehetőséget.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

Adja hozzá a Microsoft Azure IoT bővítményt az Azure CLI-hez:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

A következő parancs futtatásával hozza létre az eszköz identitását az IoT hub-ban. Cserélje le a **YourIoTHubName** és a **YourDevice** helyőrzőket a tényleges nevekre. Ha nem rendelkezik IoT Hubval, [az alábbi útmutatást követve hozhat létre egyet](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Futtassa az alábbi parancsokat az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ beszerzéséhez:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Futtassa az alábbi parancsokat a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

Jegyezze fel az eszköz kapcsolati sztringjét, amely a következőképpen néz ki:

```json
HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}
```

Ezt az értéket később a gyors útmutatóban fogja használni.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

### <a name="get-azure-iot-device-sdk-for-c"></a>A C Azure IoT Device SDK beszerzése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő az Azure IoT C Device SDK [Vcpkg](https://github.com/microsoft/vcpkg)használatával történő telepítésével.

1. Nyisson meg egy parancssort. Futtassa a következő parancsot a Vcpkg telepítéséhez:

    ```cmd/sh
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Ezután a felhasználói szintű [integráció](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)összekapcsolásához futtassa a következőt: (Megjegyzés: az első használathoz rendszergazdai jogosultság szükséges):

    ```cmd/sh
    .\vcpkg.exe integrate install
    ```

1. Az Azure IoT C Device SDK Vcpkg telepítése:

    ```cmd/sh
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>A modell szerzője

Ebben a rövid útmutatóban egy meglévő minta-eszköz képesség modellt és társított csatolókat használ.

1. Hozzon létre egy `pnp_app` könyvtárat a helyi meghajtón.

1. Töltse le az [eszköz képességeinek modelljét](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) és a [felületi mintát](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) , és mentse a fájlokat `pnp_app` mappába.

    > [!TIP]
    > Egy fájl GitHubról való letöltéséhez navigáljon a fájlhoz, kattintson a jobb gombbal a **RAW**elemre, majd válassza a **hivatkozás mentése másként**lehetőséget.

1. Nyissa meg `pnp_app` mappát a VS Code-ban. A fájlokat az IntelliSense használatával tekintheti meg:

    ![Eszköz képességeinek modellje](media/quickstart-create-pnp-device/dcm.png)

1. A letöltött fájlokban cserélje le a `<YOUR_COMPANY_NAME_HERE>`t a `@id` és a `schema` mezőkbe egy egyedi értékkel. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja. További információ: [digitális kettős azonosító formátuma](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>A C kód kiváltása

Most már rendelkezik egy DCM-rel és a hozzá tartozó csatolókkal, létrehozhatja a modellt megvalósító eszköz kódját. A C-kód a (z) VS Code-ban való létrehozásához:

1. Ha a DCM-fájlok mappája meg van nyitva, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

    > [!NOTE]
    > Amikor első alkalommal használja a IoT Plug and Play CodeGen CLI-t, néhány másodperc elteltével automatikusan letöltheti és telepítheti.

1. Válassza ki azt a DCM-fájlt, amelyet az eszköz kódjának létrehozásához használni kíván.

1. Adja meg a projekt nevét **sample_device**, és ez lesz az eszköz alkalmazásának neve.

1. Válassza az **ANSI C** nyelvet.

1. Válassza a **IoT hub eszköz kapcsolatok karakterlánca** lehetőséget a csatlakoztatási módszerként.

1. Válassza **a Windows rendszerhez készült CMAK-projekt lehetőséget a** Project sablonként.

1. Válassza a **Vcpkg keresztül** lehetőséget az eszköz SDK-nak való felvételéhez.

1. A VS Code egy új ablakot nyit meg a generált kódlap-fájlokkal.
    ![eszköz kódja](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>A kód létrehozása

A generált eszköz kódját a Device SDK-val együtt kell felépíteni. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon létre egy `cmake` alkönyvtárat a `sample_device` mappában, és navigáljon a következő mappába:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a generált kód kiépítéséhez:

    ```cmd\sh
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > A Visual Studio 2017-es vagy a 2015-es verziójának használata esetén meg kell adnia a CMak-generátort az Ön által használt build-eszközök alapján:
    >```cmd\sh
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Ha a Csatlakozáskezelő felügyeleti csomag nem C++ találja a fordítót, az előző parancs futtatásakor hibaüzeneteket kaphat. Ha ez történik, próbálja meg futtatni ezt a parancsot a [Visual Studio parancssorában](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. A létrehozás sikeres befejezése után futtassa az alkalmazást a IoT hub Device-kapcsolatok karakterláncának paraméterként való átadásával.

    ```cmd\sh
    .\Debug\sample_device.exe "[IoT Hub device connection string]"
    ```

1. Az eszköz megkezdi az adatok küldését a IoT Hubba.

    ![Eszközön futó alkalmazás](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>A kód ellenőrzése

### <a name="publish-device-model-files-to-model-repository"></a>Eszköz-modell fájljainak közzététele a Model repositoryban

Az eszköz kódjának az **Azure IoT Explorerrel**való ellenőrzéséhez közzé kell tennie a fájlokat a modell adattárában.

1. Ha a DCM-fájlok mappája meg van nyitva, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be és válassza a **IoT Plug & Play: fájlok elküldése a modell adattárba**lehetőséget.

1. Válassza ki `SampleDevice.capabilitymodel.json` és `EnvironmentalSensor.interface.json` fájlokat.

1. Adja meg a vállalati modell adattárához tartozó kapcsolatok sztringjét.

    > [!NOTE]
    > A kapcsolati karakterláncra csak akkor van szükség, amikor először csatlakozik az adattárhoz.

1. A VS Code output ablakban és az értesítésben megtekintheti, hogy a fájlok közzététele sikeresen megtörtént-e.

    > [!NOTE]
    > Ha hibaüzenet jelenik meg az eszköz-modell fájljainak közzétételekor, akkor próbálja meg használni a **IoT Plug and Play: Jelentkezzen ki a modell tárházában** a kijelentkezéshez, és ismételje meg a lépéseket.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

1. Nyissa meg az Azure IoT Explorert, és megjelenik az **alkalmazás konfigurációja** lap.

1. Adja meg IoT Hub kapcsolati karakterláncát, és kattintson a **Csatlakoztatás**gombra.

1. A csatlakoztatása után megjelenik az eszköz áttekintő lapja.

1. A vállalati tárház hozzáadásához válassza a **Beállítások**, majd az **+ új**, majd a **vállalati adattár**elemet.

1. Adja hozzá a vállalati modell adattárának kapcsolatok sztringjét. Kattintson a **Csatlakozás** gombra.

1. Az eszköz áttekintése lapon keresse meg a korábban létrehozott eszköz identitását, és válassza ki a további részletek megtekintéséhez.

1. Bontsa ki az **urn: azureiot: EnvironmentalSensor: 1** azonosítójú felületet a IoT Plug and Play primitívek – tulajdonságok, parancsok és telemetria megtekintéséhez.

1. Válassza ki a **telemetria** lapot, és tekintse meg az eszköz által küldött telemetria adatokat.

1. Válassza a **Tulajdonságok (nem írható)** lapot az eszköz által jelentett nem írható tulajdonságok megtekintéséhez.

1. Válassza a **Tulajdonságok (írható)** lapot a frissíteni kívánt írható tulajdonságok megtekintéséhez.

1. Bontsa **ki a tulajdonságnév, a**frissítés új névvel elemet, majd válassza az **írható tulajdonság frissítése**lehetőséget.

1. Ha látni szeretné, hogy az új név megjelenik a **jelentett tulajdonság** oszlopban, kattintson a lap tetején található **frissítés** gombra.

1. Kattintson a **parancs** lapra az eszköz által támogatott összes parancs megtekintéséhez.

1. Bontsa ki a **Blink** parancsot, és állítson be egy új villogási időközt. Válassza a **Küldés parancs** lehetőséget a parancs meghívásához az eszközön.

1. Lépjen a szimulált eszközre, és győződjön meg arról, hogy a parancs a várt módon lett végrehajtva.

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre IoT Plug and Play-eszközt DCM használatával.

Ha többet szeretne megtudni a IoT Plug and Playről, folytassa az Oktatóanyaggal:

> [!div class="nextstepaction"]
> [Eszköz-képesség modell létrehozása és tesztelése a Visual Studio Code használatával](tutorial-pnp-visual-studio-code.md)
