---
title: Ismerkedés az Azure IoT Hub-eszközök ikreivel (Csomópont) | Microsoft dokumentumok
description: Az Azure IoT Hub-eszközök twins használata címkék hozzáadásához, majd egy IoT Hub-lekérdezés használatához. Az Azure IoT SDK-k node.js a szimulált eszköz alkalmazás és egy szolgáltatás alkalmazás, amely hozzáadja a címkéket, és futtatja az IoT Hub-lekérdezést.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: elioda
ms.openlocfilehash: 55dc7f73a3e5bbff2e6e331ba0bd7d4088a86536
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110826"
---
# <a name="get-started-with-device-twins-nodejs"></a>Az eszközök ikreinek első lépései (Node.js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Az oktatóanyag végén két Node.js konzolalkalmazás lesz:

* **AddTagsAndQuery.js**, a Node.js háttéralkalmazás, amely címkéket és lekérdezéseket ad hozzá az eszközök twins.AddTagsAndQuery.js , a Node.js back-end app, amely hozzáadja a címkéket és lekérdezések eszköz twins.

* **TwinSimulatedDevice.js**, egy Node.js alkalmazás, amely szimulálja az eszközt, amely csatlakozik az IoT hub az eszköz identitása korábban létrehozott, és jelenti a kapcsolódási feltétel.

> [!NOTE]
> Az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) az Azure IoT SDK-k, amelyek segítségével eszköz- és háttéralkalmazásokat hozhat létre.
>

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Node.js 10.0.x vagy újabb verzió.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy Node.js konzolalkalmazást hoz létre, amely helymetaadatokat ad hozzá a **myDeviceId-hoz**társított ikereszközhöz. Ezután lekérdezi az IT hubban tárolt ikereszközök az Egyesült Államokban található eszközök kiválasztásával, majd azokat, amelyek a mobilhálózati kapcsolatot jelentik.

1. Hozzon létre egy új üres mappát, az **addtagsandqueryapp**néven. Az **addtagsandqueryapp** mappában hozzon létre egy új package.json fájlt a következő paranccsal a parancssorból. A `--yes` paraméter elfogadja az összes alapértelmezést.

    ```cmd/sh
    npm init --yes
    ```

2. Az **addtagsandqueryapp** mappában lévő parancssorból futtassa a következő parancsot az **azure-iothub** csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Szövegszerkesztő használatával hozzon létre egy új **AddTagsAndQuery.js** fájlt az **addtagsandqueryapp** mappában.

