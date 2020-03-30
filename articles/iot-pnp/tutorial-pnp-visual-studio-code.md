---
title: IoT Plug and Play előzetes eszköz létrehozása és tesztelése | Microsoft dokumentumok
description: Eszközfejlesztőként ismerje meg, hogyan hozhat létre és tesztelhetsz egy új eszközképesség-modellt egy IoT Plug and Play előzetes eszközhöz.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 720b3e56e1dd45bd2940b337adefa6ebdaa2e5a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719721"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>Oktatóanyag: Eszközképesség-modell létrehozása és tesztelése a Visual Studio-kód használatával

Ez az oktatóanyag bemutatja, hogy eszközfejlesztőként hogyan használhatja a Visual Studio Code alkalmazást _eszközképesség-modell_létrehozásához. A modell segítségével csontváz-kódot hozhat létre egy olyan eszközön, amely csatlakozik egy Azure IoT Hub-példánya a felhőben.

Az oktatóanyag azon szakasza, amely leírja, hogyan hozhat létre a létrehozott csontvázkódot, feltételezi, hogy windowsos rendszert használ.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszközképességi modell létrehozása
> * Csontváz eszközkód létrehozása a modellből
> * A létrehozott kódcsonkjainak megvalósítása
> * Futtassa a kódot az IoT hubbal való interakciók teszteléséhez

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag ban az eszközképesség-modell elvégzett munkához a következőkre van szükség:

