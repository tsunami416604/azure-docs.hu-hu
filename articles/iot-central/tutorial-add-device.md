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
ms.openlocfilehash: 8e7eee40bed29117d2873393395a852e4b738533
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793481"
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

* [Új eszköztípus definiálása](tutorial-define-device-type.md) (Kötelező)
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md) (Nem kötelező)
* [Operátori nézetek testreszabása](tutorial-customize-operator.md) (Nem kötelező)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Ha valós eszközt szeretne hozzáadni az alkalmazáshoz, használja az [Új eszköztípus definiálása](tutorial-define-device-type.md) című oktatóanyagban létrehozott **Csatlakoztatott légkondicionáló** eszközsablont.

1. Ha új eszközt szeretne hozzáadni operátorként, válassza a bal oldali navigációs menüben lévő **Eszközkereső** elemet:

   ![Csatlakoztatott légkondicionálót megjelenítő Eszközkereső oldal](media/tutorial-add-device/explorer.png)

   A **Device Explorer** jeleníti meg a **csatlakoztatott Légkondicionálóját** eszköz sablon és a egy szimulált eszköz. Eszköz sablont hoz létre, amikor az IoT-központ automatikusan létrehoz egy szimulált eszközt.

2. Kapcsolat egy valós csatlakoztatott légkondicionálóját eszköz elindításához válassza **+**, majd **valós**:

   ![Új, valós csatlakoztatott légkondicionáló eszköz hozzáadásának megkezdése](media/tutorial-add-device/newreal.png)

