---
title: IoT Plug and Play eszköz csatlakoztatása IoT Centralhoz | Microsoft Docs
description: Az eszköz fejlesztőinek tekintse meg, hogyan hozhat létre és tesztelheti a IoT Plug and Play eszközt, amely a IoT Centralhoz csatlakozik.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997234"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>A Visual Studio Code használatával hozzon létre egy IoT Plug and Play-eszközt, amely a következőhöz csatlakozik IoT Central

Ez a útmutató a következőket mutatja be:

* Kezelőként egy valós eszköz hozzáadásához és konfigurálásához az Azure IoT Central alkalmazásban.

* Eszköz-fejlesztőként a Visual Studio Code használatával hozzon létre egy [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) eszközt, amely csatlakozik a IoT Central alkalmazáshoz.

Az [eszköz képességeinek modelljét](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a IoT Centralhoz csatlakozó eszköz definiálására használhatja. Ebben az útmutatóban egy olyan modellt használ, amely meghatározza a környezeti érzékelő eszközét.

Az eszköz fejlesztői a modellt használják az eszközhöz tartozó ügyfél kódjának létrehozásához, és a készítő a modell használatával [hozza létre az eszköz sablonját](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a IoT Central. A modell szerződésként működik az eszköz és a IoT Central alkalmazás között.

Az útmutatóban szereplő szakasz ismerteti, hogyan kell létrehozni a generált kódot, feltételezi, hogy a Windowst használja.

Ebből az útmutatóból a következőket tanulhatja meg:

* Eszköz-képesség modell importálása IoT Centralban található eszköz-sablonba
* Irányítópult hozzáadása a sablonhoz, amely az eszköz telemetria jeleníti meg
* Valódi eszköz hozzáadása a sablonból
* Eszköz-képesség modell importálása a Visual Studio Code-ba
* C-eszköz ügyfélalkalmazás létrehozása a modellből
* A C-eszköz ügyfélalkalmazás felépítése és csatlakoztatása IoT Central

## <a name="prerequisites"></a>Előfeltételek

Az útmutatóban szereplő eszköz kódjának teszteléséhez az előnézeti alkalmazás sablonjában létrehozott IoT Central alkalmazásra van szükség. Ha még nem rendelkezik a használni kívánt IoT Central alkalmazással, fejezze be az [Azure IoT Central-alkalmazás létrehozása (előzetes verziójú funkciók)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)című rövid útmutatót:

Az útmutatóban szereplő eszköz-képesség modellel való együttműködéshez a következőkre lesz szüksége:

* [Visual Studio Code](https://code.visualstudio.com/download): A Visual Studio Code több platformon is elérhető
* Azure IoT Device Workbench bővítmény a Visual Studio Code-hoz. Az alábbi lépésekkel telepítheti az Azure IoT Device Workbench bővítményt a VS Code-ban:

    1. A VS Code-ban válassza a kiterjesztések lapot.
    1. Keressen rá az **Azure IoT Device Workbench**kifejezésre.
    1. Válassza az **Install** (Telepítés) lehetőséget.

    > [!NOTE]
    > A bővítményben lévő Code Generator aktuális verziója nem támogatja a **Geopoint** és a **vektoros** sémák típusát, illetve a **gyorsítás**, a **sebesség**és a **hely** szemantikai típusát. Ezek a séma-és szemantikai típusok IoT Central támogatottak.

    > [!NOTE]
    > A IoT Central használatához az eszköz képességeinek modellje csak ugyanabban a fájlban definiált összes illesztőfelülettel rendelkezhet.

A generált C kód a Windowsban való létrehozásához az útmutatóban a következőkre lesz szüksége:

* [Visual Studio (közösségi, szakmai vagy vállalati)](https://visualstudio.microsoft.com/downloads/) – ügyeljen arra, hogy a **NuGet csomagkezelő** összetevőjét és az **asztali C++ fejlesztést** a Visual Studio telepítésekor számítási feladatokkal végezze el.
* [Git](https://git-scm.com/download)
* [CMAK](https://cmake.org/download/) – a **CMAK**telepítésekor válassza a **CMAK hozzáadása**a rendszerútvonalhoz lehetőséget.
* Az Azure IoT C SDK helyi példánya:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Ha követni szeretné az eszköz első lépéseit a következő útmutató végén, azt is telepítenie kell:

* [Node.js](https://nodejs.org)
* A [DPS-keygen eszköz](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>Eszköz sablonjának létrehozása

Hozzon létre egy **pnp_app** nevű mappát az **Azure-IOT-SDK-c** mappában, amely tartalmazza a KLÓNOZOTT Azure IOT c SDK-t. Töltse le a [EnvironmentalSensor. capabilitymodel. JSON](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) eszköz képességeinek modelljét a githubról, és mentse a fájlt a **pnp_app** mappába. Cserélje le a nevét a `<YOUR_COMPANY_NAME_HERE>` két példányra. Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja. Jegyezze fel a `"@id"` mező teljes értékét, amely egyedileg azonosítja az eszköz képességeinek modelljét, később szüksége lesz rá. Ez a modell két felületet tartalmaz:

* A **DeviceManagement** általános felülete.
* a **EnvironmentalSensor** egyéni felülete.

Eszköz sablon létrehozása a környezeti érzékelő eszközhöz a IoT Centralban:

1. Lépjen az **eszközök sablonjai** lapra, és válassza az **+ új**lehetőséget. Ezután válassza az **Egyéni**lehetőséget.

1. Adja meg a **környezeti érzékelőt** az eszköz sablonjának neveként.

1. Válassza az **importálási képesség modell**lehetőséget. Ezután keresse meg a létrehozott **EnvironmentalSensor. capabilitymodel. JSON** fájlt, és kattintson a **Megnyitás**gombra. A két felület, az **eszköz adatai** és a **környezeti érzékelő**a **környezeti érzékelő képesség modellben**jelenik meg.

1. Válassza ki a **nézetek** elemet, majd jelenítse meg **az eszközt**.

1. Az irányítópultra felvehető mezők listájában válassza ki a két **telemetria** értéket, a **páratartalom** és a **hőmérséklet** elemet, majd válassza a **összevonás**lehetőséget. Ezután válassza a **Mentés** lehetőséget.

Most már rendelkezik egy olyan sablonnal, amely a **környezeti érzékelő** modellt használja, és amely egy egyszerű irányítópulttal jeleníti meg a telemetria az eszközről.

## <a name="publish-the-template-and-add-a-real-device"></a>A sablon közzététele és a valódi eszköz hozzáadása

A sablon közzétételéhez és a valódi eszköz hozzáadásához nyissa meg az **eszközök sablonjai** lapot, és válassza ki a **környezeti érzékelő** eszköz sablonját. Válassza a **Közzététel**lehetőséget, tekintse át az adatokat, majd válassza a **Közzététel**lehetőséget.

Az ügyfélalkalmazás csatlakoztatása előtt hozzá kell adnia egy eszközt az **eszközök** lapon, és le kell kérnie a kapcsolati adatait:

1. Lépjen az **eszközök** lapra, és válassza a **környezeti érzékelő**elemet. Az **eszközök** lap segítségével kezelheti az alkalmazáshoz csatlakoztatható eszközöket.

1. Valódi eszköz hozzáadásához válassza az **+ új**lehetőséget, módosítsa az eszköz azonosítóját a **sensor01**értékre, majd válassza a **Létrehozás**lehetőséget. Győződjön meg arról , hogy a szimulált érték **ki van kapcsolva**.

1. Az eszközök listájában válassza ki a **környezeti érzékelő** eszközét. Ezután válassza a **kapcsolat**lehetőséget.

1. Jegyezze fel a hatókör- **azonosítót**, az **eszköz azonosítóját**és az **elsődleges kulcsot**. Ezután válassza a **Bezárás**lehetőséget.

## <a name="generate-a-device-client-application"></a>Eszköz ügyfélalkalmazás létrehozása

A Visual Studio Code használatával egy csontváz-eszköz ügyfélalkalmazás hozható elő az eszköz képességeinek modelljéből:

1. Indítsa el a Visual Studio Code-ot, és nyissa meg a **pnp_app** mappát.

1. A Command paletta megnyitásához használja a **CTRL + SHIFT + P** billentyűkombinációt.

1. Adja meg **Plug and Play** , majd válassza az **eszköz kódjának**előállítása lehetőséget.

1. Válassza ki a **EnvironmentalSensor. capabilitymodel. JSON** eszköz képességi modell fájlját.

1. Adja meg a **sensor_app** nevet a projekt neveként.

1. Válassza az **ANSI C** nyelvet.

1. Célként válassza a **CMAK-projekt** lehetőséget.

1. Válassza a **DPS (eszköz kiépítési szolgáltatás) szimmetrikus kulcsát** a csatlakoztatási módszerként.

A Visual Studio Code egy új ablakot nyit meg a generált C kóddal a **sensor_app** mappában.

## <a name="add-connection-details-to-the-device-client"></a>Kapcsolat adatainak hozzáadása az eszköz ügyfeléhez

A kapcsolódási adatoknak az eszköz ügyfeléhez való hozzáadásához nyissa meg a **Main. c** mappát a generált kódot tartalmazó mappában:

1. Cserélje `[DPS Id Scope]` le a értéket a **Hatókör-azonosító** értékre, amelyet korábban jegyzett készített.

1. Cserélje `[DPS symmetric key]` le a értéket a korábban létrehozott **elsődleges kulcs** értékére.

1. A `[device registration Id]` helyére írja be az **eszköz azonosítójának** értékét, amelyet korábban jegyzett tett.

1. Mentse a módosításokat.

## <a name="build-and-run-the-client"></a>Az ügyfél létrehozása és futtatása

Az ügyfél létrehozásához és futtatásához testre kell szabnia az Azure IoT C SDK buildjét:

1. Nyissa meg a **CMakeLists. txt** fájlt az **Azure-IOT-SDK-c** mappa gyökerében.

1. Adja hozzá a következő sort a fájl végén, hogy tartalmazza az új ügyfélalkalmazás a buildben:

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. Mentse a módosításokat.

1. Nyisson meg egy parancssort, és navigáljon az **Azure-IOT-SDK-c** mappához.

1. Az alkalmazás létrehozásához futtassa a következő parancsokat:

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Az alkalmazás futtatásához futtassa a következő parancsot ugyanabból a parancssorból:

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>A telemetria megtekintése

Néhány perc elteltével megtekintheti a telemetria az eszköz irányítópultján az IoT Central alkalmazásban.

## <a name="connect-device-first"></a>Eszköz csatlakoztatása – első

A IoT Plug and Play eszköz csatlakoztatása az eszközről az első kapcsolaton keresztül, a [kapcsolat](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)című témakörben leírtak szerint. A felderítési folyamat a következő sorrendet követi:

1. Társítja az eszköz sablonját, ha az IoT Central alkalmazásban már közzé van téve.

1. Beolvassa a képesség modellt a közzétett és tanúsított képességi modellek [nyilvános tárházában](https://aka.ms/ACFI) .

A Visual Studio Code és a fentiekben hivatkozott, az **eszköz kódjának létrehozása** parancs használatával az alábbi lépéseket követve csatlakozhat az eszközhöz, és automatikusan bekapcsolhatja a közzétett eszköz képességeinek modelljét a nyilvános tárházból a IoT Centralba:

1. Egy meglévő, a nyilvános tárházban közzétett eszköz-képesség modell használata. Szüksége lesz a teljes eszköz képességi modellre, és jegyezze fel a modell URNjét.

1. Kövesse a fenti lépéseket [egy eszköz-ügyfélalkalmazás](#generate-a-device-client-application) létrehozásához a Visual Studio Code használatához és az eszköz kódjának létrehozásához.

1. A IoT Central alkalmazásban nyissa meg az **Adminisztráció** lapot, és válassza ki az **eszköz csatlakoztatása** szakaszt. Jegyezze fel az alkalmazás **hatókör** -azonosítóját és **elsődleges kulcsának** értékeit.

    > [!NOTE]
    > Az ezen a lapon megjelenő **elsődleges kulcs** egy csoport közös hozzáférésű aláírása, amely segítségével több eszköz kulcsát is létrehozhatja az alkalmazáshoz.

1. A [DPS keygen](https://www.npmjs.com/package/dps-keygen) eszközzel létrehozhat egy, a korábban jegyzett elsődleges kulcsból származó termékkulcsot. Az eszköz kulcsának létrehozásához futtassa a következő parancsot:

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. A **Hatókör-azonosító**, az **elsődleges kulcs**és az **eszköz azonosítójának**hozzáadásához kövesse az előző szakaszban található [kapcsolat részleteinek hozzáadása az eszköz ügyfeléhez](#add-connection-details-to-the-device-client) című szakasz lépéseit.

1. Az [ügyfél létrehozásához és futtatásához](#build-and-run-the-client)kövesse az előző szakasz lépéseit.

1. Most láthatja, hogy az eszköz csatlakozik a IoT Central alkalmazáshoz, és automatikusan az eszköz képességeinek modelljét a nyilvános tárházból eszköz sablonként helyezi el.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan csatlakoztatható egy valós eszköz a IoT Centralhoz, a következő lépés az eszköz sablonjaival kapcsolatos további tudnivalók a [sablon beállítása](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) című témakörben.
