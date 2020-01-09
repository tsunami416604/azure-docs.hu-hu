---
title: Oktatóanyag – IoT Plug and Play előnézeti eszköz csatlakoztatása az Azure-hoz IoT Central
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhatja ki az eszköz kódját az eszköz-képesség modell használatával. Ezután futtassa az eszköz kódját, tekintse meg az eszköz csatlakozik a IoT Central alkalmazáshoz, és használja az automatikusan létrehozott nézeteket.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 233d8f345eceb188fcc849457c5243a3c64aaec2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75548853"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Oktatóanyag: eszköz-képességi modell használata IoT-Plug and Play eszköz létrehozásához és a IoT Central-alkalmazáshoz való csatlakoztatásához

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Az _eszköz képességi modellje_ (DCM) ismerteti a [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) eszköz képességeit. IoT Central a DCM használatával létrehozhat egy eszköz sablont és vizualizációkat az eszközhöz, amikor az eszköz az első alkalommal csatlakozik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Visual Studio Code használatával hozzon létre egy IoT Plug and Play eszközt DCM használatával.
> * Futtassa az eszköz kódját a Windowsban, és tekintse meg a IoT Central alkalmazáshoz való kapcsolódást ismertető témakört.
> * Az eszköz által küldött szimulált telemetria megtekintése.

## <a name="prerequisites"></a>Előfeltételek

Fejezze be az [Azure IoT Central-alkalmazás létrehozása (előzetes verziójú funkciók)](./quick-deploy-iot-central.md) című rövid útmutatót egy IoT Central alkalmazás létrehozásához az **egyéni alkalmazás > előzetes verziójú alkalmazás** sablonjának használatával.

Az oktatóanyag elvégzéséhez telepítenie kell a következő szoftvereket a helyi gépre:

