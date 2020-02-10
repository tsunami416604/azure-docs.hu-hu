---
title: Felhőből az eszközre irányuló üzenetek az Azure IoT Hub (Python) használatával | Microsoft Docs
description: A felhőből az eszközre irányuló üzenetek küldése egy Azure IoT hub-eszközről a Pythonhoz készült Azure IoT SDK-k használatával. Módosít egy szimulált eszközt a felhőből az eszközre irányuló üzenetek fogadására és a felhőből az eszközre irányuló üzenetek küldésére szolgáló háttérbeli alkalmazás módosítására.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110419"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Üzenetküldés a felhőből az eszközre IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás hátterében. A [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-python.md) -gyors üzembe helyezési útmutató bemutatja, hogyan hozhat létre egy IoT hubot, kiépítheti a benne lévő eszköz identitását, valamint kódot készíthet egy szimulált eszközről, amely az eszközről a felhőbe irányuló üzeneteket küld.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetria küldött eszközről egy IoT hubhoz](quickstart-send-telemetry-python.md)épít. A következőket mutatja be:

* A megoldás hátterében a felhőből az eszközre irányuló üzeneteket a IoT Hub használatával egyetlen eszközre küldheti.

* A felhőből az eszközre irányuló üzenetek fogadása az eszközön.

* A megoldási háttérből kérjen kézbesítési visszaigazolást (*visszajelzés*) a IoT hub eszközről küldött üzenetekhez.

A felhőből az eszközre irányuló üzenetekről a [IoT hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Python-konzolos alkalmazást fog futtatni:

* A **SimulatedDevice.py**az alkalmazás egy olyan módosított verziója, amely a [telemetria küldése az eszközről egy IoT hubhoz](quickstart-send-telemetry-python.md), amely csatlakozik a IoT hubhoz, és fogadja a felhőből az eszközre irányuló üzeneteket.

* A **SendCloudToDeviceMessage.py**, amely egy felhőből az eszközre irányuló üzenetet küld a szimulált eszköz alkalmazásnak IoT hubon keresztül, majd megkapja a kézbesítési visszaigazolást.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. A cikkben szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszköz alkalmazásban

Ebben a szakaszban egy Python-konzol alkalmazást hoz létre, amely szimulálja az eszközt, és fogadja a felhőből az eszközre irányuló üzeneteket az IoT hub-ból.

1. Egy szövegszerkesztővel hozzon létre egy **SimulatedDevice.py** -fájlt.

2. Adja hozzá a következő `import` utasításokat és változókat a **SimulatedDevice.py** -fájl elején:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Adja hozzá a következő kódot a **SimulatedDevice.py** fájlhoz. Cserélje le a (z) {deviceConnectionString} helyőrző értékét a [telemetria küldése eszközről egy IoT hub](quickstart-send-telemetry-python.md) -rövid útmutatóra létrehozott eszközhöz tartozó eszköz-kapcsolódási karakterlánccal.

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Adja hozzá a következő függvényt a fogadott üzenetek a konzolra való nyomtatásához:

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

5. Adja hozzá a következő kódot az ügyfél inicializálásához, és várja meg a felhőből az eszközre irányuló üzenet fogadását:

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

6. Adja hozzá a következő fő függvényt:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Mentse és zárjuk be a **SimulatedDevice.py** fájlt.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebből a cikkből megtudhatja, hogyan küldhet el egy háttér-szolgáltatást a felhőből az eszközre irányuló üzenetek küldéséhez a IoT hub használatával, amelyet a [telemetria küldése az eszközről az IoT hubhoz](quickstart-send-telemetry-python.md)hozott létre. A felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás kapcsolódási** engedélyére. Alapértelmezés szerint minden IoT Hub a **szolgáltatás** nevű közös hozzáférési házirenddel jön létre, amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszköz alkalmazásnak. Szüksége lesz annak az eszköznek az eszköz-AZONOSÍTÓJÁRA, amelyet a [telemetria küldése az eszközről az IoT hub](quickstart-send-telemetry-python.md) -gyors üzembe helyezéshez adott meg. Szüksége lesz a korábban átmásolt IoT hub-kapcsolatok karakterláncára is a [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)című részében.

1. Egy szövegszerkesztővel hozzon létre egy **SendCloudToDeviceMessage.py** -fájlt.

2. Adja hozzá a következő `import` utasításokat és változókat a **SendCloudToDeviceMessage.py** -fájl elején:

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

3. Adja hozzá a következő kódot a **SendCloudToDeviceMessage.py** fájlhoz. Cserélje le a "{IOT hub-beli kapcsolatok karakterlánc}" és "{Device id}" helyőrző értékeket az IoT hub-beli kapcsolatok karakterláncára és a korábban feljegyzett eszköz AZONOSÍTÓra:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Adja hozzá a következő függvényt a visszajelzési üzenetek a konzolra való nyomtatásához:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Adja hozzá a következő kódot, hogy üzenetet küldjön az eszköznek, és kezelje a visszajelzési üzenetet, amikor az eszköz tudomásul veszi a felhőből az eszközre irányuló üzenetet:

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

6. Adja hozzá a következő fő függvényt:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Mentse és zárjuk be a **SendCloudToDeviceMessage.py** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. Nyisson meg egy parancssort, és telepítse a **Pythonhoz készült Azure IoT hub Device SDK**-t.

    ```shell
    pip install azure-iothub-device-client
    ```

2. A parancssorban futtassa a következő parancsot a felhőből az eszközre irányuló üzenetek figyeléséhez:

    ```shell
    python SimulatedDevice.py
    ```

    ![A szimulált eszköz alkalmazásának futtatása](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Nyisson meg egy új parancssort, és telepítse a **Pythonhoz készült Azure IoT hub Service SDK**-t.

    ```shell
    pip install azure-iothub-service-client
    ```

4. A parancssorban futtassa a következő parancsot egy felhőből az eszközre irányuló üzenet küldéséhez, és várja meg az üzenet visszajelzését:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Futtassa az alkalmazást a felhőből az eszközre irányuló parancs küldéséhez.](./media/iot-hub-python-python-c2d/send-command.png)

5. Jegyezze fel az eszköz által fogadott üzenetet.

    ![Üzenet érkezett](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat üzeneteket a felhőből az eszközre.

Ha szeretné megtekinteni a IoT Hubt használó teljes körű megoldásokat, tekintse meg az [Azure IoT távoli monitorozási megoldásának gyorsítása](https://azure.microsoft.com/documentation/suites/iot-suite/)című témakört.

Ha többet szeretne megtudni a IoT Hub-megoldások fejlesztéséről, tekintse meg a [IoT hub fejlesztői útmutatót](iot-hub-devguide.md).
