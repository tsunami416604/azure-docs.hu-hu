---
title: Eszköz sémája a távoli figyelési megoldásban – Azure | Microsoft Docs
description: Ez a cikk azt a JSON-sémát ismerteti, amely egy szimulált eszközt definiál a távoli figyelési megoldásban.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683744"
---
# <a name="understand-the-device-model-schema"></a>Az eszközmodell sémájának megismerése

A távoli figyelési megoldásban szimulált eszközöket használhat a működésének teszteléséhez. A távoli figyelési megoldás tartalmaz egy eszköz-szimulációs szolgáltatást a szimulált eszközök futtatásához. A távoli figyelési megoldás telepítésekor a rendszer automatikusan kiépíti a szimulált eszközök gyűjteményét. Testreszabhatja a meglévő szimulált eszközöket, vagy létrehozhat sajátt is.

Ez a cikk a szimulált eszköz képességeit és viselkedését meghatározó eszköz-modell sémát ismerteti. Az eszköz modelljét egy JSON-fájlban tárolja a rendszer.

> [!NOTE]
> Ez az eszköz-modell séma csak a Device szimulációs szolgáltatásban üzemeltetett szimulált eszközök esetében használható. Ha valódi eszközt szeretne létrehozni, tekintse meg [az eszköz csatlakoztatása a távoli figyelési megoldáshoz gyorssegédet](iot-accelerators-connecting-devices.md).

A következő cikkek kapcsolódnak az aktuális cikkhez:

* [Az eszköz modellje működésének megvalósítása](iot-accelerators-remote-monitoring-device-behavior.md) a szimulált eszköz viselkedésének megvalósításához használt JavaScript-fájlokat ismerteti.
* [Hozzon létre egy új szimulált eszközt](iot-accelerators-remote-monitoring-create-simulated-device.md) , és bemutatjuk, hogyan helyezhet üzembe egy új szimulált eszközt a megoldásban.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

>[!div class="checklist"]
> * Szimulált eszköz modellének meghatározása JSON-fájllal
> * A szimulált eszköz tulajdonságainak megadása
> * A szimulált eszköz által küldött telemetria meghatározása
> * Adja meg az eszköz által válaszoló felhőből eszközre irányuló módszereket

## <a name="the-parts-of-the-device-model-schema"></a>Az eszköz modell sémájának részei

Az egyes eszközök, például a hűtő vagy a teherautó, a szimulációs szolgáltatás által szimulálható eszköz típusát határozzák meg. Minden eszköz modelljét egy JSON-fájlban tárolja a rendszer a következő legfelső szintű sémával:

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

A [devicemodels mappában](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels) megtekintheti az alapértelmezett szimulált eszközökhöz tartozó sémafájl-fájlokat a githubon.

A következő táblázat a legfelső szintű sémák bejegyzéseit ismerteti:

| Séma bejegyzése | Leírás |
| -- | --- |
| `SchemaVersion` | A séma verziószáma mindig `1.0.0` a fájl formátumára vonatkozik. |
| `Id` | Az eszköz modelljéhez tartozó egyedi azonosító. |
| `Version` | Az eszköz modelljének verzióját azonosítja. |
| `Name` | Az eszköz modelljének felhasználóbarát neve. |
| `Description` | Az eszköz modelljének leírása. |
| `Protocol` | Az eszköz által használt kapcsolati protokoll. A, `AMQP` `MQTT`a és `HTTP`az egyike lehet. |

A következő szakaszok a JSON-séma további szakaszait ismertetik:

## <a name="simulation"></a>Szimuláció

A `Simulation` szakaszban megadhatja a szimulált eszköz belső állapotát. Az eszköz által eljuttatott összes telemetria-értéknek az eszköz állapotának kell lennie.

Az eszköz állapotának definíciója két elemet tartalmaz:

* `InitialState`meghatározza az eszköz állapot objektumának összes tulajdonságának kezdeti értékeit.
* `Script`egy ütemezett JavaScript-fájlt azonosít, amely frissíti az eszköz állapotát. Ezt a parancsfájlt használhatja az eszköz által eljuttatott telemetria-értékek véletlenszerű megadására.

További információ az eszköz állapot objektumát frissítő JavaScript-fájlról: [az eszköz modellje működésének megismerése](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md).

Az alábbi példa egy szimulált hűtő eszköz eszköz állapot objektumának definícióját mutatja be:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

