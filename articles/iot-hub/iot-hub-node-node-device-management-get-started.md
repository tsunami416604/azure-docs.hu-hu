---
title: Ismerkedés az Azure IoT Hub eszközfelügyelettel (Node) | Microsoft dokumentumok
description: Az IoT Hub eszközfelügyeleti használata távoli eszköz újraindításának kezdeményezéséhez. Az Azure IoT SDK node.js használatával valósíthat meg egy szimulált eszközalkalmazást, amely közvetlen metódust és egy közvetlen metódust meghívó szolgáltatásalkalmazást tartalmaz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: mqtt
ms.openlocfilehash: a7814970d4ffb051ab54c965d27394f925d79e59
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732330"
---
# <a name="get-started-with-device-management-nodejs"></a>Az eszközkezelés első lépései (Node.js)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az [Azure Portalhasználatával](https://portal.azure.com) hozzon létre egy IoT Hubot, és hozzon létre egy eszközidentitást az IoT-központban.

* Hozzon létre egy szimulált eszközalkalmazást, amely közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy Node.js konzolalkalmazást, amely meghívja az újraindítás közvetlen metódusát a szimulált eszközalkalmazásban az IoT hubon keresztül.

Az oktatóanyag végén két Node.js konzolalkalmazás található:

* **dmpatterns_getstarted_device.js**, amely csatlakozik az IoT hub az eszköz identitása korábban létrehozott, kap egy újraindítás közvetlen metódust, szimulálja a fizikai újraindítás, és jelenti az időt az utolsó újraindítás.

* **dmpatterns_getstarted_service.js**, amely meghívja a szimulált eszközalkalmazásban egy közvetlen metódust, megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0.x vagy újabb verzió. [A fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Node.js-t ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban:

* Egy Node.js-konzolalkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol

* Szimulált eszköz újraindításának aktiválása

* A jelentett tulajdonságok használatával engedélyezheti az ikereszközök lekérdezéseit az eszközök azonosítására és az utolsó újraindításukkor

1. Hozzon létre egy **manageddevice** nevű üres mappát.  A **manageddevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. A **felügyelt eszköz** mappában lévő parancssorból futtassa a következő parancsot az **azure-iot-device** SDK csomag és az **azure-iot-device-mqtt** csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_device.js** fájlt a **manageddevice** mappában.

4. A **dmpatterns_getstarted_device.js** fájl elején adja hozzá a következő "szükséges" utasításokat:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje `{yourdeviceconnectionstring}` le a helyőrző értéket a korábban másolt eszközkapcsolati karakterláncra [az IoT hub új eszköz regisztrálása című részben.](#register-a-new-device-in-the-iot-hub)  

    ```javascript
    var connectionString = '{yourdeviceconnectionstring}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adja hozzá a következő funkciót a közvetlen metódus eszközre való implementzásához

    ```javascript
    var onReboot = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });

        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Nyissa meg a kapcsolatot az IoT hub, és indítsa el a közvetlen metódus figyelője:

    ```javascript
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Mentse és zárja be a **dmpatterns_getstarted_device.js** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például exponenciális visszamaradást) kell megvalósítania, ahogy azt a cikk, [átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)című cikk ben javasolt.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás aktiválása az eszközön közvetlen módszerrel

Ebben a szakaszban hozzon létre egy Node.js konzolalkalmazást, amely egy távoli újraindítást kezdeményez egy eszközön egy közvetlen metódus használatával. Az alkalmazás az eszköz ikerlekérdezéseit használja az eszköz utolsó újraindítási idejének felderítéséhez.

1. Hozzon létre egy **triggerrebootondevice**nevű üres mappát. Az **triggerrebootondevice** mappában hozzon létre egy package.json fájlt a következő paranccsal a parancssorból. Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. A parancssorból az **triggerrebootondevice** mappában futtassa a következő parancsot az **azure-iothub** device SDK-csomag és az **azure-iot-device-mqtt** csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_service.js** fájlt a **triggerrebootondevice** mappában.

4. A **dmpatterns_getstarted_service.js** fájl elején adja hozzá a következő "szükséges" utasításokat:

    ```javascript
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adja hozzá a következő változódeklarációkat, és cserélje le a `{iothubconnectionstring}` helyőrző értéket az IoT hub kapcsolati karakterláncával, amelyet korábban másolt az [IoT hub kapcsolati karakterláncának bekérése kor:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Adja hozzá a következő függvényt az eszközmetódus meghívásához a céleszköz újraindításához:

    ```javascript
    var startRebootDevice = function(twin) {

        var methodName = "reboot";

        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };

        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Adja hozzá a következő függvényt az eszköz lekérdezéséhez, és az utolsó újraindítási időpontot kapja:

    ```javascript
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Adja hozzá a következő kódot a közvetlen újraindítást kiváltó függvények és lekérdezés hívásához az utolsó újraindítási időponthoz:

    ```javascript
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Mentse és zárja be a **dmpatterns_getstarted_service.js** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A **manageddevice** mappában lévő parancssorból futtassa a következő parancsot az újraindítási közvetlen metódus figyeléséhez.

    ```cmd/sh
    node dmpatterns_getstarted_device.js
    ```

2. A **triggerrebootondevice** mappában lévő parancssorból futtassa a következő parancsot a távoli újraindítás elindításához, és az ikereszköz lekérdezésével keresse meg az utolsó újraindítási időt.

    ```cmd/sh
    node dmpatterns_getstarted_service.js
    ```

3. Megjelenik az eszköz válasza az újraindítás közvetlen metódusára és az újraindítás állapota a konzolon.

   A következőkben látható a szolgáltatás által küldött újraindítási közvetlen metódusra adott válasz:

   ![manageddevice alkalmazás kimenete](./media/iot-hub-node-node-device-management-get-started/device.png)

   A következő azt mutatja, hogy a szolgáltatás elindítja az újraindítást, és lekérdezi az eszközt iker az utolsó újraindítás ia.

   ![triggerrebootondevice alkalmazás kimenete](./media/iot-hub-node-node-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
