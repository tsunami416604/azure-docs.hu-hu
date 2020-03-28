---
title: A minősítésre kész IoT Plug and Play előzetes eszköz létrehozása | Microsoft dokumentumok
description: Eszközfejlesztőként ismerje meg, hogyan hozhat létre olyan IoT Plug and Play előzetes verziójú eszközt, amely készen áll a minősítésre.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e97aa07d2a43a03805fd881c674157ee676c37b4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239911"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>A minősítésre kész IoT Plug and Play előzetes eszköz létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy IoT Plug and Play előzetes verziójú eszközt eszközfejlesztőként, amely készen áll a minősítésre.

A tanúsítási tesztek ellenőrzik, hogy:

- Az IoT Plug and Play eszközkódja telepítve van az eszközön.
- Az IoT Plug and Play eszközkódja az Azure IoT SDK-val készült.
- Az eszközkód támogatja az [Azure IoT Hub eszközkiépítési szolgáltatást.](../iot-dps/about-iot-dps.md)
- Az eszközkód megvalósítja az eszközinformációs felületet.
- A képességmodell és az eszközkód együttműködik az IoT Centrallal.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- [Visual Studio kód](https://code.visualstudio.com/download)
- [Azure IoT-eszközök a VS Code bővítménycsomaghoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

Az [Eszközképesség-modell használata](quickstart-create-pnp-device-windows.md) eszközgyorsindítást is el kell végeznie a Windows számára. A rövid útmutató bemutatja, hogyan állíthatja be a fejlesztői környezetet vcpkg használatával, és hozzon létre egy mintaprojektet.

## <a name="store-a-capability-model-and-interfaces"></a>Képességmodell és -összeköttetések tárolása

Az IoT Plug and Play eszközök esetében létre kell adnia egy képességmodellt és felületeket, amelyek az eszköz képességeit JSON-fájlokként határozzák meg.

Ezeket a JSON-fájlokat három különböző helyen tárolhatja:

- A nyilvános modell tárháza.
- A vállalati modell tárháza.
- A készüléken.

Az eszköz hitelesítéséhez a fájlokat vagy a vállalati modelltárházban, vagy a nyilvános modelltárházban kell tárolni.

## <a name="include-the-required-interfaces"></a>A szükséges felületek felvétele

A minősítési folyamat végrehajtásához a képességmodellben fel kell tüntetnie és végre kell hajtania az **Eszközinformációs** felületet. Ez az interfész a következő azonosítóval rendelkezik:

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> Ha befejezte a [rövid útmutató: Eszközképesség-modell használatával hozzon létre egy eszközt,](quickstart-create-pnp-device-windows.md)akkor már felvette a modellbe az **Eszközinformáció** felületet.

Ha az **eszközinformációs felületet** fel szeretné felvenni az `implements` eszközmodellbe, adja hozzá a kapcsolatazonosítót a képességmodell tulajdonságához:

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Az **Eszközinformációs** felület megtekintése a VS-kódban:

1. A parancspaletta megnyitásához használja a **Ctrl+Shift+P** billentyűkombinációt.

1. Írja be a **Plug and Play parancsot,** majd válassza az **IoT Plug and Play Open Model Repository parancsot.** Válassza **a Nyilvános modell tárházának megnyitása**lehetőséget. A nyilvános modell tárház a VS-kódban nyílik meg.

1. A nyilvános modelltárban válassza a **Kapcsolatok** lapot, jelölje ki a szűrő ikont, és írja be az **Eszközadatok értéket** a szűrőmezőbe.

1. Az **Eszközinformáció-felület** helyi példányának létrehozásához jelölje ki azt a szűrt listában, majd kattintson a **Letöltés gombra.** A VS Code megjeleníti a kapcsolatfájlt.

Az **Eszközinformációs** felület megtekintése az Azure CLI használatával:

1. [Telepítse az Azure IoT CLI bővítményt.](howto-install-pnp-cli.md)

1. A következő Azure CLI-paranccsal megjeleníthetegy felületet az eszközinformációs felület azonosítójával:

    ```azurecli
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

További információ: [Az Azure IoT-bővítmény telepítése és használata az Azure CLI-hez](howto-install-pnp-cli.md)című témakörben.

## <a name="update-device-code"></a>Eszközkód frissítése

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Eszközkiépítés engedélyezése az Azure IoT-eszközkiépítési szolgáltatáson (DPS) keresztül

Az eszköz hitelesítéséhez engedélyeznie kell az [Azure IoT-eszközlétesítési szolgáltatáson (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)keresztül történő kiépítést. A DPS használatának hozzáadásához létrehozhatja a C-kódcsonkot a VS-kódban. Kövesse az alábbi lépéseket:

1. Nyissa meg a DCM-fájlt tartalmazó mappát a VS-kódban, a **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát, írja be az **IoT Plug and Play parancsot,** és válassza **az Eszközkód csonk létrehozása lehetőséget.**

1. Válassza ki azt a DCM-fájlt, amelyet az eszközkódcsonk létrehozásához kíván használni.

1. Írja be a projekt nevét, például **sample_device**. Ez az eszközalkalmazás neve.

1. Válassza az **ANSI C nyelvet.**

1. Válassza **a Via DPS (Device Provisioning Service) szimmetrikus kulcsot** csatlakozási módszerként.

1. Projektsablonként válassza **a CMake Project on Windows** lehetőséget.

1. Válassza a **Via Vcpkg** lehetőséget az eszköz SDK-jának felvételére.

1. A VS Code új ablakot nyit meg generált eszközkódcsonkfájlokkal.

## <a name="build-and-run-the-code"></a>A kód létrehozása és futtatása

A Vcpkg csomag segítségével hozza létre a generált eszközkód csonkot. Az alkalmazást hoz létre szimulálja egy eszköz, amely csatlakozik egy IoT hub. Az alkalmazás telemetriai adatokat és tulajdonságokat küld, és parancsokat fogad.

1. Hozzon `cmake` létre egy `sample_device` alkönyvtárat a mappában, és keresse meg azt a mappát:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Futtassa a következő parancsokat a létrehozott kódcsonk létrehozásához (a helyőrző helyett a Vcpkg tártár könyvtára):

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build .
    ```
    
    > [!NOTE]
    > Ha a Visual Studio 2017-et vagy 2015-öt használja, a CMake-generátort a használt buildeszközök alapján kell megadnia:
    >```cmd
    ># Either
    >cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    ># or
    >cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}\scripts\buildsystems\vcpkg.cmake"
    >```

    > [!NOTE]
    > Ha a cmake nem találja a C++ fordítót, az előző parancs futtatásakor buildhibákat kap. Ebben az esetben próbálja meg futtatni ezt a parancsot a [Visual Studio parancssori parancssorból.](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)

1. A létrehozás sikeres befejezése után adja meg a DPS hitelesítő adatait **(DPS ID hatókör,** **DPS szimmetrikus kulcs**, **eszközazonosító)** az alkalmazás paramétereiként. A hitelesítő adatok beszerezése a tanúsítványportálról, [olvassa el az IoT Plug and Play eszköz csatlakoztatása és tesztelése című témakört.](tutorial-certification-test.md#connect-and-discover-interfaces)

    ```cmd\sh
    .\Debug\sample_device.exe [Device ID] [DPS ID Scope] [DPS symmetric key]
    ```

### <a name="implement-standard-interfaces"></a>Szabványos összeköttetések megvalósítása

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>A modellinformációs és SDK-információs felületek megvalósítása

Az Azure IoT-eszköz SDK megvalósítja a modell információs és SDK-információs felületek. Ha a VS Code kódkód kódgenerálási funkcióját használja, az eszközkód az IoT Plug and Play eszköz SDK-t használja.

Ha úgy döntött, hogy nem használja az Azure IoT-eszköz SDK, használhatja az SDK-forráskódot referenciaként a saját megvalósítása.

#### <a name="implement-the-device-information-interface"></a>Az eszközinformációs felület megvalósítása

Valósítsa meg az **eszközinformációs felületet** az eszközön, és adja meg az eszközspecifikus információkat futási időben.

Használhatja a [linuxos](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) **eszközinformációs** felület példaimplementációját referenciaként.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>A modellben definiált összes képesség megvalósítása

A minősítés során az eszközt programozott módon teszteljük annak érdekében, hogy az interfészekben meghatározott képességeket valósítson meg. Az 501-es HTTP-állapotkód dal válaszolhat az írási és olvasási tulajdonság- és parancskérelmekre, ha az eszköz nem valósítja meg őket.

## <a name="next-steps"></a>További lépések

Most, hogy már készített egy IoT Plug and Play eszközt, amely készen áll a minősítésre, a javasolt következő lépés a következő:

> [!div class="nextstepaction"]
> [További információ az eszköz hitelesítéséhez](tutorial-certification-test.md)
