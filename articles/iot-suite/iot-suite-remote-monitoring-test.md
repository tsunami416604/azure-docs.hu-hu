---
title: "Eszköz szimulálása a távoli felügyeleti megoldás - Azure |} Microsoft Docs"
description: "Az oktatóanyag bemutatja, az eszköz szimulátor használata a távoli felügyeleti előkonfigurált megoldás."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0e6cc412fdb3ea7b9d8291b9f963e6412ae994a9
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>A megoldás tesztelése szimulált eszközökkel

Az oktatóanyag bemutatja, hogyan használható az eszköz szimulátor mikroszolgáltatási a távoli felügyeleti előkonfigurált megoldás testreszabása. Ez az oktatóanyag két esetben használja a Contoso IoT alkalmazásban megjelenítése az eszköz szimulátor képességeit.

Az első esetben Contoso szeretné egy új intelligens villanykörte eszközt. A tesztek kerülnek végrehajtásra, létrehozhat egy új szimulált eszköz a következő jellemzőkkel:

*Tulajdonságok*

| Név                     | Értékek                      |
| ------------------------ | --------------------------- |
| Szín                    | Fehér, a piros, kék            |
| Fényerő               | 0 és 100 közötti                    |
| Becsült hátralévő élettartamát | 10 000 óra visszaszámlálási |

*Telemetria*

A következő táblázat a ligthbulb a felhőbe, mint egy adatfolyam jelentések adatainak megjelenítése:

| Név   | Értékek      |
| ------ | ----------- |
| status | "a" "off" |
| online | IGAZ, hamis |

> [!NOTE]
> A **online** telemetriai értéke összes szimulált esetében kötelező.

*Módszerek*

Az alábbi táblázat a műveletek az új eszköz támogatja:

| Név        |
| ----------- |
| Kapcsoló   |
| Kikapcsolás  |

*Kezdeti állapot*

A következő táblázat az eszköz kezdeti állapotának megjelenítése:

| Név                     | Értékek |
| ------------------------ | -------|
| Kezdeti szín            | Fehér  |
| Kezdeti fényerő       | 75     |
| Kezdeti fennmaradó élettartama   | 10,000 |
| Kezdeti telemetriai állapota | "a"   |

A második forgatókönyvben hozzáadhat egy új telemetriai típus contoso meglévő **hűtő** eszköz.

Az oktatóanyag bemutatja, az eszköz szimulátor használata a távoli figyelési előkonfigurált megoldást:

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * Hozzon létre egy új eszköz típusa
> * Egyéni eszközviselkedés szimulálása
> * Új eszköz-típus hozzáadása az irányítópulton
> * Egyéni telemetriai adatokat küldhet egy meglévő eszközt típusból

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag van szükség a távoli felügyeleti megoldás telepített példányát az Azure-előfizetésben.

Ha még nem telepítette a távoli figyelési megoldást igényelnek, még el kell végeznie a [a távoli felügyeleti előkonfigurált megoldás üzembe helyezéséhez](iot-suite-remote-monitoring-deploy.md) oktatóanyag.

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>Az eszköz szimuláció szolgáltatás

A eszköz szimuláció szolgáltatás az előkonfigurált megoldás lehetővé teszi, hogy módosítja a beépített szimulált eszköz típusa, és hozzon létre új szimulált eszköz típusa. Egyéni eszköztípus segítségével tesztelje a működését a távoli figyelési megoldást igényelnek, mielőtt a fizikai eszközök csatlakoznak a megoldás.

## <a name="create-a-simulated-device-type"></a>Hozzon létre egy szimulált eszköz típusa

A legegyszerűbb hozzon létre egy új eszköztípus a szimuláció mikroszolgáltatási módja másolása és egy már meglevő típus módosítása. A következő lépések bemutatják, hogyan másolhatja a beépített **hűtő** hozzon létre egy új eszköz **villanykörte** eszköz:

1. Az alábbi parancs segítségével klónozza a **eszköz-szimuláció** GitHub-tárházban a helyi számítógépen:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Minden eszköz rendelkezik a modell JSON-fájl és a kapcsolódó parancsprogramokat a `Services/data/devicemodels` mappát. Másolás a **hűtő** fájlokat, hogy a **villanykörte** fájlok az alábbi táblázatban látható módon:

    | Forrás                      | Cél                   |
    | --------------------------- | ----------------------------- |
    | hűtő-01.json             | Villanykörte-01.json             |
    | parancsfájlok/hűtő-01-state.js | parancsfájlok/villanykörte-01-state.js |
    | parancsfájlok/újraindítás-method.js    | parancsfájlok/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Adja meg az új eszköztípus jellemzői

