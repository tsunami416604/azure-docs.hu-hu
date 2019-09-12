---
title: Az Azure IoT Device Workbench használata IoT Plug and Play előnézeti eszközök létrehozásához | Microsoft Docs
description: Használja az Azure IoT Device Workbench bővítményt a Visual Studio Code-ban, hogy felgyorsítsa a IoT Plug and Play az eszközök modelljeinek létrehozását és az eszköz kódjának megvalósítását
author: liydu
ms.author: liydu
ms.date: 07/25/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b9f091caaaf85cd2b999db7781b14a497474f564
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881260"
---
# <a name="use-azure-iot-device-workbench-extension-in-visual-studio-code"></a>Az Azure IoT Device Workbench bővítmény használata a Visual Studio Code-ban

Az Azure IoT-eszköz Workbench Visual Studio Code bővítmény integrált környezetet biztosít az eszköz-képességi modellek (DCM) és felületek létrehozásához, a modell-Tárházak közzétételéhez, valamint a csontváz C kód létrehozásához az eszköz alkalmazásának megvalósításához.

Ez a cikk bemutatja, hogyan végezheti el a következőket:

- Eszköz kódjának és alkalmazási Projektének előállítása.
- Használja a generált kódot az eszköz projektben.
- Ismételje meg a csontváz kód újragenerálását.

Ha többet szeretne megtudni arról, hogyan használhatja a Device Workbench bővítményt a IoT [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)-eszközök fejlesztéséhez, lásd:.

## <a name="prerequisites"></a>Előfeltételek

