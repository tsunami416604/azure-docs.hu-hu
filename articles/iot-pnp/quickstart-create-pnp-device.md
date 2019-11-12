---
title: IoT-Plug and Play előnézeti eszköz (Windows) létrehozása | Microsoft Docs
description: Eszköz-képesség modell használata az eszköz kódjának létrehozásához. Ezután futtassa az eszköz kódját, és tekintse meg az eszközt a IoT Hubhoz való kapcsolódáshoz.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4ee9bf218765ea4c3966e7f0a8b20a8108de7655
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931912"
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

Töltse le és telepítse az **Azure IoT Explorer** legújabb kiadását az eszköz [tárházának](https://github.com/Azure/azure-iot-explorer/releases) oldaláról, ehhez válassza ki az. msi fájlt a legutóbbi frissítéshez az "eszközök" területen.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>A vállalati modell adattárához tartozó kapcsolatok karakterláncának beolvasása

A _vállalati modell adattárának kapcsolati karakterláncát_ az [Azure Certified for IoT portál](https://preview.catalog.azureiotsolutions.com) portálon találja, ha Microsoft munkahelyi vagy iskolai fiókkal jelentkezik be, vagy ha rendelkezik Microsoft partner-azonosítóval. A bejelentkezést követően válassza a **vállalati tárház** , majd a **kapcsolatok karakterláncok**lehetőséget.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. Ha nem rendelkezik IoT-hubhoz, kövesse az [alábbi utasításokat, és hozzon létre egyet](../iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

A következő parancs futtatásával hozza létre az eszköz identitását az IoT hub-ban. Cserélje le a **YourIoTHubName** és a **YourDevice** helyőrzőket a tényleges nevekre.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDevice>
```

Futtassa az alábbi parancsot az imént regisztrált eszköz _kapcsolati sztringjének_ lekéréséhez:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

Futtassa a következő parancsot a hub _IoT hub kapcsolódási karakterláncának_ lekéréséhez:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

### <a name="get-azure-iot-device-sdk-for-c"></a>A C Azure IoT Device SDK beszerzése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő az Azure IoT C Device SDK [Vcpkg](https://github.com/microsoft/vcpkg)használatával történő telepítésével.

1. Nyisson meg egy parancssort. Futtassa a következő parancsot a Vcpkg telepítéséhez:

    ```cmd/sh
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Ezután a felhasználó szintű [integráció](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)összekapcsolásához futtassa a következőt (Megjegyzés: az első használathoz rendszergazdai jogosultság szükséges):

    ```cmd/sh
    .\vcpkg.exe integrate install
    ```

1. Az Azure IoT C Device SDK Vcpkg telepítése:

    ```cmd/sh
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="author-your-model"></a>A modell szerzője

Ebben a rövid útmutatóban egy meglévő minta-eszköz képesség modellt és társított csatolókat használ.

1. Hozzon létre egy `pnp_app` könyvtárat a helyi meghajtón. Ezt a mappát kell használnia az eszköz modell fájljaihoz és az eszköz kódjához.

1. Töltse le az [eszköz képességeinek modelljét és a felületi minta fájljait](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) [, és mentse](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) a fájlokat `pnp_app` mappába.

    > [!TIP]
    > Egy fájl GitHubról való letöltéséhez navigáljon a fájlhoz, kattintson a jobb gombbal a **RAW**elemre, majd válassza a **hivatkozás mentése másként**lehetőséget.

1. Nyissa meg `pnp_app` mappát a VS Code-ban. A fájlokat az IntelliSense használatával tekintheti meg:

    ![Eszköz képességeinek modellje](media/quickstart-create-pnp-device/dcm.png)

1. A letöltött fájlokban cserélje le a `<YOUR_COMPANY_NAME_HERE>`t a `@id` és a `schema` mezőkbe egy egyedi értékkel. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja. További információ: [digitális kettős azonosító formátuma](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>A C kód kiváltása

Most, hogy már rendelkezik DCM-rel és a hozzá tartozó csatolókkal, létrehozhatja a modellt megvalósító eszköz kódját. A C-kód a (z) VS Code-ban való létrehozásához:

1. A VS Code-ban megnyitott `pnp_app` mappában a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

    > [!NOTE]
    > Amikor első alkalommal használja a IoT Plug and Play CodeGen CLI-t, néhány másodperc elteltével automatikusan letöltheti és telepítheti.

1. Válassza ki az **SampleDevice. capabilitymodel. JSON** fájlt, amelyet az eszköz kódjának generálásához kíván használni.

1. Adja meg a projekt nevét **sample_device**. Ez lesz az eszköz-alkalmazás neve.

1. Válassza az **ANSI C** nyelvet.

1. Válassza a **IoT hub eszköz kapcsolatok karakterlánca** lehetőséget a csatlakoztatási módszerként.

1. Válassza a **Windows rendszerhez készült CMAK-projekt** lehetőséget a Project sablonként.

1. Válassza a **Vcpkg keresztül** lehetőséget az eszköz SDK-nak való felvételéhez.

1. A rendszer egy **sample_device** nevű új mappát hoz létre a DCM-fájllal megegyező helyen, és ez a generált kódlap-fájlok. A VS Code egy új ablakot nyit meg, amely megjeleníti ezeket.
    ![eszköz kódja](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-and-run-the-code"></a>A kód létrehozása és futtatása

Az eszköz SDK forráskódját a generált eszköz kódjának összeállításához használhatja. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon létre egy `cmake` alkönyvtárat a `sample_device` mappában, és navigáljon a következő mappába:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a generált kód létrehozásához (a helyőrzőt cserélje le a Vcpkg-tárház könyvtárára):

    ```cmd\sh
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

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

1. A létrehozás sikeres befejezése után futtassa az alkalmazást, és adja át az IoT hub-eszközhöz tartozó kapcsolatok sztringjét paraméterként.

    ```cmd\sh
    .\Debug\sample_device.exe "<device connection string>"
    ```

1. Az eszköz megkezdi az adatok küldését a IoT Hubba.

    ![Eszközön futó alkalmazás](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>A kód ellenőrzése

### <a name="publish-device-model-files-to-model-repository"></a>Eszköz-modell fájljainak közzététele a Model repositoryban

Az eszköz kódjának az **Azure IoT Explorerrel**való ellenőrzéséhez közzé kell tennie a fájlokat a modell adattárában.

1. A VS Code-ban megnyitott `pnp_app` mappában a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a parancsot, majd válassza a **IoT Plug & Play: fájlok elküldése a modell adattárba**lehetőséget.

1. Válassza ki `SampleDevice.capabilitymodel.json` és `EnvironmentalSensor.interface.json` fájlokat.

1. Adja meg a vállalati modell adattárához tartozó kapcsolatok sztringjét.

    > [!NOTE]
    > A kapcsolati karakterláncra csak akkor van szükség, amikor először csatlakozik az adattárhoz.

1. A VS Code output ablakban és az értesítésben megtekintheti, hogy a fájlok közzététele sikeresen megtörtént-e.

    > [!NOTE]
    > Ha hibaüzenet jelenik meg az eszköz-modell fájljainak közzétételekor, akkor próbálja meg használni a **IoT Plug and Play: Jelentkezzen ki a modell tárházában** a kijelentkezéshez, és ismételje meg a lépéseket.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

1. Nyissa meg az Azure IoT Explorert. Megjelenik az **alkalmazás-konfigurációk** lap.

1. Adja meg _IoT hub kapcsolati karakterláncát_ , és válassza a **Csatlakoztatás**lehetőséget.

1. A csatlakoztatása után megjelenik az eszköz áttekintő lapja.

1. A vállalati tárház hozzáadásához válassza a **Beállítások**, majd a **modul-definíciós forrás**, majd a **vállalati tárház**lehetőséget. Adja hozzá a vállalati modell adattárának kapcsolati karakterláncát, majd kattintson a **Mentés és a csatlakoztatás**lehetőségre.

1. Az eszköz áttekintése lapon keresse meg a korábban létrehozott eszköz identitását, és válassza ki a további részletek megtekintéséhez.

1. Bontsa ki az **urn: < YOUR_INTERFACE_NAME >: EnvironmentalSensor: 1** azonosítójú felületet, és tekintse meg a IoT Plug and Play primitívek – tulajdonságok, parancsok és telemetria. A megjelenő felület neve a modell készítésekor behelyezett név.

1. Válassza ki a **telemetria** lapot, és kattintson a _Start_ gombra az eszköz által küldött telemetria-adatok megtekintéséhez.

1. Válassza a **Tulajdonságok (nem írható)** lapot az eszköz által jelentett nem írható tulajdonságok megtekintéséhez.

1. Válassza a **Tulajdonságok (írható)** lapot a frissíteni kívánt írható tulajdonságok megtekintéséhez.

1. Bontsa **ki a tulajdonságnév, a**frissítés új névvel elemet, majd válassza az **írható tulajdonság frissítése**lehetőséget.

1. Ha meg szeretné jeleníteni az új nevet a **jelentett tulajdonság** oszlopban, válassza a lap tetején található **frissítés** gombot.

1. Válassza a **parancsok** lapot az eszköz által támogatott összes parancs megtekintéséhez.

1. Bontsa ki a **Blink** parancsot, és állítson be egy új villogási időközt. Válassza a **Küldés parancs** lehetőséget a parancs meghívásához az eszközön.

1. Nyissa meg a szimulált eszköz parancssorát, és olvassa el a kinyomtatott megerősítő üzeneteket, és ellenőrizze, hogy a parancsok a várt módon lettek-e végrehajtva.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre IoT Plug and Play-eszközt DCM használatával.

Ha többet szeretne megtudni a DCMs és a saját modelljeinek létrehozásáról, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Oktatóanyag: eszköz-képesség modell létrehozása és tesztelése a Visual Studio Code használatával](tutorial-pnp-visual-studio-code.md)
