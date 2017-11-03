---
title: "Ismerkedés az Azure IoT Hub kezelés (csomópont) |} Microsoft Docs"
description: "Hogyan használható az IoT-központ kezelés távoli eszköz újraindítás kezdeményezése. Az Azure IoT SDK for Node.js használatával megvalósítható a szimulált eszköz alkalmazást, amely magában foglalja a közvetlen módszer és a service-alkalmazást, amely hívja meg a közvetlen módszer."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: juanpere
ms.openlocfilehash: edb86f73f637e3e1722c5027e3dcea531b19af53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-device-management-node"></a>Ismerkedés az Eszközfelügyelet (csomópont)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure-portál használatával létrehoz egy IoT-központot, és az IoT hub hozzon létre egy eszközidentitás.
* Létrehoz egy szimulált eszköz alkalmazást, amely tartalmazza a közvetlen módszer, amely az eszköz újraindul. Közvetlen módszerek a felhőből hívják.
* Hozzon létre egy Node.js-Konzolalkalmazás, amely behívja az újraindítás közvetlen módszer a szimulált eszköz alkalmazásban az IoT hub keresztül.

Ez az oktatóanyag végén két Node.js konzol alkalmazások közül választhat:

**dmpatterns_getstarted_device.js**, amely kapcsolódik az IoT hub korábban létrehozott eszköz identitású kap egy újraindítás közvetlen módszer, szimulálja a fizikai számítógép újraindítása, és jelentést az idő az utolsó újraindítás.

**dmpatterns_getstarted_service.js**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazás jeleníti meg a választ, és jeleníti meg a frissített jelentett tulajdonságok.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* NODE.js-verzió 4.0.x vagy újabb verzióját, <br/>  [A fejlesztőkörnyezet előkészítése] [ lnk-dev-setup] ismerteti, hogyan telepítse a Node.js-ebben az oktatóanyagban a Windows vagy Linux.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ez a szakasz tartalma

* Egy Node.js-konzolalkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol
* A szimulált eszköz újraindítását eseményindító
* A jelentésben szereplő tulajdonságok használatával engedélyezhető az eszköz iker lekérdezések azonosíthatja azokat az eszközöket, és ha tartanak újraindítása után

1. Hozzon létre egy **manageddevice** nevű üres mappát.  A **manageddevice** mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. Parancsot a parancssorba a a **manageddevice** mappa telepítéséhez a következő parancsot a **azure iot-eszközök** eszköz SDK-csomagot és **azure-iot-eszközök – mqtt** csomag:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_device.js** fájlt a **manageddevice** mappa.
4. Adja hozzá a következő "szükséges" utasítások elején a **dmpatterns_getstarted_device.js** fájlt:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Adjon hozzá egy **connectionString** változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le a kapcsolati karakterlánc az eszköz kapcsolati karakterláncot.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Az eszközön a közvetlen módszer végrehajtásához a következő függvény hozzáadása
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
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
7. Nyissa meg a kapcsolatot az IoT hub, és indítsa el a közvetlen módszer figyelő:
   
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
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Az eszközön, a közvetlen módszer használatával távoli újraindítás eseményindító
Ebben a szakaszban egy Node.js-Konzolalkalmazás közvetlen metódussal eszköz távoli újraindítást kezdeményezett hoz létre. Az alkalmazás számára az eszköz legutóbbi újraindítás eszköz iker lekérdezések használ.

1. Hozzon létre egy üres nevű **triggerrebootondevice**.  Az a **triggerrebootondevice** mappa, hozzon létre egy package.json fájlt parancsot a parancssorba az alábbi parancs segítségével.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorban a a **triggerrebootondevice** mappa telepítéséhez a következő parancsot a **azure-IOT hubbal** eszköz SDK-csomagot és **azure-iot-eszközök – mqtt** csomag:
   
    ```
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_service.js** fájlt a **triggerrebootondevice** mappa.
4. Adja hozzá a következő "szükséges" utasítások elején a **dmpatterns_getstarted_service.js** fájlt:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Adja hozzá a következő változók deklarációja, és cserélje le a helyőrző értékeket:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Adja hozzá a következő függvény a céleszközt újraindítását a eszköz metódus indításához:
   
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
7. Adja hozzá a következő függvény lekérdezni az eszközt, és az utolsó újraindítás időpontja:
   
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
8. Adja hozzá az alábbi kódot a függvényeket, amelynek hatására a közvetlen módszer újraindítás és a lekérdezés az utolsó újraindítás időpont:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Mentse és zárja be a **dmpatterns_getstarted_service.js** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **manageddevice** mappa, a következő parancsot a rendszer újraindítása közvetlen módszer figyelését.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. A parancssorban a **triggerrebootondevice** mappa, a következő parancsot a távoli újraindítás és a lekérdezés megadása a eszköz iker található az utolsó újraindítás idő elindítani.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. A közvetlen módszer a konzolon eszköz válasz megjelenik.

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
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