A szimulációs szolgáltatás öt másodpercenként futtatja a **Chiller-01-State. js** fájlt az eszköz állapotának frissítéséhez. Az alapértelmezett szimulált eszközök JavaScript-fájljait a GitHub [Scripts mappájában](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) tekintheti meg. Az ilyen JavaScript-fájlok az utótag **-állapot** alapján különböztetik meg a metódus viselkedését megvalósító fájlokból.

## <a name="properties"></a>Tulajdonságok

A `Properties` séma szakasza határozza meg, hogy az eszköz milyen tulajdonságot ad a megoldásnak. Például:

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

A megoldás indításakor a rendszer lekérdezi az összes szimulált eszközt, hogy felépítse a felhasználói felületen használandó `Type` értékek listáját. A megoldás a és `Latitude` `Longitude` a tulajdonságok használatával adja hozzá az eszköz helyét a térképhez az irányítópulton.

## <a name="telemetry"></a>Telemetria

A `Telemetry` tömb felsorolja a szimulált eszköz által a megoldásnak küldött összes telemetria-típust.

Az alábbi példa egy JSON telemetria üzenetet küld 10 másodpercenként a `floor`, `vibration`a és `temperature` a felvonó érzékelőkből származó adatokkal:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate`meghatározza a szimulált eszköz által küldött JSON-üzenet szerkezetét. A helyőrzői a `MessageTemplate` következő szintaxist `${NAME}` használják `NAME` , ahol az az [eszköz állapot objektumának](#simulation)egyik kulcsa. A sztringeket idézőjelbe kell állítani, a számoknak nem kell szerepelniük.

`MessageSchema`meghatározza a szimulált eszköz által küldött üzenet sémáját. Az üzenet sémája IoT Hub is közzé van téve, hogy a háttérbeli alkalmazások újra felhasználhassa az adatokat a bejövő telemetria értelmezéséhez.

Jelenleg csak a JSON-üzenetek sémái használhatók. A sémában felsorolt mezők a következő típusokból állhatnak:

* Objektum – szerializált JSON használatával
* Bináris – Base64 használatával szerializált
* Szöveg
* Logikai
* Egész szám
* Double
* DateTime

Ha a telemetria üzeneteket különböző időközönként szeretné elküldeni, adjon hozzá több telemetria- `Telemetry` típust a tömbhöz. Az alábbi példa 10 másodpercenként küld hőmérséklet-és páratartalom-adatokat, valamint a fény állapotát percenként:

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

Egy szimulált eszköz válaszolhat az IoT hub által hívott felhőből eszközre irányuló metódusokra. Az `CloudToDeviceMethods` eszköz modellje séma fájljának szakasza:

* A szimulált eszköz által válaszolható metódusok meghatározása.
* A végrehajtandó logikát tartalmazó JavaScript-fájlt azonosítja.

A szimulált eszköz elküldi az általa támogatott metódusok listáját az IoT hub számára, amelyhez csatlakoztatva van.

Ha többet szeretne megtudni az eszköz viselkedését megvalósító JavaScript-fájlról, olvassa el [az eszköz modell működésének megismerése](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)című témakört.

Az alábbi példa három támogatott módszert és a metódusokat megvalósító JavaScript-fájlokat határozza meg:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Az alapértelmezett szimulált eszközök JavaScript-fájljait a GitHub [Scripts mappájában](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts) tekintheti meg. Az ilyen JavaScript **-** fájlok a következő utótaggal vannak elkülönítve: az állapotot megvalósító fájlokból kioszthatják azokat.

## <a name="next-steps"></a>További lépések

Ez a cikk azt ismerteti, hogyan hozhatja létre saját egyéni szimulált eszköz modelljét. Ez a cikk a következőket mutatja be:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Szimulált eszköz modellének meghatározása JSON-fájllal
> * A szimulált eszköz tulajdonságainak megadása
> * A szimulált eszköz által küldött telemetria meghatározása
> * Adja meg az eszköz által válaszoló felhőből eszközre irányuló módszereket

Most, hogy megismerte a JSON-sémát, a javasolt következő lépés a [szimulált eszköz viselkedésének megvalósítását](iot-accelerators-remote-monitoring-device-behavior.md)ismerteti.

A távoli figyelési megoldással kapcsolatos további információkért lásd:

* [Fejlesztői referencia-útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Fejlesztői hibaelhárítási útmutató](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
