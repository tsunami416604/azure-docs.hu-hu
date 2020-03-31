---
title: Felhőből eszközre irányuló üzenetek az Azure IoT Hub (Python) használatával | Microsoft dokumentumok
description: Felhőből az eszközre irányuló üzenetek küldése egy eszközre egy Azure IoT hubból az Azure IoT SDK-k pythonhoz használatával. Egy szimulált eszközalkalmazást módosítasz a felhőből az eszközre irányuló üzenetek fogadásához, és módosíthat egy háttéralkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110419"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Felhőből eszközre irányuló üzenetek küldése az IoT Hubbal (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás háttérkiszolgáló között. A [Telemetriaküldése egy eszközről egy IoT hub](quickstart-send-telemetry-python.md) rövid útmutató bemutatja, hogyan hozhat létre egy IoT hub, egy eszköz identitását, és a kód egy szimulált eszköz alkalmazás, amely elküldi az eszköz-felhő üzeneteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetriai adatok küldése eszközről egy IoT hubra](quickstart-send-telemetry-python.md)épül. Ez megmutatja, hogyan:

* A megoldás háttér-tartalék, felhőből az eszközre üzeneteket küldhet egyetlen eszközre az IoT Hubon keresztül.

* Felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldás háttérrendszeréről kérje a kézbesítési nyugtázást (*visszajelzést)* az IoT Hubról az eszközre küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekről az [IoT Hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Python konzolalkalmazást futtat:

* **SimulatedDevice.py**, az alkalmazás módosított verziója a [telemetriai adatok küldése az eszközről egy IoT hub,](quickstart-send-telemetry-python.md)amely csatlakozik az IoT hubhoz, és fogadja a felhőből az eszközre üzeneteket.

* **SendCloudToDeviceMessage.py**, amely egy felhőből az eszközre üzenetet küld a szimulált eszközalkalmazásnak az IoT Hubon keresztül, majd megkapja a kézbesítési nyugtázást.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszközalkalmazásban

Ebben a szakaszban hozzon létre egy Python konzolalkalmazást az eszköz szimulálására és az IoT hubról felhőből az eszközre irányuló üzenetek fogadására.

1. Szövegszerkesztő használatával hozzon létre egy **SimulatedDevice.py** fájlt.

2. A `import` **SimulatedDevice.py** fájl elején adja hozzá a következő állításokat és változókat:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Adja hozzá a következő kódot **SimulatedDevice.py** fájlhoz. Cserélje le a(z) "{deviceConnectionString}" helyőrző értéket a [telemetria küldése az eszközről egy IoT hubról](quickstart-send-telemetry-python.md) rövid útmutatóba létrehozott eszköz csatlakoztatási karakterláncára:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Adja hozzá a következő függvényt a fogadott üzenetek konzolra történő nyomtatásához:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Adja hozzá a következő kódot az ügyfél inicializálásához, és várja meg a felhőből az eszközre irányuló üzenetet:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Adja hozzá a következő fő funkciót:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Mentse és zárja be **SimulatedDevice.py** fájlt.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben létrehoz egy háttérszolgáltatás felhőből az eszközre üzeneteket az IoT hub on létrehozott [Telemetriai adatok küldése egy eszközről egy IoT hub.](quickstart-send-telemetry-python.md) Felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből eszközre irányuló üzenet küldése

Ebben a szakaszban hozzon létre egy Python-konzolalkalmazást, amely felhőből az eszközre irányuló üzeneteket küld a szimulált eszközalkalmazásnak. Szüksége van az eszköz azonosítóját az eszköz hozzáadott az [eszköz küldése telemetriai adatok](quickstart-send-telemetry-python.md) at egy eszköz egy IoT hub rövid útmutató. Szüksége van az IoT hub kapcsolati karakterláncra is, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben.](#get-the-iot-hub-connection-string)

1. Szövegszerkesztő használatával hozzon létre egy **SendCloudToDeviceMessage.py** fájlt.

2. A `import` **SendCloudToDeviceMessage.py** fájl elején adja hozzá a következő állításokat és változókat:

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Adja hozzá a következő kódot **SendCloudToDeviceMessage.py** fájlhoz. Cserélje le a(z) "{iot hub connection string}" és "{device id}" helyőrző értékeket a korábban említett IoT hub kapcsolati karakterláncra és eszközazonosítóra:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Adja hozzá a következő funkciót a visszacsatolási üzenetek konzolra való nyomtatásához:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Adja hozzá a következő kódot, hogy üzenetet küldjön az eszközére, és kezelje a visszajelzési üzenetet, amikor az eszköz nyugtázza a felhőből az eszközre irányuló üzenetet:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Adja hozzá a következő fő funkciót:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Mentse és zárja be **SendCloudToDeviceMessage.py** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. Nyisson meg egy parancssort, és telepítse az **Azure IoT Hub Device SDK pythonhoz.**

    ```shell
    pip install azure-iothub-device-client
    ```

2. A parancssorban futtassa a következő parancsot a felhőből az eszközre irányuló üzenetek figyeléséhez:

    ```shell
    python SimulatedDevice.py
    ```

    ![A szimulált eszközalkalmazás futtatása](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Nyisson meg egy új parancssort, és telepítse az **Azure IoT Hub Service SDK pythonhoz című szolgáltatását.**

    ```shell
    pip install azure-iothub-service-client
    ```

4. A parancssorban futtassa a következő parancsot a felhőből az eszközre irányuló üzenet küldéséhez, és várja meg az üzenetvisszajelzést:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Az alkalmazás futtatása a felhőből az eszközre parancs elküldéséhez](./media/iot-hub-python-python-c2d/send-command.png)

5. Jegyezze fel az eszköz által fogadott üzenetet.

    ![Fogadott üzenet](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat felhőből az eszközre irányuló üzeneteket.

Az IoT Hubot használó teljes körű megoldások példáinak megtekintéséhez tekintse meg az [Azure IoT Remote Monitoring megoldásgyorsító című témakört.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Ha többet szeretne megtudni a megoldások IoT Hubkal való fejlesztéséről, tekintse meg az [IoT Hub fejlesztői útmutatóját.](iot-hub-devguide.md)
