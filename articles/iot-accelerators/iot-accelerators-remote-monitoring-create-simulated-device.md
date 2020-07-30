---
title: Eszköz szimulálása IoT-alapú távoli figyeléssel – Azure | Microsoft Docs
description: Ez a útmutató bemutatja, hogyan használhatja az eszközt a távoli figyelési megoldás-gyorsító használatával.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: mqtt, devx-track-javascript
ms.openlocfilehash: c0aac897d85cd96d537238bc81cbc89d3626a07c
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422759"
---
# <a name="create-and-test-a-new-simulated-device"></a>Új szimulált eszköz létrehozása és tesztelése

A távoli figyelési megoldás gyorsítása lehetővé teszi saját szimulált eszközök definiálását. Ez a cikk bemutatja, hogyan határozhat meg egy új szimulált villanykörte-eszközt, majd hogyan tesztelheti helyileg. A megoldás-gyorsító olyan szimulált eszközöket tartalmaz, mint a hűtők és a teherautók. A valós eszközök üzembe helyezése előtt azonban megadhat saját szimulált eszközöket a IoT-megoldások teszteléséhez.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan használhatók a szimulált eszközök a Device szimulációs szolgáltatásban. Ha valódi eszközt szeretne létrehozni, tekintse meg [az eszköz csatlakoztatása a távoli figyelési megoldáshoz gyorssegédet](iot-accelerators-connecting-devices.md).

Ez az útmutató bemutatja, hogyan szabhatja testre az eszköz szimulációs szolgáltatását. Ez a szolgáltatás a távoli figyelési megoldáshoz tartozó gyorssegéd része. Az eszköz szimulációs képességeinek megjelenítéséhez ez a útmutató a contoso IoT alkalmazásban két forgatókönyvet használ:

Az első forgatókönyvben új telemetria-típust ad hozzá a contoso meglévő, **Chiller** típusú eszközéhez.

A második forgatókönyvben a contoso egy új intelligens villanykörte-eszközt szeretne tesztelni. A tesztek futtatásához létre kell hoznia egy új szimulált eszközt a következő jellemzőkkel:

*Tulajdonságok*

| Name                     | Értékek                      |
| ------------------------ | --------------------------- |
| Szín (Color)                    | Fehér, piros, kék            |
| Fényerő               | 0 – 100                    |
| Becsült hátralévő élettartam | Visszaszámlálás 10 000 órával |

*Telemetria*

Az alábbi táblázat azokat az adatstream-jelentéseket mutatja be, amelyeket a villanykörte jelent a felhőnek:

| Name   | Értékek      |
| ------ | ----------- |
| status | "on", "off" |
| Hőmérséklet | Fok F |
| online | igaz, hamis |

> [!NOTE]
> Az **online** telemetria értéke minden szimulált típus esetében kötelező.

*Metódusok*

A következő táblázat az új eszköz által támogatott műveleteket mutatja be:

| Name        |
| ----------- |
| Bekapcsolás   |
| Kikapcsolás  |

*Kezdeti állapot*

Az alábbi táblázat az eszköz kezdeti állapotát mutatja be:

| Name                     | Értékek |
| ------------------------ | -------|
| Kezdeti szín            | Fehér  |
| Kezdeti fényerő       | 75     |
| Kezdeti hátralévő élettartam   | 10,000 |
| Kezdeti telemetria állapota | a   |
| Kezdeti telemetria hőmérséklete | 200   |

A jelen útmutató lépéseinek végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A útmutató követéséhez a következőkre lesz szüksége:

