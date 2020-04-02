---
title: Fájlok feltöltése az eszközökről az Azure IoT Hubra pythonnal | Microsoft dokumentumok
description: Fájlok feltöltése az eszközről a felhőbe az Azure IoT-eszköz SDK python-alapú használatával. A feltöltött fájlok egy Azure storage blobtárolóban tárolódnak.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.openlocfilehash: 706e1920c6c4fe39e885fd3f5a631070545509ee
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529290"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Fájlok feltöltése az eszközről a felhőbe az IoT Hub (Python) segítségével

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ez a cikk bemutatja, hogyan használhatja az [IoT Hub fájlfeltöltési képességeit](iot-hub-devguide-file-upload.md) egy fájl [Azure blob storage-ba](../storage/index.yml)való feltöltéséhez. Ez az oktatóanyag a következőket mutatja be:

* Biztonságosan biztosítson tárolótárolót egy fájl feltöltéséhez.

* A Python-ügyfél segítségével töltsön fel egy fájlt az IoT hubon keresztül.

A [Telemetria küldése egy eszközről egy IoT hub](quickstart-send-telemetry-python.md) rövid útmutató bemutatja az Alapvető eszköz-felhő üzenetkezelési funkció az IoT Hub. Bizonyos esetekben azonban nem tudja könnyen leképezni az eszközöket küldött adatokat az IoT Hub által fogadott viszonylag kis méretű eszközről felhőbe irányuló üzenetekbe. Ha egy eszközről kell felnyitott fájlokat használnia, továbbra is használhatja az IoT Hub biztonságát és megbízhatóságát.

Az oktatóanyag végén futtatja a Python konzolalkalmazást:

* **FileUpload.py**, amely feltölt egy fájlt a python-eszköz SDK használatával.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Fájl feltöltése eszközalkalmazásból

Ebben a szakaszban hozza létre az eszközalkalmazást egy fájl IoT hubra való feltöltéséhez.

1. A parancssorban futtassa a következő parancsot az **azure-iot-device** csomag telepítéséhez. Ezzel a csomaggal koordinálhatja a fájlfeltöltést az IoT-központtal.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. A parancssorban futtassa a következő parancsot az [**azure.storage.blob**](https://pypi.org/project/azure-storage-blob/) csomag telepítéséhez. Ezzel a csomaggal hajthatja végre a fájlfeltöltést.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Hozzon létre egy tesztfájlt, amelyet feltölt a blob storage-ba.

1. Szövegszerkesztő használatával hozzon létre egy **FileUpload.py** fájlt a munkamappában.

1. Adja hozzá `import` a következő állításokat és változókat a **FileUpload.py** fájl elején.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. A fájlban `[Device Connection String]` cserélje le az IoT hub-eszköz kapcsolati karakterláncát. Cserélje `[Full path to local file]` le a létrehozott tesztfájl vagy a feltölteni kívánt fájl elérési útját.

1. Hozzon létre egy függvényt a fájl blobstorage-ba való feltöltéséhez:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Ez a függvény elemzi a *blob_info* struktúrát átadott, hogy hozzon létre egy URL-t, amely et használ inicializálására [azure.storage.blob.BlobClient.](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python) Ezután feltölti a fájlt az Azure blob storage ezzel az ügyféllel.

1. Adja hozzá a következő kódot az ügyfél csatlakoztatásához és a fájl feltöltéséhez:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Ez a kód létrehoz egy aszinkron **IoTHubDeviceClient,** és a következő API-kat használja a fájl feltöltésének kezelésére az IoT hub:

    * **get_storage_info_for_blob** az IoT hubtól kap információt a korábban létrehozott összekapcsolt tárfiókról. Ez az információ tartalmazza az állomásnevet, a tároló nevét, a blob nevét és egy SAS-jogkivonatot. A tárolási adatok átadott a store_blob függvény (az előző lépésben létrehozott), így a BlobClient ebben a függvényben hitelesíthető az Azure storage.The storage info is passed to the **store_blob** function (created in the previous step), so the **BlobClient** in that function can authenticate with Azure storage. A **get_storage_info_for_blob** módszer egy correlation_id is ad vissza, amelyet a **notify_blob_upload_status** metódus használ. A correlation_id az IoT Hub módja annak megjelölése, hogy melyik blobon dolgozik.

    * **notify_blob_upload_status** értesíti az IoT Hubot a blob storage-művelet állapotáról. Adja át a **get_storage_info_for_blob** módszerrel kapott correlation_id. Az IoT Hub minden olyan szolgáltatás értesítésére használja, amely a fájlfeltöltési feladat állapotáról szóló értesítést figyelhet.

1. Mentse és zárja be a **UploadFile.py** fájlt.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Most már készen áll az alkalmazás futtatására.

1. A munkamappában lévő parancssorban futtassa a következő parancsot:

    ```cmd/sh
    python FileUpload.py
    ```

2. A következő képernyőkép a **FileUpload** alkalmazás kimenetét mutatja be:

    ![Kimenet szimulált eszközalkalmazásból](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. A portál segítségével megtekintheti a feltöltött fájlt a konfigurált tárolóban:

    ![Feltöltött fájl](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az IoT Hub fájlfeltöltési képességeit az eszközökről történő fájlfeltöltés egyszerűsítéséhez. Az IoT hub funkcióit és forgatókönyveit a következő cikkekkel folytathatja:

* [IoT-központ létrehozása programozott módon](iot-hub-rm-template-powershell.md)

* [Bevezetés a C SDK-ba](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

További információ az Azure Blob Storage-ról az alábbi hivatkozásokkal:

* [Az Azure Blob Storage dokumentációja](https://docs.microsoft.com/azure/storage/blobs/)

* [Az Azure Blob Storage python API-dokumentációja](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
