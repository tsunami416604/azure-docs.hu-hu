---
title: "Az Azure IoT Hub csomóponttal eszközökről fájlok feltöltése |} Microsoft Docs"
description: "Hogyan tölthet fel a fájlokat az eszközről a felhőbe Azure IoT-eszközök SDK for Node.js használatával. Egy Azure blob tároló feltöltött fájlok tárolják."
services: iot-hub
documentationcenter: nodejs
author: msebolt
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: v-masebo
ms.openlocfilehash: cff0f2fc664e0c09bfa1f8f0e0d488a049a6f448
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Az eszközről a felhőbe, IoT-központ fájlok feltöltése

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag épít, a kód a [IoT Hub-felhő eszközre üzenetek](iot-hub-node-node-c2d.md) mutatjuk be, hogyan használható az oktatóanyag a [fájl feltöltése képességeit az IoT-központ](iot-hub-devguide-file-upload.md) feltölteni a fájlt [Azure blob tárolási](../storage/index.yml). Az oktatóanyag bemutatja, hogyan számára:

- Biztonságosan adjon meg egy eszközt az Azure blob URI-fájl feltöltése.
- Az IoT-központ fájl feltöltése értesítések használatával indul el, az alkalmazás háttérbeli fájl feldolgozása.

A [Ismerkedés az IoT-központ](iot-hub-node-node-getstarted.md) és [IoT Hub-felhő eszközre üzenetek](iot-hub-node-node-c2d.md) oktatóanyagok alapvető eszköz-felhő és a felhő eszközre üzenetkezelési funkcióit az IoT-központ megjelenítése. Bizonyos esetekben azonban leképezése nem az eszközök elküldik üzenetbe a viszonylag kis eszközről a felhőbe, amely az IoT-központ fogadja az adatokat könnyen. Példa:

* Nagy fájlok, amelyek képeket
* Videók
* Nagy gyakoriságú lekérdező vibráció adatok
* Valamilyen előre feldolgozott adatokat.

Ezek a fájlok jellemzően eszközökkel, mint a felhőben feldolgozható [Azure Data Factory](../data-factory/introduction.md) vagy a [Hadoop](../hdinsight/index.md) verem. Ha egy eszközről kell felvidéki fájlok, a biztonsága és megbízhatósága szempontjából az IoT-központ továbbra is használhatja.

Ez az oktatóanyag végén, futtatja a Node.js-konzol két alkalmazásokat:

* **SimulatedDevice.js**, amely feltölt egy fájlt az IoT hub által biztosított SAS URI segítségével.
* **ReadFileUploadNotification.js**, amely fájl feltöltése értesítéseket fogad az IoT hub.

> [!NOTE]
> Az IoT-Központ támogatja sok eszköz platformok és nyelvek (beleértve a C, .NET, Javascript, Python vagy Java) Azure IoT eszközoldali SDK-k használatával. Tekintse meg a [Azure IoT fejlesztői központ] az eszköz csatlakoztatása az Azure IoT Hub részletes útmutatást.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A Node.js 4.0.x vagy újabb verziója.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Egy eszköz alkalmazás-fájl feltöltése

Ebben a szakaszban az eszköz alkalmazás lehet feltölteni a fájlt az IoT-központ hoz létre.

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

    ```nodejs
    'use strict';
    
    var fs = require('fs');
    var mqtt = require('azure-iot-device-mqtt').Mqtt;
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    ```

1. Adjon hozzá egy ```deviceconnectionstring``` változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le ```{deviceconnectionstring}``` nevű, az eszköz hozott létre a _létrehoz egy IoT-központot_ szakasz:

    ```nodejs
    var connectionString = '{deviceconnectionstring}';
    var filename = 'myimage.png';
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért a kapcsolati karakterlánc megtalálható a kódot: Ez nem az ajánlott eljárás, és attól függően, hogy a használati eset és architektúra érdemes figyelembe venni a biztonságosabb módszer a titkos kulcs tárolása.

1. Adja hozzá a következő kódot az ügyfél csatlakozni:

    ```nodejs
    var client = clientFromConnectionString(connectionString);
    console.log('Client connected');
    ```

1. Egy visszahívás létrehozásáról és használatáról a **uploadToBlob** függvény feltölteni a fájlt.

    ```nodejs
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