4. Adja hozzá a következő kódot az **AddTagsAndQuery.js** fájlhoz. Cserélje `{iot hub connection string}` le az IoT Hub kapcsolati karakterláncát, amelyet [az IoT hub kapcsolati karakterláncának bekapása koralatt](#get-the-iot-hub-connection-string)másolt.

   ``` javascript
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };

                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   ```

    A **rendszerleíró objektum** elérhetővé teszi az összes olyan módszert, amely a szolgáltatásból származó ikereszközökkel való együttműködéshez szükséges. Az előző kód először inicializálja a **rendszerleíró objektumot,** majd lekéri a **myDeviceId**ikereszközét, és végül frissíti a címkéket a kívánt helyadatokkal.

    A címkék frissítése után meghívja a **queryTwins** függvényt.

5. Adja hozzá a következő kódot az **AddTagsAndQuery.js** végén a **queryTwins** függvény megvalósításához:

   ```javascript
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });

            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   ```

    Az előző kód két lekérdezést hajt végre: az első csak a **Redmond43-üzemben** található eszközök ikereszközeit választja ki, a második pedig finomítja a lekérdezést, hogy csak azokat az eszközöket válassza ki, amelyek szintén mobilhálózaton keresztül csatlakoznak.

    Amikor a kód létrehozza a **lekérdezési** objektumot, megadja a visszaküldött dokumentumok maximális számát a második paraméterben. A **lekérdezésobjektum** egy **hasMoreResults** logikai tulajdonságot tartalmaz, amely segítségével a **nextAsTwin** metódusokat többször is meghívhatja az összes eredmény lekéréséhez. A **következő** nevű metódus érhető el az eredmények, amelyek nem eszköz twins, például az összesítési lekérdezések eredményeit.

6. Futtassa az alkalmazást a következővel:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

   Meg kell jelennie egy eszköz az eredmények a lekérdezés tanusítandó összes eszköz **redmond43** és nincs a lekérdezés, amely korlátozza az eredményeket a mobilhálózatot használó eszközök.

   ![Az egyetlen eszköz megtekintése a lekérdezés eredményében](media/iot-hub-node-node-twin-getstarted/service1.png)

A következő szakaszban létrehoz egy eszközalkalmazást, amely jelenti a kapcsolódási adatokat, és módosítja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Node.js konzolalkalmazást, amely **myDeviceId**néven csatlakozik a hubhoz, majd frissíti az ikereszköz jelentett tulajdonságait, hogy tartalmazza a mobilhálózaton keresztül csatlakoztatott információkat.

1. Hozzon létre egy új üres mappát, a reportconnectivity nevű **mappát.** A **jelentéskapcsolódási** mappában hozzon létre egy új package.json fájlt a következő paranccsal a parancssorból. A `--yes` paraméter elfogadja az összes alapértelmezést.

    ```cmd/sh
    npm init --yes
    ```

2. A **jelentéskapcsolódási** mappában lévő parancssorból futtassa a következő parancsot az **azure-iot-device**és az **azure-iot-device-mqtt** csomagok telepítéséhez:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Szövegszerkesztő használatával hozzon létre egy új **ReportConnectivity.js** fájlt a **jelentéskapcsolódási** mappában.

4. Adja hozzá a következő kódot a **ReportConnectivity.js** fájlhoz. Cserélje `{device connection string}` le a **myDeviceId** eszközidentitás létrehozásakor másolt eszközkapcsolati karakterláncot [az IoT hub új eszköz regisztrálása című szolgáltatásban.](#register-a-new-device-in-the-iot-hub)

    ```javascript
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
    ```

    Az **ügyfélobjektum** minden olyan módszert elérhetővé tesz, amely az eszköz twins-i eszköztwins-ekkel való interakcióhoz szükséges. Az előző kód, miután inicializálja az **ügyfél** objektumot, lekéri az eszköz iker **myDeviceId** és frissíti a jelentett tulajdonság a kapcsolódási adatokat.

5. Az eszközalkalmazás futtatása

    ```cmd/sh
        node ReportConnectivity.js
    ```

    Meg kell jelennie az üzenetnek. `twin state reported`

6. Most, hogy az eszköz jelentette a kapcsolódási adatait, mindkét lekérdezésben meg kell jelennie. Lépjen vissza az **addtagsandqueryapp** mappába, és futtassa újra a lekérdezéseket:

    ```cmd/sh
        node AddTagsAndQuery.js
    ```

    Ezúttal **a myDeviceId** mindkét lekérdezési eredményben meg kell jelennie.

    ![MyDeviceId megjelenítése mindkét lekérdezéseredményben](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttéralkalmazásból, és írt egy szimulált eszközalkalmazást az eszközkapcsolati adatok jelentéséhez az ikereszközben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az információkat az SQL-szerű IoT Hub lekérdezési nyelv használatával.

Az alábbi források segítségével megtudhatja, hogyan:

* telemetriai adatok küldése az Eszközökről az Első lépések az [IoT Hubkal](quickstart-send-telemetry-node.md) oktatóanyaggal,

* eszközök konfigurálása az ikereszköz kívánt tulajdonságainak használatával a Kívánt tulajdonságok használata az [eszközök oktatóanyagának konfigurálásához,](tutorial-device-twins.md)

* interaktívan vezérelhető eszközök (például egy ventilátor bekapcsolása egy felhasználó által vezérelt alkalmazásból) a [Közvetlen módszerek használata](quickstart-control-device-node.md) oktatóanyaggal.