A `lightbulb-01.json` fájl határozza meg a típus jellemzői, például a telemetriai adatokat hoz létre, és a módszereket támogatja. Az alábbi lépéseket a frissítés a `lightbulb-01.json` fájlt adja meg a **villanykörte** eszköz:

1. Az a `lightbulb-01.json` fájlt, az eszköz metaadatainak frissítéséhez, ahogy az a következő kódrészletet:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. Az a `lightbulb-01.json` fájl, a szimuláció definition frissítése, ahogy az a következő kódrészletet:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. Az a `lightbulb-01.json` fájlt, az eszköz típus tulajdonságainak frissítéséhez látható módon a következő kódrészletet:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. Az a `lightbulb-01.json` fájl, a eszköz típusa telemetriai definíciók frissítése, ahogy az a következő kódrészletet:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. Az a `lightbulb-01.json` fájlt, az eszköz metódusai frissítése, ahogy az a következő kódrészletet:

    ```json
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
    ```

1. Mentse a `lightbulb-01.json` fájlt.

### <a name="simulate-custom-device-behavior"></a>Egyéni eszközviselkedés szimulálása

A `scripts/lightbulb-01-state.js` fájl szimuláció viselkedését határozza meg a **villanykörte** típusa. Az alábbi lépéseket a frissítés a `scripts/lightbulb-01-state.js` viselkedésének meghatározása a fájl a **villanykörte** eszköz:

1. Az állapot definíciójának szerkesztése a `scripts/lightbulb-01-state.js` fájl, ahogy az a következő kódrészletet:

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. Cserélje le a **eltérő** függvényt a következő **tükrözés** függvény:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Szerkessze a **fő** funkció viselkedését végrehajtásához, ahogy az az alábbi kódrészletet:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Mentse a `scripts/lightbulb-01-state.js` fájlt.

A `scripts/SwitchOn-method.js` valósít meg fájlt a **kapcsoló a** metódust egy **villanykörte** eszköz. Az alábbi lépéseket a frissítés a `scripts/SwitchOn-method.js` fájlt:

1. Az állapot definíciójának szerkesztése a `scripts/SwitchOn-method.js` fájl, ahogy az a következő kódrészletet:

    ```js
    var state = {
       status: "on"
    };
    ```

1. A villanykörte váltani, szerkesztheti a **fő** működéséhez az alábbiak szerint:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Mentse a `scripts/SwitchOn-method.js` fájlt.

1. Készítsen másolatot a `scripts/SwitchOn-method.js` nevű `scripts/SwitchOff-method.js`.

1. A villanykörte kikapcsolni, szerkessze a **fő** működni a `scripts/SwitchOff-method.js` fájlt az alábbiak szerint:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Mentse a `scripts/SwitchOff-method.js` fájlt.

### <a name="test-the-lightbulb-device-type"></a>A villanykörte eszköztípus tesztelése

Tesztelheti a **villanykörte** eszköztípus, először tesztelheti az eszköz típusának viselkedik-e egy helyi példányát futtató által várt a **eszköz-szimuláció** szolgáltatás. Amikor tesztelése, és az új eszköz típusának helyileg indítja, építse újra a tárolót, és telepítse újra a **eszköz-szimuláció** szolgáltatás az Azure-bA.

Tesztelése és hibakeresése a módosításokat a helyi [eszköz szimuláció áttekintése](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md).

Másolja az új projekt konfigurálása **villanykörte** eszköz fájlok a kimeneti könyvtárba:

* Ha a Visual Studio használ, ellenőrizze, hogy adja hozzá az előző szakaszban létrehozott három új villanykörte fájlokat a **szolgáltatások** projektre a megoldásban. Ezután **megoldáskezelőben** megjelölhetők a kimeneti könyvtárba kell másolni.

* Ha a Visual Studio Code használ, nyissa meg a **Services.csproj** fájlt, és adja hozzá az előző szakaszban létrehozott három új villanykörte fájlokat. Tekintse meg a meglévő eszköz modell fájl bejegyzések a **Services.csproj** fájl példaként.

Az új eszköz a telepített megoldás teszteléséhez valamelyike látható:

