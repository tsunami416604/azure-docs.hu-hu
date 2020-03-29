---
title: Eszközszimuláció IoT távoli figyeléssel - Azure | Microsoft dokumentumok
description: Ez az útmutató bemutatja, hogyan használhatja az eszközszimulátort a távoli figyelési megoldásgyorsítóval.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 8babacfede6e13fde629492e1cd9f80af7f0e53f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943072"
---
# <a name="create-and-test-a-new-simulated-device"></a>Új szimulált eszköz létrehozása és tesztelése

A Távoli figyelési megoldás gyorsító lehetővé teszi a saját szimulált eszközök meghatározását. Ez a cikk bemutatja, hogyan definiáljon egy új szimulált villanykörte eszközt, majd tesztelje azt helyileg. A megoldásgyorsító szimulált eszközöket, például hűtőket és teherautókat tartalmaz. Azonban megadhatja a saját szimulált eszközök az IoT-megoldások teszteléséhez, mielőtt valódi eszközöket telepítene.

> [!NOTE]
> Ez a cikk az eszközszimulációs szolgáltatásban tárolt szimulált eszközök használatát ismerteti. Ha valódi eszközt szeretne létrehozni, olvassa el [Az eszköz csatlakoztatása a távfigyelési megoldás gyorsítóhoz című témakört.](iot-accelerators-connecting-devices.md)

Ez az útmutató bemutatja, hogyan szabhatja testre az eszközszimulációs mikroszolgáltatást. Ez a mikroszolgáltatás a távfigyelési megoldásgyorsító része. Az eszközszimulációs képességek megjelenítéséhez ez az útmutató két forgatókönyvet használ a Contoso IoT alkalmazásban:

Az első forgatókönyvben egy új telemetriai típust ad hozzá a Contoso meglévő hűtőeszköztípusához. **Chiller**

A második forgatókönyvben a Contoso egy új intelligens villanykörte eszközt szeretne tesztelni. A tesztek futtatásához hozzon létre egy új szimulált eszközt, amely a következő jellemzőkkel rendelkezik:

*Tulajdonságok*

| Név                     | Értékek                      |
| ------------------------ | --------------------------- |
| Color                    | Fehér, Piros, Kék            |
| Fényerő               | 0-tól 100-ig                    |
| Becsült hátralévő élettartam | Visszaszámlálás 10 000 órától |

*Telemetria*

Az alábbi táblázat azokat az adatokat mutatja be, amelyeket a villanykörte adatfolyamként jelent a felhőnek:

| Név   | Értékek      |
| ------ | ----------- |
| status | "be", "ki" |
| Hőmérséklet | F fok |
| online | igaz, hamis |

> [!NOTE]
> Az **online** telemetriai érték minden szimulált típuskötelező.

*Metódusok*

Az alábbi táblázat az új eszköz által támogatott műveleteket mutatja be:

| Név        |
| ----------- |
| Bekapcsolás   |
| Kapcsoljuk ki  |

*Kezdeti állapot*

Az alábbi táblázat az eszköz kezdeti állapotát mutatja be:

| Név                     | Értékek |
| ------------------------ | -------|
| Kezdeti szín            | Fehér  |
| Kezdeti fényerő       | 75     |
| Kezdeti hátralévő élettartam   | 10,000 |
| Kezdeti telemetriai állapot | "be"   |
| Kezdeti telemetriai hőmérséklet | 200   |

Az útmutató lépései végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követéséhez a következőkre van szükség:

