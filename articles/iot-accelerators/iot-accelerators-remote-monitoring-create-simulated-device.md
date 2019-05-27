---
title: Eszköz szimulálása az IoT távoli figyelés – Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan a készülékszimulátort a távoli figyelési megoldásgyorsító használandó.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 5044f8b85e59911633a4ffab509efc000948144a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832579"
---
# <a name="create-and-test-a-new-simulated-device"></a>Hozhat létre és tesztelhet egy új szimulált eszköz

A távoli figyelési megoldásgyorsító meghatározhatja a saját szimulált eszközökhöz. Ez a cikk bemutatja, hogyan határozza meg az új eszköz szimulált villanykörte, és ezután helyben tesztelheti. A megoldásgyorsító például hőmérsékletű és tehergépkocsik szimulált eszközt magában foglal. Azonban megadhat saját szimulált eszközök az IoT-megoldások teszteléséhez, valódi eszközök üzembe helyezése előtt.

> [!NOTE]
> Ez a cikk ismerteti, hogyan használható a szimulált eszközök az eszköz szimulálása szolgáltatásban üzemeltetett. Ha szeretne létrehozni egy igazi eszközön, tekintse meg [az eszköz csatlakoztatása a távoli figyelési megoldásgyorsító](iot-accelerators-connecting-devices.md).

Ez az útmutató bemutatja, hogyan szabhatja testre az eszköz szimulálása mikroszolgáltatás. A mikroszolgáltatások a távoli figyelési megoldásgyorsító részét képezi. Mutatja be az eszköz szimulálása képességeket, ez az útmutató két forgatókönyvet használja fel a Contoso IoT-alkalmazást:

Az első esetben hozzáadhat egy új telemetriatípus contoso meglévő **hűtő** típusú eszközt.

A második forgatókönyvben a Contoso biztosítani szeretné egy új intelligens villanykörte eszköz teszteléséhez. A tesztek futtatásához létrehozhat egy új szimulált eszköz a következő jellemzőkkel:

*Tulajdonságok*

| Name (Név)                     | Értékek                      |
| ------------------------ | --------------------------- |
| Szín                    | Fehér, a vörös, kék            |
| Brightness               | 0 – 100                    |
| Becsült hátralévő élettartamának | Visszaszámlálás 10 000 óra |

*Telemetria*

Az alábbi táblázat mutatja az adatok a villanykörte a felhőbe, mint egy adatfolyam-jelentések:

| Name (Név)   | Értékek      |
| ------ | ----------- |
| Állapot | "on" "off" |
| Hőmérséklet | F fok |
| online | IGAZ, hamis |

> [!NOTE]
> A **online** telemetriaérték minden szimulált típusok megadása kötelező.

*Methods*

Az alábbi táblázat a műveletek az új eszköz támogatja:

| Name (Név)        |
| ----------- |
| Váltás   |
| Kikapcsolás  |

*Kezdeti állapot*

Az alábbi táblázat az eszköz kezdeti állapotát jeleníti meg:

| Name (Név)                     | Értékek |
| ------------------------ | -------|
| Kezdeti színe            | Fehér  |
| Kezdeti fényereje       | 75     |
| Kezdeti fennmaradó élettartama   | 10,000 |
| Kezdeti telemetriai állapota | "a"   |
| Kezdeti telemetriai hőmérséklet | 200   |

Ez az útmutató a lépések elvégzéséhez, aktív Azure-előfizetés szükséges.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató követéséhez lesz szüksége:

