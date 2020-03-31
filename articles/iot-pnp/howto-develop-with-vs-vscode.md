---
title: IoT Plug and Play előzetes eszközök létrehozása a Visual Studio és a Visual Studio code használatával | Microsoft dokumentumok
description: A Visual Studio és a Visual Studio-kód segítségével felgyorsíthatja az IoT Plug and Play eszközmodellek szerzői használatát és megvalósíthatja az eszközkódot.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159234"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>IoT Plug and Play eszközök létrehozása a Visual Studio és a Visual Studio kódja segítségével

Az Azure IoT Tools for Visual Studio Code integrált környezetet biztosít az eszközképességi modellek (DCM) és az összeköttetések létrehozásához, a modelltárolókban való közzétételhez, valamint az eszközalkalmazás megvalósításához létrehozott C-kód létrehozásához.

Ez a cikk a következőkhöz nyújt útmutatást:

- Eszközkód és alkalmazásprojekt létrehozása.
- Használja a létrehozott kódot az eszközprojektben.
- A csontváz kód regenerálásával itatjuk.

Ha többet szeretne tudni arról, hogy miként fejleszthet iot-eszközöket a VS-kód használatával: . [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)

## <a name="prerequisites"></a>Előfeltételek

Telepítse [a Visual Studio-kódot](https://code.visualstudio.com/).

A bővítménycsomag VS Code-ban való telepítéséhez kövesse az alábbi lépéseket.

1. A VS-kód csoportban válassza **a Bővítmények** lapot.
1. Keresse meg és telepítse az **Azure IoT-eszközöket** a piactérről.

## <a name="generate-device-code-and-project"></a>Eszközkód és projekt létrehozása

A VS-kód ban a **Ctrl+Shift+P billentyűkombinációval** nyissa meg a parancspalettát, írja be az **IoT Plug and Play** **parancsot,** és válassza az Eszközkód csonk létrehozása lehetőséget a csontváz kód és a projekttípus konfigurálásához. Az alábbi lista részletesen ismerteti az egyes lépéseket:

- **A kód létrehozásához használt DCM-fájl.** A csontváz eszközkód létrehozásához nyissa meg az általa megvalósított DCM- és felületfájlokat tartalmazó mappát. Ha a kódgenerátor nem találja a DCM által igényelt felületet, szükség szerint letölti azt a modelltárházból.

- **Eszközkód nyelve**. Jelenleg a kódgenerátor csak az ANSI C-t támogatja.

- **Projekt neve**. A projekt név a létrehozott kód és más projektfájlok mappaneveként szolgál. A mappa alapértelmezés szerint a DCM-fájl mellett van elhelyezve. Annak elkerülése érdekében, hogy a DCM frissítése kor és az eszközkód újragenerálásakor manuálisan másolja a létrehozott kódmappát, tartsa a DCM-fájlt ugyanabban a mappában, mint a projekt mappában.

- **Az Azure IoT-hez való csatlakozás módja.** A létrehozott fájlok is tartalmaznak kódot az eszköz konfigurálásához az Azure IoT Hub hoz való csatlakozáshoz. Választhat, hogy közvetlenül csatlakozik [az Azure IoT Hubhoz,](https://docs.microsoft.com/azure/iot-hub) vagy használja az [eszközkiépítési szolgáltatást.](https://docs.microsoft.com/azure/iot-dps)

    - **Az IoT Hub-eszköz kapcsolati karakterláncán**keresztül: adja meg az eszköz kapcsolati karakterláncát az eszközalkalmazás közvetlenül az IoT Hubhoz való csatlakozáshoz.
    - **A DPS szimmetrikus kulcson keresztül:** adja meg az **ID hatókört,** a **szimmetrikus kulcsot** és az **eszközazonosítót** az on-mail-alkalmazáshoz, amely az IoT Hubhoz vagy az IoT Centralhoz a DPS használatával való csatlakozáshoz szükséges.

- **Projekttípusa**. A kód generátor is generál egy CMake vagy Arduino projekt. Jelenleg a támogatott projekttípusok a következők:

    - **CMake Project on Windows**: olyan eszközprojekthez, amely [a CMake](https://cmake.org/) rendszert használja buildrendszerként a Windows rendszeren. Ez a `CMakeLists.txt` beállítás a C-kóddal azonos mappában lévő eszköz SDK-konfigurációkkal jön létre.
    - **CMake Project linuxos:** olyan eszközprojekthez, amely [a CMake-et](https://cmake.org/) linuxos buildrendszerként használja. Ez a `CMakeLists.txt` beállítás a C-kóddal azonos mappában lévő eszköz SDK-konfigurációkkal jön létre.
    - **MXChip IoT DevKit projekt:** egy [MXChip IoT DevKit](https://aka.ms/iot-devkit) eszközön futó eszközprojekthez. Ez a beállítás egy Arduino-projektet hoz létre, amelyet a [VS Code-ban](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) vagy az Arduino IDE-ben használhat egy IoT DevKit-eszközön való létrehozáshoz és futtatáshoz.

- **Eszköz SDK-típusa**. Ha a CMake projekttípust választja, ez a lépés annak konfigurálása, hogy a `CMakeLists.txt`generált kód hogyan tartalmazza az Azure IoT C eszköz SDK-ját a következőben:

    - **Via Forráskód:** a generált kód támaszkodik az [eszköz SDK forráskód](https://github.com/Azure/azure-iot-sdk-c) tartalmazza, és építeni vele együtt. Ez akkor ajánlott, ha testre szabta az eszköz SDK forráskódját.
    - **Via Vcpkg:** a generált kód támaszkodik az [eszköz SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) felvenni, és építeni vele együtt. Ez az ajánlott módszer Windows, Linux vagy macOS rendszert futtató eszközök esetében.

    > [!NOTE]
    > macOS-támogatás az Azure IoT C-eszköz SDK Vcpkg folyamatban van.

A kódgenerátor a helyi mappában található DCM és interface fájlokat próbálja használni. Ha a felület fájlok nem a helyi mappában, a kód generátor megkeresi őket a nyilvános modell tárházban vagy vállalati modell tárházban. [A közös felületfájlok](./concepts-common-interfaces.md) a nyilvános modelltárházban tárolódnak.

A kódgenerálás befejezése után a bővítmény megnyit egy új VS Code ablakot a kóddal. Ha megnyit egy létrehozott fájlt, például **a main.c**fájlt, előfordulhat, hogy az IntelliSense azt jelenti, hogy nem tudja megnyitni a C SDK forrásfájlokat. A megfelelő IntelliSense és kódnavigáció engedélyezéséhez a Következő lépésekkel veheti fel a C SDK-forrást:

1. A VS-kód mezőben a **Ctrl+Shift+P billentyűkombinációval** nyissa meg a parancspalettát, írja be a Parancsot, és válassza a **C/C++: Konfigurációk szerkesztése (JSON) parancsot** a **c_cpp_properties.json** fájl megnyitásához.

1. Adja hozzá az eszköz SDK `includePath` elérési útját a szakaszban:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Mentse a fájlt.

## <a name="use-the-generated-code"></a>A létrehozott kód használata

Az alábbi utasítások ismertetik, hogyan kell használni a generált kódot a saját eszköz projekt különböző fejlesztői gép platformokon. Az utasítások feltételezik, hogy egy IoT Hub-eszköz kapcsolati karakterláncot használ a létrehozott kóddal:

### <a name="linux"></a>Linux

Az eszköz kódjának a C SDK Vcpkg eszközzel együtt történő létrehozása a CMake használatával Linux környezetben, például Ubuntu vagy Debian környezetben:

1. Nyisson meg egy terminálalkalmazást.

1. Telepítse **a GCC,** **Git**, `cmake`és `apt-get` az összes függőséget a következő paranccsal:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ellenőrizze, hogy `cmake` a verzió meghaladja-e a **2.8.12-t,** és a **GCC** verziója **4.4.7**felett van- e.

    ```bash
    cmake --version
    gcc --version
    ```

1. Telepítse a Vcpkg-ot:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Ezután a felhasználói szintű [integráció](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)csatlakoztatásához futtassa a következőket:

    ```bash
    ./vcpkg integrate install
    ```

1. Telepítse az Azure IoT C-eszköz SDK Vcpkg:Install Azure IoT C device SDK Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Hozzon `cmake` létre egy alkönyvtárat a mappában, amely tartalmazza a létrehozott kódcsonkot, és keresse meg a mappát:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a CMake használatához az eszköz SDK és a létrehozott kódcsonk létrehozásához:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Miután a build sikeres, futtassa az ioT Hub-eszköz kapcsolati karakterláncparaméterként megadott alkalmazás.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Ha az eszközkódot a CMake és a Visual Studio C/C++ fordítók segítségével a Windows Rendszeren futó C SDK eszközzel együtt szeretné felépíteni a parancssorból, olvassa el az [IoT Plug and Play rövid útmutatót.](./quickstart-create-pnp-device-windows.md) A következő lépések bemutatják, hogyan hozhat létre az eszközkódot a C SDK Vcpkg eszközzel együtt CMake projektként a Visual Studióban.

1. Kövesse a [rövid útmutató lépéseit](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) az Azure IoT-eszköz C-hez való telepítéséhez Vcpkg-en keresztül.

1. Telepítse a [Visual Studio 2019-et (közösségi, professzionális vagy nagyvállalati verzió)](https://visualstudio.microsoft.com/downloads/) – győződjön meg arról, hogy tartalmazza a **NuGet csomagkezelő** összetevőt és az **Asztali fejlesztés c++ munkaterheléssel.**

1. Nyissa meg a Visual Studio alkalmazást, és `CMakeLists.txt` válassza a Fájl > a **Megnyitás > a CMake lehetőséget...** a létrehozott kódot tartalmazó mappában.

1. Az **Általános** eszköztáron keresse meg a Konfigurációk legördülő **menüt.** Válassza **a Konfiguráció kezelése lehetőséget** a projekt CMake beállításának hozzáadásához.

    ![Konfiguráció kezelése](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. A **CMake-beállítások ban**adjon hozzá egy új konfigurációt, és válassza **az x86-Debug** lehetőséget célként.

1. A **CMake parancs argumentumai területen**adja hozzá a következő sort:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Mentse a fájlt.

1. Váltson **x86-Debug** ra a **Konfigurációk** legördülő menüben. Néhány másodpercre van szüksége ahhoz, hogy a CMake létrehozza a gyorsítótárat. Tekintse meg a Kimenet ablakot a folyamat megtekintéséhez.

    ![CMake kimenet](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. A **Megoldáskezelőben**kattintson a `CMakeLists.txt` jobb gombbal a gyökérmappában lévőre, és válassza a helyi menü **Build parancsát** a létrehozott kódcsonk létrehozásához az eszköz SDK-jával.

1. Miután a létrehozás sikeres, a parancssorba, futtassa az alkalmazást, amely paraméterként megadja az IoT Hub-eszköz kapcsolati karakterláncát.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Ha többet szeretne tudni a CMake visual studio-ban való használatáról, olvassa el a [CMake-projekt készítése című témakört.](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects)

### <a name="macos"></a>macOS

A következő lépések bemutatják, hogyan építheti fel az eszközkódját a C SDK eszköz forráskódjával együtt a macOS rendszeren a CMake használatával:

1. Nyissa meg a terminálalkalmazást.

1. Az összes függőség telepítéséhez használja a [Homebrew](https://homebrew.sh) alkalmazást:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Ellenőrizze, hogy a [CMake](https://cmake.org/) legalább **2.8.12-es**verziójú-e:

    ```bash
    cmake --version
    ```

1. [Patch CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) a legújabb verzióelérhető.

1. A létrehozott kódot tartalmazó mappában klónozza az [Azure IoT C SDK-tárházat:](https://github.com/Azure/azure-iot-sdk-c)

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Hozzon létre `cmake` egy létrehozott kódot tartalmazó mappát tartalmazó mappát, és keresse meg azt a mappát.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a CMake használatához az eszköz SDK és a létrehozott kódcsonk létrehozásához:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Miután a build sikeres, futtassa az ioT Hub-eszköz kapcsolati karakterláncparaméterként megadott alkalmazás.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>A csontváz kód regenerálásával iteráljuk.

A kódgenerátor regenerálhatja a kódot, ha frissíti a DCM vagy interfész fájlokat. Feltéve, hogy már létrehozta az eszközkódot egy DCM-fájlból, hogy újragenerálja a kódot:

1. A DCM-fájlokkal rendelkező mappa megnyitása kor a **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát, írja be az **IoT Plug and Play parancsot,** és válassza **az Eszközkód csonk létrehozása lehetőséget.**

1. Válassza ki a frissített DCM-fájlt.

1. Válassza **a(z) {project name} kód újragenerálása lehetőséget.**

1. A kódgenerátor a beállított előző beállítást használja, és újragenerálja a kódot. Azonban nem írja felül azokat a fájlokat, `main.c` amelyek `{project_name}_impl.c`felhasználói kódot tartalmazhatnak, például és .

> [!NOTE]
> Ha frissíti az URN-azonosítót a kapcsolatfájlban, a rendszer új felületként kezeli. Amikor újra létrehozza a kódot, a kódgenerátor kódot hoz létre a kapcsolathoz, `{project_name}_impl.c` de nem írja felül az eredetit a fájlban.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Az Azure IoT Tools egy nyílt forráskódú projekt a GitHubon. Bármilyen probléma és szolgáltatáskérés esetén [létrehozhat egy problémát a GitHubon.](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)

## <a name="next-steps"></a>További lépések

Ebben az útmutató cikkben megtanulta, hogyan használhatja a Visual Studio és a Visual Studio-kódot az eszközalkalmazás megvalósításához a C csontváz kód létrehozásához. A javasolt következő lépés az Azure IoT Explorer eszköz [telepítésének és használatának megismerése.](./howto-install-iot-explorer.md)
