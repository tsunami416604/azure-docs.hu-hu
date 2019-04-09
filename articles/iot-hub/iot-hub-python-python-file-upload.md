---
title: Fájlok feltöltése eszközökről az Azure IoT hubra Python használatával |} A Microsoft Docs
description: Hogyan tölthetők fel fájlok egy eszközről a felhőbe a Pythonhoz készült Azure IoT eszközoldali SDK használatával. Feltöltött fájlok vannak tárolva egy Azure storage blob-tárolóba.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: kgremban
ms.openlocfilehash: 6195c37780acaf8c8f432fa09c5ac01a75363c04
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59265526"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Töltse fel a fájlokat az eszközről a felhőbe, az IoT hubbal

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez a cikk bemutatja, hogyan használhatja a [fájl feltöltése IoT Hub képességeiről](iot-hub-devguide-file-upload.md) feltölteni egy fájlt [az Azure blob storage-bA](../storage/index.yml). Ez az oktatóanyag a következőket mutatja be:

* Biztonságosan adja meg a storage-tároló feltölt egy fájlt.

* A Python-kliens használatával feltölthet egy fájlt az IoT hub segítségével.

A [telemetriát küldjön az IoT Hub](quickstart-send-telemetry-python.md) a rövid útmutató ismerteti az IoT Hub alapvető eszköz-felhő üzenetkezelési funkcióit. Bizonyos esetekben azonban leképezése nem tudja az eszközöket az IoT Hub elfogad viszonylag kis eszköz – felhő üzenetek küldése az adatok egyszerűen. Ha egy eszközről kell felvidéki fájlok, biztonságának és megbízhatóságának IoT-központ továbbra is használhatja.

> [!NOTE]
> IoT Hub Python SDK jelenleg csak a támogatja például szöveges fájlok feltöltése **.txt** fájlokat.

Ez az oktatóanyag végén a Python-Konzolalkalmazás futtatása:

* **FileUpload.py**, amely feltölt egy fájlt a Python eszközoldali SDK használatával.

> [!NOTE]
> IoT Hub által támogatott számos eszközplatformok és nyelveken (például a C, .NET, Javascript, Python és Java) az Azure IoT eszközoldali SDK-k használatával. Tekintse meg a [Azure IoT fejlesztői központ](https://azure.microsoft.com/develop/iot) részletesen ismerteti, hogy az eszköz csatlakoztatása Azure IoT Hub számára.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét](https://pip.pypa.io/en/stable/installing/).

* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra](https://www.microsoft.com/download/confirmation.aspx?id=48145) van szükség a Python natív DLL-jei használatához.

* Aktív Azure-fiók. Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.

* Azure-fiókjába, a fájlfeltöltési funkcióval tesztelési egy eszközidentitást az IoT hubot. 

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Töltsön fel egy fájlt egy eszköz alkalmazásból

Ebben a szakaszban az eszközalkalmazás feltölthet egy fájlt az IoT hub létrehozása.

1. A parancssorban futtassa a telepítéséhez a következő parancsot a **azure-iothub-device-client** csomag:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Egy szövegszerkesztővel hozzon létre egy blobtárolóba feltölteni kívánt fájl tesztelése.

    > [!NOTE]
    > IoT Hub Python SDK jelenleg csak a támogatja például szöveges fájlok feltöltése **.txt** fájlokat.

3. Egy szövegszerkesztővel hozzon létre egy **FileUpload.py** fájlt a munkamappában.

4. Adja hozzá a következő `import` utasításokat és változókat elején a **FileUpload.py** fájlt. 

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

5. A fájlban cserélje le `[Device Connection String]` a kapcsolati karakterlánccal, IoT hub-eszközök. Cserélje le `[Full path to file]` az Ön által létrehozott fájl tesztelése, vagy bármely, az eszközön, a feltölteni kívánt fájl elérési útját. Cserélje le `[File name for storage]` a névre, kíván adni a fájl után a blob storage fel van töltve. 

6. Hozzon létre egy visszahívást a **upload_blob** függvény:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. Adja hozzá a következő kódot az ügyfél csatlakozhat, és töltse fel a fájlt. Emellett tartalmazza a `main` rutin:

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

Most már készen áll az alkalmazás futtatásához.

1. A munkamappában egy parancssorban futtassa a következő parancsot:

    ```cmd/sh
    python FileUpload.py
    ```

2. Az alábbi képernyőfelvételen a kimenete a **FileUpload** alkalmazás:

    ![A szimulált eszközalkalmazás kimenete](./media/iot-hub-python-python-file-upload/1.png)

3. A portál használatával beállított storage-tárolót a feltöltött fájl megtekintése:

    ![A feltöltött fájl](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan egyszerűsítheti a fájlok feltöltése eszközökről a fájl feltöltése képességeit az IoT Hub használatával. Folytathatja az IoT hub szolgáltatásainak, és az ezekben a cikkekben forgatókönyvek megismerése:

* [IoT hub létrehozása programokon keresztül](iot-hub-rm-template-powershell.md)

* [Alapvető ismeretek az C SDK-ról](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)