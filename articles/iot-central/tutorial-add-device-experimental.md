---
title: Valós eszköz hozzáadása Azure IoT Central-alkalmazáshoz | Microsoft Docs
description: Operátorként valós eszközt adhat az Azure IoT Central-alkalmazáshoz.
author: sandeeppujar
ms.author: sandeepu
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 8792873407a6103134300f11b4dae002e4df107a
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216220"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Oktatóanyag: Valós eszköz hozzáadása az Azure IoT Central-alkalmazáshoz

Ez az oktatóanyag bemutatja, hogyan adhat hozzá és konfigurálhat valós eszközt a Microsoft Azure IoT Central-alkalmazásához.

Ez az oktatóanyag két részből áll:

1. Először operátorként megismeri, hogyan adhat hozzá és konfigurálhat valós eszközt az Azure IoT Central -alkalmazásban. A jelen rész végén lekér egy kapcsolati sztringet, amelyet a második részben fog használni.
2. Ezt követően eszközfejlesztőként megismeri a valós eszközben lévő kódot. Az első részben lévő kapcsolati sztringet hozzáadja a mintakódhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új valós eszköz hozzáadása
> * A valós eszköz konfigurálása
> * Kapcsolati sztring lekérése a valós eszközhöz az alkalmazásból
> * Annak megértése, hogyan történik az ügyfélkód leképezése az alkalmazásra
> * Ügyfélkód konfigurálása valós eszközhöz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a szerkesztőnek el kell végeznie legalább az Azure IoT Central-alkalmazás létrehozásának első szerkesztői oktatóanyagát:

* [Új eszköztípus definiálása](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (Kötelező)
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (Nem kötelező)
* [Operátori nézetek testreszabása](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (Nem kötelező)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Ha valós eszközt szeretne hozzáadni az alkalmazáshoz, használja az [Új eszköztípus definiálása](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) című oktatóanyagban létrehozott **Csatlakoztatott légkondicionáló** eszközsablont.

1. Ha új eszközt szeretne hozzáadni operátorként, válassza a bal oldali navigációs menüben lévő **Eszközkereső** elemet:

   ![Csatlakoztatott légkondicionálót megjelenítő Eszközkereső oldal](media/tutorial-add-device-experimental/explorer.png)

   Az **Eszközkereső** a **Csatlakoztatott légkondicionáló** eszközsablont és azt a szimulált eszközt jeleníti meg, amely automatikusan jött létre, amikor a szerkesztő létrehozta az eszközsablont.

2. Kapcsolat egy valós csatlakoztatott légkondicionálóját eszköz elindításához kattintson **+**, majd **valós**:

   ![Új, valós csatlakoztatott légkondicionáló eszköz hozzáadásának megkezdése](media/tutorial-add-device-experimental/newreal.png)

3. Adja meg az eszköz azonosítója (kell lennie a kisbetűs) vagy a javasolt eszközazonosítót használ. Vagy megadhatja az új eszköz nevét, és kiválaszthatja a **Létrehozás** lehetőséget.

   ![Az eszköz átnevezése](media/tutorial-add-device-experimental/rename.png)

## <a name="configure-a-real-device"></a>Valós eszköz konfigurálása

A valós eszköz a **Csatlakoztatott légkondicionáló** eszközsablonból jön létre. A **Beállításokkal** konfigurálhatja az eszközt és beállíthatja a tulajdonságértékeket az eszközzel kapcsolatos információk rögzítéséhez.

1. A **Beállítások** lapon figyelje meg, hogy a **Megadott hőmérséklet** beállítás állapota **nincs frissítés** értékű. Ebben az állapotban marad, amíg a valós eszköz nem csatlakozik az alkalmazáshoz, és nem igazolja vissza, hogy módosította a beállítást.

    ![Szinkronizálást mutató beállítások](media/tutorial-add-device-experimental/settingssyncing.png)

2. Az új, valós csatlakoztatott légkondicionáló eszköz **Tulajdonságok** lapján a szolgáltatás helye és az utolsó szervizelés dátuma is az eszköz szerkeszthető tulajdonsága. A sorozatszám és a belső vezérlőprogram verziójának mezője üres, amíg az eszközt nem csatlakoztatja az alkalmazáshoz. Ezek az eszközről küldött írásvédett értékek, és nem szerkeszthetők.

    ![Valós eszköz eszköztulajdonságai](media/tutorial-add-device-experimental/setproperties1.png)

3. Megtekintheti a valós eszköz **Mérések**, **Szabályok** és **Irányítópult** lapját.

## <a name="generate-connection-string"></a>Kapcsolati karakterlánc létrehozása

Egy eszközfejlesztőnek be kell ágyaznia a valós eszköz *kapcsolati sztringjét* az eszközön futó kódba. A kapcsolati sztring lehetővé teszi, hogy az eszköz biztonságosan csatlakozzon az Azure IoT Central alkalmazáshoz. A kapcsolati sztringet a következő lépésekben a Node.js-kódban írt ügyfélkód előkészítésének részeként hozza létre. A Node.js-alkalmazás a valós csatlakoztatott légkondicionáló eszköz szerepét tölti be. 

## <a name="prepare-the-client-code"></a>Az ügyfélkód előkészítése

A cikkben szereplő példakód [Node.js](https://nodejs.org/) nyelven van megírva, és pont elég kódot jelenít meg a következőkhöz:

* Csatlakozás eszközként az Azure IoT Central-alkalmazáshoz.
* Hőmérséklet-telemetria küldése csatlakoztatott légkondicionáló eszközként.
* Eszköztulajdonságok elküldése az Azure IoT Central-alkalmazásnak.
* Válasz a **Megadott hőmérséklet** beállítást használó operátornak.
* Az Echo parancs kezelése az Azure IoT Central-alkalmazásból.

A [Következő lépések](#next-steps) szakaszban szereplő „Útmutató” hivatkozások teljesebb mintákat nyújtanak, és bemutatják más programozói nyelvek használatát. Az eszközök Azure IoT Central-alkalmazáshoz való kapcsolódásának módjáról további információt az [Eszközkapcsolatok](concepts-connectivity-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) című cikkben talál.

A következő lépések bemutatják, hogyan készítheti elő a [Node.js](https://nodejs.org/) mintát:

1. Telepítse a [Node.js](https://nodejs.org/) 4.0.x vagy újabb verzióját a gépén. A Node.js az operációs rendszerek széles körében elérhető.

1. Hozzon létre egy `connectedairconditioner` nevű mappát a gépén.

1. A parancssori környezetben keresse meg a létrehozott `connectedairconditioner` mappát.

1. A következő paranccsal telepítheti a DPS-kulcskészítőt:

    ```cmd/sh
    npm i -g dps-keygen
    ```

   A parancssori eszközről [itt tudhat meg többet](https://www.npmjs.com/package/dps-keygen).

1. Az alkalmazásban az eszközpéldányok kapcsolati sztringje az IoT Central által megadott eszközinformációkból jön létre.

   Térjen vissza az IoT Central portálra. A valós csatlakoztatott légkondicionáló eszköz képernyőjén válassza a **Csatlakozás** lehetőséget.

   ![A kapcsolat információit megjelenítő hivatkozást mutató eszközoldal](media/tutorial-add-device-experimental/connectionlink.png)

1. Az Eszközkapcsolat oldalon másolja ki és illessze be a hatókör azonosítóját, az eszköz azonosítóját és az elsődleges kulcsot egy szövegszerkesztőbe, majd mentse a fájlt. Ezeket az értékeket a következő lépésben fogja használni.

   ![Kapcsolat adatai](media/tutorial-add-device-experimental/device-connect.png)

1. Térjen vissza a parancssori környezethez, és hozza létre a kapcsolati sztringet az alábbiak végrehajtásával:

    ```cmd/sh
    dps-keygen -si:<scope_id> -di:<device_id> -dk:<Primary Key>
    ```

   Másolja ki a kimenetet, és mentse egy új fájlba (például a connection.txt fájlba).

1. A Node.js projekt elindításához futtassa az alábbi parancsot az összes alapérték elfogadásával:

    ```cmd/sh
    npm init
      ```

1. A szükséges csomagok telepítéséhez futtassa az alábbi parancsot:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Egy szövegszerkesztővel hozzon létre egy **ConnectedAirConditioner.js** fájlt a `connectedairconditioner` mappában.

1. Adja hozzá a következő `require` utasításokat a **ConnectedAirConditioner.js** fájl elejéhez:

    ```javascript
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Adja a következő változódeklarációkat a fájlhoz:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    > [!NOTE]
    > A `{your device connection string}` helyőrzőt egy későbbi lépésben fogja frissíteni.

1. Mentse az eddig végrehajtott módosításokat, de hagyja nyitva a fájlt.

## <a name="review-client-code"></a>Tekintse át az Ügyfélkód

Az előző szakaszban létrehozott egy Node.js vázprojektet az Azure IoT Central-alkalmazáshoz kapcsolódó alkalmazáshoz. Ebben a szakaszban hozzáadja a kódot a következők elvégzéséhez:

* Csatlakozás az Azure IoT Central-alkalmazáshoz
* Telemetria küldése az Azure IoT Central-alkalmazásnak
* Eszköztulajdonságok elküldése az Azure IoT Central-alkalmazásnak.
* Beállítások fogadása az Azure IoT Central-alkalmazásból
* Az Echo parancs kezelése az Azure IoT Central-alkalmazásból.

1. Ha hőmérséklet telemetriát szeretne küldeni az Azure IoT Central-alkalmazásnak, adja a következő kódot a **ConnectedAirConditioner.js** fájlhoz:

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Az elküldött JSON-fájlban lévő mező nevének egyeznie kell az eszközsablonban a hőmérséklet-telemetriához megadott mező nevével. Ebben a példában a mező neve **temperature**.

1. Az eszköz tulajdonságai, például a **firmwareVersion** és a **serialNumber** elküldéséhez adja hozzá a következő definíciót:

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) => 
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. Az eszköz által támogatott beállítások, például a **setTemperature** meghatározásához adja hozzá a következő definíciót:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Az Azure IoT Centralból küldött beállítások kezeléséhez adja hozzá a következő függvényt, amely megkeresi és végrehajtja a megfelelő eszközkódot:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

    Ez a függvény:

    * Az Azure IoT Centralt figyeli, a kívánt tulajdonságot küldve.
    * Megkeresi a hívni kívánt megfelelő függvényt a beállítás módosításának kezeléséhez.
    * Nyugtázást küld vissza az Azure IoT-Central alkalmazásnak.

1. Ha válaszolni szeretne egy (például az **echo**) parancsra az Azure IoT Central-alkalmazásból, adja hozzá az alábbi definíciót:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
    }
    ```

1. Adja hozzá a következő kódot az Azure IoT Central-alkalmazáshoz való csatlakozás befejezéséhez és az ügyfélkódban lévő függvények csatlakoztatásához:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Setup device command callbacks
        client.onDeviceMethod('echo', onCommandEcho);
        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. Mentse az eddig végrehajtott módosításokat, de hagyja nyitva a fájlt.

## <a name="configure-client-code"></a>Ügyfélalkalmazás konfigurálása

<!-- Add the connection string to the sample code, build, and run --> Ha az ügyfélkódot az Azure IoT Central-alkalmazáshoz való kapcsolódáshoz szeretné konfigurálni, hozzá kell adnia a valós eszköz az oktatóanyag korábbi részében feljegyzett kapcsolati sztringjét.

1. A **ConnectedAirConditioner.js** fájlban keresse meg a következő kódsort:

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. A `{your device connection string}` helyére illessze be a valós eszköz kapcsolati sztringjét. Korábban egy szövegszerkesztőbe mentette a kapcsolati sztringet.

1. Mentse a **ConnectedAirConditioner.js** fájl módosításait.

1. A mintakód futtatásához írja be a következő parancsot a parancssori környezetbe:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Győződjön meg róla, hogy a `connectedairconditioner` mappában áll a parancs futtatásakor.

1. Az alkalmazás a kimenetet a konzolban jeleníti meg:

   ![Az ügyfélalkalmazás kimenete](media/tutorial-add-device-experimental/output.png)

1. Körülbelül 30 másodperc múlva megjelenik a telemetria az eszköz **Mérések** oldalán:

   ![Real ~~telemetry](media/tutorial-add-device-experimental/realtelemetry.png)

1. A **Beállítások** lapon láthatja, hogy a beállítás most szinkronizálva van. Az eszköz első csatlakoztatásakor az megkapta a beállításértéket és elfogadta a módosítást:

   ![Szinkronizált beállítás](media/tutorial-add-device-experimental/settingsynced.png)

1. A **Beállítások** lapon állítsa az eszköz hőmérsékletét **95** értékre, és válassza az **Eszköz frissítése** lehetőséget. A mintaalkalmazás fogadja és feldolgozza ezt a módosítást:

   ![Beállítás fogadása és feldolgozása](media/tutorial-add-device-experimental/receivesetting.png)

   > [!NOTE]
   > Két „beállítás frissítése” üzenet van. Egy, amikor a rendszer `pending` állapotot küld, és egy, amikor `completed` állapotot.

1. A **Mérések** oldalon láthatja, hogy az eszköz magasabb hőmérsékletértékeket küld:

    ![A hőmérséklet-telemetria most nagyobb](media/tutorial-add-device-experimental/highertemperature.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="nextstepaction"]
> * Új valós eszköz hozzáadása
> * Az új eszköz konfigurálása
> * Kapcsolati sztring lekérése a valós eszközhöz az alkalmazásból
> * Annak megértése, hogyan történik az ügyfélkód leképezése az alkalmazásra
> * Ügyfélkód konfigurálása valós eszközhöz

Most, hogy valós eszközt csatlakoztatott az Azure IoT Central-alkalmazáshoz, itt talál néhány javasolt következő lépést:

Operátorként a következőket sajátítja el:

* [Saját eszközök kezelése](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Eszközkészletek használata](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Egyéni elemzések létrehozása](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

Eszközfejlesztőként a következőket sajátítja el:

* [DevKit előkészítése és csatlakoztatása](howto-connect-devkit-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Raspberry Pi előkészítése és csatlakoztatása](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [A kód testreszabása][lnk-nodejs-device-ref]


[lnk-nodejs-device-ref]: /javascript/api/azure-iot-device/?view=azure-iot-typescript-latest