* Visual Studio Code. [A Visual Studio Code letölthető Mac, Linux és Windows rendszerekhez](https://code.visualstudio.com/download).
* .NET Core. [A .net Core for Mac, Linux és Windows rendszerhez](https://www.microsoft.com/net/download)is letölthető.
* [C# a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Postman. [A Poster letölthető Mac, Windows vagy Linux rendszerű számítógépekre](https://www.getpostman.com/apps).
* Az [Azure-előfizetéshez üzembe helyezett IoT hub](../../articles/iot-hub/iot-hub-create-through-portal.md). Az útmutató lépéseinek elvégzéséhez szüksége lesz az IoT hub kapcsolódási karakterláncára. A Azure Portal a kapcsolatok sztringjét is beszerezheti.
* Egy Cosmos DB adatbázis, amely az SQL API-t használja, és [erős konzisztencia](../../articles/cosmos-db/how-to-manage-database-account.md)használatára van konfigurálva. A jelen útmutató lépéseinek elvégzéséhez szüksége lesz a Cosmos DB adatbázisához tartozó kapcsolódási karakterláncra. A Azure Portal a kapcsolatok sztringjét is beszerezheti.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

A fejlesztési környezet előkészítéséhez végezze el a következő feladatokat:

* Töltse le az eszköz szimulációs szolgáltatásának forrását.
* Töltse le a Storage adapter-szolgáltatás forrását.
* Futtassa helyileg a Storage-adapter-szolgáltatást.

A cikkben szereplő utasítások feltételezik, hogy a Windowst használja. Ha másik operációs rendszert használ, előfordulhat, hogy módosítania kell néhány fájl elérési útját és parancsát a környezetének megfelelően.

### <a name="download-the-microservices"></a>A szervizcsomagok letöltése

Töltse le és csomagolja ki a [távoli monitorozási szolgáltatásokat](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) a githubról a helyi gépen található megfelelő helyre. A cikk azt feltételezi, hogy a mappa neve **távoli figyelés-Services-DotNet-Master**.

Töltse le és csomagolja ki az [eszköz szimulációs szolgáltatását](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) a githubról egy megfelelő helyre a helyi gépen. A cikk feltételezi, hogy a mappa neve: **Device-szimulációs-DotNet-Master**.

### <a name="run-the-storage-adapter-microservice"></a>A Storage-adapter szolgáltatásának futtatása

Nyissa meg a **Remote-Monitoring-Services-DotNet-master\storage-adapter** mappát a Visual Studio Code-ban. A feloldatlan függőségek kijavításához kattintson bármelyik **visszaállítási** gombra.

Nyissa meg a **Storage-adapter/webszolgáltatás/appsettings.ini** fájlt, és rendelje hozzá a Cosmos db-kapcsolódási karakterláncot a **documentDBConnectionString** változóhoz.

Ha helyileg szeretné futtatni a szolgáltatást, kattintson a **hibakeresés > a hibakeresés indítása**elemre.

A Visual Studio Code-ban található **Terminálablak** a futó szolgáltatás kimenetét jeleníti meg, beleértve a webszolgáltatás állapot-ellenőrzési URL-címét: [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status) . Amikor navigál erre a címre, az állapotnak "OK: Alive and Well" értéknek kell lennie.

A következő lépések elvégzése után a Visual Studio Code ezen példányán ne futtassa a Storage-adapter-szolgáltatást.

## <a name="modify-the-chiller"></a>A hűtő módosítása

Ebben a szakaszban új **belső hőmérséklet** -telemetria-típust ad hozzá a meglévő **hűtő** típusú eszközhöz:

1. Hozzon létre egy új mappát a **C:\temp\devicemodels** a helyi gépen.

1. Másolja a következő fájlokat az új mappába az eszköz szimulációs szolgáltatásának letöltött példányáról:

    | Forrás | Cél |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.jsbekapcsolva | C:\temp\devicemodels\chiller-01.jsbekapcsolva |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Nyissa meg a **C:\temp\devicemodels\chiller-01.js** fájlt.

1. A **InitialState** szakaszban adja hozzá a következő két definíciót:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. A **telemetria** tömbben adja hozzá a következő definíciót:

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

1. Mentse a **C:\temp\devicemodels\chiller-01.js** fájlt.

1. Nyissa meg a **C:\temp\devicemodels\scripts\chiller-01-state.js** fájlt.

1. Adja hozzá a következő mezőket az **állapot** változóhoz:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Frissítse a **fő** függvényt az alábbiak szerint:

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

Ebben a szakaszban egy új **villanykörte** -eszköz típusát adja meg:

1. Hozzon létre egy fájlt **C:\temp\devicemodels\lightbulb-01.jsbe** , és adja hozzá a következő tartalmat:

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

    Mentse a **C:\temp\devicemodels\lightbulb-01.js**módosításait.

1. Hozzon létre egy fájlt **C:\temp\devicemodels\scripts\lightbulb-01-state.js** és adja hozzá a következő tartalmat:

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

    Mentse **C:\temp\devicemodels\scripts\lightbulb-01-state.js**módosításait.

1. Hozzon létre egy fájlt **C:\temp\devicemodels\scripts\SwitchOn-method.js** és adja hozzá a következő tartalmat:

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

    Mentse **C:\temp\devicemodels\scripts\SwitchOn-method.js**módosításait.

1. Hozzon létre egy fájlt **C:\temp\devicemodels\scripts\SwitchOff-method.js** és adja hozzá a következő tartalmat:

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

    Mentse **C:\temp\devicemodels\scripts\SwitchOff-method.js**módosításait.

Ezzel létrehozta a **Chiller** -eszköz típusának testreszabott verzióját, és létrehozott egy új **villanykörte** típusú eszközt.

## <a name="test-the-devices"></a>Az eszközök tesztelése

Ebben a szakaszban az előző szakaszokban létrehozott típusú eszközök helyi tesztelését teszteli.

### <a name="run-the-device-simulation-microservice"></a>Az eszköz szimulációs szolgáltatásának futtatása

Nyissa meg a GitHubról letöltött **Device-szimulációs-DotNet-Master** mappát a Visual Studio Code új példányán. A feloldatlan függőségek kijavításához kattintson bármelyik **visszaállítási** gombra.

Nyissa meg a **webszolgáltatási/appsettings.ini** fájlt, és rendelje hozzá a Cosmos db-kapcsolódási karakterláncot a **documentdb_connstring** változóhoz, és módosítsa a beállításokat a következőképpen:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

Ha helyileg szeretné futtatni a szolgáltatást, kattintson a **hibakeresés > a hibakeresés indítása**elemre.

A Visual Studio Code-ban található **Terminálablak** a futó szolgáltatás kimenetét jeleníti meg.

A következő lépések elvégzése után hagyja a Visual Studio Code ezen példányán futó Device szimulációs szolgáltatást.

### <a name="set-up-a-monitor-for-device-events"></a>Figyelő beállítása az eszköz eseményeihez

Ebben a szakaszban az Azure CLI-vel állít be egy eseményt figyelőt az IoT hub-hoz csatlakoztatott eszközökről érkező telemetria megtekintéséhez.

A következő parancsfájl feltételezi, hogy az IoT hub neve **Device-szimulációs-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

A szimulált eszközök tesztelése közben hagyja futni az eseménynaplót.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Szimuláció létrehozása a frissített hűtő eszköz típusával

Ebben a szakaszban a Poster eszköz használatával kérheti le az eszköz szimulációs szolgáltatását, hogy futtasson egy szimulációt a frissített hűtő típusú eszköz használatával. A Poster egy olyan eszköz, amely lehetővé teszi a REST-kérések küldését egy webszolgáltatásnak. A szükséges Poster konfigurációs fájlok a **Device-szimulációs-DotNet** adattár helyi példányán találhatók.

Poster beállítása:

1. Nyissa meg a Poster szolgáltatást a helyi gépen.

1. Kattintson a **fájl > importálás**elemre. Ezután kattintson a **fájlok kiválasztása**elemre.

1. Navigáljon a **Device-szimulációs-DotNet-Master/docs/Poster** mappába. Válassza az **Azure IoT-eszköz szimulációs megoldás gyorsító. postman_collection** és az **Azure IoT-eszköz szimulációs megoldásának gyorsítása elemet. postman_environment** és kattintson a **Megnyitás**gombra.

1. Bontsa ki az **Azure IoT-eszköz szimulációs megoldásának Gyorssegédjét** a küldendő kérelmekre.

1. Kattintson a **nincs környezet** elemre, és válassza az **Azure IoT-eszköz szimulációs megoldásának gyorsítása**elemet.

Most már betöltött egy gyűjteményt és egy környezetet a Poster-munkaterületen, amely az eszköz-szimulációs szolgáltatással való kommunikációhoz használható.

A szimuláció konfigurálása és futtatása:

1. A Poster-gyűjteményben válassza a **módosított Chiller-szimuláció létrehozása** elemet, majd kattintson a **Küldés**gombra. Ez a kérelem a szimulált hűtő típusú eszköz négy példányát hozza létre.

1. Az Eseménynapló kimenete az Azure CLI-ablakban a szimulált eszközök telemetria jeleníti meg, beleértve az új **internal_temperature** értékeket is.

A szimuláció leállításához jelölje ki a **Szimuláció leállítására** irányuló kérelmet a Poster alkalmazásban, és kattintson a **Küldés**gombra.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Szimuláció létrehozása a villanykörte-eszköz típusával

Ebben a szakaszban a Poster eszköz használatával kéri az eszköz szimulációs szolgáltatását, hogy futtasson egy szimulációt a villanykörte eszköz típusával. A Poster egy olyan eszköz, amely lehetővé teszi a REST-kérések küldését egy webszolgáltatásnak.

A szimuláció konfigurálása és futtatása:

1. A Poster-gyűjteményben válassza a **villanykörte-szimuláció létrehozása** elemet, majd kattintson a **Küldés**gombra. Ez a kérelem a szimulált villanykörte típusú eszköz két példányát hozza létre.

1. Az Eseménynapló kimenete az Azure CLI-ablakban a szimulált izzók telemetria jeleníti meg.

A szimuláció leállításához jelölje ki a **Szimuláció leállítására** irányuló kérelmet a Poster alkalmazásban, és kattintson a **Küldés**gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A két helyileg futó Service-t leállíthatja a Visual Studio Code-példányaiban (**hibakeresés > hibakeresés leállítása**).

Ha már nincs szüksége a IoT Hubra és Cosmos DB példányokra, törölje azokat az Azure-előfizetésből a szükségtelen költségek elkerülése érdekében.

## <a name="next-steps"></a>Következő lépések

Ez az útmutató bemutatja, hogyan hozhat létre egyéni szimulált eszközöket, és hogyan tesztelheti őket az eszköz-szimulációs szolgáltatás helyi futtatásával.

A következő lépés az, hogy megtudja, hogyan helyezheti üzembe az egyéni szimulált eszközök típusát a [távoli figyelési megoldás-gyorsító](iot-accelerators-remote-monitoring-deploy-simulated-device.md)eszközön.
