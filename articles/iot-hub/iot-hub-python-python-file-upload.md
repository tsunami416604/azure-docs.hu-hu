---
title: "Az Azure IoT Hub Python eszközökről fájlok feltöltése |} Microsoft Docs"
description: "Hogyan tölt fel az eszközről a felhőbe, Python-hez készült Azure IoT-eszközök SDK használatával. Egy Azure blob tároló feltöltött fájlok tárolják."
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: kgremban
ms.openlocfilehash: 5939f87684e92e1f95d39ea5bd52b424ca683acc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Az eszközről a felhőbe, IoT-központ fájlok feltöltése

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez az oktatóanyag a következő használata a [fájl feltöltése képességeit az IoT-központ](iot-hub-devguide-file-upload.md) feltölteni a fájlt [Azure blob storage](../storage/index.yml). Az oktatóanyag bemutatja, hogyan számára:

- Biztonságosan adja meg a tároló feltölteni a fájlt.
- A Python-ügyfél használatával keresztül az IoT hub-fájl feltöltése.

A [Ismerkedés az IoT-központ](iot-hub-node-node-getstarted.md) és [IoT Hub-felhő eszközre üzenetek](iot-hub-node-node-c2d.md) oktatóanyagok alapvető eszköz-felhő és a felhő eszközre üzenetkezelési funkcióit az IoT-központ megjelenítése. Bizonyos esetekben azonban leképezése nem az eszközök elküldik üzenetbe a viszonylag kis eszközről a felhőbe, amely az IoT-központ fogadja az adatokat könnyen. Ha egy eszközről kell felvidéki fájlok, a biztonsága és megbízhatósága szempontjából az IoT-központ továbbra is használhatja.

> [!NOTE]
> Az IoT Hub Python SDK jelenleg csak feltöltését támogatja a szöveges fájlok például **.txt** fájlokat.

Ez az oktatóanyag végén a Python Konzolalkalmazás futtatja:

* **FileUpload.py**, amely feltölt egy fájlt a Python eszközt SDK segítségével.

> [!NOTE]
> Az IoT-Központ támogatja sok eszköz platformok és nyelvek (beleértve a C, .NET, Javascript, Python vagy Java) Azure IoT eszközoldali SDK-k használatával. Tekintse meg a [Azure IoT fejlesztői központ] az eszköz csatlakoztatása az Azure IoT Hub részletes útmutatást.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](http://azure.microsoft.com/pricing/free-trial/) néhány perc alatt.)


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Egy eszköz alkalmazás-fájl feltöltése

Ebben a szakaszban az eszköz alkalmazás lehet feltölteni a fájlt az IoT-központ hoz létre.

1. A parancssorba a következő parancsot a telepítendő a **azure-IOT hubbal-eszközügyfél** csomag:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Egy szövegszerkesztő használatával hozzon létre egy **FileUpload.py** működő mappában található fájlt.

1. Adja hozzá a következő `import` utasítások és változók elején a **FileUpload.py** fájlt. Cserélje le `deviceConnectionString` az IoT hub eszköz kapcsolati karakterlánccal:

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.HTTP

    FILENAME = 'sample.txt'
    ```

1. Hozzon létre egy visszahívás a **upload_blob** függvény:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Adja hozzá a következő kódot az ügyfél és a fájl feltöltése. Bevonhatja a `main` rutin:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )
        
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            client.upload_blob_async(FILENAME, FILENAME, os.path.getsize(FILENAME), blob_upload_conf_callback, 0)
        
            print ( "" )
            print ( "File upload initiated..." )
        
            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )
        
    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

1. Mentse és zárja be a **UploadFile.py** fájlt.

1. Minta szöveges fájl másolása a használt mappába, és adjon neki `sample.txt`.

    > [!NOTE]
    > Az IoT Hub Python SDK jelenleg csak feltöltését támogatja a szöveges fájlok például **.txt** fájlokat.


## <a name="run-the-application"></a>Az alkalmazás futtatása

Most már készen áll az alkalmazás futtatásához.

1. A működő mappájába, futtassa a következő parancsot a parancssorba:

    ```cmd/sh
    python FileUpload.py
    ```

1. Az alábbi képernyőfelvételen látható kimenetét a **FileUpload** alkalmazást:

    ![Szimulált eszköz alkalmazás kimenete](./media/iot-hub-python-python-file-upload/1.png)

1. A portál segítségével a tároló, konfigurálta a feltöltött fájl megtekintése:

    ![Fájl feltöltése](./media/iot-hub-python-python-file-upload/2.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban megismerte fájlfeltöltéseket eszközökről leegyszerűsítése érdekében az IoT-központ fájl feltöltése funkcióinak használatát. IoT hub-szolgáltatások és a forgatókönyvet a következő cikkek az eltérések felfedezése továbbra is:

* [Programozott módon létrehoz egy IoT-központot][lnk-create-hub]
* [C SDK bemutatása][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks]

<!-- Links -->
[Azure IoT fejlesztői központ]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/