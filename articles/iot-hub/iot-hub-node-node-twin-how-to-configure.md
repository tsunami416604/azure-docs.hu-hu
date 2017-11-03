---
title: "Azure IoT Hub eszköz iker tulajdonságait (csomópont) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub eszköz twins eszközök konfigurálásához. Az Azure IoT SDK for Node.js használatával valósítja meg a szimulált eszköz alkalmazás és egy szolgáltatás-alkalmazást, amely módosítja egy eszköz konfigurálásának egy eszköz iker használatával."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: elioda
ms.openlocfilehash: 6ff6f1c331d5a77e7ac0a47af6806f5d90fb0fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices-node"></a>Használja a kívánt tulajdonságait (csomópont) eszközök konfigurálása
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Ez az oktatóanyag végén meg kell két Node.js konzol alkalmazásokat:

* **SimulateDeviceConfiguration.js**, a szimulált eszköz alkalmazás, amely megvárja-e a szükséges konfiguráció frissítése a jelentést készít egy szimulált konfigurációs frissítési folyamat állapotát.
* **SetDesiredConfigurationAndQuery.js**, a Node.js háttér-alkalmazás, amely beállítja a kívánt konfiguráció egy eszközön, és lekérdezi a konfigurációs frissítési folyamat.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Ha követte a [Ismerkedés az eszköz twins] [ lnk-twin-tutorial] oktatóanyagban már rendelkezik egy IoT-központot, és egy eszközidentitás nevű **myDeviceId**; és a ugorjon[a szimulált eszköz-alkalmazás létrehozása] [ lnk-how-to-configure-createapp] szakasz.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-simulated-device-app"></a>A szimulált eszköz-alkalmazás létrehozása
Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely kapcsolódik a hub, létrehozhat **myDeviceId**megvárja-e a szükséges konfiguráció frissítése a, majd jelentést készít a frissítések szimulált konfigurációs frissítési folyamat.

1. Hozzon létre egy új üres nevű **simulatedeviceconfiguration**. Az a **simulatedeviceconfiguration** mappa, hozzon létre egy új package.json fájlt parancsot a parancssorba az alábbi parancs segítségével. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorba a **simulatedeviceconfiguration** mappa telepítéséhez a következő parancsot a **azure iot-eszközök**, és **azure-iot-eszközök – mqtt** csomag:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy új **SimulateDeviceConfiguration.js** fájlt a **simulatedeviceconfiguration** mappa.
4. Adja hozzá a következő kódot a **SimulateDeviceConfiguration.js** fájlt, és lecserélni a **{eszköz kapcsolati karakterlánc}** helyőrző az eszköz kapcsolati karakterlánccal létrehozása után másolja a **myDeviceId** eszközidentitás:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    A **ügyfél** vezérlőnek az eszközről eszköz twins együttműködhet szükséges összes módszert. Az előző kód után állíthatja a **ügyfél** objektumazonosító, beolvassa az eszköz iker a **myDeviceId**, és csatolja a kívánt tulajdonságokkal a frissítés kezelőjét. A kezelő ellenőrzi, hogy egy tényleges Helykonfiguráció-változtatási kérelem a configIds összehasonlításával, akkor hív meg, olyan módszer, amelyik elindul a konfigurációs módosítást.
   
    Vegye figyelembe, hogy az egyszerűség kedvéért, az előző kód egy kódolt alapértelmezett értéket használja, a kezdeti konfiguráció. Egy valós alkalmazás valószínűleg szeretné, hogy a konfigurálás betöltése a helyi tárterület.
   
   > [!IMPORTANT]
   > Kívánt tulajdonság állapotváltozási események mindig kibocsátott egyszer eszköz csatlakozáskor, ügyeljen arra, hogy ellenőrizze, hogy van-e egy tényleges módosítása a kívánt tulajdonságaiban bármilyen művelet végrehajtása előtt.
   > 
   > 
5. Adja hozzá a következő metódusokat előtt a `client.open()` hívása:
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    A **initConfigChange** metódus frissíti a helyi eszközön a két objektum jelentett tulajdonságainak a konfigurációs frissítési kérelmet az állapota, **függőben lévő**, majd a frissítések az eszköz iker a a szolgáltatás. Miután sikeresen frissített a eszköz iker, egy hosszú ideig tartó folyamatot, amely végrehajtása során szimulálja **completeConfigChange**. Ez a módszer frissíti a helyi eszközön iker jelentett tulajdonságok az állapot **sikeres** és eltávolítása a **pendingConfig** objektum. Ezután frissíti az eszköz a két szolgáltatás.
   
    Megjegyzés: a, hogy a sávszélességet, hogy csak a módosítani kívánt tulajdonságok megadásával tulajdonságának frissítésekor (nevű **javítás** a fenti kódban), a teljes dokumentum felülírása helyett.
   
   > [!NOTE]
   > Ez az oktatóanyag nem szimulálása egyidejű keresni minden olyan esetben. Néhány konfigurációs frissítési folyamat közben fut-e a frissítés, mások lehet a várólistába helyezni őket, és mások sikerült hibát elutasítása a célként megadott konfigurációs módosítások befogadásához lehet. Ügyeljen arra, hogy fontolja meg a kívánt viselkedés, a konfigurációs folyamat, és adja hozzá a megfelelő logikai kezdeményezése a konfiguráció módosítása előtt.
   > 
   > 
