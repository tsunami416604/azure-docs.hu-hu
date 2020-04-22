---
title: Fájlok feltöltése az eszközökről az Azure IoT Hubba csomóponttal | Microsoft dokumentumok
description: Fájlok feltöltése az eszközről a felhőbe az Azure IoT-eszköz SDK node.js használatával. A feltöltött fájlok egy Azure storage blobtárolóban tárolódnak.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom: mqtt
ms.openlocfilehash: af9743233a61e8e6d816b362d35e6a38735df35b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732249"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Fájlok feltöltése az eszközről a felhőbe az IoT Hub (Node.js) segítségével

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag az [IoT Hubmal való felhőből eszközre üzenetek küldése](iot-hub-node-node-c2d.md) című oktatóanyagban található kódra épül, amely bemutatja, hogyan használhatja az [IoT Hub fájlfeltöltési funkcióit](iot-hub-devguide-file-upload.md) egy fájl [Azure blobstorage-ba](../storage/index.yml)való feltöltéséhez. Ez az oktatóanyag a következőket mutatja be:

* Biztonságosan biztosítson egy eszközt egy Azure blob URI-val egy fájl feltöltéséhez.

* Az IoT Hub fájlfeltöltési értesítései segítségével indítsa el a fájl feldolgozását az alkalmazás háttérrendszerében.

A [Telemetria küldése egy eszközről egy IoT hub](quickstart-send-telemetry-node.md) rövid útmutató bemutatja az Alapvető eszköz-felhő üzenetkezelési funkció az IoT Hub. Bizonyos esetekben azonban nem tudja könnyen leképezni az eszközöket küldött adatokat az IoT Hub által fogadott viszonylag kis méretű eszközről felhőbe irányuló üzenetekbe. Például:

* Képeket tartalmazó nagyfájlok
* Videók
* Nagy frekvenciájú rezgésadatok
* Valamilyen előre feldolgozott adat.

Ezeket a fájlokat általában kötegelt a felhőben olyan eszközök használatával, mint az [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) verem használatával. Ha egy eszközről kell felnyitott fájlokat használnia, továbbra is használhatja az IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén két Node.js konzolalkalmazást futtat:

* **SimulatedDevice.js**, amely feltölt egy fájlt a tárolóba az IoT hub által biztosított SAS URI használatával.

* **ReadFileUploadNotification.js**, amely fájlfeltöltési értesítéseket kap az IoT hubról.

> [!NOTE]
> Az IoT Hub számos eszközplatformot és nyelvet támogat (például a C, a .NET, a Javascript, a Python és a Java) az Azure IoT-eszköz SDK-kon keresztül. Tekintse meg az [Azure IoT Developer Center] lépésről-lépésre, hogyan csatlakoztathatja az eszközt az Azure IoT Hub.

## <a name="prerequisites"></a>Előfeltételek