* [Hozzon létre eszközöket a Visual studióhoz](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) ,  **C++ és hozzon létre eszközöket** és **Nuget csomagkezelő összetevő** -számítási feladatokat. Ha már rendelkezik a [Visual Studióval (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 vagy 2015-val, és ugyanazokat a számítási feladatokat telepítette.
* [Git](https://git-scm.com/download/).
* [CMAK](https://cmake.org/download/) – a **CMAK**telepítésekor válassza a **CMAK hozzáadása a rendszerútvonalhoz**lehetőséget.
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* A `dps-keygen` segédprogram:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Az Azure IoT-eszközök telepítése

Az alábbi lépésekkel telepítheti az Azure IoT Tools Extension Pack csomagot a VS Code-ban:

1. A VS Code-ban válassza a **kiterjesztések** lapot.
1. Keresse meg az **Azure IoT-eszközöket**.
1. Válassza az **Install** (Telepítés) lehetőséget.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben az oktatóanyagban a [Vcpkg](https://github.com/microsoft/vcpkg) Library Manager használatával telepítheti az Azure IoT C Device SDK-t a fejlesztői környezetbe.

1. Nyisson meg egy parancssort. Futtassa a következő parancsot a Vcpkg telepítéséhez:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Ezután a felhasználói szintű [integráció](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)összekapcsolása érdekében futtassa a következő parancsot. Amikor először futtatja ezt a parancsot, rendszergazdai jogosultságok szükségesek:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Az Azure IoT C Device SDK Vcpkg telepítése:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Eszköz kulcsának előállítása

Egy eszköz IoT Central alkalmazáshoz való csatlakoztatásához szükség van egy eszköz kulcsára. Eszköz kulcsának létrehozása:

1. Jelentkezzen be a létrehozott IoT Central alkalmazásba az **egyéni alkalmazás > előzetes verziójú alkalmazás** sablonjában az [Azure IoT Central-alkalmazás létrehozása (előzetes verziójú funkciók)](./quick-deploy-iot-central.md) című útmutatóban.

1. Lépjen az **Adminisztráció** lapra, és válassza az **eszköz csatlakoztatása**lehetőséget.

1. Jegyezze fel az **azonosító hatókörét** és a **kulcsok megtekintésekor**megjelenő **elsődleges kulcsot** . Ezeket az értékeket az oktatóanyag későbbi részében használhatja.

    ![Eszköz csatlakoztatása](./media/tutorial-connect-pnp-device/device-connection.png)

1. Nyisson meg egy parancssort, és futtassa a következő parancsot egy eszköz kulcsának létrehozásához:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Jegyezze fel a generált _eszköz kulcsát_, ezt az értéket az oktatóanyag egy későbbi lépésében használhatja.

## <a name="download-your-model"></a>A modell letöltése

Ebben az oktatóanyagban a nyilvános DCM-et használja egy MxChip IoT fejlesztői készlet-eszközhöz. A kód futtatásához nincs szükség tényleges fejlesztői készlet-eszközre, ebben az oktatóanyagban pedig a Windows rendszeren való futtatásra fordítja le a kódot.

1. Hozzon létre egy `central_app` nevű mappát, és nyissa meg a VS Code-ban.

1. A **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza a **modell-adattár megnyitása**lehetőséget. Válassza a **nyilvános tárház**lehetőséget. A VS Code a nyilvános modell tárházában lévő DCMs listáját jeleníti meg.

1. Válassza ki a **MXChip IoT fejlesztői készlet** DCM azonosítót `urn:mxchip:mxchip_iot_devkit:1`. Ezután válassza a **Letöltés**lehetőséget. Most már rendelkezik egy másolattal a DCM-ről a `central_app` mappában.

![A Model adattár és a DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> A IoT Central használatához az eszköz képességeinek modellje csak ugyanabban a fájlban definiált összes illesztőfelülettel rendelkezhet.

## <a name="generate-the-c-code-stub"></a>A C kód kiváltása

Most már rendelkezik a **MXChip IoT fejlesztői készlet** DCM-vel és a hozzá tartozó felületekkel, létrehozhatja a modellt megvalósító eszköz kódját. A C-kód a (z) VS Code-ban való létrehozásához:

1. Ha a DCM-fájlok mappája meg van nyitva, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

    > [!NOTE]
    > Amikor első alkalommal használja a IoT Plug and Play Code Generator segédprogramot, a letöltés eltarthat néhány másodpercig.

1. Válassza ki az imént letöltött **MXChip IoT fejlesztői készlet** DCM-fájlt.

1. Adja meg a projekt nevét **devkit_device**.

1. Válassza az **ANSI C** nyelvet.

1. Válassza a **DPS (eszköz kiépítési szolgáltatás) szimmetrikus kulcsát** a csatlakoztatási módszerként.

1. Válassza a **Windows rendszerhez tartozó CMAK-projekt** lehetőséget a projekt típusaként. Ne válassza a **MXChip IoT fejlesztői készlet-projekt**lehetőséget, ha valódi fejlesztői készlet-eszközzel rendelkezik.

1. Válassza a **Vcpkg keresztül** lehetőséget az SDK belefoglalásához.

1. A VS Code egy új ablakot nyit meg, amely a `devkit_device` mappában generált kódlap-fájlokat hoz létre.

![Generált eszköz kódja](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz SDK használatával hozza létre a generált eszköz kódját. Az Ön által létrehozott alkalmazás szimulál egy **MXChip IoT fejlesztői készlet** eszközt, és csatlakozik a IoT Central alkalmazáshoz. Az alkalmazás elküldi a telemetria és a tulajdonságokat, és fogadja a parancsokat.

1. A parancssorban hozzon létre egy `cmake` alkönyvtárat a `devkit_device` mappában, és navigáljon a következő mappába:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a generált kód kiépítéséhez. Cserélje le a `<directory of your Vcpkg repo>` helyőrzőt a **Vcpkg** -tárház másolatának elérési útjára:

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Ha a Visual Studio 2017-es vagy a 2015-es verzióját használja, meg kell adnia a CMak-generátort az Ön által használt build-eszközök alapján:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. A létrehozás sikeres befejezése után ugyanazon a parancssorban futtassa az alkalmazást. Cserélje le a `<scopeid>` és a `<primarykey>` értéket a korábban feljegyzett értékekre:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <primarykey>
    ```

1. Az eszköz megkezdi az adatok küldését a IoT Hubba. Időnként megjelenik az előző parancs első futtatásakor `Error registering device for DPS` hiba. Ha ezt a hibát látja, próbálja megismételni a parancsot.

## <a name="view-the-device"></a>Az eszköz megtekintése

Miután az eszköz kódja csatlakozik a IoT Centralhoz, megtekintheti az általa küldött tulajdonságokat és telemetria:

1. A IoT Central alkalmazásban nyissa meg az **eszközök** lapot, és válassza ki a **mxchip-01** eszközt. Az eszköz automatikusan hozzá lett adva a csatlakoztatott eszköz kódjához:

    ![Áttekintő lap](./media/tutorial-connect-pnp-device/overview-page.png)

    Néhány perc elteltével a lap az eszköz által küldött telemetria diagramjait jeleníti meg.

1. A **Névjegy** lapon megtekintheti az eszköz által eljuttatott tulajdonságértékek értékét.

1. Válassza a **parancsok** lapot, és hívja meg a parancsokat az eszközön. Megtekintheti az eszközt, amely az eszköz kódját futtató parancssorban válaszol.

1. Nyissa meg az **eszközbeállítások** lapot, és tekintse meg a DCM-ből a nyilvános tárházban létrehozott IoT Central sablont:

    ![Eszközök sablonjai lap](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz, amely egy DCM-ből jött létre a nyilvános modell adattárában.

Ha többet szeretne megtudni a DCMs és a saját modelljeinek létrehozásáról, folytassa a következő útmutatóval:

> [!div class="nextstepaction"]
> [Eszközcsoport létrehozása](./tutorial-use-device-groups.md)
