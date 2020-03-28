---
title: Oktatóanyag – IoT Plug and Play (előzetes verzió) eszköz csatlakoztatása az Azure IoT Centralhoz
description: Ez az oktatóanyag bemutatja, hogyan használhat eszközképességi modellt az eszközkód létrehozásához. Ezután futtassa az eszközkódot, tekintse meg az eszköz csatlakozását az IoT Central alkalmazáshoz, és használja az automatikusan létrehozott nézeteket.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 42098d54725cc12691839b63c508efbecf042aa0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80064415"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>Oktatóanyag: Használjon eszközképességi modellt egy IoT Plug and Play (előzetes verzió) eszköz létrehozásához és az IoT Central alkalmazáshoz való csatlakoztatásához.

Az _eszközképességi modell_ (DCM) az [IoT Plug and Play (előzetes verzió)](../../iot-pnp/overview-iot-plug-and-play.md) eszközök képességeit írja le. Az IoT Central egy DCM segítségével létrehozhat egy eszközsablont és vizualizációkat egy eszközhöz, amikor az eszköz először csatlakozik.

Az [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) támogatása előzetes verzióban érhető el, és csak a kiválasztott régiókban támogatott.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Visual Studio-kód használatával IoT Plug and Play (előzetes verzió) eszközt hozhat létre DCM használatával.
> * Futtassa az eszközkódot a Windows rendszerben, és tekintse meg, hogy csatlakozzon az IoT Central alkalmazáshoz.
> * Tekintse meg az eszköz által küldött szimulált telemetriai adatokat.

## <a name="prerequisites"></a>Előfeltételek

Végezze el az [Azure IoT Central alkalmazás](./quick-deploy-iot-central.md) létrehozása rövid útmutatót egy IoT Central alkalmazás létrehozásához az egyéni alkalmazás > egyéni **alkalmazássablon** használatával.

Az oktatóanyag befejezéséhez telepítenie kell a következő szoftvert a helyi számítógépre:

