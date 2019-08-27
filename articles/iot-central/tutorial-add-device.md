---
title: Valós eszköz hozzáadása Azure IoT Central-alkalmazáshoz | Microsoft Docs
description: Operátorként valós eszközt adhat az Azure IoT Central-alkalmazáshoz.
author: sandeeppujar
ms.author: sandeepu
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 8731d66c9d2dca0043307ac2f6a0d1828aeaa275
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050514"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Oktatóanyag: Valós eszköz hozzáadása az Azure IoT Central-alkalmazáshoz

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

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

Mielőtt elkezdené, a szerkesztőnek el kell végeznie legalább az Azure IoT Central-alkalmazás létrehozásának első szerkesztői oktatóanyagát: [Új eszköztípus definiálása](tutorial-define-device-type.md) (Kötelező)


Telepítse a [Node. js](https://nodejs.org/) -verziót 8.0.0 vagy újabb verzióra a fejlesztői gépen. A parancssorban `node --version` futtatva ellenőrizhető a verzió. A Node.js az operációs rendszerek széles körében elérhető.

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Ha valós eszközt szeretne hozzáadni az alkalmazáshoz, használja az [Új eszköztípus definiálása](tutorial-define-device-type.md) című oktatóanyagban létrehozott **Csatlakoztatott légkondicionáló** eszközsablont.

1. Új eszköz hozzáadása operátorként a bal oldali navigációs menüben válassza az **eszközök** lehetőséget:

   ![Csatlakoztatott légkondicionálót megjelenítő Eszközkereső oldal](media/tutorial-add-device/explorer.png)

   A **Device Explorer** megjeleníti a **csatlakoztatott légkondicionáló** eszköz sablonját és egy szimulált eszközt. Amikor létrehoz egy eszközt, IoT Central automatikusan létrehoz egy szimulált eszközt.

2. A valódi csatlakoztatott légkondicionáló eszköz csatlakoztatásának megkezdéséhez válassza **+** a (z), majd a **Real**:

   ![Új, valós csatlakoztatott légkondicionáló eszköz hozzáadásának megkezdése](media/tutorial-add-device/newreal.png)

3. Adja meg az eszköz AZONOSÍTÓját (kisbetűnek kell lennie), vagy használja a javasolt eszköz AZONOSÍTÓját. Vagy megadhatja az új eszköz nevét, és kiválaszthatja a **Létrehozás** lehetőséget.

   ![Az eszköz átnevezése](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Valós eszköz konfigurálása

A valós eszköz a **Csatlakoztatott légkondicionáló** eszközsablonból jön létre. A **Beállításokkal** konfigurálhatja az eszközt és beállíthatja a tulajdonságértékeket az eszközzel kapcsolatos információk rögzítéséhez.

1. A **Beállítások** lapon figyelje meg, hogy a **Megadott hőmérséklet** beállítás állapota **nincs frissítés** értékű. Ebben az állapotban marad, amíg a valós eszköz nem csatlakozik az alkalmazáshoz, és nem igazolja vissza, hogy módosította a beállítást.

    ![Szinkronizálást mutató beállítások](media/tutorial-add-device/settingssyncing.png)

2. Az új, valódi eszköz **Tulajdonságok** lapján a szolgáltatás és az utolsó szolgáltatás dátuma is szerkeszthető tulajdonságok. A sorozatszám és a belső vezérlőprogram verziójának mezője üres, amíg az eszközt nem csatlakoztatja az alkalmazáshoz. Ezeket az írásvédett értékeket a rendszer az eszközről továbbítja, és nem szerkeszthető.

    ![Valós eszköz eszköztulajdonságai](media/tutorial-add-device/setproperties1.png)

3. Megtekintheti a valós eszköz **Mérések**, **Szabályok** és **Irányítópult** lapját.

## <a name="generate-connection-string"></a>Kapcsolatok karakterláncának létrehozása

Egy eszközfejlesztőnek be kell ágyaznia a valós eszköz *kapcsolati sztringjét* az eszközön futó kódba. A kapcsolati karakterlánc lehetővé teszi, hogy az eszköz biztonságosan kapcsolódjon az alkalmazáshoz. A következő lépések bemutatják a kapcsolati karakterlánc előállítását és az ügyfél Node. js-kódjának előkészítését.

## <a name="prepare-the-client-code"></a>Az ügyfélkód előkészítése

A cikkben szereplő mintakód a [Node. js](https://nodejs.org/) -ben van megírva, és az alábbihoz elég kódot mutat:

* Csatlakozás eszközként az Azure IoT Central-alkalmazáshoz.
* Hőmérséklet-telemetria küldése csatlakoztatott légkondicionáló eszközként.
* Eszköztulajdonságok elküldése az Azure IoT Central-alkalmazásnak.
* Válasz a **Megadott hőmérséklet** beállítást használó operátornak.
* Az Echo parancs kezelése az Azure IoT Central-alkalmazásból.

A [következő lépések](#next-steps) szakaszban felsorolt cikkek több teljes mintát tartalmaznak, és más programozási nyelveket mutatnak be. Az eszközök Azure IoT Central-alkalmazáshoz való kapcsolódásának módjáról további információt az [Eszközkapcsolatok](concepts-connectivity.md) című cikkben talál.

A következő lépések bemutatják, hogyan készítheti elő a [Node.js](https://nodejs.org/) mintát:

### <a name="get-the-device-connection-information"></a>Az eszköz csatlakoztatási adatainak beolvasása

1. Az alkalmazásban az eszközpéldányok kapcsolati sztringje az IoT Central által megadott eszközinformációkból jön létre.

   A valós csatlakoztatott légkondicionáló eszköz képernyőjén válassza a **Csatlakozás** lehetőséget.

   ![A kapcsolat információit megjelenítő hivatkozást mutató eszközoldal](media/tutorial-add-device/connectionlink.png)

1. Jegyezze fel a **hatókör-azonosítót**, az **eszköz azonosítóját** és az **elsődleges kulcs** értékeit az eszköz csatlakoztatása lapon. Ezeket az értékeket a következő lépésben fogja használni.

   ![Kapcsolat részletei](media/tutorial-add-device/device-connect.png)

### <a name="generate-the-connection-string"></a>A kapcsolatok karakterláncának létrehozása

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

### <a name="prepare-the-nodejs-project"></a>A Node. js-projekt előkészítése

1. Hozzon létre egy `connectedairconditioner` nevű mappát a fejlesztői gépen.

1. A parancssori környezetben keresse meg a létrehozott `connectedairconditioner` mappát.

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

## <a name="review-client-code"></a>Ügyfél kódjának áttekintése

Az előző szakaszban létrehozott egy Node.js vázprojektet az Azure IoT Central-alkalmazáshoz kapcsolódó alkalmazáshoz. A következő lépés a kód hozzáadása:

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
    * Visszaigazolást küld az Azure IoT Central alkalmazásnak.

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

## <a name="configure-client-code"></a>Az ügyfél kódjának konfigurálása

<!-- Add the connection string to the sample code, build, and run -->
Ha az ügyfélkódot az Azure IoT Central-alkalmazáshoz való kapcsolódáshoz szeretné konfigurálni, hozzá kell adnia a valós eszköz az oktatóanyag korábbi részében feljegyzett kapcsolati sztringjét.

1. A **ConnectedAirConditioner.js** fájlban keresse meg a következő kódsort:

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. A `{your device connection string}` helyére illessze be a valós eszköz kapcsolati sztringjét. Az előző lépésben létrehozott kapcsolatok sztringet másolta.

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

   ![Valódi ~ ~ telemetria](media/tutorial-add-device/realtelemetry.png)

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

Most, hogy a valódi eszközt csatlakoztatta az Azure IoT Central-alkalmazáshoz, néhány javasolt lépés.

Operátorként a következőket sajátítja el:

* [Saját eszközök kezelése](howto-manage-devices.md)
* [Eszközkészletek használata](howto-use-device-sets.md)
* [Egyéni elemzések létrehozása](howto-use-device-sets.md)

Eszközfejlesztőként a következőket sajátítja el:

* [Fejlesztői készlet-eszköz előkészítése és csatlakoztatása (C)](howto-connect-devkit.md)
* [Málna PI (Python) előkészítése és összekötése](howto-connect-raspberry-pi-python.md)
* [Málna PI (C#) előkészítése és összekapcsolása](howto-connect-raspberry-pi-csharp.md)
* [Windows 10 IoT Core-eszköz előkészítése és csatlakoztatásaC#()](howto-connect-windowsiotcore.md)
* [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)