3. Adja meg az eszköz azonosítója (kell lennie a kisbetűs) vagy a javasolt eszközazonosítót használ. Vagy megadhatja az új eszköz nevét, és kiválaszthatja a **Létrehozás** lehetőséget.

   ![Az eszköz átnevezése](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Valós eszköz konfigurálása

A valós eszköz a **Csatlakoztatott légkondicionáló** eszközsablonból jön létre. A **Beállításokkal** konfigurálhatja az eszközt és beállíthatja a tulajdonságértékeket az eszközzel kapcsolatos információk rögzítéséhez.

1. A **Beállítások** lapon figyelje meg, hogy a **Megadott hőmérséklet** beállítás állapota **nincs frissítés** értékű. Ebben az állapotban marad, amíg a valós eszköz nem csatlakozik az alkalmazáshoz, és nem igazolja vissza, hogy módosította a beállítást.

    ![Szinkronizálást mutató beállítások](media/tutorial-add-device/settingssyncing.png)

2. Az a **tulajdonságok** oldalon az új, valós eszköz, mindkét szolgáltatás és az utolsó szolgáltatás dátumának helye: tartozik szerkeszthető tulajdonság. A sorozatszám és a belső vezérlőprogram verziójának mezője üres, amíg az eszközt nem csatlakoztatja az alkalmazáshoz. Ezek csak olvasható az értékek akkor küldi el az eszközt, és nem szerkeszthető.

    ![Valós eszköz eszköztulajdonságai](media/tutorial-add-device/setproperties1.png)

3. Megtekintheti a valós eszköz **Mérések**, **Szabályok** és **Irányítópult** lapját.

## <a name="generate-connection-string"></a>Kapcsolati karakterlánc létrehozása

Egy eszközfejlesztőnek be kell ágyaznia a valós eszköz *kapcsolati sztringjét* az eszközön futó kódba. A kapcsolati karakterlánc lehetővé teszi, hogy az eszköz biztonságosan csatlakozhat az alkalmazáshoz. A következő lépések bemutatják, Ön hozza létre a kapcsolati karakterláncot, és az ügyfélszoftver előkészítése a Node.js-kódot.

## <a name="prepare-the-client-code"></a>Az ügyfélkód előkészítése

Ebben a cikkben a példakód nyelven van megírva [Node.js](https://nodejs.org/) és elég kódot mutatja:

* Csatlakozás eszközként az Azure IoT Central-alkalmazáshoz.
* Hőmérséklet-telemetria küldése csatlakoztatott légkondicionáló eszközként.
* Eszköztulajdonságok elküldése az Azure IoT Central-alkalmazásnak.
* Válasz a **Megadott hőmérséklet** beállítást használó operátornak.
* Az Echo parancs kezelése az Azure IoT Central-alkalmazásból.

A felsorolt cikkek a [lépések](#next-steps) szakasz tartalmazza a teljes mintát, és más programozási nyelveket megjelenítése. Az eszközök Azure IoT Central-alkalmazáshoz való kapcsolódásának módjáról további információt az [Eszközkapcsolatok](concepts-connectivity.md) című cikkben talál.

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

   ![A kapcsolat információit megjelenítő hivatkozást mutató eszközoldal](media/tutorial-add-device/connectionlink.png)

1. Az Eszközkapcsolat oldalon másolja ki és illessze be a hatókör azonosítóját, az eszköz azonosítóját és az elsődleges kulcsot egy szövegszerkesztőbe, majd mentse a fájlt. Ezeket az értékeket a következő lépésben fogja használni.

   ![Kapcsolat adatai](media/tutorial-add-device/device-connect.png)

1. Térjen vissza a parancssori környezetet és a kapcsolati karakterlánc létrehozása végrehajtásával:

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

Az előző szakaszban létrehozott egy Node.js vázprojektet az Azure IoT Central-alkalmazáshoz kapcsolódó alkalmazáshoz. A következő lépés, hogy adja hozzá kódot:

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

<!-- Add the connection string to the sample code, build, and run -->
Ha az ügyfélkódot az Azure IoT Central-alkalmazáshoz való kapcsolódáshoz szeretné konfigurálni, hozzá kell adnia a valós eszköz az oktatóanyag korábbi részében feljegyzett kapcsolati sztringjét.

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

   ![Az ügyfélalkalmazás kimenete](media/tutorial-add-device/output.png)

1. Körülbelül 30 másodperc múlva megjelenik a telemetria az eszköz **Mérések** oldalán:

   ![Real ~~telemetry](media/tutorial-add-device/realtelemetry.png)

1. A **Beállítások** lapon láthatja, hogy a beállítás most szinkronizálva van. Az eszköz első csatlakoztatásakor az megkapta a beállításértéket és elfogadta a módosítást:

   ![Szinkronizált beállítás](media/tutorial-add-device/settingsynced.png)

1. A **Beállítások** lapon állítsa az eszköz hőmérsékletét **95** értékre, és válassza az **Eszköz frissítése** lehetőséget. A mintaalkalmazás fogadja és feldolgozza ezt a módosítást:

   ![Beállítás fogadása és feldolgozása](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Két „beállítás frissítése” üzenet van. Egy, amikor a rendszer `pending` állapotot küld, és egy, amikor `completed` állapotot.

1. A **Mérések** oldalon láthatja, hogy az eszköz magasabb hőmérsékletértékeket küld:

    ![A hőmérséklet-telemetria most nagyobb](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="nextstepaction"]
> * Új valós eszköz hozzáadása
> * Az új eszköz konfigurálása
> * Kapcsolati sztring lekérése a valós eszközhöz az alkalmazásból
> * Annak megértése, hogyan történik az ügyfélkód leképezése az alkalmazásra
> * Ügyfélkód konfigurálása valós eszközhöz

Most, hogy az Azure IoT Central alkalmazásnak csatlakozott egy igazi eszközön, az alábbiakban a javasolt következő lépések:

Operátorként a következőket sajátítja el:

* [Saját eszközök kezelése](howto-manage-devices.md)
* [Eszközkészletek használata](howto-use-device-sets.md)
* [Egyéni elemzések létrehozása](howto-use-device-sets.md)

Eszközfejlesztőként a következőket sajátítja el:

* [Készítse elő és (C) eszköz DevKit csatlakoztatása](howto-connect-devkit.md)
* [Készítse elő, és csatlakozzon a Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Készítse elő és a Raspberry Pi csatlakoztatása (C#)](howto-connect-raspberry-pi-csharp.md)
* [Készítse elő és a egy Windows 10 IoT core-eszköz csatlakoztatása (C#)](howto-connect-windowsiotcore.md)
* [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)
