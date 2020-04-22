---
title: Kezdje az Azure IoT Hub modul identitásával & ikermodullal (Node.js)
description: Ismerje meg, hogyan hozhat létre modulidentitást és frissítheti az ikermodult az IoT SDK-k node.js használatával.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/26/2018
ms.custom: amqp
ms.openlocfilehash: 8e1599b1bd5db5e410e8bbd76fffbe0beb5f066e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732302"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-nodejs"></a>Az IoT Hub modulidentitás és az ikermodul (Node.js) első lépései

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Míg az Azure IoT Hub-eszköz identitása és az ikereszköz lehetővé teszi, hogy a háttéralkalmazás konfiguráljon egy eszközt, és láthatóvá teszi az eszköz feltételeit, a modulidentitás és a modul ikermodul biztosítja ezeket a képességeket az eszköz egyes összetevőiszámára. A megfelelő, több összetevős eszközök, például az operációs rendszeren vagy a belső vezérlőprogramon alapuló eszközök esetében lehetővé teszi az elkülönített konfigurációk és feltételek beállítását az egyes összetevőkhöz.

Az oktatóanyag végén két Node.js alkalmazás sal rendelkezik:

* A **CreateIdentities** egy eszközidentitást, egy modulidentitást valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat az eszközhöz és a modulügyfelekhez.

* Az **UpdateModuleTwinReportedProperties** a moduliker jelentett tulajdonságainak frissítését továbbítja az IoT Hub részére.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0.x vagy újabb verzió. [A fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Node.js-t ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Eszközidentitás és modulidentitás létrehozása az IoT Hubban

Ebben a szakaszban hozzon létre egy Node.js alkalmazást, amely létrehoz egy eszközidentitást és egy modulidentitást az IoT hub identitás-beállításjegyzékében. Egy eszköz vagy modul csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információt az [IoT Hub fejlesztői útmutatójának](iot-hub-devguide-identity-registry.md)"Identitásjegyzék" című szakaszában talál. A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Ezekkel az értékekkel az eszköz és a modul azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az azonosítók megkülönböztetik a kis- és nagybetűket.

1. Hozzon létre egy könyvtárat, hogy tartsa a kódot.

2. A könyvtáron belül először futtassa **az npm init -y-t** egy üres package.json alapértelmezett értékkel. Ez a kód projektfájlja.

3. Az **npm install -S\@azure-iothub modules-preview** futtatásával telepítse az SDK szolgáltatást a **node_modules** alkönyvtáron belül.

    > [!NOTE]
    > Az alkönyvtár neve node_modules a szómodult "csomópontkönyvtárat" jelent. A kifejezés itt semmi köze az IoT Hub-modulok.

4. Hozza létre a következő .js fájlt a könyvtárban. Nevezzük **add.js**. Másolja és illessze be a központi kapcsolati karakterláncot és a hub nevét.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

      // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

        // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Ez az alkalmazás létrehoz egy eszköz identitásazonosítóazonosítóazonosítót **myD myFirstDevice** és egy modul identitás id **myFirstModule** eszköz alatt **myFirstDevice**. (Ha a modulazonosító már létezik az identitásjegyzékben, a kód egyszerűen beolvassa a meglévő moduladatokat.) Az alkalmazás ezután megjeleníti az adott identitás elsődleges kulcsát. Ezt a kulcsot a szimulált modulalkalmazásban használja az IoT Hubhoz való csatlakozáshoz.

Futtassa ezt az add.js csomópont használatával. Ez ad egy kapcsolati karakterláncot az eszköz identitását, és egy másik at a modul identitását.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszköz- és modulidentitásokat. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információ: [IoT Hub fejlesztői útmutató.](iot-hub-devguide-identity-registry.md)

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Az ikermodul frissítése a Node.js eszköz SDK használatával

Ebben a szakaszban hozzon létre egy Node.js alkalmazást a szimulált eszközön, amely frissíti a modul iker jelentett tulajdonságait.

1. **A modulkapcsolati karakterlánc beszereznie** – Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/) Keresse meg az IoT Hubot, és kattintson az IoT-eszközök elemre. Keresse meg a myFirstDevice-t, nyissa meg, és láthatja, hogy a myFirstModule sikeresen létrejött. Másolja ki a modul kapcsolati sztringjét. A következő lépés során szükség lesz rá.

   ![Az Azure Portal moduladatai](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Hasonlóan a fenti lépéshez, hozzon létre egy könyvtárat az eszköz kódjához, és az NPM segítségével inicializálja és telepítse az eszközt SDK **(npm install -S azure-iot-device-amqp\@modules-preview**).

   > [!NOTE]
   > Az npm telepítési parancs lassúnak tűnhet. Légy türelmes, sok kódot szed le a csomagtárolóból.

   > [!NOTE]
   > Ha olyan hibát lát, amely azt mondja, npm ERR! iktató hivatal hiba elemzési json, ez biztonságosfigyelmen kívül hagyni. Ha olyan hibát lát, amely azt mondja, npm ERR! iktató hivatal hiba elemzési json, ez biztonságosfigyelmen kívül hagyni.

3. Hozzon létre egy twin.js nevű fájlt. Másolja és illessze be a modul identitáskarakterláncát.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
        // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
        // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
        // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
        // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

4. Most futtassa ezt a parancsot **csomópont twin.js**.

   ```cmd/sh
   F:\temp\module_twin>node twin.js
   ```

   Ezután látni fogja:

   ```console
   client opened
   twin contents:
   { reported: { update: [Function: update], '$version': 1 },
     desired: { '$version': 1 } }
   new desired properties received:
   {"$version":1}
   twin state reported
   ```

## <a name="next-steps"></a>További lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszközfelügyelet – első lépések](iot-hub-node-node-device-management-get-started.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)