* Visual Studio Code. A [Visual Studio Code for Mac, Linux és Windows alkalmazást letöltheti.](https://code.visualstudio.com/download)
* .NET Core. A [.NET Core letölthető Mac, Linux és Windows rendszerre](https://www.microsoft.com/net/download).
* [C# a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Postás. Letöltheti [postman Mac, Windows vagy Linux](https://www.getpostman.com/apps).
* [Az Azure-előfizetésében üzembe helyezett IoT-központ.](../../articles/iot-hub/iot-hub-create-through-portal.md) Az útmutatóban ismertetett lépések végrehajtásához az IoT hub kapcsolati karakterláncára van szükség. A kapcsolati karakterlánc az Azure Portalon.
* A Cosmos DB adatbázis, amely az SQL API-t használja, és amely [erős konzisztenciára](../../articles/cosmos-db/how-to-manage-database-account.md)van konfigurálva. A Cosmos DB adatbázis kapcsolati karakterláncának az útmutatóban leírt lépések végrehajtásához szükséges. A kapcsolati karakterlánc az Azure Portalon.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

A fejlesztői környezet előkészítéséhez hajtsa végre a következő feladatokat:

* Töltse le az eszközszimulációs mikroszolgáltatás forrását.
* Töltse le a forrást a tárolóadapter mikroszolgáltatás.
* Futtassa a tárolóadapter mikroszolgáltatását helyileg.

A cikkben található utasítások feltételezik, hogy a Windows rendszert használja. Ha más operációs rendszert használ, előfordulhat, hogy a környezetének megfelelően módosítania kell néhány fájlelérési utat és parancsot.

### <a name="download-the-microservices"></a>A mikroszolgáltatások letöltése

Töltse le és csomagolja ki a [távoli figyelési mikroszolgáltatásokat](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) a GitHubról a helyi számítógépen lévő megfelelő helyre. A cikk feltételezi, hogy a mappa neve **távoli-monitoring-services-dotnet-master**.

Töltse le és csomagolja ki az [eszközszimulációs mikroszolgáltatást](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) a GitHubról a helyi számítógépen lévő megfelelő helyre. A cikk feltételezi, hogy a mappa neve **eszköz-szimuláció-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>A tárolóadapter mikroszolgáltatásának futtatása

Nyissa meg a **Remote-monitoring-services-dotnet-master\storage-adapter** mappát a Visual Studio-kódban. A **feloldatlan** függőségek javításához kattintson a Visszaállítás gombra.

Nyissa meg a **storage-adapter/WebService/appsettings.ini** fájlt, és rendelje hozzá a Cosmos DB kapcsolati karakterláncot a **documentDBConnectionString** változóhoz.

A mikroszolgáltatás helyi futtatásához kattintson a **Hibakeresés > a Hibakeresés indítása gombra.**

A Visual Studio-kód **Terminál** ablaka a futó mikroszolgáltatás kimenetét jeleníti [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status)meg, beleértve a webszolgáltatás állapot-ellenőrzésének URL-címét: . Amikor erre a címre navigál, az állapotnak "OK: Él és virul" állapotnak kell lennie.

A következő lépések végrehajtásával hagyja futni a tárolóadapter mikroszolgáltatását a Visual Studio-kód ezen példányában.

## <a name="modify-the-chiller"></a>A hűtő módosítása

Ebben a szakaszban egy új **belső hőmérséklet** telemetriai típust ad hozzá a meglévő **hűtőeszköz** típushoz:

1. Hozzon létre egy új **mappát C:\temp\devicemodels** a helyi számítógépen.

1. Másolja a következő fájlokat az új mappába az eszközszimulációs mikroszolgáltatás letöltött példányából:

    | Forrás | Cél |
    | ------ | ----------- |
    | Szolgáltatások\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Szolgáltatások\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Szolgáltatások\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Szolgáltatások\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Szolgáltatások\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Szolgáltatások\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Nyissa meg a **C:\temp\devicemodels\chiller-01.json** fájlt.

1. Az **InitialState** szakaszban adja hozzá a következő két definíciót:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. A **Telemetriai** tömbben adja hozzá a következő definíciót:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Mentse a **C:\temp\devicemodels\chiller-01.json** fájlt.

1. Nyissa meg a **C:\temp\devicemodels\scripts\chiller-01-state.js** fájlt.

1. Adja hozzá a **state** következő mezőket az állapotváltozóhoz:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Frissítse a **fő** funkciót az alábbiak szerint:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. Mentse a **C:\temp\devicemodels\scripts\chiller-01-state.js** fájlt.

## <a name="create-the-lightbulb"></a>A villanykörte létrehozása

Ebben a szakaszban új **villanykörte-eszköztípust** határoz meg:

1. Hozzon létre egy **c:\temp\devicemodels\lightbulb-01.json** fájlt, és adja hozzá a következő tartalmat:

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    Mentse a **C:\temp\devicemodels\lightbulb-01.json módosításait.**

1. Hozzon létre egy **fájlt C:\temp\devicemodels\scripts\lightbulb-01-state.js** és adja hozzá a következő tartalmat:

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    Mentse a **C:\temp\devicemodels\scripts\lightbulb-01-state.js módosításait.**

1. Hozzon létre egy **C:\temp\devicemodels\scripts\SwitchOn-method.js** fájlt, és adja hozzá a következő tartalmat:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    Mentse a **C:\temp\devicemodels\scripts\SwitchOn-method.js fájl módosításait.**

1. Hozzon létre egy **C:\temp\devicemodels\scripts\SwitchOff-method.js** fájlt, és adja hozzá a következő tartalmat:

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    Mentse a **C:\temp\devicemodels\scripts\SwitchOff-method.js módosításait.**

Most létrehozta a **Hűtő** eszköztípus testreszabott verzióját, és létrehozott egy új **villanykörte** eszköztípust.

## <a name="test-the-devices"></a>Az eszközök tesztelése

Ebben a szakaszban az előző szakaszokban létrehozott eszköztípusokat helyileg tesztelheti.

### <a name="run-the-device-simulation-microservice"></a>Az eszközszimulációs mikroszolgáltatás futtatása

Nyissa meg a GitHubról letöltött **eszköz-szimulációs-dotnet-master** mappát a Visual Studio-kód új példányában. A **feloldatlan** függőségek javításához kattintson a Visszaállítás gombra.

Nyissa meg a **WebService/appsettings.ini** fájlt, rendelje hozzá a Cosmos DB kapcsolati karakterláncot a **documentdb_connstring** változóhoz, és módosítsa a beállításokat az alábbiak szerint:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

A mikroszolgáltatás helyi futtatásához kattintson a **Hibakeresés > a Hibakeresés indítása gombra.**

A Visual Studio-kód **Terminál** ablaka a futó mikroszolgáltatás kimenetét jeleníti meg.

Hagyja futni az eszközszimulációs mikroszolgáltatást a Visual Studio-kód ezen példányában a következő lépések végrehajtásával.

### <a name="set-up-a-monitor-for-device-events"></a>Eszközesemények figyelőjének beállítása

Ebben a szakaszban az Azure CLI használatával egy eseményfigyelő telemetriai adatok megtekintéséhez az IoT hubhoz csatlakoztatott eszközökről küldött telemetriai adatok megtekintéséhez.

A következő parancsfájl feltételezi, hogy az IoT hub neve **eszköz-szimuláció-teszt.**

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Hagyja futni az eseményfigyelőt a szimulált eszközök tesztelése közben.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Szimuláció létrehozása a frissített hűtőeszköz-típussal

Ebben a szakaszban a Postman eszközzel kérheti az eszközszimulációs mikroszolgáltatás a szimuláció futtatásához a frissített hűtőeszköz-típus használatával. Postman egy olyan eszköz, amely lehetővé teszi, hogy küldjön REST-kérelmek egy webszolgáltatás. A postás konfigurációs fájlokat, amire szüksége van a helyi példányát az **eszköz-szimuláció-dotnet** tárház.

A Postman beállítása:

1. Nyisd meg a postása a helyi gépen.

1. Kattintson **a Fájl > importálása gombra.** Ezután kattintson **a Fájlok kiválasztása gombra.**

1. Nyissa meg az **eszköz-szimuláció-dotnet-master/docs/postman** mappát. Válassza az **Azure IoT-eszközszimulációs megoldásgyorsítót.postman_collection** és **az Azure IoT-eszközszimulációs megoldásgyorsítót.postman_environment** és kattintson a **Megnyitás gombra.**

1. Bontsa ki az **Azure IoT-eszközszimulációs megoldásgyorsítót** a küldhető kérelmekre.

1. Kattintson **a Nincs környezet elemre,** és válassza **az Azure IoT-eszközszimulációs megoldásgyorsítót.**

Most már van egy gyűjtemény és környezet betöltve a Postman-munkaterület, amely segítségével az eszköz szimulációs mikroszolgáltatás.

A szimuláció konfigurálása és futtatása:

1. A Postman gyűjteményben válassza a **Módosított hűtőszimuláció létrehozása lehetőséget,** és kattintson a **Küldés**gombra. Ez a kérelem a szimulált hűtőeszköz-típus négy példányát hozza létre.

1. Az Azure CLI ablakesemény-figyelő kimenete a szimulált eszközök telemetriai értékét jeleníti meg, beleértve az új **internal_temperature** értékeket is.

A szimuláció leállításához válassza a **Szimuláció leállítása** kérést a Postman alkalmazásban, és kattintson a **Küldés**gombra.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Szimuláció létrehozása a villanykörte eszköztípusával

Ebben a szakaszban a Postman eszközzel kérheti az eszközszimulációs mikroszolgáltatás talizmáneszköz-típus használatával szimuláció futtatásához. Postman egy olyan eszköz, amely lehetővé teszi, hogy küldjön REST-kérelmek egy webszolgáltatás.

A szimuláció konfigurálása és futtatása:

1. A Postman gyűjteményben válassza a **Villanykörte szimuláció létrehozása lehetőséget,** és kattintson a **Küldés**gombra. Ez a kérés a szimulált villanykörte eszköztípus két példányát hozza létre.

1. Az Azure CLI ablakesemény-figyelő kimenete a szimulált izzók telemetriai adatokat jeleníti meg.

A szimuláció leállításához válassza a **Szimuláció leállítása** kérést a Postman alkalmazásban, és kattintson a **Küldés**gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Leállíthatja a két helyileg futó mikroszolgáltatásokat a Visual Studio-kódpéldányaikban (**Hibakeresés > a Hibakeresés leállítása**).

Ha már nincs szüksége az IoT Hub és a Cosmos DB-példányokra, törölje őket az Azure-előfizetésből a szükségtelen díjak elkerülése érdekében.

## <a name="next-steps"></a>További lépések

Ez az útmutató bemutatja, hogyan hozhat létre egyéni szimulált eszköztípusokat, és hogyan tesztelheti őket az eszközszimulációs mikroszolgáltatás helyi futtatásával.

A javasolt következő lépés az egyéni szimulált eszköztípusok központi telepítésének megismerése a [Távoli figyelési megoldás gyorsítójára.](iot-accelerators-remote-monitoring-deploy-simulated-device.md)
