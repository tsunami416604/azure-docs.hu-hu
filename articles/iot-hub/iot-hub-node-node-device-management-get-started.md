---
title: Az Azure IoT Hub-Eszközfelügyelet (Node) – első lépések |} A Microsoft Docs
description: Hogyan használhatja az IoT Hub-Eszközfelügyelet egy távoli eszköz-újraindítás kezdeményezése. Az Azure IoT SDK for Node.js használatával valósítható meg egy szimulált eszközalkalmazás, amely tartalmazza a közvetlen metódus és a egy service-alkalmazás, amely a közvetlen metódust hív meg.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.openlocfilehash: 3f861259c8c760b921f1c33f4e449853a3686a40
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010803"
---
# <a name="get-started-with-device-management-node"></a>Ismerkedés az eszközfelügyelettel (Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure portal használatával hozzon létre egy IoT hubot, és hozzon létre egy új eszközidentitást az IoT hubban.
* Egy szimulált eszközalkalmazás létrehozása, amely közvetlen metódus, amely az eszköz újraindul. Közvetlen metódusok a felhő kerül meghívásra.
* Hozzon létre egy Node.js-konzolalkalmazást az IoT hub segítségével a szimulált eszközalkalmazás az újraindítás közvetlen metódus meghívásához.

Ez az oktatóanyag végén két Node.js-konzolalkalmazással fog rendelkezni:

**dmpatterns_getstarted_device.js**, csatlakozik az IoT hubhoz a korábban létrehozott eszközidentitással újraindítás közvetlen metódus kap, szimulálja a fizikai számítógép újraindítása és az utolsó újraindítás időpontja jelenti.

**dmpatterns_getstarted_service.js**, közvetlen metódus a szimulált eszközalkalmazásnak, amely hívások jeleníti meg a választ, és megjeleníti a frissített jelentett tulajdonságokként.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* NODE.js-verzió 4.0.x vagy újabb, illetve <br/>  [A fejlesztési környezet előkészítését] [ lnk-dev-setup] ismerteti, hogyan telepítse a Node.js ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ez a szakasz tartalma

* Egy Node.js-konzolalkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol
* Az eseményindító egy szimulált eszköz-újraindítás
* A jelentett tulajdonságok használatával ikereszköz-lekérdezéseket engedélyez az eszközök azonosítására és ha azok utolsó újraindítása

1. Hozzon létre egy **manageddevice** nevű üres mappát.  A **manageddevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorban a **manageddevice** mappában futtassa a következő paranccsal telepíthető a **azure-iot-device** eszközoldali SDK csomagot és **azure-iot-device-mqtt** csomag:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_device.js** fájlt a **manageddevice** mappát.
4. Adja hozzá a következő "szükséges" elején található utasításokat a **dmpatterns_getstarted_device.js** fájlt:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le a kapcsolati karakterláncot az eszköz kapcsolati karakterláncát.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Adja hozzá a következő függvényt a közvetlen metódus megvalósításához az eszközön
   
    ```
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
7. Az IoT hub-kapcsolat megnyitásához, és indítsa el a közvetlen metódus figyelőt:
   
    ```
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
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), a cikkben leírtak implementálandó [átmeneti hibák kezelésével](/azure/architecture/best-practices/transient-faults).

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>A távoli közvetlen metódus az eszközön újra kell indítani az eseményindító
Ebben a szakaszban egy Node.js-konzolalkalmazást, amely közvetlen metódus használó eszközök távoli újraindítást kezdeményez hoz létre. Az alkalmazás számára az eszköz legutóbbi újraindítás ikereszköz-lekérdezések használja.

1. Hozzon létre egy nevű üres mappát **triggerrebootondevice**.  Az a **triggerrebootondevice** mappában hozzon létre egy package.json fájlt a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorban a **triggerrebootondevice** mappában futtassa a következő paranccsal telepíthető a **azure-iothub** eszközoldali SDK csomagot és **azure-iot-device-mqtt** csomag:
   
    ```
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_service.js** fájlt a **triggerrebootondevice** mappát.
4. Adja hozzá a következő "szükséges" elején található utasításokat a **dmpatterns_getstarted_service.js** fájlt:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Adja hozzá a következő változódeklarációkat, és cserélje le a helyőrző értékeket:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Adja hozzá a meghívni a metódust a figyelt eszköz újraindítását a következő függvényt:
   
    ```
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
7. Adja hozzá a következő függvényt az eszköz lekérdezése és lekérése az utolsó újraindítás időpontja:
   
    ```
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
8. Adja hozzá a következő kódot a függvényeket, azt a közvetlen metódus újraindítás és a lekérdezés utolsó újraindítás időpontját:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Mentse és zárja be a **dmpatterns_getstarted_service.js** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **manageddevice** mappában futtassa a következő parancsot, amellyel megkezdheti a újraindítás közvetlen metódus figyel.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. A parancssorban a **triggerrebootondevice** mappában futtassa a következő parancsot a távoli újraindítás és a lekérdezés az ikereszköz található az utolsó újraindítás időpontja eseményindítóra.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. Láthatja, hogy az eszköz válasza a közvetlen metódus a konzolon.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
