---
title: "Eszköz belső vezérlőprogram frissítése az Azure IoT Hub (csomópont) |} Microsoft Docs"
description: "Hogyan használható az eszközkezelés Azure IoT hub eszköz vezérlőprogram-frissítés kezdeményezése. Az Azure IoT SDK for Node.js használatával valósítja meg a szimulált eszköz alkalmazásának és a service-alkalmazást, amely elindítja a frissítést."
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e169367592b25ea45c3d1017937316a3b3b538b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Eszközkezelés használja indításához eszköz vezérlőprogram-frissítés (csomópontok /)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Az a [Ismerkedés az eszközkezelés] [ lnk-dm-getstarted] oktatóanyag, megtudhatta, hogyan használható a [eszköz iker] [ lnk-devtwin] és [közvetlen módszerek ] [ lnk-c2dmethod] primitívek távolról az eszköz újraindítását. Ez az oktatóanyag használja az ugyanazon az IoT-központ primitívek és nyújt útmutatást, és bemutatja, hogyan hajtsa végre egy végpontok közötti szimulált belső vezérlőprogram-frissítés.  Ebben a mintában az Intel Edison eszköz minta szolgál a belső vezérlőprogram frissítési megvalósításához.

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Node.js-Konzolalkalmazás, amely behívja a firmwareUpdate közvetlen módszer a szimulált eszköz alkalmazásban az IoT hub keresztül.
* Hozzon létre egy szimulált eszköz alkalmazást, amely egy **firmwareUpdate** közvetlen módszer. Ez a módszer indít el egy többlépcsős folyamat, amely megvárja-e a belső vezérlőprogram lemezképet letölti, letölti a belső vezérlőprogram lemezképet és a belső vezérlőprogram kép végül vonatkozik. A frissítés egyes szakasza alatt az eszköz használatával a jelentésben szereplő tulajdonságok előrehaladásról.

Ez az oktatóanyag végén két Node.js konzol alkalmazások közül választhat:

**dmpatterns_fwupdate_service.js**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazás jeleníti meg a választ, és rendszeres időközönként (minden 500ms) jeleníti meg a frissített jelenteni tulajdonságait.

**dmpatterns_fwupdate_device.js**, amely kapcsolódik az IoT hub, korábban létrehozott eszköz identitású kap egy firmwareUpdate közvetlen módszer esetén az több államot folyamatot, a belső vezérlőprogram frissítési például szimulálásához: Várakozás a kép Töltse le, az új lemezkép letöltése, és végül a kép alkalmazása.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* NODE.js-verzió 4.0.x vagy újabb verzióját, <br/>  [A fejlesztőkörnyezet előkészítése] [ lnk-dev-setup] ismerteti, hogyan telepítse a Node.js-ebben az oktatóanyagban a Windows vagy Linux.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Kövesse a [Ismerkedés az eszközkezelés](iot-hub-node-node-device-management-get-started.md) cikk az IoT hub létrehozása, és az IoT-központ kapcsolati karakterláncot.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Indítás, az eszközön, a közvetlen módszer használatával távoli vezérlőprogram-frissítés
Ebben a szakaszban egy eszköz távoli vezérlőprogram-frissítés kezdeményező Node.js-Konzolalkalmazás létrehozása. Az alkalmazás közvetlen módszer előtt használja a frissítés, és az eszköz iker lekérdezések rendszeres időközönként a aktív belső vezérlőprogram frissítése a állapot lekérdezése céljából.

1. Hozzon létre egy üres nevű **triggerfwupdateondevice**.  Az a **triggerfwupdateondevice** mappa, hozzon létre egy package.json fájlt parancsot a parancssorba az alábbi parancs segítségével.  Fogadja el az összes alapértelmezett beállítást:
   
    ```
    npm init
    ```
2. A parancssorba a **triggerfwupdateondevice** mappa telepítéséhez a következő parancsot a **azure-iot-központ** csomag:
   
    ```
    npm install azure-iothub --save
    ```
3. Egy szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_service.js** fájlt a **triggerfwupdateondevice** mappa.
4. Adja hozzá a következő "szükséges" utasítások elején a **dmpatterns_getstarted_service.js** fájlt:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Adja hozzá a következő változók deklarációja, és cserélje le a helyőrző értékeket:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Adja hozzá a következő függvény található, és a firmwareUpdate értékének megjelenítési jelentett tulajdonság.
   
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
7. Adja hozzá a következő függvény a céleszközt újraindítását a firmwareUpdate metódus indításához:
   
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
8. Végül adja hozzá a következő függvény indítsa el a belső vezérlőprogram frissítési sorrend, és a jelentésben szereplő tulajdonságok rendszeres időközönként megjelenjen kódot:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Mentse és zárja be a **dmpatterns_fwupdate_service.js** fájlt.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban a **manageddevice** mappa, a következő parancsot a rendszer újraindítása közvetlen módszer figyelését.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. A parancssorban a **triggerfwupdateondevice** mappa, a következő parancsot a távoli újraindítás és a lekérdezés megadása a eszköz iker található az utolsó újraindítás idő elindítani.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. A közvetlen módszer a konzolon eszköz válasz megjelenik.

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag elindítása egy távoli belső vezérlőprogram frissítése egy eszközön a közvetlen módszer használatával, és a jelentésben szereplő tulajdonságok segítségével nyomon követheti a belső vezérlőprogram frissítése.

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
