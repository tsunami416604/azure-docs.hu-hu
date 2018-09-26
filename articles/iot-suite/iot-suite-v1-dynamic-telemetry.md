---
title: Dinamikus telemetria használata |} A Microsoft Docs
description: Ez az oktatóanyag további dinamikus telemetria használata az Azure IoT Suite távoli figyelési előre konfigurált megoldás.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 60e9ee00fabf15a62e782c70bca251b1a8e617c3
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096616"
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Dinamikus telemetria használata a távoli figyelési előre konfigurált megoldás

Dinamikus telemetria megjelenítése semmilyen telemetriai adatot küld a távoli figyelési előre konfigurált megoldás lehetővé teszi. A szimulált eszközök, amelyek az előre konfigurált megoldás üzembe helyezéséhez küldjön hőmérséklettel és páratartalommal kapcsolatos telemetriai adatokat, amelyek az irányítópulton jelenítheti meg. Ha meglévő szimulált eszközök testreszabása, új szimulált eszköz létrehozása vagy fizikai eszközök csatlakoztatása az előre konfigurált megoldás elküldheti egyéb telemetriai értékeket, például a külső hőmérsékletet, RPM vagy szélsebesség. Majd a további telemetria az irányítópulton jelenítheti meg.

Ebben az oktatóanyagban egy egyszerű Node.js szimulált eszköz egyszerűen kísérletezhet a dinamikus telemetria módosíthatja.

Az oktatóanyag elvégzéséhez szüksége lesz:

* Aktív Azure-előfizetés. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].
* [NODE.js] [ lnk-node] verzió 0.12.x vagy újabb.

Ebben az oktatóanyagban bármely operációs rendszerben, mint például a Windows vagy Linux rendszerű, ahol telepítheti a Node.js hajthatja végre.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Egy telemetriai típus hozzáadása

A következő lépés, hogy cserélje le az új értékek vannak beállítva a Node.js szimulált eszköz által létrehozott telemetriát:

1. Írja be a Node.js szimulált eszköz leállítása **Ctrl + C** a parancssort vagy a rendszerhéjat.
2. A remote_monitoring.js fájlt láthatja a meglévő hőmérséklet, a páratartalmat és a külső hőmérsékleti telemetria alapadatok értékeit. Adjon meg egy értéket alapadatok **rpm** módon:

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. A Node.js a szimulált eszközt használja a **generateRandomIncrement** függvény hozzáadása egy véletlenszerű növekmény az alap adatértékek remote_monitoring.js fájlban. Véletlenszerűvé tétele a **rpm** hozzáadásával egy sor kódot a meglévő randomizations után a következő értéket:

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Az új rpm érték hozzáadása a JSON-adattartalom az eszköz küld az IoT hubnak:

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. A szimulált eszköz Node.js, a következő parancs futtatásával:

    `node remote_monitoring.js`

6. Figyelje meg az új RPM telemetriai típus, amely a diagramon az irányítópulton jeleníti meg:

![RPM hozzáadása az irányítópulthoz][image3]

> [!NOTE]
> Tiltsa le, majd engedélyezze a Node.js eszközoldali a szeretne a **eszközök** a változás azonnal megtekintéséhez az irányítópult lapon.

## <a name="customize-the-dashboard-display"></a>Irányítópult megjelenítéséhez

A **Device-Info** üzenetet is tartalmazhat az eszköz IoT hubbal küldhet telemetriát metaadatait. A metaadatok lehet meghatározni az telemetriát küld az eszközre. Módosítsa a **deviceMetaData** tartalmazza a remote_monitoring.js fájlban egy **Telemetriai** definíció következő a **parancsok** definíciója. A következő kód kódrészletet mutat be a **parancsok** definition (adja hozzá a `,` után a **parancsok** definíció):

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
> A távoli figyelési megoldás a kis-és egyezést használatával hasonlítsa össze az adatok használata a telemetria-adatfolyam metaadatainak definíciója.


Hozzáadás egy **Telemetriai** definícióját a fenti kódrészletben látható módon nem változtatja meg az irányítópultot. Azonban a metaadatok is lehetnek egy **DisplayName** attribútum az irányítópult megjelenítéséhez. Frissítés a **Telemetriai** metaadatainak definíciója az alábbi kódrészletben látható módon:

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

Az alábbi képernyőképen látható, hogyan módosítja ezt a módosítást a a diagram jelmagyarázatának az irányítópulton:

![A diagram jelmagyarázatának testreszabása][image4]

> [!NOTE]
> Tiltsa le, majd engedélyezze a Node.js eszközoldali a szeretne a **eszközök** a változás azonnal megtekintéséhez az irányítópult lapon.

## <a name="filter-the-telemetry-types"></a>Szűrés a telemetriai adatok típusa

Alapértelmezés szerint a diagramot az irányítópulton a telemetriai adatok Stream minden egyes adatsorra jeleníti meg. Használhatja a **Device-Info** metaadatokra ahhoz, hogy adott telemetriatípusok a diagramra a megjelenítését. 

Ahhoz, hogy a diagram megjelenítése egyetlen hőmérséklettel és páratartalommal kapcsolatos telemetriai adatokat, hagyja ki a következő **ExternalTemperature** származó a **Device-Info** **Telemetriai** metaadatait az alábbiak szerint:

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

A **öltözet hőmérséklet** már nem jelenik meg a diagramon:

![Az irányítópulton a telemetria szűrése][image5]

Ez a változás csak a diagramon megjelenítendő van hatással. A **ExternalTemperature** adatértékek továbbra is tárolja és elérhetővé minden jut a háttérbeli feldolgozásnak.

> [!NOTE]
> Tiltsa le, majd engedélyezze a Node.js eszközoldali a szeretne a **eszközök** a változás azonnal megtekintéséhez az irányítópult lapon.

## <a name="handle-errors"></a>Hibáinak kezelése

A diagramon megjeleníthető egy adatfolyam a **típus** a a **Device-Info** metaadatok meg kell egyeznie a telemetriaértékeket adattípusát. Például, ha a metaadatok azt jelenti, hogy a **típus** nedvességtartalma adatok van **int** és a egy **dupla** a telemetria-adatfolyam megtalálható, majd a páratartalommal kapcsolatos telemetriai adatok nem jelennek meg a diagramon. Azonban a **páratartalom** értékek továbbra is tárolja és elérhetővé minden háttérbeli feldolgozásra.

## <a name="next-steps"></a>További lépések

Most, hogy megtudhatta, hogyan használhatja a dinamikus telemetria, további az előre konfigurált megoldások használatával eszközadatokat kapcsolatos: [eszköz információk metaadatait a távoli figyelési előre konfigurált megoldás] [ lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[image3]: media/iot-suite-v1-dynamic-telemetry/image3.png
[image4]: media/iot-suite-v1-dynamic-telemetry/image4.png
[image5]: media/iot-suite-v1-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
