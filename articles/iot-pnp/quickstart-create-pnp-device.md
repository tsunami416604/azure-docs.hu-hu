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
ms.openlocfilehash: 4e13e6f86e9ddc494378e35f85962658d3fe5b25
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806503"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device"></a>Gyors útmutató: Eszköz-képesség modell használata IoT Plug and Play eszköz létrehozásához

Az _eszköz képességi modellje_ (DCM) ismerteti a IoT Plug and Play eszköz képességeit. A DCM gyakran társítva van egy termék SKU-hoz. A DCM-ben meghatározott képességek újrafelhasználható felületekbe vannak rendezve. A DCM-eszköz kódját létrehozhatja a DCM-ből. Ez a rövid útmutató bemutatja, hogyan használható a VS Code egy IoT-Plug and Play eszköz DCM használatával történő létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez telepítenie kell a következő szoftvereket a helyi gépre:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a **NuGet csomagkezelő** összetevőjét és az asztali fejlesztést a Visual Studio telepítésekor számítási **feladatokkal C++**  végezze el.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-device-workbench"></a>Az Azure IoT Device Workbench telepítése

Az alábbi lépésekkel telepítheti az Azure IoT Device Workbench bővítményt a VS Code-ban:

1. A VS Code-ban válassza a **kiterjesztések** lapot.
1. Keressen rá az **Azure IoT Device Workbench**kifejezésre.
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

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

### <a name="get-azure-iot-device-sdk-for-c"></a>A C Azure IoT Device SDK beszerzése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT C Device SDK-t.

1. Nyisson meg egy parancssort. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Hozzon `pnp_app` létre egy alkönyvtárat a tárház helyi klónjának gyökerében. Ezt a mappát kell használnia az eszköz modell fájljaihoz és az eszköz kódjához.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir pnp_app
    ```

## <a name="author-your-model"></a>A modell szerzője

Ebben a rövid útmutatóban egy meglévő minta-eszköz képesség modellt és társított csatolókat használ.

1. Töltse le az [eszköz képességeinek modelljét](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) és a [felületi mintát](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json) , és `pnp_app` mentse a fájlokat a mappába.

    > [!TIP]
    > Egy fájl GitHubról való letöltéséhez navigáljon a fájlhoz, kattintson a jobb gombbal a **RAW**elemre, majd válassza a **hivatkozás mentése másként**lehetőséget.

1. Mappa `pnp_app` megnyitása a vs Code-ban. A fájlokat az IntelliSense használatával tekintheti meg:

    ![Eszköz képességeinek modellje](media/quickstart-create-pnp-device/dcm.png)

1. A letöltött fájlokban cserélje le `<YOUR_COMPANY_NAME_HERE>` `@id` a és `schema` a mezőket egy egyedi értékre. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja. További információ: [digitális kettős azonosító formátuma](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format).

## <a name="generate-the-c-code-stub"></a>A C kód kiváltása

Most már rendelkezik egy DCM-rel és a hozzá tartozó csatolókkal, létrehozhatja a modellt megvalósító eszköz kódját. A C-kód a (z) VS Code-ban való létrehozásához:

1. Ha a DCM-fájlok mappája meg van nyitva, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

    > [!NOTE]
    > Amikor első alkalommal használja a IoT Plug and Play Code Generator segédprogramot, a letöltés eltarthat néhány másodpercig.

1. Válassza ki azt a DCM-fájlt, amelyet az eszköz kódjának létrehozásához használni kíván.

1. Adja meg a projekt nevét **sample_device**, és ez lesz az eszköz alkalmazásának neve.

1. Válassza az **ANSI C** nyelvet.

1. A projekt típusaként válassza a **CMAK-projekt** lehetőséget.

1. Válassza a **IoT hub eszköz kapcsolatok karakterlánca** lehetőséget a csatlakoztatási módszerként.

1. A VS Code egy új ablakot nyit meg a generált kódlap-fájlokkal.
    ![Eszköz kódja](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz SDK használatával hozza létre a generált eszköz kódját. Az Ön által létrehozott alkalmazás szimulál egy olyan eszközt, amely egy IoT hubhoz csatlakozik. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. A vs Code-ban `CMakeLists.txt` nyissa meg az eszköz SDK gyökérmappa mappát.

1. Adja hozzá az alábbi sort a `CMakeLists.txt` fájl alján, hogy tartalmazza az eszköz kód-helyettes mappáját a fordításakor:

    ```txt
    add_subdirectory(pnp_app/sample_device)
    ```

1. Hozzon létre egy CMAK-alkönyvtárat az eszköz SDK gyökérkönyvtárában, és navigáljon a következő mappába:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. A következő parancsok futtatásával hozza létre az eszköz SDK-t és a generált kódot:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Ha a Csatlakozáskezelő felügyeleti csomag nem C++ találja a fordítót, az előző parancs futtatásakor hibaüzeneteket kaphat. Ha ez történik, próbálja meg futtatni ezt a parancsot a [Visual Studio parancssorában](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. A létrehozás sikeres befejezése után futtassa az alkalmazást a IoT hub Device-kapcsolatok karakterláncának paraméterként való átadásával.

    ```cmd\sh
    cd azure-iot-sdk-c\cmake\pnp_app\sample_device\Release\
    sample_device.exe "[IoT Hub device connection string]"
    ```

1. Az eszköz megkezdi az adatok küldését a IoT Hubba.

    ![Eszközön futó alkalmazás](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>A kód ellenőrzése

### <a name="publish-device-model-files-to-model-repository"></a>Eszköz-modell fájljainak közzététele a Model repositoryban

Az eszköz kódjának az **Azure IoT Explorerrel**való ellenőrzéséhez közzé kell tennie a fájlokat a modell adattárában.

1. Ha a DCM-fájlok mappája meg van nyitva, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a **parancssort, írja be a parancsot, és válassza a IoT Plug & Play: Fájlok beküldése a Model**adattárba.

1. Válassza `SampleDevice.capabilitymodel.json` ki `EnvironmentalSensor.interface.json` a és a fájlokat.

1. Adja meg a vállalati modell adattárához tartozó kapcsolatok sztringjét.

    > [!NOTE]
    > A kapcsolati karakterláncra csak akkor van szükség, amikor először csatlakozik az adattárhoz.

1. A VS Code output ablakban és az értesítésben megtekintheti, hogy a fájlok közzététele sikeresen megtörtént-e.

    > [!NOTE]
    > Ha hibaüzenet jelenik meg az eszköz-modell fájljainak közzétételekor, akkor próbálja meg használni **a IoT Plug and Play: Jelentkezzen ki a** modell-tárházból a kijelentkezéshez, és ismételje meg a lépéseket.

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
2. Ha látni szeretné, hogy az új név megjelenik a **jelentett tulajdonság** oszlopban, kattintson a lap tetején található **frissítés** gombra.

1. Kattintson a **parancs** lapra az eszköz által támogatott összes parancs megtekintéséhez.

1. Bontsa ki a **Blink** parancsot, és állítson be egy új villogási időközt. Válassza a **Küldés parancs** lehetőséget a parancs meghívásához az eszközön.

1. Lépjen a szimulált eszközre, és győződjön meg arról, hogy a parancs a várt módon lett végrehajtva.

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre IoT Plug and Play-eszközt DCM használatával.

Ha többet szeretne megtudni a IoT Plug and Playről, folytassa az Oktatóanyaggal:

> [!div class="nextstepaction"]
> [Eszköz-képesség modell létrehozása és tesztelése a Visual Studio Code használatával](tutorial-pnp-visual-studio-code.md)