6. Az eszköz alkalmazás futtatása:
   
        node SimulateDeviceConfiguration.js
   
    Az üzenet `retrieved device twin`. Tartsa meg az alkalmazás futását.

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása
Ebben a szakaszban egy Node.js-Konzolalkalmazás, amely frissíti hoz létre a *szükséges tulajdonságok* meg az eszköz a két társított **myDeviceId** új telemetriai configuration objektummal. Ezután lekérdezi az eszköz twins az IoT hub tárolja, és a kívánt és jelentett konfigurációkat, az eszköz közötti különbséget szemlélteti.

1. Hozzon létre egy új üres nevű **setdesiredandqueryapp**. Az a **setdesiredandqueryapp** mappa, hozzon létre egy új package.json fájlt parancsot a parancssorba az alábbi parancs segítségével. Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorba a **setdesiredandqueryapp** mappa telepítéséhez a következő parancsot a **azure-IOT hubbal** csomag:
   
    ```
    npm install azure-iothub node-uuid --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy új **SetDesiredAndQuery.js** fájlt a **setdesiredandqueryapp** mappa.
4. Adja hozzá a következő kódot a **SetDesiredAndQuery.js** fájlt, és lecserélni az **{iot hub kapcsolati karakterlánc}** helyőrző a hub létrehozása után másolja az IoT-központ kapcsolati karakterlánccal:
   
        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });

    A **beállításjegyzék** vezérlőnek eszköz twins a szolgáltatás együttműködhet szükséges összes módszert. Az előző kód után állíthatja a **beállításjegyzék** objektumazonosító, beolvassa az eszköz iker a **myDeviceId**, és frissíti a kívánt tulajdonságát egy új telemetriai konfigurációs objektuma. Ezt követően meghívja a **queryTwins** működéséhez esemény 10 másodperc.

    > [!IMPORTANT]
    > Ez az alkalmazás lekérdezi az IoT-központ 10 másodpercenként szemléltetési célokat szolgál. Lekérdezésekkel számos eszközön keresztül a felhasználók számára is elérhető jelentések létrehozásához, és nem észleli a módosításokat. Ha a megoldás a valós idejű értesítések eszköz események van szüksége, [iker értesítések][lnk-twin-notifications].
    > 
    >.

1. Adja hozzá a következő kódot közvetlenül az előtt a `registry.getDeviceTwin()` meghívása megvalósításához a **queryTwins** függvény:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };
   
    Az előző kód lekérdezéseket a eszköz twins az IoT hub tárolja, és kiírja a kívánt és jelentett telemetriai konfigurációkat. Tekintse meg a [IoT-központ lekérdezési nyelv] [ lnk-query] további információt az eszközök közötti hatékony jelentések létrehozásához.
2. A **SimulateDeviceConfiguration.js** fut, futtassa az alkalmazást az:
   
        node SetDesiredAndQuery.js 5m
   
    Módosítsa a jelentésben szereplő konfigurációs kell megjelennie **sikeres** való **függőben lévő** való **sikeres** újra az új aktív küldés 24 óra helyett öt perces gyakoriságot.
   
   > [!IMPORTANT]
   > Nincs késleltetést legfeljebb egy percet, az eszköz jelentés művelet és a lekérdezési eredmények között. Ez a nagyon nagy méretekben működéséhez a lekérdezés infrastruktúra engedélyezéséhez. Egy egyetlen eszközt iker használati konzisztens nézetek beolvasása a **getDeviceTwin** metódust a **beállításjegyzék** osztály.
   > 
   > 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy szabványoskonfiguráció mint beállítása *szükséges tulajdonságok* egy háttér-alkalmazásból, és a szimulált eszköz alkalmazások észleli a változást, és a tag állapotaként reporting többlépéses frissítési folyamat szimulálása megírt  *Tulajdonságok jelentett* számára az eszköz iker.

A következő források segítségével megtudhatja, hogyan:

* telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ] [ lnk-iothub-getstarted] oktatóanyagban
* ütemezett vagy műveleteket hajtson végre a nagy mennyiségű eszközök lásd: a [ütemezés és a szórásos feladatok] [ lnk-schedule-jobs] oktatóanyag.
* az interaktív (például bekapcsolásával a felhasználó által felügyelt alkalmazásból ventilátor), eszközök szabályozásának a [közvetlen módszerekkel] [ lnk-methods-tutorial] oktatóanyag.

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
