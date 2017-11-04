---
title: "Hozzon létre egy Azure IoT peremhálózati modul Node.js |} Microsoft Docs"
description: "Ez az oktatóanyag bővíthető adatok BLA konverter moduljának használatával. a legújabb Azure IoT peremhálózati NPM-csomagok és Yeoman írásával generátor."
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.openlocfilehash: e23c4aa7bb3eb4fab18d5a13cbad28e07c18d8f2
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Hozzon létre egy Azure IoT peremhálózati modul Node.js

Ez az oktatóanyag egy modul létrehozása az Azure IoT szegélyt JS bővíthető.

Az oktatóanyag azt bízná környezetben való telepítés és írásával egy [Gedélyezése](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) adatok konverter moduljának használatával. a legújabb Azure IoT peremhálózati NPM-csomagokat.

## <a name="prerequisites"></a>Előfeltételek

Ebben a szakaszban, állítsa be a IoT peremhálózati modul fejlesztési környezetet. Ez egyaránt vonatkozik *64 bites Windows* és *(Ubuntu 14 +) 64 bites Linux* operációs rendszerek.

A következő szoftvereket is szükséges:
* [Git ügyfél](https://git-scm.com/downloads).
* [Csomópont LTS](https://nodejs.org).
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>Architektúra

Az Azure IoT peremhálózati platform fokozottan elfogadja a [Von Neumann architektúra](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Ami azt jelenti, hogy a teljes Azure IoT peremhálózati architektúra egy rendszer, amely feldolgozza a bemeneti és a kimenetet; és győződjön meg arról, hogy minden egyes modul is egy nagyon kicsi bemeneti / kimeneti alrendszer. Ebben az oktatóanyagban a következő két modulok bemutatása után:

1. A modul, amely fogad egy szimulált [Gedélyezése](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) jelezze, és konvertálja azt egy formázott [JSON](https://en.wikipedia.org/wiki/JSON) üzenet.
2. A modul, amely a fogadott kinyomtatja [JSON](https://en.wikipedia.org/wiki/JSON) üzenet.

Az alábbi képen a tipikus végpontok közötti adatfolyam ebben a projektben jeleníti meg:

![Három modulok között Adatfolyamblokk](media/iot-hub-iot-edge-create-module/dataflow.png "bemenet: szimulált BLA modul; Processzor: Konverter modul; Kimenete: Nyomtató modul")

## <a name="set-up-the-environment"></a>A környezet beállítása
Az alábbiakban azt mutatja be gyorsan környezet beállítása az első táblázat konverter modul JS írni elindításához.

### <a name="create-module-project"></a>A modul-projekt létrehozása
1. Nyisson meg egy parancssori ablakot, futtassa `yo az-iot-gw-module`.
2. Kövesse a képernyőn, a modul projekt az inicializálás befejezéséhez.

### <a name="project-structure"></a>Projektstruktúra
Egy JS modul projektet a következő összetevőkből áll:

`modules`-A testreszabott JS modul forrásfájlokat. Cserélje le az alapértelmezett `sensor.js` és `printer.js` saját modul fájlokkal.

`app.js`-Bejegyzés fájl a peremhálózati példány elindítása.

`gw.config.json`-A konfigurációs fájl testreszabása a modulokat Edge által betölteni.

`package.json`-A metaadat-információi modul projekt.

`README.md`-A modul projekt alapvető dokumentációját.


### <a name="package-file"></a>A csomagfájl

Ez `package.json` deklarál, amely tartalmazza a név, verzió, bejegyzést, parancsfájlok, futásidejű és fejlesztési függőségek modul projekthez szükséges minden metaadat-információkat.

Következő kódrészletet BLA konverter mintaprojektet konfigurálása jeleníti meg.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>Bejegyzés fájl
A `app.js` határozza meg a inicializálni az edge-példány. Itt végezheti el semmilyen módosítást nem szükséges.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>Felület modul
Egy Azure IoT Edge-modul is kezelheti, amelynek a feladata, feldolgozónak: fogadja, feldolgozni azt és eredménye.

Lehet, hogy a bemeneti hardverek (például egy mozgásérzékelő) adatait, az egyéb modulok vagy bármely más (például egy időzítő rendszeresen generálja véletlenszerűen) üzenet.

A kimenet a bemeneti hasonló, hogy elindíthatja hardver viselkedés (például a villogó LED), egy üzenetet, amely más modulok vagy bármely más (például a konzol nyomtatás).

Modulok kommunikálnak egymással használatával `message` objektum. A **tartalom** , egy `message` van, amely képes bármilyen típusú adatok, például egy bájttömböt. **Tulajdonságok** is elérhetők a `message` és egyszerűen egy karakterlánc-karakterlánc leképezése. Elképzelhető, hogy a **tulajdonságok** egy HTTPS-kérést, vagy a fájl metaadatait fejlécként.

Egy Azure IoT peremhálózati modulja JS elkészítéséhez hozzon létre egy új module objektum, amely megvalósítja a szükséges módszereket kell `receive()`. Ezen a ponton is választhatja, hogy a nem kötelező végrehajtásához `create()` vagy `start()`, vagy `destroy()` módszerek is. A következő kódrészletet a állványok JS modul objektum jeleníti meg.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>Konverter modul
| Input (Bemenet)                    | Processzor                              | Kimenet                 | Forrásfájl            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Hőmérséklet-adatok üzenet | Elemzése és létrehozni egy új JSON-üzenet | Struktúra JSON üzenet | `converter.js` |

Ez a modul az egy tipikus Azure IoT peremhálózati modul. Hőmérséklet-üzenetek származó adatok elfogad (hardveres modult, vagy ilyen esetben a szimulált BLA modul); és majd normalizálja az hőmérséklet üzenet (beleértve a Hozzáfűzés az Üzenetazonosító, hogy igazolnia kell a hőmérséklet figyelmeztetést, és így tovább tulajdonságának beállítása) strukturált JSON-üzenethez.

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>Nyomtató modul
| Input (Bemenet)                          | Processzor | Kimenet                     | Forrásfájl          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Bármely más modulok érkezett üzenetet: | N/A       | Az üzenet naplózása konzolhoz | `printer.js` |

A modul az egyszerű, értetődő, amely a fogadott üzenetek (tulajdonság, tartalom) kiírja a Terminálszolgáltatások ablakra.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>Konfiguráció
Az utolsó lépés a modulok futtatása előtt a konfigurálása az Azure IoT él és a kapcsolatot létrehozni a modulok között.

Először kell deklarálnia az `node` betöltő (óta Azure IoT peremhálózati támogatja betöltők különböző nyelvű) sikerült hivatkozhat, amely a `name` ezt követően a szakaszokban.

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

A betöltők rendelkezik deklaráltuk, miután is, valamint a modulok deklarálnia kell. Hasonló a betöltők deklaráló, azokat is hivatkozhat a `name` attribútum. Egy modul deklaráló, azt kell adnia a betöltő azt kell használnia (amely az legyen előtt meghatározott) és a belépési pont (a normalizált osztály neve a modul kellene lennie) minden modulhoz. A `simulated_device` modul az egy natív modul, amely az Azure IoT peremhálózati core runtime csomagban található. Tartalmaznak `args` a JSON-ban a fájl akkor is, ha `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

A konfigurációs végén kapcsolatot létesítünk a kapcsolatokat. Minden kapcsolat van kifejezve `source` és `sink`. Kell mindkét hivatkozó egy előre definiált modul. A kimeneti üzenetét `source` modul továbbíthatja a rendszer a bemeneti `sink` modul.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>A modulok fut
1. `npm install`
2. `npm start`

Ha azt szeretné, az alkalmazás befejezéséhez nyomja le az `<Enter>` kulcs.

> [!IMPORTANT]
> Nem ajánlott a Ctrl + C használatával az IoT-Edge alkalmazás befejezéséhez. Mivel így a folyamat rendellenesen eredményezheti.