* **C++ buildeszközökkel** és **Nuget csomagkezelői összetevők** munkaterheléseivel [fejlesztőeszközök készíthető a Visual Studio számára.](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) Vagy ha már rendelkezik [visual studio (közösségi, professzionális vagy nagyvállalati)](https://visualstudio.microsoft.com/downloads/) 2019,2017 vagy 2015 azonos számítási feladatok telepítve.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/) - a **CMake**telepítésekor válassza a **CMake hozzáadása a rendszerhez PATH**lehetőséget.
* [Visual Studio kód](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* A `dps-keygen` segédprogram:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Az Azure IoT-eszközök telepítése

Az alábbi lépésekkel telepítheti az Azure IoT Tools bővítménycsomagot a VS Code-ban:

1. A VS-kód ban válassza a **Bővítmények** lapot.
1. Keresse meg az **Azure IoT-eszközöket.**
1. Válassza az **Install** (Telepítés) lehetőséget.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

Ebben az oktatóanyagban a [Vcpkg-könyvtárkezelő](https://github.com/microsoft/vcpkg) vel telepítheti az Azure IoT C-eszköz SDK-t a fejlesztői környezetben.

1. Nyisson meg egy parancssort. A Vcpkg telepítéséhez hajtsa végre a következő parancsot:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Ezután a felhasználói szintű [integráció csatlakoztatásához](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)futtassa a következő parancsot. A parancs első futtatásakor rendszergazdai jogokra van szükség:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Telepítse az Azure IoT C-eszköz SDK Vcpkg:Install Azure IoT C device SDK Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Eszközkulcs létrehozása

Egy eszköz csatlakoztatásához egy IoT Central-alkalmazás, szüksége van egy eszközkulcs. Eszközkulcs létrehozása:

1. Jelentkezzen be az IoT Central alkalmazás hoz létre az **egyéni alkalmazássablon** használatával az [Azure IoT Central alkalmazás](./quick-deploy-iot-central.md) rövid útmutató létrehozása.

1. Nyissa meg a **Felügyeleti** lapot, és válassza **az Eszközkapcsolat**lehetőséget.

1. Jegyezze fel a **Kulcsok**megtekintése kor látható **azonosítóhatókört** és **elsődleges kulcsot.** Ezeket az értékeket az oktatóanyag későbbi részében használhatja.

    ![Eszközkapcsolat](./media/tutorial-connect-pnp-device/device-connection.png)

1. Nyisson meg egy parancssort, és futtassa a következő parancsot az eszközkulcs létrehozásához:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Jegyezze fel a létrehozott _eszközkulcsot,_ ezt az értéket az oktatóanyag egy későbbi lépésében használhatja.

## <a name="download-your-model"></a>A modell letöltése

Ebben az oktatóanyagban a nyilvános DCM-et egy MxChip IoT DevKit-eszközhöz használja. Nem kell egy tényleges DevKit-eszköz a kód futtatásához, ebben az oktatóanyagban lefordítja a windowsos futtatásához szükséges kódot.

1. Hozzon létre `central_app` egy nevű mappát, és nyissa meg a VS-kódban.

1. A **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát, írja be az **IoT Plug and Play**parancsot, és válassza a **Modelltárház megnyitása**lehetőséget. Válassza a **Nyilvános tárház lehetőséget.** A VS Code megjeleníti a nyilvános modelltárházban lévő dcm-ek listáját.

1. Válassza ki az **MXChip IoT DevKit** DCM azonosítóval. `urn:mxchip:mxchip_iot_devkit:1` Ezután válassza **a Letöltés lehetőséget.** Most már van egy példánya `central_app` a DCM a mappában.

![Modelltárház és DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Az IoT Central működéséhez az eszközképességi modellnek rendelkeznie kell az összes, a fájlban lévő inline kapcsolattal.

## <a name="generate-the-c-code-stub"></a>A C-kódcsonk létrehozása

Most már rendelkezik az **MXChip IoT DevKit** DCM és a hozzá tartozó felületek, létrehozhatja a modellt megvalósító eszközkódot. A C-kódcsonk létrehozása a VS-kódban:

1. A DCM-fájlokkal rendelkező mappa megnyitása kor a **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát, írja be az **IoT Plug and Play parancsot,** és válassza **az Eszközkód csonk létrehozása lehetőséget.**

    > [!NOTE]
    > Az IoT Plug and Play Kódgenerátor segédprogram első használatakor néhány másodpercet vesz igénybe a letöltés.

1. Válassza ki az imént letöltött **MXChip IoT DevKit** DCM fájlt.

1. Írja be a projekt nevét **devkit_device**.

1. Válassza az **ANSI C nyelvet.**

1. Válassza **a Via DPS (Device Provisioning Service) szimmetrikus kulcsot** csatlakozási módszerként.

1. Projekttípusként válassza **a CMake Project on Windows** lehetőséget. Ne válassza **az MXChip IoT DevKit Project**lehetőséget, ez a beállítás akkor érhető el, ha valódi DevKit-eszközzel rendelkezik.

1. Válassza a **Via Vcpkg** lehetőséget az SDK felvételének módjaként.

1. A VS Code új ablakot nyit meg a `devkit_device` mappában generált eszközkódfájlokkal.

![Generált eszközkód](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz SDK segítségével hozza létre a generált eszközkód csonkot. Az alkalmazást hoz létre szimulálja egy **MXChip IoT DevKit-eszköz,** és csatlakozik az IoT Central alkalmazáshoz. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. A parancssorban hozzon `cmake` létre egy `devkit_device` alkönyvtárat a mappában, és keresse meg a mappát:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a létrehozott kódcsonk létrehozásához. Cserélje `<directory of your Vcpkg repo>` le a helyőrzőt a **Vcpkg-tárház** példányának elérési útjának:

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Ha a Visual Studio 2017-et vagy 2015-öt használja, a CMake-generátort a használt buildeszközök alapján kell megadnia:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Miután a build sikeresen befejeződött, ugyanebben a parancssorban futtassa az alkalmazást. Cserélje `<scopeid>` `<devicekey>` le és írja be a korábban említett értékeket:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. Az eszközalkalmazás megkezdi az adatok küldését az IoT Hubra. Néha az előző `Error registering device for DPS` parancs első futtatásakor megjelenik a hiba. Ha ez a hibaüzenet jelenik meg, próbálkozzon újra a paranccsal.

## <a name="view-the-device"></a>Az eszköz megtekintése

Miután az eszközkód csatlakozik az IoT Centralhoz, megtekintheti az általa küldött tulajdonságokat és telemetriai adatokat:

1. Az IoT Central alkalmazásban nyissa meg az **Eszközök** lapot, és válassza ki az **mxchip-01** eszközt. Ez az eszköz automatikusan hozzáadódik, amikor az eszköz kódja csatlakoztatva van:

    ![Áttekintő lap](./media/tutorial-connect-pnp-device/overview-page.png)

    Néhány perc múlva ezen az oldalon az eszköz által küldött telemetriai adatok diagramjait jeleníti meg.

1. Válassza a **Beet** lapot az eszköz által küldött tulajdonságértékek megtekintéséhez.

1. Jelölje ki a **Parancsok** lapot az eszközön lévő parancsok hívásához. Az eszköz válaszol a parancssorból, amely az eszközkódot futtatja.

1. Az **Eszközsablonok** lapon megtekintheti az IoT Central által a DCM-ből létrehozott sablont a nyilvános tárházban:

    ![Eszközsablonok lapja](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play (előzetes verzió) eszközt, amely a nyilvános modelltárházban egy DCM-ből jött létre.

Ha többet szeretne megtudni a dcm-ekről és a saját modellek létrehozásáról, folytassa az útmutatóval:

> [!div class="nextstepaction"]
> [Új IoT-eszköztípus definiálása](./howto-set-up-template.md)
