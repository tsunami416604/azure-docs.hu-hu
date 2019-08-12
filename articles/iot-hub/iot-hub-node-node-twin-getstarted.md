---
title: Ismerkedés az Azure IoT Hub Device ikrek (node) szolgáltatással | Microsoft Docs
description: Az Azure IoT Hub-eszközök ikrek használata címkék hozzáadásához és IoT Hub-lekérdezés használatához. A Node. js-hez készült Azure IoT SDK-k segítségével megvalósíthatja a szimulált eszköz alkalmazást és egy olyan szolgáltatási alkalmazást, amely hozzáadja a címkéket, és futtatja a IoT Hub lekérdezést.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: eb8b7c62e765ab233b50310dca9c21e9728ed56a
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667999"
---
# <a name="get-started-with-device-twins-nodejs"></a>Ismerkedés az eszközökhöz tartozó ikrekkel (node. js)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Az oktatóanyag végén két Node. js-konzollal rendelkező alkalmazás lesz:

* **AddTagsAndQuery. js**, Node. js háttérbeli alkalmazás, amely címkéket és lekérdezéseket tesz az ikrek számára.

* **TwinSimulatedDevice. js**, egy Node. js-alkalmazás, amely egy olyan eszközt szimulál, amely az IoT hubhoz a korábban létrehozott eszköz identitásával csatlakozik, és a kapcsolati feltételét jelzi.

> [!NOTE]
> Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k cikke olyan Azure IoT SDK-kat tartalmaz, amelyek segítségével mind az eszközök, mind a háttérbeli alkalmazások készíthetők.
>

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Node. js 10.0. x vagy újabb verzió.

* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy Node. js-konzol alkalmazást hoz létre, amely a hely metaadatainak hozzáadását adja a **myDeviceId**-hez társított eszközökhöz. Ezután lekérdezi az IoT hub-ban tárolt, az USA-ban található eszközöket, majd a mobil kapcsolatot jelentő eszközöket.

1. Hozzon létre egy új, **addtagsandqueryapp**nevű üres mappát. A **addtagsandqueryapp** mappában hozzon létre egy új Package. JSON fájlt a következő parancs használatával a parancssorban. Fogadja el az összes alapértelmezett beállítást:

    ```
    npm init
    ```

2. A **addtagsandqueryapp** mappában a parancssorban futtassa a következő parancsot az **Azure-iothub** csomag telepítéséhez:
   
    ```
    npm install azure-iothub --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **AddTagsAndQuery. js** fájlt a **addtagsandqueryapp** mappában.

4. Adja hozzá a következő kódot a **AddTagsAndQuery. js** fájlhoz, és cserélje le a **{IOT hub kapcsolódási karakterlánc}** helyőrző értékét a korábban a [IOT hub kapcsolódási karakterláncának](#get-the-iot-hub-connection-string)lekérése során másolt IoT hub kapcsolódási karakterláncra:

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

    A **beállításjegyzék** -objektum az összes olyan metódust elérhetővé teszi, amely az eszközökhöz tartozó ikrek szolgáltatással való interakcióhoz szükséges. Az előző kód először inicializálja a **beállításjegyzék** -objektumot, majd lekéri a **myDeviceId**, és végül frissíti a címkéket a kívánt hely adataival.

    A címkék frissítése után meghívja a **queryTwins** függvényt.

5. Adja hozzá a következő kódot a **AddTagsAndQuery. js** végén a **queryTwins** függvény megvalósításához:

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

    Az előző kód két lekérdezést hajt végre: az első csak a **Redmond43** -üzemben található eszközökhöz tartozó ikreket választja ki, a második pedig csak azokat az eszközöket jelöli ki, amelyek a mobil hálózaton keresztül is csatlakoztatva vannak.

    A korábbi kód a **lekérdezési** objektum létrehozásakor megadja a visszaadott dokumentumok maximális számát. A **lekérdezési** objektum egy **hasMoreResults** logikai tulajdonságot tartalmaz, amelyet a **nextAsTwin** metódusok többszöri meghívására használhat az összes eredmény lekéréséhez. A **következő** nevű metódus elérhető az olyan eredmények esetében, amelyek nem az eszközök ikrek, például az összesítési lekérdezések eredményei.

6. Futtassa az alkalmazást az alábbiakkal:

    ```
        node AddTagsAndQuery.js
    ```

   A lekérdezés eredményei között egy eszközt kell látnia, amely a **Redmond43** -ban található összes eszközt kéri, a nem pedig a lekérdezést, amely a mobil hálózatot használó eszközökre korlátozza az eredményeket.
   
    ![A lekérdezés eredményei között található egyetlen eszköz megtekintése](media/iot-hub-node-node-twin-getstarted/service1.png)

A következő szakaszban létrehoz egy olyan eszközt, amely jelentést készít a kapcsolati adatokról, és megváltoztatja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszköz-alkalmazás létrehozása

Ebben a szakaszban egy Node. js-konzol alkalmazást hoz létre, amely a **myDeviceId**-hez csatlakozik a központhoz, majd frissíti az eszköz két jelentett tulajdonságát, hogy tartalmazza a mobil hálózaton keresztül csatlakoztatott adatokat.

1. Hozzon létre egy új, **reportconnectivity**nevű üres mappát. A **reportconnectivity** mappában hozzon létre egy új Package. JSON fájlt a következő parancs használatával a parancssorban. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```