1. Egy képfájl másolja a `simulateddevice` mappa, és nevezze át `myimage.png`.

## <a name="receive-a-file-upload-notification"></a>Fájl feltöltése értesítést

Ebben a szakaszban egy Node.js-Konzolalkalmazás, hogy a fájl feltöltése értesítési üzeneteket fogad az IoT Hub létrehozása.

Használhatja a **iothubowner** befejeződik ez a szakasz az IoT Hub származó kapcsolati karakterlánc. A kapcsolati karakterlánc megtalálja a [Azure-portálon](https://portal.azure.com/) a a **megosztott hozzáférési házirend** panelen.

1. Hozzon létre egy ```fileuploadnotification``` nevű üres mappát.  A(z) ```fileuploadnotification``` mappában hozzon létre egy package.json fájlt úgy, hogy beírja a következő parancsot a parancssorba.  Fogadja el az összes alapértelmezett beállítást:

    ```cmd/sh
    npm init
    ```

1. A parancssorba a ```fileuploadnotification``` mappa telepítéséhez a következő parancsot a **azure-IOT hubbal** SDK-csomagot:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Egy szövegszerkesztő használatával hozzon létre egy **FileUploadNotification.js** fájlt a ```fileuploadnotification``` mappát.

1. Adja hozzá a következő ```require``` elején utasítások a **FileUploadNotification.js** fájlt:

    ```nodejs
    'use strict';
    
    var Client = require('azure-iothub').Client;
    ```

1. Adjon hozzá egy ```iothubconnectionstring``` változót, és ezzel hozzon létre egy **Ügyfél** példányt.  Cserélje le ```{iothubconnectionstring}``` a létrehozott IoT hub kapcsolati karakterlánccal a _létrehoz egy IoT-központot_ szakasz:

    ```nodejs
    var connectionString = '{iothubconnectionstring}';
    ```

    > [!NOTE]
    > Az egyszerűség kedvéért a kapcsolati karakterlánc megtalálható a kódot: Ez nem az ajánlott eljárás, és attól függően, hogy a használati eset és architektúra érdemes figyelembe venni a biztonságosabb módszer a titkos kulcs tárolása.

1. Adja hozzá a következő kódot az ügyfél csatlakozni:

    ```nodejs
    var serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Nyissa meg az ügyfél, és használja a **getFileNotificationReceiver** függvény állapot frissítését.

    ```nodejs
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

A parancsot a parancssorba a `fileuploadnotification` mappa, a következő parancsot:

```cmd/sh
node FileUploadNotification.js
```

A parancsot a parancssorba a `simulateddevice` mappa, a következő parancsot:

```cmd/sh
node SimulatedDevice.js
```

Az alábbi képernyőfelvételen látható kimenetét a **SimulatedDevice** alkalmazást:

![Szimulált eszköz alkalmazás kimenete](./media/iot-hub-node-node-file-upload/simulated-device.png)

Az alábbi képernyőfelvételen látható kimenetét a **FileUploadNotification** alkalmazást:

![Olvasási-fájl-feltöltési-értesítés alkalmazás kimenete](./media/iot-hub-node-node-file-upload/read-file-upload-notification.png)

A portál segítségével a tároló, konfigurálta a feltöltött fájl megtekintése:

![Fájl feltöltése](./media/iot-hub-node-node-file-upload/uploaded-file.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban megismerte fájlfeltöltéseket eszközökről leegyszerűsítése érdekében az IoT-központ fájl feltöltése funkcióinak használatát. IoT hub-szolgáltatások és a forgatókönyvet a következő cikkek az eltérések felfedezése továbbra is:

* [Programozott módon létrehoz egy IoT-központot][lnk-create-hub]
* [C SDK bemutatása][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

<!-- Links -->
[Azure IoT fejlesztői központ]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
