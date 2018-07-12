---
title: Eszköz belső vezérlőprogramjának frissítése az Azure IoT Hub (Node) szolgáltatással |} A Microsoft Docs
description: Hogyan lehet Azure IoT Hub eszközfelügyeleti használatával kezdeményezheti az eszköz belső vezérlőprogram frissítése. Az Azure IoT SDK for Node.js használatával valósítható meg egy szimulált eszközalkalmazás és a egy service-alkalmazás, amely elindítja a belső vezérlőprogram frissítését.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: 0cd8c019cf9a65e0e72227ba99c1995a45ed4067
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452430"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Eszközfelügyelet használatával kezdeményezheti az eszköz belső vezérlőprogram frissítése (csomópont/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Az a [Eszközfelügyelet – első lépések] [ lnk-dm-getstarted] oktatóanyag bemutatta, hogyan használható a [ikereszköz] [ lnk-devtwin] és [közvetlen metódusok ] [ lnk-c2dmethod] primitívek távolról újraindítja az eszközt. Ebben az oktatóanyagban használja az ugyanazon az IoT Hub primitívek és útmutatást nyújt, és bemutatja, hogyan teheti egy teljes körű szimulált belsővezérlőprogram-frissítést.  Ezt a mintát az Intel Edison eszköz minta a belső vezérlőprogram frissítési végrehajtására szolgál.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Node.js-konzolalkalmazást az IoT hub segítségével a szimulált eszköz alkalmazásban firmwareUpdate közvetlen metódus meghívásához.
* Egy szimulált eszközalkalmazás létrehozása, amely valósít meg egy **firmwareUpdate** közvetlen metódust. Ez a módszer folyamatot kezdeményez, többlépcsős, a belső vezérlőprogram rendszerképének letöltésére vár, a belső vezérlőprogram rendszerképének letöltése és végül alkalmazza a belsővezérlőprogram-lemezképet. A frissítés minden egyes fázisa során az eszköz jelentett tulajdonságait felhasználva jelenti az állapotot.

Ez az oktatóanyag végén két Node.js-konzolalkalmazással fog rendelkezni:

**dmpatterns_fwupdate_service.js**, amely meghívja a közvetlen metódus a szimulált eszközalkalmazásnak, a jeleníti meg a válasz és rendszeres időközönként (minden 500ms) jeleníti meg a frissített jelentett tulajdonságokként.

**dmpatterns_fwupdate_device.js**, csatlakozik az IoT hubhoz a korábban létrehozott eszközidentitással firmwareUpdate közvetlen metódus kap, szimulálja a belső vezérlőprogram frissítési többek között több állapot folyamaton keresztül futtatja: Várakozás a kép Töltse le, az új lemezkép letöltése, és végül a lemezkép alkalmazása.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* NODE.js-verzió 4.0.x vagy újabb, illetve <br/>  [A fejlesztési környezet előkészítését] [ lnk-dev-setup] ismerteti, hogyan telepítse a Node.js ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Kövesse a [Eszközfelügyelet – első lépések](iot-hub-node-node-device-management-get-started.md) a cikk az IoT hub létrehozása és az IoT Hub kapcsolati karakterláncának beszerzése.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Aktiválja az eszközön, a közvetlen metódus használatával távoli belső vezérlőprogram frissítése
Ebben a szakaszban egy Node.js-konzolalkalmazást, amely kezdeményezi egy távoli belső vezérlőprogram frissítése egy eszközön hoz létre. Az alkalmazás közvetlen metódus kezdeményezni a frissítést, és használja az ikereszköz-lekérdezések rendszeres időközönként beolvasni az aktív belső vezérlőprogram frissítésének állapotát.

1. Hozzon létre egy nevű üres mappát **triggerfwupdateondevice**.  Az a **triggerfwupdateondevice** mappában hozzon létre egy package.json fájlt a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorban a **triggerfwupdateondevice** mappában futtassa a következő paranccsal telepíthető a **azure-iot-hub** csomag:
   
    ```
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_service.js** fájlt a **triggerfwupdateondevice** mappát.
4. Adja hozzá a következő "szükséges" elején található utasításokat a **dmpatterns_getstarted_service.js** fájlt:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Adja hozzá a következő változódeklarációkat, és cserélje le a helyőrző értékeket:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Adja hozzá a következő függvényt található, és a firmwareUpdate értékének megjelenítése jelentett tulajdonság.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Adja hozzá a következő függvényt az újraindításhoz a céleszközön a firmwareUpdate metódus meghívásához:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Végül adja hozzá a következő függvény kód indítsa el a belső vezérlőprogram frissítési folyamata és a jelentett tulajdonságok rendszeres időközönként megjelenítése:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Mentse és zárja be a **dmpatterns_fwupdate_service.js** fájlt.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **manageddevice** mappában futtassa a következő parancsot, amellyel megkezdheti a újraindítás közvetlen metódus figyel.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. A parancssorban a **triggerfwupdateondevice** mappában futtassa a következő parancsot a távoli újraindítás és a lekérdezés az ikereszköz található az utolsó újraindítás időpontja eseményindítóra.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Láthatja, hogy az eszköz válasza a közvetlen metódus a konzolon.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban használt közvetlen metódus aktiválhat egy távoli belső vezérlőprogram frissítése egy eszközön, és hajtsa végre a belső vezérlőprogram frissítésének állapotát a jelentett tulajdonságok segítségével.

Ismerje meg, hogyan bővítheti az IoT-megoldás és az ütemezés metódus meghívja a több eszközre, tekintse meg a [feladatok ütemezése és szórása] [ lnk-tutorial-jobs] oktatóanyag.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