2. A **reportconnectivity** mappában a parancssorban futtassa a következő parancsot az **Azure-IOT-Device**és az **Azure-IOT-Device-mqtt** csomag telepítéséhez:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy új **ReportConnectivity. js** fájlt a **ReportConnectivity** mappában.

4. Adja hozzá a következő kódot a **ReportConnectivity. js** fájlhoz, és cserélje le a **{Device kapcsolódási karakterlánc}** helyőrzőt az **myDeviceId** -eszköz identitásának létrehozásakor másolt eszköz-kapcsolódási karakterláncra:

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

    Az **ügyfél** -objektum minden olyan módszert feltesz, amely az eszközön található ikrekkel való interakcióhoz szükséges. Az előző kód azt követően, hogy inicializálta az **ügyféltanúsítványt** , lekéri az **myDeviceId** , és frissíti a jelentett tulajdonságot a kapcsolati információkkal.

5. Az eszköz alkalmazás futtatása

    ```   
        node ReportConnectivity.js
    ```

    Ekkor megjelenik az üzenet `twin state reported`.

6. Most, hogy az eszköz bejelentette a kapcsolati adatait, mindkét lekérdezésben szerepelnie kell. Lépjen vissza a **addtagsandqueryapp** mappába, és futtassa újra a lekérdezéseket:

    ```   
        node AddTagsAndQuery.js
    ```

    Ez az idő **myDeviceId** mindkét lekérdezési eredményben szerepelnie kell.

    ![MyDeviceId megjelenítése mindkét lekérdezési eredményben](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttérbeli alkalmazáshoz, és írt egy szimulált eszközt, amely az eszköz kapcsolati adatait jelenti a Twin-ben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az adatokat az SQL-Like IoT Hub lekérdezési nyelv használatával.

Az alábbi források segítségével megismerheti a következőket:

* telemetria küldése az eszközökről az első [lépések IoT hub](quickstart-send-telemetry-node.md) oktatóanyaggal

* eszközök konfigurálása a kívánt tulajdonságokkal a kívánt tulajdonságok használatával az [eszközök konfigurálásához](tutorial-device-twins.md) oktatóanyag,

* vezérelheti az eszközöket interaktív módon (például egy felhasználó által vezérelt alkalmazásból való bekapcsolással), a [Direct Methods oktatóanyag használatával](quickstart-control-device-node.md) .