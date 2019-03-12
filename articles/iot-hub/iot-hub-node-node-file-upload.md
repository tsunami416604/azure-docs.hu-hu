---
title: Fájlok feltöltése eszközökről az Azure IoT hubhoz a Node |} A Microsoft Docs
description: Hogyan tölthetők fel fájlok egy eszközről a felhőbe az Azure IoT eszközoldali SDK for node.js használatával. Feltöltött fájlok vannak tárolva egy Azure storage blob-tárolóba.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: f110fe84ab09e930947411a79c950af21cc5334c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544514"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Töltse fel a fájlokat az eszközről a felhőbe, az IoT hubbal

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ebben az oktatóanyagban található kódot használja fel a [küldése a felhőből az eszközre irányuló üzenetek IoT hubbal való](iot-hub-node-node-c2d.md) megmutatjuk, hogyan használható az oktatóanyagban a [fájl feltöltése IoT Hub képességeiről](iot-hub-devguide-file-upload.md) feltölteni egy fájlt [az Azure blob tárolási](../storage/index.yml). Ez az oktatóanyag a következőket mutatja be:

- Biztonságosan adja meg az eszköz egy Azure blob-URI-fájl feltöltése.
- Az IoT Hub fájl feltöltése értesítések használatával elindíthatja a feldolgozása a fájlt az alkalmazás háttérrendszere.

A [IoT Hub használatának első lépései](quickstart-send-telemetry-node.md) az oktatóanyag bemutatja az IoT Hub alapvető eszköz-felhő üzenetkezelési funkcióit. Bizonyos esetekben azonban leképezése nem tudja az eszközöket az IoT Hub elfogad viszonylag kis eszköz – felhő üzenetek küldése az adatok egyszerűen. Példa:

* Nagy méretű képeket tartalmazó fájlok
* Videók
* Rezgés adatok mintavételezése, nagyon gyakori
* Valamilyen előre feldolgozott adatokat.

Ezek a fájlok jellemzően a felhőben, mint például az eszközök használatával feldolgozott kötegelt [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.yml) stack. Ha egy eszközről kell felvidéki fájlok, biztonságának és megbízhatóságának IoT-központ továbbra is használhatja.

Ez az oktatóanyag végén két Node.js-konzolalkalmazással futtassa:

* **SimulatedDevice.js**, amely feltölt egy fájlt, a storage-bA az IoT hub által biztosított SAS URI használatával.
* **ReadFileUploadNotification.js**, amely a fájl feltöltése értesítéseket fogad az IoT hubról.

> [!NOTE]
> IoT Hub által támogatott számos eszközplatformok és nyelveken (például a C, .NET, Javascript, Python és Java) az Azure IoT eszközoldali SDK-k használatával. Tekintse meg a [Azure IoT fejlesztői központ] részletesen ismerteti, hogy az eszköz csatlakoztatása Azure IoT Hub számára.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Töltsön fel egy fájlt egy eszköz alkalmazásból

Ebben a szakaszban az eszközalkalmazás feltölthet egy fájlt az IoT hub létrehozása.

1. Hozzon létre egy ```simulateddevice``` nevű üres mappát.  A(z) ```simulateddevice``` mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

1. Telepítse az **azure-iot-device** eszközoldali SDK csomagot és az **azure-iot-device-mqtt** csomagot. Ehhez futtassa egy parancssorból a következő parancsot a(z) ```simulateddevice``` mappában:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Egy szövegszerkesztővel hozzon létre egy **SimulatedDevice.js** fájlt a(z) ```simulateddevice``` mappában.

1. Adja hozzá a következő ```require``` utasításokat a **SimulatedDevice.js** fájl elejéhez:

    ```javascript
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Adjon hozzá egy ```deviceconnectionstring``` változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le ```{deviceconnectionstring}``` létrehozott az eszköz nevére az _hozzon létre egy IoT hubot_ szakaszban:

    ```javascript
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért a kapcsolati karakterláncot a kódban szerepel: Ez nem ajánlott gyakorlat, és a használati esetekre és architektúra függően érdemes figyelembe venni a titkos kulcs tárolása biztonságosabb módszert is.

1. Adja hozzá az ügyfél kapcsolódni a következő kódot:

    ```javascript
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Hozzon létre egy visszahívást, és használja a **uploadToBlob** meghívásával feltölti a fájlt.

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

1. Mentse és zárja be a **SimulatedDevice.js** fájlt.

1. A képfájl másolja a `simulateddevice` mappát, és nevezze át `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Fájlfeltöltési üzenetet fogadni

Ebben a szakaszban egy Node.js-konzolalkalmazást, amely a fájl feltöltése értesítési üzeneteket fogad a IoT Hub létrehozása.

Használhatja a **iothubowner** befejeződik ez a szakasz az IoT Hub kapcsolati karakterláncra. A kapcsolati karakterlánc található a [az Azure portal](https://portal.azure.com/) a a **megosztott hozzáférési szabályzat** panelen.

1. Hozzon létre egy ```fileuploadnotification``` nevű üres mappát.  A(z) ```fileuploadnotification``` mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

1. A parancssorban a ```fileuploadnotification``` mappában futtassa a következő paranccsal telepíthető a **azure-iothub** SDK-csomagot:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Egy szövegszerkesztővel hozzon létre egy **FileUploadNotification.js** fájlt a ```fileuploadnotification``` mappát.

1. Adja hozzá a következő ```require``` elején található utasításokat a **FileUploadNotification.js** fájlt:

    ```javascript
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Adjon hozzá egy ```iothubconnectionstring``` változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le ```{iothubconnectionstring}``` az szakaszban létrehozott IoT hub kapcsolati karakterlánccal a _hozzon létre egy IoT hubot_ szakaszban:

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért a kapcsolati karakterláncot a kódban szerepel: Ez nem ajánlott gyakorlat, és a használati esetekre és architektúra függően érdemes figyelembe venni a titkos kulcs tárolása biztonságosabb módszert is.

1. Adja hozzá az ügyfél kapcsolódni a következő kódot:

    ```javascript
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Nyissa meg az ügyfél és az a **getFileNotificationReceiver** függvény ügyfélállapot-frissítés fogadásához.

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

1. Mentse és zárja be a **FileUploadNotification.js** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

A parancsot a parancssorba a `fileuploadnotification` mappában futtassa a következő parancsot:

```cmd/sh
node FileUploadNotification.js
```

A parancsot a parancssorba a `simulateddevice` mappában futtassa a következő parancsot:

```cmd/sh
node SimulatedDevice.js
```

Az alábbi képernyőfelvételen a kimenete a **SimulatedDevice** alkalmazás:

![A szimulált eszközalkalmazás kimenete](./media/iot-hub-node-node-file-upload/simulated-device.png)

Az alábbi képernyőfelvételen a kimenete a **FileUploadNotification** alkalmazás:

![Olvasási-fájl – feltöltés – értesítés alkalmazás kimenete](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

A portál használatával beállított storage-tárolót a feltöltött fájl megtekintése:

![A feltöltött fájl](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan egyszerűsítheti a fájlok feltöltése eszközökről a fájl feltöltése képességeit az IoT Hub használatával. Folytathatja az IoT hub szolgáltatásainak, és az ezekben a cikkekben forgatókönyvek megismerése:

* [IoT hub létrehozása programozott módon][lnk-create-hub]
* [Bevezetés a C SDK-t][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

<!-- Links -->
[Azure IoT fejlesztői központ]: https://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