* [Visual Studio Kód:](https://code.visualstudio.com/download)VS Kód áll rendelkezésre több platformon
* [Azure IoT-eszközök vs kód](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítménycsomaghoz. A bővítménycsomag VS Code-ban való telepítéséhez kövesse az alábbi lépéseket:

    1. A VS-kód ban válassza a **Bővítmények** lapot.
    1. Keresse meg az **Azure IoT-eszközöket.**
    1. Válassza az **Install** (Telepítés) lehetőséget.

A létrehozott C-kód windowsos létrehozásához ebben az oktatóanyagban a következőkre van szükség:

* **C++ buildeszközökkel** és **NuGet csomagkezelői összetevők** munkaterheléseivel [fejlesztőeszközök](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) kelhet létre a Visual Studio számára. Vagy ha már rendelkezik [visual studio (közösségi, professzionális vagy nagyvállalati)](https://visualstudio.microsoft.com/downloads/) 2019,2017 vagy 2015 azonos számítási feladatok telepítve.
* [Git](https://git-scm.com/download)
* [CMake között](https://cmake.org/download/)

Az oktatóanyagban az eszközkódjának teszteléséhez a következőkre van szükség:

* Az [Azure IoT felfedezője.](https://github.com/Azure/azure-iot-explorer/releases)
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>Az eszköz modellezése

A _digitális ikerdefiníciós nyelvet_ használja egy eszközképességi modell létrehozásához. A modell általában több _illesztőfelület-definíciós_ fájlból és egyetlen modellfájlból áll. Az **Azure IoT-eszközök vs kód** eszközöket tartalmaz, amelyek segítségével hozza létre és szerkesztheti ezeket a JSON-fájlokat.

### <a name="create-the-interface-file"></a>A kapcsolatfájl létrehozása

Az IoT-eszköz képességeit a VS-kódban definiáló felületfájl létrehozása:

1. Hozzon létre egy devicemodel nevű **mappát.**

1. Indítsa el a VS Code programot, és a **Ctrl+Shift+P** billentyűkombinációval nyissa meg a parancspalettát.

1. Írja be a **Plug and Play parancsot** &, majd válassza az **IoT-lejátszás: Felület létrehozása** parancsot.

1. Tallózással keresse meg és jelölje ki a létrehozott **eszközmodell** mappát.

1. Ezután írja be az **EnvironmentalSensor** nevet a felület neveként, majd nyomja le az **Enter billentyűt.** A VS Code létrehoz egy **EnvironmentalSensor.interface.json**nevű mintaillesztőfájlt.

1. Cserélje le a fájl tartalmát a következő `{your name}` JSON-ra, és cserélje le a `@id` mezőt egy egyedi értékre. Csak az a-z, A-Z, 0-9 és aláhúzáskaraktereket használja. További információ: [Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format). A kapcsolatazonosítónak egyedinek kell lennie ahhoz, hogy a tárolóban mentse a kapcsolatot:

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    Ez a felület definiálja az eszköz tulajdonságait, például **az ügyfél nevét,** a telemetriai típusokat, például **a Hőmérsékletet**és a parancsokat , például **a turnon parancsot.**

1. A **csatolófájl** végén a blink nevű parancsfunkció hozzáadása. A parancs hozzáadása előtt mindenképpen adjon hozzá vesszőt. Próbálja meg beírni a definíciót, hogy lássa, az intellisense, az automatikus kiegészítés és az érvényesítés hogyan segíthet a felület definíciójának szerkesztésében:

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. Mentse a fájlt.

### <a name="create-the-model-file"></a>A modellfájl létrehozása

A modellfájl meghatározza az IoT Plug and Play eszköz által megvalósított felületeket. Egy modellben általában legalább két felület található – egy vagy több, amelyek meghatározzák az eszköz adott képességeit, és egy szabványos felület, amelyet minden IoT Plug and Play eszköznek végre kell hajtania.

Olyan modellfájl létrehozása, amely meghatározza az IoT Plug and Play eszköz által a VS-kódban megvalósított felületeket:

1. A parancspaletta megnyitásához használja a **Ctrl+Shift+P** billentyűkombinációt.

1. Írja be a **Plug and Play parancsot,** majd válassza az **IoT Plug & Play: Képességmodell létrehozása** parancsot. Ezután írja be **a SensorboxModel** modellt a modell neveként. A VS Code létrehoz egy **SensorboxModel.capabilitymodel.json**nevű mintaillesztőfájlt.

1. Cserélje le a fájl tartalmát a következő `{your name}` JSON-ra, és cserélje le a `@id` mezőben és a `EnvironmentalSensor` felületen az **EnvironmentalSensor.interface.json** fájlban használt értékkel. A kapcsolatazonosítónak egyedinek kell lennie ahhoz, hogy a tárolóban mentse a kapcsolatot:

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    A modell egy olyan eszközt határoz meg, amely megvalósítja az **EnvironmentalSensor** felületet és a szabványos **DeviceInformation** felületet.

1. Mentse a fájlt.

### <a name="download-the-deviceinformation-interface"></a>A DeviceInformation felület letöltése

Mielőtt létrehozna egy csontvázkódot a modellből, létre kell hoznia a **DeviceInformation** helyi példányát a *nyilvános modelltárházból.* A nyilvános modelltár már tartalmazza a **DeviceInformation** felületet.

A **DeviceInformation** felület letöltése a nyilvános modelltárból a VS Code használatával:

1. A parancspaletta megnyitásához használja a **Ctrl+Shift+P** billentyűkombinációt.

1. Írja be a **Plug and Play**parancsot, válassza a **Modelltár megnyitása parancsot,** majd válassza **a Nyilvános modelltár megnyitása**lehetőséget.

1. Válassza **az Összeköttetések**lehetőséget, majd válassza `urn:azureiot:DeviceManagement:DeviceInformation:1`ki az azonosítóval ellátott eszközinformációs felületet, majd a **Letöltés**lehetőséget.

Most már rendelkezik az eszköz képességmodelljét alkotó három fájlval:

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>A modell közzététele

Az Azure IoT-kezelő eszköz az eszköz képességmodelljének olvasásához közzé kell tennie azt a vállalati tárházban. A VS-kódból történő közzétételhez a vállalati tárház kapcsolati karakterláncára van szükség:

1. Nyissa meg az [Azure Certified for IoT portált.](https://aka.ms/ACFI)

1. A portálra való bejelentkezéshez használja _Microsoft-munkahelyi fiókját._

1. Válassza **a Vállalati tárház,** majd **a Kapcsolati karakterláncok lehetőséget.**

1. Másolja a kapcsolati karakterláncot.

A vállalati tárház megnyitása a VS Code-ban:

1. A parancspaletta megnyitásához használja a **Ctrl+Shift+P** billentyűkombinációt.

1. Írja be a **Plug and Play parancsot,** majd válassza az **IoT Plug & Play: Open Model Repository parancsot.**

1. Válassza **a Nyílt szervezeti modell tárházának megnyitását** és beillesztését a kapcsolati karakterláncba.

1. Nyomja **le az Enter** billentyűt a vállalati tárház megnyitásához.

Az eszközképességi modell és -összeköttetések közzététele a vállalati tárházban:

1. A parancspaletta megnyitásához használja a **Ctrl+Shift+P** billentyűkombinációt.

1. Írja be a **Plug and Play parancsot,** majd válassza ki az **IoT Plug & Play: Fájlok küldése a Modelltárházba parancsot.**

1. Válassza ki az **EnvironmentalSensor.interface.json** és **a SensorboxModel.capabilitymodel.json** fájlokat, és válassza **az OK gombot.**

A fájlok most már a vállalati tárházban tárolódnak.

## <a name="generate-code"></a>Kód létrehozása

Az Azure **IoT-eszközök vs-kódhoz** segítségével létrehozhat csontváz C-kódot a modellből. A VS-kódban lévő csontvázkód létrehozása:

1. A parancspaletta megnyitásához használja a **Ctrl+Shift+P** billentyűkombinációt.

1. Írja be a **Plug and Play parancsot,** majd válassza az **IoT Plug & Play: Device Code Stub** parancsot.

1. Válassza ki a **SensorboxModel.capabilitymodel.json** képességmodell-fájlt.

1. Adja meg **sensorbox_app** projektnévként.

1. Válassza az **ANSI C nyelvet.**

1. Válassza a **Via IoT Hub eszköz kapcsolati karakterlánc,** mint a csatlakozás módja.

1. Projektsablonként válassza **a CMake Project on Windows** lehetőséget.

1. Válassza a **Via Vcpkg** lehetőséget az eszköz SDK-jának felvételéhez.

A VS Code létrehozza a c-kód csontvázát, és a **sensorbox_app** mappába menti a fájlokat a **modelcode** mappába. A VS Code megnyit egy új ablakot, amely a létrehozott kódfájlokat tartalmazza.

## <a name="update-the-generated-code"></a>A létrehozott kód frissítése

A kód létrehozása és futtatása előtt végre kell hajtania a csonka tulajdonságokat, a telemetriai adatokat és a parancsokat.

A VS Code csonkolt kódjának implementációinak biztosítása:

1. Nyissa meg a **SensorboxModel_impl.c** fájlt.

1. Adjon hozzá kódot a csonkolt függvények megvalósításához.

1. Mentse a módosításokat.

## <a name="build-the-code"></a>A kód létrehozása

Mielőtt futtatná a kódot az IoT Plug and Play-eszköz Azure IoT hubbal való teszteléséhez, le kell fordítania a kódot.

A **sensorbox_app-as** mappában található **Readme.md** fájl utasításait követve hozd létre és futtassa a kódot Windows rendszeren. A következő szakasz az eszközkód futtatásakor használandó eszközkapcsolati karakterlánc beolvasására vonatkozó utasításokat tartalmazza.

## <a name="test-the-code"></a>A kód tesztelése

A kód futtatásakor csatlakozik az IoT Hubhoz, és megkezdi a minta telemetriai adatok és tulajdonságértékek küldését. Az eszköz az IoT Hubról küldött parancsokra is válaszol. A viselkedés ellenőrzése:

1. IoT Hub létrehozása:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. Vegyen fel egy eszközt az IoT hubhoz, és olvassa be a kapcsolati karakterláncot:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    Jegyezze fel a kapcsolati karakterláncot.

1. A parancssorban keresse meg az **azure-iot-sdk-c** mappát, ahol az SDK-t és a mintákat készítette. Ezután keresse meg a **cmake\\sensorbox_app\\Release** mappát.

1. Futtassa az alábbi parancsot:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Az Azure IoT-kezelő eszközzel kommunikálhat az IoT Plug and Play eszközzel, amely az IoT hubhoz csatlakozik. További információ: [Install and use Azure IoT Explorer](./howto-install-iot-explorer.md).

## <a name="next-steps"></a>További lépések

Most, hogy már készített egy, a minősítésre kész IoT Plug and Play játékot, olvassa el a következőket:

> [!div class="nextstepaction"]
> [Minősítésre kész eszköz létrehozása](tutorial-build-device-certification.md)