* Visual Studio Code. Is [Visual Studio Code letöltése Windows, Mac és Linux](https://code.visualstudio.com/download).
* .NET Core. Letöltheti a [.NET Core for Mac, Linux és Windows](https://www.microsoft.com/net/download).
* [C# a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman. Letöltheti a [Mac, Windows vagy Linux rendszerű Postman](https://www.getpostman.com/apps).
* Egy [üzembe helyezve az Azure-előfizetéshez az IoT hub](../../articles/iot-hub/iot-hub-create-through-portal.md). A jelen útmutató lépéseit az IoT hub kapcsolati karakterláncra van szüksége. A kapcsolati karakterlánc kaphat az Azure Portalról.
* Cosmos DB-adatbázis, amely az SQL API-t használ, és megfelelően van konfigurálva a [erős konzisztencia](../../articles/cosmos-db/how-to-manage-database-account.md). A jelen útmutató lépéseit a Cosmos DB-adatbázis kapcsolati karakterláncra van szüksége. A kapcsolati karakterlánc kaphat az Azure Portalról.

## <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése

Hajtsa végre a következő feladatokat a fejlesztési környezet előkészítését:

* Töltse le az eszköz szimulálása mikroszolgáltatás forrását.
* Töltse le a tárolási adapter mikroszolgáltatás forrását.
* A tárolási adapter mikroszolgáltatások helyi futtatásához.

A jelen cikkben lévő utasítások feltételezik, hogy Windows használata esetén. Ha egy másik operációs rendszert használ, szükség lehet néhány fájlelérési utakat és parancsok a környezethez illeszkedve kell módosítani.

### <a name="download-the-microservices"></a>Töltse le a mikroszolgáltatások

Töltse le és csomagolja ki a [távoli mikroszolgáltatás-alapú figyelési](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) egy megfelelő helyre a helyi gépen a githubról. A cikk feltételezi, hogy ez a mappa nevére **remote-monitoring-services-dotnet-master**.

Töltse le és csomagolja ki a [eszköz szimulálása mikroszolgáltatás](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) egy megfelelő helyre a helyi gépen a githubról. A cikk feltételezi, hogy ez a mappa nevére **eszköz-szimuláció-dotnet-master**.

### <a name="run-the-storage-adapter-microservice"></a>A tárolási adapter mikroszolgáltatás futtatása

Nyissa meg a **remote-monitoring-services-dotnet-master\storage-adapter** mappát a Visual Studio Code-ban. Kattintson bármelyik **visszaállítása** kijavításához gombok feloldatlan függőségek.

Nyissa meg a **storage-adapter/WebService/appsettings.ini** fájlt, és rendelje hozzá a Cosmos DB kapcsolati karakterláncot a **documentDBConnectionString** változó.

A mikroszolgáltatások helyi futtatásához kattintson **Debug > Start Debugging**.

A **terminálon** ablak a Visual Studio Code többek között a webalkalmazás állapot-ellenőrzése egy URL-címet a futó mikroszolgáltatási kimenetét mutatja: [ http://127.0.0.1:9022/v1/status ](http://127.0.0.1:9022/v1/status). Erre a címre lépve, a kell lennie az állapot "OK: Tartási és jól".

Hagyja meg a tárolási adapter mikroszolgáltatás jelen példánya a Visual Studio Code-ban futó a következő lépések végrehajtása.

## <a name="modify-the-chiller"></a>A hűtő módosítása

Ebben a szakaszban, vegyen fel egy új **belső hőmérséklet** telemetriai adatok típusa a meglevő **hűtő** eszköz típusa:

1. Hozzon létre egy új mappát **C:\temp\devicemodels** a helyi gépen.

1. Másolja a következő fájlokat az új mappába, az eszköz szimulálása mikroszolgáltatás letöltött másolatából:

    | Source | Célhely |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-Method.js | C:\temp\devicemodels\scripts\Reboot-Method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. Nyissa meg a **C:\temp\devicemodels\chiller-01.json** fájlt.

1. Az a **InitialState** területen adja hozzá a következő két definíciókat:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Az a **Telemetriai** tömböt, adja hozzá a következő-definíciót:

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

1. Adja hozzá a következő mezőket a **állapot** változó:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Frissítés a **fő** függvényt az alábbiak szerint:

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

Ebben a szakaszban határoz meg egy új **villanykörte** eszköz típusa:

1. Hozzon létre egy fájlt **C:\temp\devicemodels\lightbulb-01.json** , és adja hozzá az alábbi tartalommal:

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

    A módosítások mentése **C:\temp\devicemodels\lightbulb-01.json**.

1. Hozzon létre egy fájlt **C:\temp\devicemodels\scripts\lightbulb-01-state.js** , és adja hozzá az alábbi tartalommal:

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

    A módosítások mentése **C:\temp\devicemodels\scripts\lightbulb-01-state.js**.

1. Hozzon létre egy fájlt **C:\temp\devicemodels\scripts\SwitchOn-method.js** , és adja hozzá az alábbi tartalommal:

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

    A módosítások mentése **C:\temp\devicemodels\scripts\SwitchOn-method.js**.

1. Hozzon létre egy fájlt **C:\temp\devicemodels\scripts\SwitchOff-method.js** , és adja hozzá az alábbi tartalommal:

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

    A módosítások mentése **C:\temp\devicemodels\scripts\SwitchOff-method.js**.

Sikeresen létrehozott egy testre szabott verzióját a **hűtő** eszköztípus és létrehozott egy új **villanykörte** típusú eszközt.

## <a name="test-the-devices"></a>Az eszközök tesztelése

Ebben a szakaszban tesztelni az eszköztípusok helyileg a korábbi szakaszokban létrehozott.

### <a name="run-the-device-simulation-microservice"></a>Az eszköz szimulálása mikroszolgáltatás futtatása

Nyissa meg a **eszköz-szimuláció-dotnet-master** mappát a Visual Studio Code egy új példányát a Githubról letöltötte. Kattintson bármelyik **visszaállítása** kijavításához gombok feloldatlan függőségek.

Nyissa meg a **WebService/appsettings.ini** fájlt, és rendelje hozzá a Cosmos DB kapcsolati karakterláncot a **documentdb_connstring** változó és is módosíthatja a beállításokat az alábbiak szerint:

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

A mikroszolgáltatások helyi futtatásához kattintson **Debug > Start Debugging**.

A **terminálon** ablak a Visual Studio Code-ban a futó mikroszolgáltatási kimenetét jeleníti meg.

Hagyja meg az eszköz szimulálása mikroszolgáltatás jelen példánya a Visual Studio Code-ban futó a következő lépések végrehajtása.

### <a name="set-up-a-monitor-for-device-events"></a>Állítsa be az eszközeseményeket egy figyelő.

Ebben a szakaszban használhatja az Azure CLI-figyelő beállítása csatlakozik az IoT hub az eszközök által küldött telemetriai adatok megtekintéséhez.

Az alábbi parancsfájl feltételezi, hogy az IoT hub nevére **eszköz-szimuláció-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Hagyja meg a figyelő a szimulált eszközök tesztelése során.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>Hozzon létre egy a frissített hűtő eszköz típusa

Ebben a szakaszban használatával a Postman eszközzel kérése az eszköz szimulálása mikroszolgáltatás-szimuláció használatával a frissített hűtő eszköz típusa. Postman olyan eszköz, amely lehetővé teszi a webszolgáltatások REST-kérelmeket küldjön. Postman konfigurálása a szükséges fájlok vannak a helyi példányának a **eszköz-szimuláció-dotnet** tárház.

A Postman beállítása:

1. Nyissa meg a Postmant a helyi gépen.

1. Kattintson a **fájl > Importálás**. Kattintson a **fájlok kiválasztása**.

1. Keresse meg a **eszköz-szimuláció-dotnet-master/docs/postman** mappát. Válassza ki **Azure IoT-eszköz szimulálása megoldás accelerator.postman_collection** és **Azure IoT-eszköz szimulálása megoldás accelerator.postman_environment** kattintson **megnyitása**.

1. Bontsa ki a **Azure IoT-eszköz szimulálása megoldásgyorsító** elküldheti a kérelmekre.

1. Kattintson a **nem környezet** válassza **Azure IoT-eszköz szimulálása megoldásgyorsító**.

Most már rendelkezik egy gyűjtemény és a Postman-munkaterületen, amellyel kommunikálhat az eszköz szimulálása mikroszolgáltatás betöltött környezetben.

Konfigurálja, és a szimuláció futtatása:

1. A Postman-gyűjteményben, válassza ki a **létrehozás módosított hűtő szimuláció** kattintson **küldése**. Ezt a kérést hoz létre a szimulált hűtő eszköz típusú négy példány.

1. A esemény figyelése az Azure parancssori felület ablakában megjelenítheti a szimulált eszközök, például az új származó telemetria **internal_temperature** értékeket.

A szimuláció leállításához válassza ki a **szimuláció leállítása** kérelem Postman, majd kattintson a **küldése**.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>Hozzon létre egy villanykörte eszköztípus

Ebben a szakaszban a Postman eszközzel kérése az eszköz szimulálása mikroszolgáltatás-szimuláció használatával a villanykörte eszköztípus használata. Postman olyan eszköz, amely lehetővé teszi a webszolgáltatások REST-kérelmeket küldjön.

Konfigurálja, és a szimuláció futtatása:

1. A Postman-gyűjteményben, válassza ki a **villanykörte szimuláció létrehozása** kattintson **küldése**. Ezt a kérést hoz létre a szimulált villanykörte eszköztípus két példánya.

1. Az esemény figyelése az Azure parancssori felület ablakában megjelenítheti a szimulált lightbulbs származó telemetria.

A szimuláció leállításához válassza ki a **szimuláció leállítása** kérelem Postman, majd kattintson a **küldése**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Állítsa le a két helyileg futó mikroszolgáltatásokat a Visual Studio Code-példányok (**hibakeresés > Hibakeresés leállításához**).

Ha már nincs szüksége az IoT Hub és a Cosmos DB-példányokhoz, törölheti őket a felesleges költségek elkerülése érdekében az Azure-előfizetésből.

## <a name="next-steps"></a>További lépések

Ez az útmutató láthatta, hogyan hozhat létre egyéni szimulált eszköz típusok, és tesztelje le azokat az eszköz szimulálása mikroszolgáltatások helyi futtatásával.

Megtudhatja, hogyan helyezhet üzembe az egyéni szimulált eszköz típusa, akkor a javasolt következő lépésre az [távoli figyelési megoldásgyorsító](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
