---
title: IoT csatlakoztatása Plug and Play előnézeti eszközről az Azure IoT Centralba | Microsoft Docs
description: Eszköz-képesség modell használata az eszköz kódjának létrehozásához. Ezután futtassa az eszköz kódját, tekintse meg az eszköz csatlakozik a IoT Central alkalmazáshoz, és használja az automatikusan létrehozott nézeteket.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: bc7858aeceea7cbd35bc2d834ddfb4fb5d656321
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174831"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Gyors útmutató: eszköz-képesség modell használata IoT-Plug and Play eszköz létrehozásához és a IoT Central-alkalmazáshoz való csatlakoztatásához

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Az _eszköz képességi modellje_ (DCM) ismerteti a [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) eszköz képességeit. IoT Central a DCM használatával létrehozhat egy eszköz sablont és vizualizációkat az eszközhöz, amikor az eszköz az első alkalommal csatlakozik. Ez a rövid útmutató a következőket mutatja be:

* A Visual Studio Code használatával hozzon létre egy IoT Plug and Play eszközt DCM használatával.
* Futtassa az eszköz kódját a Windowsban, és tekintse meg a IoT Central alkalmazáshoz való kapcsolódást ismertető témakört.
* Az eszköz által küldött szimulált telemetria megtekintése.

## <a name="prerequisites"></a>Előfeltételek

Fejezze be az [Azure IoT Central-alkalmazás létrehozása (előzetes verziójú funkciók)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) című rövid útmutatót egy IoT Central alkalmazás létrehozásához az **előzetes verziójú alkalmazás** sablonjának használatával.

A rövid útmutató elvégzéséhez telepítenie kell a következő szoftvereket a helyi gépre:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a **NuGet csomagkezelő** összetevőjét és az asztali fejlesztést a Visual Studio telepítésekor számítási **feladatokkal C++**  végezze el.
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

### <a name="get-azure-iot-device-sdk-for-c"></a>A C Azure IoT Device SDK beszerzése

Hozzon létre egy fejlesztési környezetet, amellyel létrehozhatja az Azure IoT C Device SDK-t.

1. Nyisson meg egy parancssort. A következő parancs végrehajtásával klónozza az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-adattárat:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Ez a művelet várhatóan több percig is eltarthat.