Telepítse a [Visual Studio Code](https://code.visualstudio.com/)-ot.

A bővítmény a VS Code-ban való telepítéséhez kövesse az alábbi lépéseket.

1. A VS Code-ban válassza a **bővítmények** fület.
1. Keresse meg és telepítse az **Azure IoT Device Workbench** alkalmazást a piactéren.

## <a name="generate-device-code-and-project"></a>Eszköz kódjának és Projektének előállítása

A VS Code-ban a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása** lehetőséget a csontváz kód és a projekttípus konfigurálásához. Az alábbi lista részletesen ismerteti az egyes lépéseket:

- **A kód generálásához használandó DCM-fájl**. A csontváz-eszköz kódjának létrehozásához nyissa meg azt a mappát, amely az általa megvalósított DCM-és illesztőfelület-fájlokat tartalmazza. Ha a Code Generator nem talál olyan felületet, amelyet a DCM igényel, akkor szükség szerint letölti a modell adattárában.

- Az **eszköz kódjának nyelve** A Code Generator jelenleg csak az ANSI C-t támogatja.

- **Projekt neve**. A projekt neve a generált kód és más projektfájlok mappájának neveként szerepel. A mappa alapértelmezés szerint a DCM-fájl mellett van elhelyezve. Ha nem szeretné manuálisan átmásolni a generált kód mappát, amikor frissíti a DCM-et, és újragenerálja az eszköz kódját, tartsa meg a DCM-fájlt a Project mappával megegyező mappában.

- **Projekt típusa** A Code Generator egy projektfájlt is létrehoz, hogy integrálni tudja a kódot a saját projektbe vagy az eszköz SDK-projektbe. Jelenleg a támogatott projektek típusai a következők:

    - **CMAK-projekt**: egy olyan eszköz-projekthez, amely a [cmakt](https://cmake.org/) használja Build rendszerként. Ez a beállítás a `CMakeLists.txt` C kóddal megegyező mappában hozza létre a fájlt.
    - **MXChip IoT fejlesztői készlet-projekt**: egy [MXChip IoT fejlesztői készlet](https://aka.ms/iot-devkit) eszközön futó eszköz-projekthez. Ez a beállítás egy Arduino-projektet hoz létre, amelyet [vs Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) -ban vagy az Arduino IDE-ben használhat a IoT fejlesztői készlet-eszközön való létrehozásához és futtatásához.

- Az **Azure IoT való kapcsolódás módszere**. A generált fájlok emellett kódot is tartalmaznak, amelyekkel konfigurálhatja az eszközt az Azure IoT Hubhoz való kapcsolódáshoz. Dönthet úgy is, hogy közvetlenül csatlakozik az [Azure IoT hubhoz](https://docs.microsoft.com/azure/iot-hub) , vagy használja az [eszköz kiépítési szolgáltatását](https://docs.microsoft.com/azure/iot-dps).

    - **IoT hub eszköz kapcsolati karakterlánca**: Itt adhatja meg azt az eszköz-kapcsolati karakterláncot, amelyhez az alkalmazás közvetlenül csatlakozik IoT hubhoz.
    - **DPS szimmetrikus kulcson keresztül**: Itt adhatja meg az alkalmazás **hatókör-azonosítóját**, **regisztrációs azonosítóját**és **sas-kulcsát** , amelyek szükségesek a IoT hubhoz való kapcsolódáshoz vagy az IoT Central a DPS használatával.

A Code Generator megpróbálja használni a helyi mappában található DCM és Interface fájlokat. Ha az illesztőfelület fájljai nincsenek a helyi mappában, a Code Generator a nyilvános modell adattárában vagy a vállalati modell adattárában keresi a fájlokat. Az [általános interfészek fájljait](./concepts-common-interfaces.md) a nyilvános modell tárháza tárolja.

A kód létrehozása után a bővítmény megnyílik egy új VS Code-ablak a kóddal. Ha olyan létrehozott fájlt nyit meg, mint a **Main. c**, akkor előfordulhat, hogy az IntelliSense azt jelenti, hogy nem tudja megnyitni a c SDK-forrásfájlokat. Az IntelliSense és a kód helyes navigálásának engedélyezéséhez kövesse az alábbi lépéseket a C SDK-forrás felvételéhez:

1. A VS Code-ban a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort **, írjaC++be a parancsot, és válassza a C/: Szerkessze a konfigurációkat** (JSON) a **c_cpp_properties. JSON** fájl megnyitásához.

1. Adja hozzá az eszköz SDK elérési útját `includePath` a szakaszhoz:

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

Az eszköz kódjának és a C SDK-val együtt történő létrehozása a CMak használatával Linux-környezetben, például Ubuntu vagy Debian rendszerben:

1. Nyisson meg egy terminál alkalmazást.

1. Telepítse a **GCC**, a `cmake` **git**, a és az összes függőséget a `apt-get` parancs használatával:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Ellenőrizze, hogy a `cmake` verziója meghaladja-e a **2.8.12** , és hogy a **GCC** verziója meghaladja-e a **4.4.7**-t.

    ```sh
    cmake --version
    gcc --version
    ```

1. Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -tárház klónozása:

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Másolja a generált kódot tartalmazó mappát az eszköz SDK gyökérkönyvtárba.

1. A vs Code-ban nyissa meg a `CMakeLists.txt` fájlt az eszköz SDK gyökérmappa mappájába.

1. Adja hozzá a következő sort a `CMakeLists.txt` fájl végéhez, hogy az az SDK fordításakor tartalmazza az eszköz kód-helyettes mappáját:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Hozzon létre egy `cmake` nevű mappát az eszköz SDK gyökérkönyvtárában, és navigáljon a mappához.

    ```sh
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a CMak használatával az eszköz SDK és a generált kód létrehozásához:

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .
    ```

1. A létrehozás sikerességét követően futtassa az alkalmazást, és adja meg a IoT Hub eszköz kapcsolatok sztringjét paraméterként.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Az eszköz kódjának és a Windows-eszközök C SDK-val együtt történő létrehozásához a CMak és a VisualC++ Studio C/fordítóprogramok segítségével a parancssorban tekintse meg a [IoT Plug and Play](./quickstart-create-pnp-device.md)gyors útmutató című témakört. A következő lépések bemutatják, hogyan hozhatja létre az eszköz kódját és a C SDK-t a CMak-projektként a Visual Studióban.

1. A [Visual Studio 2019 (Community, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/) telepítése – ügyeljen arra, hogy a **NuGet csomagkezelő** összetevőjét és az **asztali fejlesztést C++**  is tartalmazza a számítási feladatok segítségével.

1. Nyissa meg a Visual studiót, és az **első lépések** oldalon válassza a **klónozott vagy a kijelentkezési kód**lehetőséget:

1. A **tárház helye**területen az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -tárház klónozása:

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    A művelet elvégzése több percet is igénybe vehet, és a folyamat a **Team Explorer** ablaktáblán látható.

1. Nyissa meg az **Azure-IOT-SDK-c** tárházat a **Team Explorerban**, válassza az **ágak**lehetőséget, keresse meg a **nyilvános előzetes** verzió ágat, és nézze meg.

    ![Nyilvános előzetes](media/howto-use-iot-device-workbench/vs-public-preview.png)

1. Másolja a generált kódot tartalmazó mappát az eszköz SDK gyökérkönyvtárba.

1. Nyissa `azure-iot-sdk-c` meg a mappát a vs.

1. Nyissa `CMakeLists.txt` meg a fájlt az eszköz SDK gyökérkönyvtárában.

1. Adja hozzá a következő sort a `CMakeLists.txt` fájl végéhez, hogy az az SDK fordításakor tartalmazza az eszköz kód-helyettes mappáját:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Az **általános** eszköztáron keresse meg a **konfigurációk** legördülő menüt. Válassza a **konfiguráció kezelése** lehetőséget a projekthez tartozó CMAK-beállítás hozzáadásához.

    ![Konfiguráció kezelése](media/howto-use-iot-device-workbench/vs-manage-config.png)

1. A **CMAK beállításainál**adjon hozzá egy új konfigurációt, és válassza az **x64-Release** elemet célként.

1. A **CMAK parancs argumentumai**területen adja hozzá a következő sort:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Mentse a fájlt.

1. A **megoldáskezelő**kattintson `CMakeLists.txt` a jobb gombbal a gyökérkönyvtárban található elemre, majd a helyi menüből válassza a **Létrehozás** lehetőséget az eszköz SDK és a generált kód létrehozásához.

1. A létrehozás sikerességét követően futtassa az alkalmazást a parancssorban, és adja meg az IoT Hub eszköz-kapcsolatok karakterláncát paraméterként.

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Ha többet szeretne megtudni a CMak a Visual Studióban való használatáról, olvassa el a következő témakört: a [CMAK-projekt létrehozása](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

A következő lépések bemutatják, hogyan hozhatja létre az eszköz kódját az C SDK-val macOS rendszeren a CMak használatával:

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

1. Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) -tárház klónozása:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Másolja a generált kódot tartalmazó mappát az eszköz SDK gyökérkönyvtárba.

1. A vs Code-ban nyissa meg a `CMakeLists.txt` fájlt az eszköz SDK gyökérmappa mappájába.

1. Adja hozzá a következő sort a `CMakeLists.txt` fájl végéhez, hogy az az SDK fordításakor tartalmazza az eszköz kód-helyettes mappáját:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Hozzon létre egy `cmake` nevű mappát az eszköz SDK gyökérkönyvtárában, és navigáljon a mappához.

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
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iteráció a csontváz kódjának újragenerálásával

A kód generátora újragenerálhatja a kódot, ha módosítja a DCM-vagy az illesztőfelület-fájlokat. Feltételezve, hogy már létrehozta az eszköz kódját egy DCM-fájlból, a kód újragenerálásához:

1. Ha a DCM-fájlok mappája meg van nyitva, a **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza az **eszköz kódjának előállítása**lehetőséget.

1. Válassza ki a frissített DCM-fájlt.

1. Válassza **a {Project Name} kód újbóli előállítása**lehetőséget.

1. A Code Generator a korábban konfigurált beállításokat használja, és újragenerálja a kódot. Azonban nem írja felül azokat a fájlokat, amelyek felhasználói kódokat `main.c` (például és `{project_name}_impl.c`) tartalmazhatnak.

> [!NOTE]
> Ha frissíti az URN-azonosítót a csatoló fájljában, azt a rendszer új csatolóként kezeli. A kód újbóli létrehozásakor a Code Generator programkódot hoz létre a csatolóhoz, de nem írja felül az eredetit `{project_name}_impl.c` a fájlban.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Az Azure IoT Device Workbench bővítmény egy nyílt forráskódú projekt a GitHubon. A problémák és a szolgáltatások iránti kérelmek esetében [probléma hozható létre a githubon](https://github.com/microsoft/vscode-iot-workbench/issues).

## <a name="next-steps"></a>További lépések

Ebben a útmutatóban megtanulta, hogyan hozhat létre DCM-és csatoló-fájlokat az Azure IoT Device Workbench használatával. Azt is megtanulta, hogyan hozhat létre csontváz C-kódot az eszköz alkalmazásának megvalósításához. A következő lépés azt ismerteti, hogyan [telepítheti és használhatja az Azure IoT Explorer](./howto-install-iot-explorer.md) eszközt.
