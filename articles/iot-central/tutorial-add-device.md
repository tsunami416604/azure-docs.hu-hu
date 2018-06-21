---
title: Valós eszköz hozzáadása Azure IoT Central-alkalmazáshoz | Microsoft Docs
description: Operátorként valós eszközt adhat az Azure IoT Central-alkalmazáshoz.
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: dd68b65825c9c22453e0191d42a0fcce3b65ca64
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236086"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Oktatóanyag: Valós eszköz hozzáadása az Azure IoT Central-alkalmazáshoz

Ez az oktatóanyag bemutatja, hogyan adhat hozzá és konfigurálhat valós eszközt a Microsoft Azure IoT Central-alkalmazásához.

Ez az oktatóanyag két részből áll:

1. Először operátorként megismeri, hogyan adhat hozzá és konfigurálhat valós eszközt az Azure IoT Central -alkalmazásban. A jelen rész végén lekér egy kapcsolati sztringet, amelyet a második részben fog használni.
2. Ezt követően eszközfejlesztőként megismeri a valós eszközben lévő kódot. Az első részben lévő kapcsolati sztringet hozzáadja a mintakódhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új valós eszköz hozzáadása
> * Az új eszköz konfigurálása
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

   Az **Eszközkereső** a **Csatlakoztatott légkondicionáló** eszközsablont és azt a szimulált eszközt jeleníti meg, amely automatikusan jött létre, amikor a szerkesztő létrehozta az eszközsablont.

2. Valós csatlakoztatott légkondicionáló eszköz csatlakoztatásához válassza az **Új**, majd a **Valós** lehetőséget:

   ![Új, valós csatlakoztatott légkondicionáló eszköz hozzáadásának megkezdése](media/tutorial-add-device/newreal.png)

