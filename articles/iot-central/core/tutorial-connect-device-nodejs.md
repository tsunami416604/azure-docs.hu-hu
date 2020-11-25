---
title: Oktatóanyag – általános Node.js-ügyfélalkalmazás összekötése az Azure IoT Centraltal | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan lehet egy eszköz fejlesztőként összekötni egy Node.js ügyfélalkalmazás futtató eszközt az Azure IoT Central-alkalmazásba. Az automatikusan létrehozott sablon úgy módosítható, ha olyan nézeteket ad hozzá, amelyek lehetővé teszik, hogy az operátor kommunikáljon egy csatlakoztatott eszközzel.
author: dominicbetts
ms.author: dobett
ms.date: 11/03/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mqtt
- device-developer
- devx-track-js
ms.openlocfilehash: 6175be702f0824ad2cd2b146e96641037407ca0b
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "96018800"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>Oktatóanyag: Ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central-alkalmazáshoz (Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Ebből az oktatóanyagból megtudhatja, hogyan lehet egy Node.js ügyfélalkalmazás csatlakoztatásához az Azure IoT Central-alkalmazáshoz. A Node.js alkalmazás szimulálja egy termosztátos eszköz viselkedését. Amikor az alkalmazás csatlakozik a IoT Centralhoz, elküldi a termosztát-eszköz modell-AZONOSÍTÓját. IoT Central a modell AZONOSÍTÓját használja az eszköz modell lekéréséhez és az eszköz sablonjának létrehozásához. A testreszabásokat és nézeteket hozzá kell adni az eszköz sablonhoz, hogy az operátor kapcsolatba lépjen az eszközzel.

Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Hozza létre és futtassa a Node.js eszköz kódját, és tekintse meg a IoT Central alkalmazáshoz való kapcsolódást ismertető témakört.
> * Az eszközről eljuttatott szimulált telemetria megtekintése.
> * Egyéni nézetek hozzáadása egy eszköz sablonhoz.
> * Tegye közzé az eszköz sablonját.
> * Az eszköz tulajdonságainak kezeléséhez használja a nézetet.
> * Az eszköz vezérléséhez hívja meg a parancsot.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* Egy Azure IoT Central-alkalmazás, amely az **egyéni alkalmazás** sablonnal lett létrehozva. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md). Az alkalmazást a 2020. július 14-én vagy azt követően kell létrehozni.
* A [Node.js](https://nodejs.org/) 6-os vagy újabb verzióját futtató fejlesztési gép. A `node --version` parancssorban futtatva ellenőrizhető a verzió. Az oktatóanyagban szereplő utasítások feltételezik, hogy a Windows-parancssorban futtatja a **Node** parancsot. Számos más operációs rendszeren azonban Node.js is használhatja.
* A [Microsoft Azure IOT SDK ](https://github.com/Azure/azure-iot-sdk-node) helyi másolata Node.jsGitHub-tárházhoz, amely tartalmazza a mintakód. Ezzel a hivatkozással töltheti le az adattár másolatát: [zip letöltése](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Ezután bontsa ki a fájlt egy megfelelő helyre a helyi gépen.

## <a name="review-the-code"></a>A kód áttekintése

A korábban letöltött Node.js Microsoft Azure IoT SDK másolatában nyissa meg az *Azure-IoT-SDK-Node/Device/Samples/PnP/simple_thermostat.js* fájlt egy szövegszerkesztőben.

Amikor futtatja a mintát IoT Centralhoz való csatlakozáshoz, az eszköz kiépítési szolgáltatását (DPS) használja az eszköz regisztrálásához és egy kapcsolati karakterlánc létrehozásához. A minta lekérdezi a DPS által igényelt, a parancssori környezettől kapott kapcsolatok információit.

A `main` metódus:

* Létrehoz egy `client` objektumot, és beállítja a `dtmi:com:example:Thermostat;1` modell azonosítóját a kapcsolatok megnyitása előtt.
* Létrehoz egy parancsfájl-kezelőt.
* Elindít egy hurkot, amely 10 másodpercenként elküldi a hőmérséklet-telemetria.
* A `maxTempSinceLastReboot` tulajdonságot a IoT Central küldi. IoT Central figyelmen kívül hagyja a `serialNumber` tulajdonságot, mert az nem része az eszköz modelljének.
* Egy írható tulajdonságok kezelőjét hozza létre.

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

A `provisionDevice` függvény azt mutatja be, hogy az eszköz hogyan használja a DPS-t a IoT Central regisztrálásához és a kapcsolódáshoz. Az adattartalom tartalmazza a modell AZONOSÍTÓját:

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

A `sendTelemetry` függvény azt mutatja, hogy az eszköz hogyan küldi el a hőmérséklet telemetria IoT Central. A `getCurrentTemperatureObject` metódus egy olyan objektumot ad vissza, amely a következőképpen néz ki `{ temperature: 45.6 }` :

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

A `main` metódus a következő két módszer használatával küldi `maxTempSinceLastReboot` el a tulajdonságot IoT Central. A metódus a következőhöz `main` `createReportPropPatch` hasonló objektummal hív meg `{maxTempSinceLastReboot: 80.9}` :

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

A `main` metódus a következő két módszert használja a _cél hőmérséklet_ írható tulajdonságának IoT Centralból való kezelésére. Figyelje meg, hogy a `propertyUpdateHandle` Válasz hogyan épül fel a verzióra és az állapotkódot:

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
  console.log('updated the property');
};
```

A `main` metódus a következő két módszert használja a parancs hívásának kezelésére `getMaxMinReport` . A `getMaxMinReportObject` METÓDUS JSON-objektumként hozza létre a jelentést:

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

[!INCLUDE [iot-central-connection-configuration](../../../includes/iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>A kód futtatása

A minta alkalmazás futtatásához nyisson meg egy parancssori környezetet, és navigáljon az *Azure-IOT-SDK-Node/Device/Samples/PnP* mappába, amely tartalmazza az *simple_thermostat.js* -mintát.

[!INCLUDE [iot-central-connection-environment](../../../includes/iot-central-connection-environment.md)]

Telepítse a szükséges csomagokat:

```cmd/sh
npm install
```

Minta futtatása:

```cmd/sh
node simple_thermostat.js
```

Az alábbi kimenetben az eszköz regisztrálása és a IoT Centralhoz való csatlakozás látható. A minta ezután elküldi a `maxTempSinceLastReboot` tulajdonságot a telemetria küldésének megkezdése előtt:

```cmd/sh
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](../../../includes/iot-central-monitor-thermostat.md)]