* [Egyéni docker-hub fiókból tárolók üzembe helyezése](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [A frissítés manuális másolatot keresztül telepített tárolója](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

Az a **eszközök** lapon megadhat az új típusú példányok:

![Rendelkezésre álló szimulációja listájának megtekintése](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

A szimulált eszköz telemetriai tekintheti meg:

![Nézet villanykörte telemetriai adat](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Hívása a **SwitchOn** és **SwitchOff** módszerek az eszközön:

![Hívás villanykörte módszerek](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Tekintse meg az új eszköztípus Azure üzembe helyezése a Docker-lemezkép készítéséhez [testreszabott Docker-lemezkép](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="add-a-new-telemetry-type"></a>Új telemetria-típus hozzáadása

Ez a szakasz ismerteti, hogyan lehet módosítani egy meglévő szimulált eszköz típusa egy új telemetriai típus támogatásához.

### <a name="locate-the-chiller-device-type-files"></a>Keresse meg a hűtő eszköz típusú fájlok

A következő lépések bemutatják, hol találhatók a fájlok, amelyek meghatározzák a beépített **hűtő** eszköz:

1. Ha még nem tette meg, a következő paranccsal klónozásához a **eszköz-szimuláció** GitHub-tárházban a helyi számítógépen:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Minden eszköz rendelkezik a modell JSON-fájl és a kapcsolódó parancsprogramokat a `Services/data/devicemodels` mappát. A fájlokat, amelyek meghatározzák a szimulált **hűtő** eszköz típusa:
    * `Services/data/devicemodels/chiller-01.json`
    * `Services/data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>Adja meg azt az új telemetriai adat

A következő lépések bemutatják a adjon hozzá egy új **belső hőmérséklet** típus a **hűtő** eszköz típusa:

1. Nyissa meg az `chiller-01.json` fájlt.

1. Frissítés a **sémaverzióval** érték az alábbiak szerint:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. Az a **InitialState** területen írja be a következő két definíciók:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Az a **Telemetriai** tömb, adja hozzá a következő definícióját:

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

1. Mentse a `chiller-01.json` fájlt.

1. Nyissa meg az `scripts/chiller-01-state.js` fájlt.

1. Adja hozzá a következő mezőket a **állapot** változó:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Adja hozzá a következő sort a **fő** függvény:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Mentse a `scripts/chiller-01-state.js` fájlt.

### <a name="test-the-chiller-device-type"></a>Tesztelje a hűtő eszköz típusa

A frissített teszteléséhez **hűtő** eszköztípus, először tesztelheti az eszköz típusának viselkedik-e egy helyi példányát futtató által várt a **eszköz-szimuláció** szolgáltatás. Ha tesztelni, és helyileg a frissített eszköztípus indítja, építse újra a tárolót, és telepítse újra a **eszköz-szimuláció** szolgáltatás az Azure-bA.

Amikor futtatja a **eszköz-szimuláció** szolgáltatás helyileg küld telemetriai adatokat a távoli felügyeleti megoldás. Az a **eszközök** lap, a frissített típusú példányok létesíthet.

Tesztelése és hibakeresése a módosításokat a helyi [a szolgáltatás fut a Visual Studio](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) vagy [hozza létre, és futtassa a parancssorból](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line).

Az új eszköz a telepített megoldás teszteléséhez valamelyike látható:

* [Egyéni docker-hub fiókból tárolók üzembe helyezése](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [A frissítés manuális másolatot keresztül telepített tárolója](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

Az a **eszközök** lapon létesíthet a frissített típusú példányok:

![Vegye fel a frissített hűtő](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Megtekintheti az új **belső hőmérséklet** a szimulált eszköz telemetriai adatokat.

Tekintse meg az új eszköztípus Azure üzembe helyezése a Docker-lemezkép készítéséhez [testreszabott Docker-lemezkép](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image).

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan számára:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Hozzon létre egy új eszköz típusa
> * Egyéni eszközviselkedés szimulálása
> * Új eszköz-típus hozzáadása az irányítópulton
> * Egyéni telemetriai adatokat küldhet egy meglévő eszközt típusból

Most már rendelkezik megtudta, hogyan használhatja az eszköz szimuláció szolgáltatást, a javasolt következő lépésre megtudhatja, hogyan [egy fizikai eszköz csatlakozni a távoli felügyeleti megoldás](iot-suite-connecting-devices-node.md).

Fejlesztői kapcsolatos további információkért a távoli felügyeleti megoldás az alábbi témakörben talál:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->