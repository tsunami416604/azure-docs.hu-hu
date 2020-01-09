---
title: A Visual Studio és a Visual Studio Code használata a IoT-Plug and Play előnézeti eszközök létrehozásához | Microsoft Docs
description: A Visual Studio és a Visual Studio Code használatával felgyorsíthatja az IoT Plug and Play az eszközök modelljeinek készítését és az eszköz kódjának megvalósítását.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b4325aa6f379dc0b281d06cb593c28448698c71b
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531326"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>A Visual Studio és a Visual Studio Code használata IoT Plug and Play-eszközök létrehozásához

A Visual Studio Code-hoz készült Azure IoT-eszközök integrált környezetet biztosítanak az eszköz-képességi modellek (DCM) és felületek létrehozásához, a modell-Tárházak közzétételéhez, valamint a csontváz C kód létrehozásához az eszköz alkalmazásának megvalósításához.

Ez a cikk a következőkhöz nyújt útmutatást:

- Eszköz kódjának és alkalmazási Projektének előállítása.
- Használja a generált kódot az eszköz projektben.
- Ismételje meg a csontváz kód újragenerálását.

Ha többet szeretne megtudni a VS Code IoT-eszközök fejlesztéséről, tekintse meg a következőt: [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench).

## <a name="prerequisites"></a>Előfeltételek