Láthatja, hogy az eszköz hogyan válaszol a parancsokra és a tulajdonságokra:

```cmd/sh
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```

## <a name="view-raw-data"></a>Nyers adattárolók megtekintése

[!INCLUDE [iot-central-monitor-thermostat-raw-data](../../../includes/iot-central-monitor-thermostat-raw-data.md)]

## <a name="next-steps"></a>További lépések

Ha továbbra is szeretne IoT Central oktatóanyagokat használni, és többet szeretne megtudni egy IoT Central megoldás létrehozásáról, tekintse meg a következő témakört:

> [!div class="nextstepaction"]
> [Átjáró-eszközsablon létrehozása](./tutorial-define-gateway-device-type.md)

Most, hogy megismerte, hogyan hozhat létre egy eszközt a Node.js használatával, néhány javasolt következő lépés:

* Olvassa el a [Mi az eszközök sablonjai?](./concepts-device-templates.md) további információ az eszközök sablonjainak szerepéről az eszköz kódjának megvalósításakor.
* Olvassa el az [Azure IoT Centralhoz való csatlakozást](./concepts-get-connected.md) ismertető témakört, amelyből megtudhatja, hogyan regisztrálhat eszközöket a IoT Central, és hogyan IoT Central biztonságossá teszi az eszközök kapcsolatait.
* Olvassa el a [telemetria, a tulajdonságot és a parancs hasznos](concepts-telemetry-properties-commands.md) adatait, ha többet szeretne megtudni az eszköz IoT Centralsal való cseréjéről.
