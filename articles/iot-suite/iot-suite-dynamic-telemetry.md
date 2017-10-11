---
title: "Használjon dinamikus telemetriai |} Microsoft Docs"
description: "Ez az oktatóanyag megtudhatja, hogyan használható az Azure IoT Suite távoli felügyeleti előkonfigurált megoldás dinamikus telemetriai."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 0114f27f9b8ae76e1170d04ddf66e2c4bf20686a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Dinamikus telemetriai adatokat a távoli felügyeleti előkonfigurált megoldás

Dinamikus telemetriai lehetővé teszi bármely telemetriai adatokat küldött a távoli felügyeleti előkonfigurált megoldás megjelenítése. A szimulált eszköz, amely az előkonfigurált megoldás üzembe helyezéséhez telemetriát hőmérséklet és a páratartalom, amely az irányítópulton jelenítheti meg. Ha meglévő szimulált eszközök testreszabása, hozzon létre új szimulált eszköz, vagy fizikai eszközök csatlakoztatása az előkonfigurált megoldás az egyéb telemetriai értékek, például a külső hőmérséklet, RPM vagy szélsebesség küldhet. Majd jelenítheti meg az irányítópulton a további telemetriai adatokat.

Ez az oktatóanyag egy egyszerű Node.js szimulált eszköz könnyen módosíthatja dinamikus telemetriai kísérletezhet használja.

Az oktatóanyag elvégzéséhez szüksége lesz:

* Aktív Azure-előfizetés. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].
* [NODE.js] [ lnk-node] verzió 0.12.x vagy újabb.

Az operációs rendszereken, például a Windows vagy Linux, ahol telepítheti a Node.js oktatóanyag hajthatja végre.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>A telemetria-típus hozzáadása

A következő lépés, hogy a telemetriai adatok új értékek vannak beállítva a Node.js szimulált eszköz állítja elő:

1. A Node.js szimulált eszköz leállításához írja be a **Ctrl + C** a parancssor vagy a rendszerhéj.
2. A remote_monitoring.js fájlban megtekintheti a meglévő hőmérséklet, a páratartalom és a külső hőmérséklet telemetriai alapadatokhoz értékeit. Adja meg az alapadatokhoz értékét **rpm** az alábbiak szerint:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. A Node.js szimulált eszköz használ a **generateRandomIncrement** függvény véletlenszerű növekmény hozzáadása alapszintű adatértékek remote_monitoring.js fájlban. Ügyfélfuttatási a **rpm** érték hozzáadásával egy kódsort a meglévő randomizations után az alábbiak szerint:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Az eszköz küld az IoT-központ a JSON-adattartalmat az új rpm érték hozzáadása:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Futtassa a szimulált eszköz Node.js, a következő parancsot:

    `node remote_monitoring.js`

6. Az új RPM telemetriai típus fog megjelenni a diagram az irányítópulton a láthatja:

![Az irányítópult RPM hozzáadása][image3]

> [!NOTE]
> Tiltsa le, majd engedélyezze a a Node.js-eszköz szükség a **eszközök** lap állapotúként jelenik meg azonnal az irányítópulton.

## <a name="customize-the-dashboard-display"></a>Az irányítópult megjelenítéséhez

A **eszközinformáció** hibaüzenet is tartalmazza az eszköz küldhet az IoT-központ telemetriai adatok metaadatait. A metaadatok lehet meghatározni az telemetriai adatokat küld az eszközre. Módosítsa a **deviceMetaData** felvenni a remote_monitoring.js fájlban egy **Telemetriai** definíció alábbi a **parancsok** definition. A következő kódrészletben látható kódot a **parancsok** definition (ne feledje hozzáadni egy `,` után a **parancsok** definition):

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> A távoli felügyeleti megoldás hasonlítsa össze a metaadat-definíciójában a telemetriai adatok adatfolyam adatokat nem használja.


Hozzáadása egy **Telemetriai** definícióját, ahogy az előző kódrészletet az nem változtatja meg az irányítópult viselkedését. Azonban a metaadatok is lehetnek egy **DisplayName** attribútum az irányítópult megjelenítéséhez. Frissítés a **Telemetriai** metaadat-definíciójában, ahogy az a következő kódrészletet:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

Az alábbi képernyőfelvételen látható, hogyan Ez a változás módosítja az irányítópulton a diagram jelmagyarázatának:

![A diagram jelmagyarázatának testreszabása][image4]

> [!NOTE]
> Tiltsa le, majd engedélyezze a a Node.js-eszköz szükség a **eszközök** lap állapotúként jelenik meg azonnal az irányítópulton.

## <a name="filter-the-telemetry-types"></a>A telemetria-típusok szűrése

Alapértelmezés szerint a diagram az irányítópulton látható minden adatsort a telemetria-adatfolyamban. Használhatja a **eszközinformáció** metaadatok az adott telemetriai típusok a diagram a megjelenítését. 

Ahhoz, hogy a diagram megjelenítése csak a hőmérséklet és a páratartalom telemetriai, hagyja el **ExternalTemperature** a a **eszközinformáció** **Telemetriai** metaadat az alábbiak szerint:

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

A **külső hőmérséklet** a diagram már nem jelenik meg:

![Az irányítópult telemetriai adatok szűrése][image5]

Ez a módosítás csak a diagram megjelenítési érinti. A **ExternalTemperature** adatértékek továbbra is tárolja, a háttérbeli feldolgozás érhető el.

> [!NOTE]
> Tiltsa le, majd engedélyezze a a Node.js-eszköz szükség a **eszközök** lap állapotúként jelenik meg azonnal az irányítópulton.

## <a name="handle-errors"></a>Hibák kezelésének

A diagramon megjelenítendő egy adatfolyam a **típus** a a **eszközinformáció** metaadatok meg kell egyeznie a telemetriai adatok értékeinek adatok típusát. Például, ha a metaadatok azt jelenti, hogy a **típus** páratartalom adatok az **int** és egy **dupla** páratartalom telemetriai adatok nem jelennek meg a diagramra, majd a telemetriai adatok adatfolyamban található. Azonban a **páratartalom** értékek továbbra is tárolja, a háttér-feldolgozási érhető el.

## <a name="next-steps"></a>Következő lépések

Most, hogy korábban már látott dinamikus telemetriai használata, többet is megtudhat arról, hogyan az előkonfigurált megoldásokat eszköz adatai: [eszköz információk metaadatait a távoli figyelési megoldást előre konfigurált][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