A [Visual Studio Code](https://code.visualstudio.com/) telepítése.

Az alábbi lépésekkel telepítheti a kiterjesztési csomagot a VS Code-ban.

1. A VS Code-ban válassza a **bővítmények** fület.
1. Keresse meg és telepítse az **Azure IoT-eszközöket** a piactéren.

## <a name="generate-device-code-and-project"></a>Eszköz kódjának és Projektének előállítása

A VS Code-ban a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása** lehetőséget a csontváz kód és a projekttípus konfigurálásához. Az alábbi lista részletesen ismerteti az egyes lépéseket:

- **A kód generálásához használandó DCM-fájl**. A csontváz-eszköz kódjának létrehozásához nyissa meg azt a mappát, amely az általa megvalósított DCM-és illesztőfelület-fájlokat tartalmazza. Ha a Code Generator nem talál olyan felületet, amelyet a DCM igényel, akkor szükség szerint letölti a modell adattárában.

- Az **eszköz kódjának nyelve** A Code Generator jelenleg csak az ANSI C-t támogatja.

- **Projekt neve**. A projekt neve a generált kód és más projektfájlok mappájának neveként szerepel. A mappa alapértelmezés szerint a DCM-fájl mellett van elhelyezve. Ha nem szeretné manuálisan átmásolni a generált kód mappát, amikor frissíti a DCM-et, és újragenerálja az eszköz kódját, tartsa meg a DCM-fájlt a Project mappával megegyező mappában.

- Az **Azure IoT való kapcsolódás módszere**. A generált fájlok emellett kódot is tartalmaznak, amelyekkel konfigurálhatja az eszközt az Azure IoT Hubhoz való kapcsolódáshoz. Dönthet úgy is, hogy közvetlenül csatlakozik az [Azure IoT hubhoz](https://docs.microsoft.com/azure/iot-hub) , vagy használja az [eszköz kiépítési szolgáltatását](https://docs.microsoft.com/azure/iot-dps).

    - **IoT hub eszköz kapcsolati karakterlánca**: Itt adhatja meg azt az eszköz-kapcsolati karakterláncot, amelyhez az alkalmazás közvetlenül csatlakozik IoT hubhoz.
    - **DPS szimmetrikus kulcson keresztül**: Itt adhatja meg az eszköz alkalmazásának **hatókörét**, a **szimmetrikus kulcs** és az **eszköz azonosítóját** , amely szükséges ahhoz, hogy a DPS használatával IoT hub vagy IoT Centralhoz kapcsolódjon.

- **Projekt típusa** A Code Generator egy CMak-vagy Arduino-projektet is létrehoz. Jelenleg a támogatott projektek típusai a következők:

    - A **Windows rendszerhez készült CMAK-projekt**: olyan eszköz-projekthez, amely a Windows rendszerű Build-rendszerként a [cmakt](https://cmake.org/) használja. Ez a beállítás a C kóddal megegyező mappában hozza létre a `CMakeLists.txt` az eszköz SDK-konfigurációjáról.
    - **A Linux**rendszerhez készült CMAK-projekt: egy olyan eszköz-projekthez, amely a [Cmakt](https://cmake.org/) használja Linux-alapú Build rendszerként. Ez a beállítás a C kóddal megegyező mappában hozza létre a `CMakeLists.txt` az eszköz SDK-konfigurációjáról.
    - **MXChip IoT fejlesztői készlet-projekt**: egy [MXChip IoT fejlesztői készlet](https://aka.ms/iot-devkit) eszközön futó eszköz-projekthez. Ez a beállítás egy Arduino-projektet hoz létre, amelyet [vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) -ban vagy az Arduino IDE-ben használhat a IoT fejlesztői készlet-eszközön való létrehozásához és futtatásához.

- **Eszköz SDK-típusa** Ha a CMak a projekt típusaként lehetőséget választja, ez a lépés azt adja meg, hogy a generált kód hogyan fogja tartalmazni a `CMakeLists.txt`Azure IoT C Device SDK-t:

    - **Forráskódon keresztül**: a generált kód az [eszköz SDK forráskódján](https://github.com/Azure/azure-iot-sdk-c) alapul, amely tartalmazza a-t, és azzal együtt épít. Ez akkor ajánlott, ha testre szabta az eszköz SDK-forráskódját.
    - **Vcpkg**-on keresztül: a generált kód az [eszköz SDK-Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) támaszkodik, amely tartalmazza a-t, és együtt is épít vele. Ez a Windows, Linux vagy macOS rendszerű eszközök ajánlott módja.

    > [!NOTE]
    > Az Azure IoT C Device SDK Vcpkg macOS-támogatása folyamatban van.

A Code Generator megpróbálja használni a helyi mappában található DCM és Interface fájlokat. Ha az illesztőfelület fájljai nincsenek a helyi mappában, a Code Generator a nyilvános modell adattárában vagy a vállalati modell adattárában keresi a fájlokat. Az [általános interfészek fájljait](./concepts-common-interfaces.md) a nyilvános modell tárháza tárolja.

A kód létrehozása után a bővítmény megnyílik egy új VS Code-ablak a kóddal. Ha olyan létrehozott fájlt nyit meg, mint a **Main. c**, akkor előfordulhat, hogy az IntelliSense azt jelenti, hogy nem tudja megnyitni a c SDK-forrásfájlokat. Az IntelliSense és a kód helyes navigálásának engedélyezéséhez kövesse az alábbi lépéseket a C SDK-forrás felvételéhez:

1. A vs Code-ban a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be és válassza a **C/C++: Edit configurations (JSON)** parancsot a **c_cpp_properties. JSON** fájl megnyitásához.

1. Adja hozzá az eszköz SDK elérési útját a `includePath` szakaszban:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Mentse a fájlt.

## <a name="use-the-generated-code"></a>A generált kód használata

Az alábbi utasítások azt ismertetik, hogyan használható a létrehozott kód a saját eszközök projektben különböző fejlesztői gépi platformokon. Az utasítások feltételezik, hogy IoT Hub eszközt használ a generált kóddal:

### <a name="linux"></a>Linux

Az eszköz kódjának és az eszköz C SDK-Vcpkg együtt történő létrehozása a CMak használatával Linux-környezetben, például Ubuntu vagy Debian rendszerben:

1. Nyisson meg egy terminál alkalmazást.

1. Telepítse a **GCC**, a **Git**, a `cmake`és az összes függőséget az `apt-get` parancs használatával:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ellenőrizze, hogy a `cmake` verziója meghaladja-e a **2.8.12** , és a **GCC** verziója meghaladja a **4.4.7**-t.

    ```bash
    cmake --version
    gcc --version
    ```

1. A Vcpkg telepítése:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Ezután a felhasználó szintű [integráció](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)összekapcsolása érdekében futtassa a következőt:

    ```bash
    ./vcpkg integrate install
    ```

1. Az Azure IoT C Device SDK Vcpkg telepítése:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Hozzon létre egy `cmake` alkönyvtárat a mappában, amely tartalmazza a generált kódot, és navigáljon a következő mappába:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a CMak használatával az eszköz SDK és a generált kód létrehozásához:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. A létrehozás sikerességét követően futtassa az alkalmazást, és adja meg a IoT Hub eszköz kapcsolatok sztringjét paraméterként.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Az eszköz kódjának és a Windows-eszközök C SDK-val együtt történő létrehozásához a CMak és a VisualC++ Studio C/fordítóprogramok segítségével a parancssorban tekintse meg a [IoT Plug and Play](./quickstart-create-pnp-device-windows.md)gyors útmutató című témakört. Az alábbi lépések bemutatják, hogyan hozhatja létre az eszköz kódját a C SDK-Vcpkg a Visual Studióban a CMak-projektként.

1. Kövesse a rövid útmutató lépéseit, és telepítse a C Azure IoT Device [SDK-t](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) a Vcpkg-on keresztül.

1. A [Visual Studio 2019 (Community, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/) telepítése – ügyeljen arra, hogy a **NuGet csomagkezelő** összetevőjét és az **asztali fejlesztést C++**  is tartalmazza a számítási feladatok segítségével.

1. Nyissa meg a Visual studiót, válassza a **fájl > megnyitás > CMAK..** . lehetőséget, hogy megnyissa a `CMakeLists.txt` a mappában, amely generált kódot tartalmaz.

1. Az **általános** eszköztáron keresse meg a **konfigurációk** legördülő menüt. Válassza a **konfiguráció kezelése** lehetőséget a projekthez tartozó CMAK-beállítás hozzáadásához.

    ![Konfiguráció kezelése](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. A **CMAK beállításainál**adjon hozzá egy új konfigurációt, és válassza az **x86-debug** lehetőséget célként.

1. A **CMAK parancs argumentumai**területen adja hozzá a következő sort:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Mentse a fájlt.

1. Váltson az **x86-debug** értékre a **konfigurációk** legördülő menüben. Szükség van néhány másodpercre, hogy a CMak létrehozza a gyorsítótárat. Tekintse meg a kimenet ablakot a folyamat előrehaladásának megtekintéséhez.

    ![A CMak kimenete](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. A **megoldáskezelő**kattintson a jobb gombbal a gyökérkönyvtárban található `CMakeLists.txt`ra, majd válassza a **Létrehozás** lehetőséget a helyi menüből a generált kód kiépítéséhez az eszköz SDK-val.

1. A létrehozás sikerességét követően futtassa az alkalmazást a parancssorban, és adja meg az IoT Hub eszköz-kapcsolatok karakterláncát paraméterként.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Ha többet szeretne megtudni a CMak a Visual Studióban való használatáról, olvassa el a következő témakört: a [CMAK-projekt létrehozása](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

A következő lépések bemutatják, hogyan hozhatja létre az eszköz kódját a macOS-hez készült C SDK-forráskód használatával a CMak segítségével:

1. Nyissa meg a Terminal alkalmazást.

1. Használja a [Homebrew](https://homebrew.sh) -t az összes függőség telepítéséhez:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Győződjön meg arról, hogy a [CMAK](https://cmake.org/) legalább verziója **2.8.12**:

    ```bash
    cmake --version
    ```

1. Az elérhető legújabb verzióra a [fürt javítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) .

1. A generált kódot tartalmazó mappában az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -tárház klónozása:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Hozzon létre egy `cmake` nevű mappát a generált kódot tartalmazó mappában, és navigáljon a mappához.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a CMak használatával az eszköz SDK és a generált kód létrehozásához:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. A létrehozás sikerességét követően futtassa az alkalmazást, és adja meg a IoT Hub eszköz kapcsolatok sztringjét paraméterként.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iteráció a csontváz kódjának újragenerálásával

A kód generátora újragenerálhatja a kódot, ha módosítja a DCM-vagy az illesztőfelület-fájlokat. Feltételezve, hogy már létrehozta az eszköz kódját egy DCM-fájlból, a kód újragenerálásához:

1. Ha a DCM-fájlok mappája meg van nyitva, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

1. Válassza ki a frissített DCM-fájlt.

1. Válassza **a {Project Name} kód újbóli előállítása**lehetőséget.

1. A Code Generator a korábban konfigurált beállításokat használja, és újragenerálja a kódot. Azonban nem írja felül azokat a fájlokat, amelyek felhasználói kódokat tartalmazhatnak, például `main.c` és `{project_name}_impl.c`.

> [!NOTE]
> Ha frissíti az URN-azonosítót a csatoló fájljában, azt a rendszer új csatolóként kezeli. A kód újbóli létrehozásakor a Code Generator programkódot hoz létre a csatolóhoz, de nem írja felül az eredetit a `{project_name}_impl.c` fájlban.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Az Azure IoT Tools egy nyílt forráskódú projekt a GitHubon. A problémák és a szolgáltatások iránti kérelmek esetében [probléma hozható létre a githubon](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan használhatja a Visual studiót és a Visual Studio Code-ot a csontváz C kód létrehozásához az eszköz alkalmazásának megvalósításához. A következő lépés azt ismerteti, hogyan [telepítheti és használhatja az Azure IoT Explorer](./howto-install-iot-explorer.md) eszközt.
