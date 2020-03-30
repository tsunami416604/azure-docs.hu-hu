---
title: Fájlok feltöltése az eszközökről az Azure IoT Hubra pythonnal | Microsoft dokumentumok
description: Fájlok feltöltése az eszközről a felhőbe az Azure IoT-eszköz SDK python-alapú használatával. A feltöltött fájlok egy Azure storage blobtárolóban tárolódnak.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: f1c0c046c40ff8edbc33c5e93e4207d9fe2fc67a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110752"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Fájlok feltöltése az eszközről a felhőbe az IoT Hub (Python) segítségével

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez a cikk bemutatja, hogyan használhatja az [IoT Hub fájlfeltöltési képességeit](iot-hub-devguide-file-upload.md) egy fájl [Azure blob storage-ba](../storage/index.yml)való feltöltéséhez. Ez az oktatóanyag a következőket mutatja be:

* Biztonságosan biztosítson tárolótárolót egy fájl feltöltéséhez.

* A Python-ügyfél segítségével töltsön fel egy fájlt az IoT hubon keresztül.

A [Telemetria küldése egy eszközről egy IoT hub](quickstart-send-telemetry-python.md) rövid útmutató bemutatja az Alapvető eszköz-felhő üzenetkezelési funkció az IoT Hub. Bizonyos esetekben azonban nem tudja könnyen leképezni az eszközöket küldött adatokat az IoT Hub által fogadott viszonylag kis méretű eszközről felhőbe irányuló üzenetekbe. Ha egy eszközről kell felnyitott fájlokat használnia, továbbra is használhatja az IoT Hub biztonságát és megbízhatóságát.

> [!NOTE]
> Az IoT Hub Python SDK jelenleg csak a karakteralapú fájlok, például **a .txt** fájlok feltöltését támogatja.

Az oktatóanyag végén futtatja a Python konzolalkalmazást:

* **FileUpload.py**, amely feltölt egy fájlt a python-eszköz SDK használatával.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Ez az útmutató az elavult V1 Python SDK-t használja, mivel a Fájlfeltöltés funkció még nincs megvalósítva az új V2 SDK-ban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése eszközalkalmazásból

Ebben a szakaszban hozza létre az eszközalkalmazást egy fájl IoT hubra való feltöltéséhez.

1. A parancssorban futtassa a következő parancsot az **azure-iothub-device-client** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Szövegszerkesztő használatával hozzon létre egy tesztfájlt, amelyet a blob storage-ba fog feltölteni.

    > [!NOTE]
    > Az IoT Hub Python SDK jelenleg csak a karakteralapú fájlok, például **a .txt** fájlok feltöltését támogatja.

3. Szövegszerkesztő használatával hozzon létre egy **FileUpload.py** fájlt a munkamappában.

4. Adja hozzá `import` a következő állításokat és változókat a **FileUpload.py** fájl elején. 

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

5. A fájlban `[Device Connection String]` cserélje le az IoT hub-eszköz kapcsolati karakterláncát. Cserélje `[Full path to file]` le a létrehozott tesztfájl elérési útját, vagy a feltölteni kívánt fájlt az eszközön. Cserélje `[File name for storage]` le a fájlt a blob storage-ba való feltöltésután megadni kívánt névre. 

6. Visszahívás létrehozása a **upload_blob** függvényhez:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Adja hozzá a következő kódot az ügyfél csatlakoztatásához és a fájl feltöltéséhez. Is tartalmazza `main` a rutin:

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

8. Mentse és zárja be a **UploadFile.py** fájlt.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Most már készen áll az alkalmazás futtatására.

1. A munkamappában lévő parancssorban futtassa a következő parancsot:

    ```cmd/sh
    python FileUpload.py
    ```

2. A következő képernyőkép a **FileUpload** alkalmazás kimenetét mutatja be:

    ![Kimenet szimulált eszközalkalmazásból](./media/iot-hub-python-python-file-upload/1.png)

3. A portál segítségével megtekintheti a feltöltött fájlt a konfigurált tárolóban:

    ![Feltöltött fájl](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az IoT Hub fájlfeltöltési képességeit az eszközökről történő fájlfeltöltés egyszerűsítéséhez. Az IoT hub funkcióit és forgatókönyveit a következő cikkekkel folytathatja:

* [IoT-központ létrehozása programozott módon](iot-hub-rm-template-powershell.md)

* [Bevezetés a C SDK-ba](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)
