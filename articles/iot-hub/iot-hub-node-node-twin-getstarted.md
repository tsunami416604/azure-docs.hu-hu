---
title: Ismerkedés az Azure IoT Hub eszköz twins (csomópont) |} Microsoft Docs
description: Hogyan használható az Azure IoT Hub eszköz twins címkéket, majd az IoT Hub-lekérdezést. Az Azure IoT SDK for Node.js használatával megvalósítható a szimulált eszköz alkalmazást és egy szolgáltatás-alkalmazást, amely hozzáadja a címkéket és az IoT Hub-lekérdezés futtatása.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: dbb28d7df104887a4056489e9f693ce28ec406df
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635100"
---
# <a name="get-started-with-device-twins-node"></a>Ismerkedés az eszköz twins (csomópont)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ez az oktatóanyag végén meg kell két Node.js konzol alkalmazásokat:

* **AddTagsAndQuery.js**, a Node.js háttér-alkalmazás, amely címkét ad hozzá, és lekérdezi az eszköz twins.
* **TwinSimulatedDevice.js**, a Node.js-alkalmazás, amely egy eszköz, amely összeköti az IoT hub korábban létrehozott eszköz identitású szimulálja, és jelenti a kapcsolat állapotát.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása
Ebben a szakaszban hoz létre egy Node.js-Konzolalkalmazás, a társított eszközök a két hely metaadatok hozzáadó **myDeviceId**. Ezután lekérdezi az eszköz twins tárolja az IoT hub, az eszközök az Egyesült Államok, és a mobilhálózat kapcsolat jelentő megfelelően kiválasztása.

1. Hozzon létre egy új üres nevű **addtagsandqueryapp**. Az a **addtagsandqueryapp** mappa, hozzon létre egy új package.json fájlt parancsot a parancssorba az alábbi parancs segítségével. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorba a **addtagsandqueryapp** mappa telepítéséhez a következő parancsot a **azure-IOT hubbal** csomag:
   
    ```
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy új **AddTagsAndQuery.js** fájlt a **addtagsandqueryapp** mappa.
4. Adja hozzá a következő kódot a **AddTagsAndQuery.js** fájlt, és lecserélni az **{iot hub kapcsolati karakterlánc}** helyőrző a hub létrehozása után másolja az IoT-központ kapcsolati karakterlánccal:
   
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
   
    A **beállításjegyzék** vezérlőnek eszköz twins a szolgáltatás együttműködhet szükséges összes módszert. Az előző kód először inicializálja a **beállításjegyzék** objektumot, majd beolvassa az eszköz iker a **myDeviceId**, és végül frissíti a címkék a kívánt helyre információkkal.
   
    A címkék frissítése után hívja a **queryTwins** függvény.
5. Adja hozzá a következő kódot végén **AddTagsAndQuery.js** megvalósításához a **queryTwins** függvény:
   
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
   
    Az előző kód két lekérdezést hajt végre: az első csak az eszköz twins található eszközök kiválasztja a **Redmond43** gépek és a második rendszerint a lekérdezést csak azokat az eszközöket is keresztül mobilhálózati kapcsolódó kiválasztásához.
   
    Az előző kód, amikor létrehozza a **lekérdezés** objektumazonosító, a visszaadott dokumentumok maximális számát határozza meg. A **lekérdezés** objektum tartalmaz egy **hasMoreResults** logikai tulajdonság, amely segítségével meghívni a **nextAsTwin** módszerek több alkalommal fordult elő az összes eredmények beolvasásához. A metódus hívása **következő** eredmények, amelyek például nem eszköz twins, összesítési-lekérdezések eredményének érhető el.
6. Futtassa az alkalmazást:
   
        node AddTagsAndQuery.js
   
    Megjelenik az eredmények között egy eszközön a lekérdezés kérni a minden eszköz a mappában lévő **Redmond43** és a lekérdezés, amely korlátozza az eredmények mobilhálózati használó eszközök sem.
   
    ![][1]

A következő szakaszban hozzon létre egy eszköz alkalmazást, amely a kapcsolódási adatokat, és módosítja az előző szakaszban a lekérdezés eredménye.

## <a name="create-the-device-app"></a>Az eszköz-alkalmazás létrehozása
Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely kapcsolódik a hub, létrehozhat **myDeviceId**, és az eszköz iker által jelentett tulajdonságok a információkat, hogy mobilhálózat használata csatlakozik tartalmazzák majd a frissítéseket.


1. Hozzon létre egy új üres nevű **reportconnectivity**. Az a **reportconnectivity** mappa, hozzon létre egy új package.json fájlt parancsot a parancssorba az alábbi parancs segítségével. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorba a **reportconnectivity** mappa telepítéséhez a következő parancsot a **azure iot-eszközök**, és **azure-iot-eszközök – mqtt** csomag:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy új **ReportConnectivity.js** fájlt a **reportconnectivity** mappa.
4. Adja hozzá a következő kódot a **ReportConnectivity.js** fájlt, és lecserélni a **{eszköz kapcsolati karakterlánc}** helyőrző a létrehozásautánmásoljaeszközkapcsolatikarakterlánccalrendelkező**myDeviceId** eszközidentitás:
   
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
   
    A **ügyfél** vezérlőnek az eszköz twins az eszközről interaktív szükséges összes módszert. Az előző kód után állíthatja a **ügyfél** objektumazonosító, beolvassa az eszköz iker a **myDeviceId** , és frissíti a jelentett tulajdonsága a kapcsolati információ.
5. Az eszköz alkalmazás futtatása
   
        node ReportConnectivity.js
   
    Az üzenet `twin state reported`.
6. Most, hogy az eszköz jelentette a kapcsolat adatait, akkor mindkét lekérdezések meg kell jelennie. Lépjen vissza a **addtagsandqueryapp** mappa, és futtassa újból a lekérdezést:
   
        node AddTagsAndQuery.js
   
    Most **myDeviceId** mindkét lekérdezési eredmények jelenjenek meg.
   
    ![][3]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Fel van véve eszköz metaadatait címkék egy háttér-alkalmazásból, és a szimulált eszköz alkalmazásának megírt az eszköz a két jelentés eszköz kapcsolódási adatok. Megtudta, ezt az információt az SQL-szerű IoT Hub lekérdezési nyelv lekérdezése is.

A következő források segítségével megtudhatja, hogyan:

* telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ] [ lnk-iothub-getstarted] oktatóanyagban
* eszköz iker kívánt tulajdonságokkal rendelkező eszközök konfigurálása a [használata szükséges eszközök tulajdonságok] [ lnk-twin-how-to-configure] oktatóanyagban
* az interaktív (például bekapcsolásával a felhasználó által felügyelt alkalmazásból ventilátor), eszközök szabályozásának a [közvetlen módszerekkel] [ lnk-methods-tutorial] oktatóanyag.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