3. Át is nevezheti az új eszközt, ha kiválasztja az eszköz nevét, és szerkeszti az értéket:

   ![Az eszköz átnevezése](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Valós eszköz konfigurálása

A valós eszköz a **Csatlakoztatott légkondicionáló** eszközsablonból jön létre. Szerkesztőként a **Beállításokkal** konfigurálhatja az eszközt és állíthatja be a tulajdonságértékeket az eszközzel kapcsolatos információk rögzítéséhez.

1. A **Beállítások** lapon figyelje meg, hogy a **Megadott hőmérséklet** beállítás állapota **nincs frissítés** értékű. Ebben az állapotban marad, amíg a valós eszköz nem csatlakozik és nem fogadja el, hogy módosította a beállítást:

    ![Szinkronizálást mutató beállítások](media/tutorial-add-device/settingssyncing.png)

2. Az új, valós csatlakoztatott légkondicionáló eszköz **Tulajdonságok** lapján állítsa a **Sorozatszámot** **rcac0010** értékre, a **Belső vezérlőprogram verzióját** pedig 9.75 értékre. Ezután válassza a **Mentés** lehetőséget:

    ![Valós eszköz tulajdonságainak beállítása](media/tutorial-add-device/setproperties.png)

3. Szerkesztőként megtekintheti a valós eszköz **Mérések**, **Szabályok** és **Irányítópult** lapjait.

## <a name="get-connection-string-for-real-device-from-application"></a>Kapcsolati sztring lekérése a valós eszközhöz az alkalmazásból

Egy eszközfejlesztőnek be kell ágyaznia a valós eszköz *kapcsolati sztringjét* az eszközön futó kódba. A kapcsolati sztring lehetővé teszi, hogy az eszköz biztonságosan csatlakozzon az Azure IoT Central alkalmazáshoz. Minden eszközpéldány egyedi kapcsolati sztringgel rendelkezik. Az alábbi lépések bemutatják, hogyan keresheti meg az alkalmazásban egy eszközpéldány kapcsolati sztringjét:

1. A valós csatlakoztatott légkondicionáló eszköz **Eszköz** képernyőjén válassza **Az eszköz csatlakoztatása** lehetőséget:

    ![A kapcsolat információit megjelenítő hivatkozást mutató eszközoldal](media/tutorial-add-device/connectionlink.png)

2. A **Csatlakozás** oldalon másolja az **Elsődleges kapcsolati sztringet**, és mentse azt. Ezt az értéket az oktatóanyag második felében fogja használni. Egy eszközfejlesztő ezt az értéket használja az eszközön futó ügyfélalkalmazásban:

    ![Kapcsolatisztring-értékek](media/tutorial-add-device/connectionstring.png)

## <a name="prepare-the-client-code"></a>Az ügyfélkód előkészítése

A cikkben szereplő példakód [Node.js](https://nodejs.org/) nyelven van megírva, és pont elég kódot jelenít meg a következőkhöz:

* Csatlakozás eszközként az Azure IoT Central-alkalmazáshoz.
* Hőmérséklet-telemetria küldése csatlakoztatott légkondicionáló eszközként.
* Válasz a **Megadott hőmérséklet** beállítást használó operátornak.

A [Következő lépések](#next-steps) szakaszban szereplő „Útmutató” hivatkozások teljesebb mintákat nyújtanak, és bemutatják más programozói nyelvek használatát. Az eszközök Azure IoT Central-alkalmazáshoz való kapcsolódásának módjáról további információt az [Eszközkapcsolatok](concepts-connectivity.md) című cikkben talál.

A következő lépések bemutatják, hogyan készítheti elő a [Node.js](https://nodejs.org/) mintát:

1. Telepítse a [Node.js](https://nodejs.org/) 4.0.x vagy újabb verzióját a gépén. A Node.js az operációs rendszerek széles körében elérhető.

2. Hozzon létre egy `connectedairconditioner` nevű mappát a gépén.

3. A parancssori környezetben keresse meg a létrehozott `connectedairconditioner` mappát.

4. A Node.js projekt elindításához futtassa az alábbi parancsot az összes alapérték elfogadásával:

   ```cmd/sh
   npm init
   ```

5. A szükséges csomagok telepítéséhez futtassa az alábbi parancsot:

   ```cmd/sh
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

6. Egy szövegszerkesztővel hozzon létre egy **ConnectedAirConditioner.js** fájlt a `connectedairconditioner` mappában.

7. Adja hozzá a következő `require` utasításokat a **ConnectedAirConditioner.js** fájl elejéhez:

   ```javascript
   'use strict';

   var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
   var Message = require('azure-iot-device').Message;
   var ConnectionString = require('azure-iot-device').ConnectionString;
   ```

8. Adja a következő változódeklarációkat a fájlhoz:

   ```javascript
   var connectionString = '{your device connection string}';
   var targetTemperature = 0;
   var client = clientFromConnectionString(connectionString);
   ```

   > [!NOTE]
   > A `{your device connection string}` helyőrzőt egy későbbi lépésben fogja frissíteni.

9. Mentse az eddig végrehajtott módosításokat, de hagyja nyitva a fájlt.

## <a name="understand-how-client-code-maps-to-the-application"></a>Annak megértése, hogyan történik az ügyfélkód leképezése az alkalmazásra

Az előző szakaszban létrehozott egy Node.js vázprojektet az Azure IoT Central-alkalmazáshoz kapcsolódó alkalmazáshoz. Ebben a szakaszban hozzáadja a kódot a következők elvégzéséhez:

* Csatlakozás az Azure IoT Central-alkalmazáshoz
* Telemetria küldése az Azure IoT Central-alkalmazásnak
* Beállítások fogadása az Azure IoT Central-alkalmazásból

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

2. Az eszköz által támogatott beállítások, például a **setTemperature** meghatározásához adja hozzá a következő definíciót:

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

3. Az Azure IoT Centralból küldött beállítások kezeléséhez adja hozzá a következő függvényt, amely megkeresi és végrehajtja a megfelelő eszközkódot:

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

4. Adja hozzá a következő kódot az Azure IoT Central-alkalmazáshoz való csatlakozás befejezéséhez és az ügyfélkódban lévő függvények csatlakoztatásához:

   ```javascript
   // Handle device connection to Azure IoT Central.
   var connectCallback = (err) => {
     if (err) {
       console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
     } else {
       console.log('Device successfully connected to Azure IoT Central');
        // Send telemetry measurements to Azure IoT Central every 1 second.
       setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
       client.getTwin((err, twin) => {
         if (err) {
           console.log(`Error getting device twin: ${err.toString()}`);
         } else {
           // Apply device settings and handle changes to device settings.
           handleSettings(twin);
         }
       });
     }
   };

   client.open(connectCallback);
   ```

5. Mentse az eddig végrehajtott módosításokat, de hagyja nyitva a fájlt.

## <a name="configure-client-code-for-the-real-device"></a>Ügyfélkód konfigurálása valós eszközhöz

<!-- Add the connection string to the sample code, build, and run -->
Ha az ügyfélkódot az Azure IoT Central-alkalmazáshoz való kapcsolódáshoz szeretné konfigurálni, hozzá kell adnia a valós eszköz az oktatóanyag korábbi részében feljegyzett kapcsolati sztringjét.

1. A **ConnectedAirConditioner.js** fájlban keresse meg a következő kódsort:

   ```javascript
   var connectionString = '{your device connection string}';
   ```

2. A `{your device connection string}` helyére illessze be a valós eszköz kapcsolati sztringjét. A „Kapcsolati sztring lekérése a valós eszközhöz az alkalmazásból” című szakasz végén feljegyezte a kapcsolati sztringet.

3. Mentse a **ConnectedAirConditioner.js** fájl módosításait.

4. A mintakód futtatásához írja be a következő parancsot a parancssori környezetbe:

   ```cmd/sh
   node ConnectedAirConditioner.js
   ```

   > [!NOTE]
   > Győződjön meg róla, hogy a `connectedairconditioner` mappában áll a parancs futtatásakor.

5. Az alkalmazás a kimenetet a konzolban jeleníti meg:

   ![Az ügyfélalkalmazás kimenete](media/tutorial-add-device/output.png)

6. Körülbelül 30 másodperc múlva megjelenik a telemetria az eszköz **Mérések** oldalán:

   ![Valós telemetria](media/tutorial-add-device/realtelemetry.png)

7. A **Beállítások** lapon láthatja, hogy a beállítás most szinkronizálva van. Az eszköz első csatlakoztatásakor az megkapta a beállításértéket és elfogadta a módosítást:

   ![Szinkronizált beállítás](media/tutorial-add-device/settingsynced.png)

8. A **Beállítások** lapon állítsa az eszköz hőmérsékletét **95** értékre, és válassza az **Eszköz frissítése** lehetőséget. A mintaalkalmazás fogadja és feldolgozza ezt a módosítást:

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

Most, hogy valós eszközt csatlakoztatott az Azure IoT Central-alkalmazáshoz, itt talál néhány javasolt következő lépést:

Operátorként a következőket sajátítja el:

* [Saját eszközök kezelése](howto-manage-devices.md)
* [Eszközkészletek használata](howto-use-device-sets.md)
* [Egyéni elemzések létrehozása](howto-create-analytics.md)

Eszközfejlesztőként a következőket sajátítja el:

* [DevKit előkészítése és csatlakoztatása](howto-connect-devkit.md)
* [Raspberry Pi előkészítése és csatlakoztatása](howto-connect-raspberry-pi-python.md)
* [Általános Node.js-ügyfél csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-nodejs.md)