* Node.js 10.0.x vagy újabb verzió. [A fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Node.js-t ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése eszközalkalmazásból

Ebben a szakaszban hozza létre az eszközalkalmazást egy fájl IoT hubra való feltöltéséhez.

1. Hozzon létre egy ```simulateddevice``` nevű üres mappát.  A(z) ```simulateddevice``` mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. Telepítse az **azure-iot-device** eszközoldali SDK csomagot és az **azure-iot-device-mqtt** csomagot. Ehhez futtassa egy parancssorból a következő parancsot a(z) ```simulateddevice``` mappában:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Egy szövegszerkesztővel hozzon létre egy **SimulatedDevice.js** fájlt a(z) ```simulateddevice``` mappában.

4. Adja hozzá a következő ```require``` utasításokat a **SimulatedDevice.js** fájl elejéhez:

    ```javascript
    'use strict';

    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

5. Adjon hozzá egy `deviceconnectionstring` változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje `{deviceconnectionstring}` le az *IoT Hub létrehozása* szakaszban létrehozott eszköz nevére:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért a kapcsolati karakterlánc szerepel a kódban: ez nem ajánlott gyakorlat, és a használati esettől és az architektúrától függően érdemes megfontolni a titkos adattár biztonságosabb módjait.

6. Adja hozzá a következő kódot az ügyfél csatlakoztatásához:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

7. Hozzon létre egy visszahívást, és használja a **uploadToBlob** függvényt a fájl feltöltéséhez.

    ```javascript
    fs.stat(filename, function (err, stats) {
        const rr = fs.createReadStream(filename);

        client.uploadToBlob(filename, rr, stats.size, function (err) {
            if (err) {
                console.error('Error uploading file: ' + err.toString());
            } else {
                console.log('File uploaded');
            }
        });
    });
    ```

8. Mentse és zárja be a **SimulatedDevice.js** fájlt.

9. Másolja a képfájlt `simulateddevice` a mappába, és nevezze át. `myimage.png`

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben létrehoz egy háttérszolgáltatást, amely fájlfeltöltési értesítési üzeneteket kap az [Eszközről egy IoT hubra](quickstart-send-telemetry-node.md)létrehozott IoT hubról. A fájlfeltöltési értesítési üzenetek fogadásához a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltési értesítés fogadása

Ebben a szakaszban hozzon létre egy Node.js konzolalkalmazást, amely fájlfeltöltési értesítési üzeneteket kap az IoT Hubtól.

Az IoT Hub **iothubowner** kapcsolati karakterláncai használatával befejezheti ezt a szakaszt. A kapcsolati karakterlánc ot az [Azure Portalon](https://portal.azure.com/) találja a **Megosztott hozzáférési szabályzat** panelen.

1. Hozzon létre egy ```fileuploadnotification``` nevű üres mappát.  A(z) ```fileuploadnotification``` mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

2. A mappában lévő ```fileuploadnotification``` parancssorból futtassa a következő parancsot az **azure-iothub** SDK-csomag telepítéséhez:

    ```cmd/sh
    npm install azure-iothub --save
    ```

3. Szövegszerkesztő használatával hozzon létre egy **FileUploadNotification.js** fájlt a `fileuploadnotification` mappában.

4. A `require` **FileUploadNotification.js** fájl elején adja hozzá a következő állításokat:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Adjon hozzá egy `iothubconnectionstring` változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje `{iothubconnectionstring}` le a helyőrző értéket az IoT hub kapcsolati karakterláncára, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzői közben:](#get-the-iot-hub-connection-string)

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért a kapcsolati karakterlánc szerepel a kódban: ez nem ajánlott gyakorlat, és a használati esettől és az architektúrától függően érdemes megfontolni a titkos adattár biztonságosabb módjait.

6. Adja hozzá a következő kódot az ügyfél csatlakoztatásához:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

7. Nyissa meg az ügyfelet, és használja a **getFileNotificationReceiver** függvényt az állapotfrissítések fogadásához.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

8. Mentse és zárja be a **FileUploadNotification.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

A `fileuploadnotification` mappa parancssorában futtassa a következő parancsot:

```cmd/sh
node FileUploadNotification.js
```

A `simulateddevice` mappa parancssorában futtassa a következő parancsot:

```cmd/sh
node SimulatedDevice.js
```

A következő képernyőképen a **SimulatedDevice** alkalmazás kimenete látható:

![Kimenet szimulált eszközalkalmazásból](./media/iot-hub-node-node-file-upload/simulated-device.png)

A következő képernyőkép a **FileUploadNotification** alkalmazás kimenetét mutatja be:

![Kimenet a read-file-upload-notification alkalmazásból](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

A portál segítségével megtekintheti a feltöltött fájlt a konfigurált tárolóban:

![Feltöltött fájl](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az IoT Hub fájlfeltöltési képességeit az eszközökről történő fájlfeltöltés egyszerűsítéséhez. Az IoT hub funkcióit és forgatókönyveit a következő cikkekkel folytathatja:

* [IoT-központ létrehozása programozott módon](iot-hub-rm-template-powershell.md)

* [Bevezetés a C SDK-ba](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)