1. Hozzon létre egy `central_app` mappát a tárház helyi klónjának gyökerében. Ezt a mappát kell használnia az eszköz modell fájljaihoz és az eszköz kódjához.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir central_app
    ```

## <a name="generate-device-key"></a>Eszköz kulcsának előállítása

Egy eszköz IoT Central alkalmazáshoz való csatlakoztatásához szükség van egy eszköz kulcsára. Eszköz kulcsának létrehozása:

1. Jelentkezzen be az előző rövid útmutatóban létrehozott IoT Central alkalmazásba.

1. Lépjen az **Adminisztráció** lapra, és válassza az **eszköz csatlakoztatása**lehetőséget.

1. Jegyezze fel a hatókör- **azonosítót** és az **elsődleges kulcsot**. A rövid útmutató későbbi szakaszában fogja használni ezeket az értékeket.

    ![Eszköz csatlakoztatása](./media/quick-create-pnp-device-pnp/device-connection.png)

1. Nyisson meg egy parancssort, és futtassa a következő parancsot egy eszköz kulcsának létrehozásához:

    ```cmd/sh
    dps-keygen  -di:mxchip-01 -mk:{Primary Key from previous step}
    ```

    Jegyezze fel a generált _eszköz kulcsát_, ezt az értéket kell használnia egy későbbi lépésben ebben a rövid útmutatóban.

## <a name="download-your-model"></a>A modell letöltése

Ebben a rövid útmutatóban egy MxChip IoT fejlesztői készlet-eszköz nyilvános DCM-et használ. A kód futtatásához nincs szükség tényleges fejlesztői készlet-eszközre, ebben a rövid útmutatóban lefordítja a kódot Windows rendszeren való futtatásra.

1. Nyissa meg `azure-iot-sdk-c\central_app` mappát a VS Code-ban.

1. A **CTRL + SHIFT + P** billentyűkombinációval nyissa meg a parancssort, írja be a **IoT Plug and Play**, majd válassza a **modell-adattár megnyitása**lehetőséget. Válassza a **nyilvános tárház**lehetőséget. A VS Code a nyilvános modell tárházában lévő DCMs listáját jeleníti meg.

1. Válassza ki a **MXChip IoT fejlesztői készlet** DCM azonosítót `urn:mxchip:mxchip_iot_devkit:1` azonosítóval. Ezután válassza a **Letöltés**lehetőséget. Most már rendelkezik egy másolattal a DCM-ről a `central_app` mappában.

![A Model adattár és a DCM](./media/quick-create-pnp-device-pnp/public-repository.png)

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

1. A projekt típusaként válassza a **CMAK-projekt** lehetőséget. Ne válassza a **MXChip IoT fejlesztői készlet-projekt**lehetőséget, ha valódi fejlesztői készlet-eszközzel rendelkezik.

1. Válassza a **DPS (eszköz kiépítési szolgáltatás) szimmetrikus kulcsát** a csatlakoztatási módszerként.

1. A VS Code egy új ablakot nyit meg az `devkit_device` mappában létrehozott kódlap-fájlokkal.

![Generált eszköz kódja](./media/quick-create-pnp-device-pnp/generated-code.png)

A kapcsolódási adatok hozzáadása a generált eszköz kódjához:

1. A generált C kódot tartalmazó VS Code ablakban. Nyissa meg az `main.c` fájlt.

1. Cserélje le a `[DPS Id Scope]` értéket a korábban jegyzett **hatókör-azonosítóra** .

1. Cserélje le a `[DPS symmetric key]` értéket az előző lépésben létrehozott eszköz kulcsára.

1. Cserélje le a `[device registration Id]` elemet a `mxchip-01` kérdésre.

1. Mentse a módosításokat.

## <a name="build-the-code"></a>A kód létrehozása

Az eszköz SDK használatával hozza létre a generált eszköz kódját. Az Ön által létrehozott alkalmazás szimulál egy **MXChip IoT fejlesztői készlet** eszközt, és csatlakozik a IoT Central alkalmazáshoz. Az alkalmazás elküldi a telemetria és a tulajdonságokat, és fogadja a parancsokat.

1. A VS Code-ban nyissa meg a `CMakeLists.txt` fájlt a `azure-iot-sdk-c` mappában. Győződjön meg arról, hogy a `CMakeLists.txt` fájlt a `azure-iot-sdk-c` mappában nyitja meg, nem pedig a `devkit_device` mappában.

1. Adja hozzá az alábbi sort a `CMakeLists.txt` fájl alján, hogy tartalmazza az eszköz kód-helyettes mappáját a fordításakor:

    ```txt
    add_subdirectory(central_app/devkit_device)
    ```

1. Hozzon létre egy `cmake` mappát a `azure-iot-sdk-c` mappában, majd a parancssorban navigáljon a mappához:

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. A következő parancsok futtatásával hozza létre az eszköz SDK-t és a generált kódot:

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. A létrehozás sikeres befejezése után a parancssorban futtassa az alkalmazást:

    ```cmd\sh
    .\central_app\devkit_device\Release\devkit_device.exe
    ```

1. Az eszköz megkezdi az adatok küldését a IoT Central alkalmazásba.

## <a name="view-the-device"></a>Az eszköz megtekintése

Miután az eszköz kódja csatlakozik a IoT Centralhoz, megtekintheti az általa küldött tulajdonságokat és telemetria:

1. A IoT Central alkalmazásban nyissa meg az **eszközök** lapot, és válassza ki a **mxchip-01** eszközt. Az eszköz automatikusan hozzá lett adva a csatlakoztatott eszköz kódjához:

    ![Áttekintő lap](./media/quick-create-pnp-device-pnp/overview-page.png)

    Néhány perc elteltével a lap az eszköz által küldött telemetria diagramjait jeleníti meg.

1. A **Névjegy** lapon megtekintheti az eszköz által eljuttatott tulajdonságértékek értékét.

1. Válassza a **parancsok** lapot, és hívja meg a parancsokat az eszközön. Megtekintheti az eszközt, amely az eszköz kódját futtató parancssorban válaszol.

1. Nyissa meg az **eszközbeállítások** lapot, és tekintse meg a DCM-ből a nyilvános tárházban létrehozott IoT Central sablont:

    ![Eszközök sablonjai lap](./media/quick-create-pnp-device-pnp/device-template.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz, amely egy DCM-ből jött létre a nyilvános modell adattárában.

Ha többet szeretne megtudni a DCMs és a saját modelljeinek létrehozásáról, folytassa a következő útmutatóval:

> [!div class="nextstepaction"]
> [Eszköz sablonjának beállítása és kezelése](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
