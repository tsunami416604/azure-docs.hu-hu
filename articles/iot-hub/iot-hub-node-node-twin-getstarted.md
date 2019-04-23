---
title: Ikereszközök Azure IoT Hub (Node) – első lépések |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub device twins címkéket adhat hozzá, majd az IoT Hub-lekérdezést. Az Azure IoT SDK for Node.js használatával valósítható meg a szimulált eszközalkalmazás és a egy szolgáltatás-alkalmazást, amely hozzáadja a címkék és az IoT Hub-lekérdezést.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: f93abac563d47f6505f42d29e882698ef31174bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795885"
---
# <a name="get-started-with-device-twins-node"></a>Első lépések az ikereszközökhöz (Node)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ez az oktatóanyag végén két Node.js-konzolalkalmazással fog rendelkezni:

* **AddTagsAndQuery.js**, a Node.js-háttér-alkalmazást, amely címkét ad hozzá, és lekérdezi az ikereszközök.

* **TwinSimulatedDevice.js**, a Node.js-alkalmazást, amely szimulálja a olyan eszköz, amely az IoT hubhoz a korábban létrehozott eszközidentitással, és jelenti a kapcsolat állapotát.

> [!NOTE]
> A cikk [Azure IoT SDK-k](iot-hub-devguide-sdks.md) használható eszköz és a háttér-alkalmazásokat hozhat létre az Azure IoT SDK-kkal kapcsolatos információkat biztosít.
>

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* A Node.js 4.0.x vagy újabb verziója.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Az alkalmazás létrehozása

Ebben a szakaszban egy Node.js-konzolalkalmazást, amely a hely metaadatokat ad hozzá az ikereszköz társított létrehozása **myDeviceId**. Ezután lekérdezi az ikereszközök tárolja az IoT hub kiválasztása az eszközök, az Egyesült Államok, és amelyekre a mobilhálózati kapcsolat jelent.

1. Hozzon létre egy új üres nevű **addtagsandqueryapp**. Az a **addtagsandqueryapp** mappában hozzon létre egy új package.json fájlt a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. A parancssorban a **addtagsandqueryapp** mappában futtassa a következő paranccsal telepíthető a **azure-iothub** csomag:
   
    ```
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **AddTagsAndQuery.js** fájlt a **addtagsandqueryapp** mappát.

4. Adja hozzá a következő kódot a **AddTagsAndQuery.js** fájlt, és cserélje le a **{iot hub kapcsolati karakterláncra}** helyőrzőt az IoT Hub kapcsolati karakterláncra az eseményközpont kimásolt:

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

    A **beállításjegyzék** vezérlőnek az ikereszközökhöz, a szolgáltatás használatához szükséges összes módszert. Az előző kód először inicializálja a **beállításjegyzék** objektumot, majd lekéri az ikereszközön **myDeviceId**, és végül frissíti a címkéket a kívánt helyre adatokkal.

    A címkék frissítését követően meghívja a **queryTwins** függvény.

5. Adja hozzá a következő kódot végén **AddTagsAndQuery.js** megvalósításához a **queryTwins** függvény:

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

    Az előző kód két lekérdezést hajt végre: az első kiválasztja a csak az ikereszközök található eszközök a **Redmond43** gépek és a második megjeleníthető a lekérdezést, válassza ki a keresztül mobilhálózati is csatlakozó eszközöket.

    Az előző kód, amikor létrehozza a **lekérdezés** objektumazonosító, a visszaadott dokumentumok maximális számát határozza meg. A **lekérdezés** az objektum tartalmaz egy **hasmoreresults használatával** logikai tulajdonság, amely segítségével meghívása a **nextAsTwin** módszerek többször az összes eredmény beolvasása. A metódus hívása **tovább** eredményeket nem ikereszközök, például összesítési lekérdezések eredményeit érhető el.

6. Futtassa az alkalmazást:

    ```
        node AddTagsAndQuery.js
    ```

   Megjelenik a lekérdezés feltevéséhez az eredmények között egy eszközön található összes eszköz **Redmond43** sem a lekérdezést, amely korlátozza az eredményeket a mobilhálózati használó eszközöket.
   
    ![Tekintse meg a lekérdezési eredményekben az egyik eszköz](media/iot-hub-node-node-twin-getstarted/service1.png)

A következő szakaszban, hogy egy eszközalkalmazás létrehozása, amely jelent a kapcsolati adatokat, és módosítja az előző szakaszban a lekérdezés eredménye.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban egy Node.js-konzolalkalmazást, amely csatlakozik a hubhoz, létrehozhat **myDeviceId**, és ezután a frissítések az ikereszköz a jelentett tulajdonságok alapján, hogy csatlakoztatva van mobilhálózat használata adatokat tartalmazzák.

1. Hozzon létre egy új üres nevű **reportconnectivity**. Az a **reportconnectivity** mappában hozzon létre egy új package.json fájlt a következő parancsot a parancssorba. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```

2. A parancssorban a **reportconnectivity** mappában futtassa a következő paranccsal telepíthető a **azure-iot-device**, és **azure-iot-device-mqtt** csomag:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **ReportConnectivity.js** fájlt a **reportconnectivity** mappát.

4. Adja hozzá a következő kódot a **ReportConnectivity.js** fájlt, és cserélje le a **{eszköz kapcsolati karakterláncának}** helyőrzőt az eszköz kapcsolati karakterláncának létrehozása után a kimásolt**myDeviceId** eszközidentitás:

    ```
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

    A **ügyfél** vezérlőnek az ikereszközökhöz az eszközről való kommunikációhoz szükséges összes módszert. Az előző kód után inicializálja a **ügyfél** objektumazonosító, lekéri az ikereszközön **myDeviceId** , és frissíti a jelentett tulajdonságként a kapcsolati információkat.

5. Az eszköz alkalmazás futtatása

    ```   
        node ReportConnectivity.js
    ```

    Az üzenetnek kell megjelennie `twin state reported`.

6. Most, hogy az eszköz jelenik meg a kapcsolati információkat, akkor meg kell jelennie mindkét lekérdezést. Lépjen vissza a **addtagsandqueryapp** mappát, és futtassa újból a lekérdezést:

    ```   
        node AddTagsAndQuery.js
    ```

    Ezúttal **myDeviceId** meg kell jelennie mindkét lekérdezés eredményeit.

    ![Mindkét lekérdezés eredményében myDeviceId megjelenítése](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Címkeként, egy háttér-alkalmazásból hozzáadott eszközök metaadatait, és a egy szimulált eszközalkalmazás zapsáno do kapcsolat eszközadatokat a jelentés azokat az ikereszköz. Azt is megtanulta, hogyan kérdezhet le ezt az információt az SQL-szerű az IoT Hub lekérdezési nyelv segítségével.

Az alábbi forrásanyagokból megtudhatja, hogyan lehet:

* Telemetriát az eszközökről a [IoT Hub használatának első lépései](quickstart-send-telemetry-node.md) oktatóanyagban

* konfigurálhatja az eszközöket használó eszköz ikereszköz kívánt tulajdonságait a a [használata kívánt tulajdonságok konfigurálhatja az eszközöket](tutorial-device-twins.md) oktatóanyagban

* Az eszközök, interaktív módon (például bekapcsolása egy felhasználó által felügyelt alkalmazásból ventilátor), szabályozhatja a [közvetlen metódusok használata](quickstart-control-device-node.md) oktatóanyag.