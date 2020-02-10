---
title: Fájlok feltöltése az eszközökről az Azure IoT Hubba a Python használatával | Microsoft Docs
description: Fájlok feltöltése egy eszközről a felhőbe a Pythonhoz készült Azure IoT Device SDK-val. A feltöltött fájlok tárolása egy Azure Storage blob-tárolóban történik.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110752"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Fájlok feltöltése az eszközről a felhőbe IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez a cikk bemutatja, hogyan tölthet fel egy fájlt az [Azure Blob Storage](../storage/index.yml)-ba a [IoT hub fájlfeltöltés funkciójának](iot-hub-devguide-file-upload.md) használatával. Ez az oktatóanyag a következőket mutatja be:

* Biztonságos tárolási tárolót biztosít a fájlok feltöltéséhez.

* A Python-ügyféllel feltöltheti a fájlokat az IoT hub használatával.

Az [eszközről az IoT hub-ra való telemetria küldése](quickstart-send-telemetry-python.md) az eszközről a felhőbe irányuló alapszintű üzenetküldési funkciókat mutatja be IoT hub. Bizonyos helyzetekben azonban nem lehet könnyedén leképezni az eszközök által a IoT Hub által elfogadott viszonylag kis eszközről a felhőbe küldött üzeneteket. Ha egy eszközről szeretne fájlokat felvenni, továbbra is használhatja IoT Hub biztonságát és megbízhatóságát.

> [!NOTE]
> IoT Hub Python SDK jelenleg csak a karakteres fájlok, például **. txt** fájlok feltöltését támogatja.

Az oktatóanyag végén futtassa a Python-konzol alkalmazást:

* **FileUpload.py**, amely a Python-eszköz SDK-val feltölt egy fájlt a tárolóba.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Ez az útmutató az elavult v1 Python SDK-t használja, mivel a fájlfeltöltés funkció még nem lett implementálva az új v2 SDK-ban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése egy eszköz alkalmazásból

Ebben a szakaszban létrehozza az eszköz alkalmazást, hogy feltöltse a fájlt az IoT hub-ba.

1. A parancssorban futtassa a következő parancsot az **Azure-iothub-Device-Client** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Egy szövegszerkesztővel hozzon létre egy tesztoldalt, amelyet fel fog tölteni a blob Storage-ba.

    > [!NOTE]
    > IoT Hub Python SDK jelenleg csak a karakteres fájlok, például **. txt** fájlok feltöltését támogatja.

3. Egy szövegszerkesztővel hozzon létre egy **FileUpload.py** -fájlt a munkamappában.

4. Adja hozzá a következő `import` utasításokat és változókat a **FileUpload.py** -fájl elején. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. A fájlban cserélje le a `[Device Connection String]`t az IoT hub-eszközének a kapcsolatok karakterláncára. Cserélje le a `[Full path to file]`t a létrehozott tesztüzenet elérési útjára, vagy bármely olyan fájlra, amelyet fel szeretne tölteni az eszközön. Cserélje le a `[File name for storage]`t arra a névre, amelyet a fájl a blob Storage-ba való feltöltése után szeretne megadni. 

6. Visszahívás létrehozása a **upload_blob** függvényhez:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Adja hozzá a következő kódot az ügyfél összekapcsolásához és a fájl feltöltéséhez. A `main` rutint is tartalmazza:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

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

8. Mentse és zárjuk be a **UploadFile.py** fájlt.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Most már készen áll az alkalmazás futtatására.

1. Futtassa a következő parancsot egy parancssorban a munkamappájában:

    ```cmd/sh
    python FileUpload.py
    ```

2. Az alábbi képernyőképen a **FileUpload** alkalmazás kimenete látható:

    ![Szimulált eszköz alkalmazás kimenete](./media/iot-hub-python-python-file-upload/1.png)

3. A portál használatával megtekintheti a feltöltött fájlt a konfigurált tárolóban:

    ![Feltöltött fájl](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a IoT Hub fájlfeltöltés funkcióit az eszközökről történő fájlfeltöltés egyszerűsítése érdekében. A IoT hub funkcióit és forgatókönyveit továbbra is megismerheti a következő cikkekkel:

* [IoT hub programozott módon történő létrehozása](iot-hub-rm-template-powershell.md)

* [A C SDK bemutatása](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)
