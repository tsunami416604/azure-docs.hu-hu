---
title: Felhőből eszközre irányuló üzenetek az Azure IoT Hub (Python) használatával | Microsoft dokumentumok
description: Felhőből az eszközre irányuló üzenetek küldése egy eszközre egy Azure IoT hubból az Azure IoT SDK-k pythonhoz használatával. Egy szimulált eszközalkalmazást módosítasz a felhőből az eszközre irányuló üzenetek fogadásához, és módosíthat egy háttéralkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.openlocfilehash: 0c3b35eeed85dd3a1c44dea6ec46203eb812e1e8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257831"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Felhőből eszközre irányuló üzenetek küldése az IoT Hubbal (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációt több millió eszköz és egy megoldás háttérkiszolgáló között. A [Telemetriaküldése egy eszközről egy IoT hub](quickstart-send-telemetry-python.md) rövid útmutató bemutatja, hogyan hozhat létre egy IoT hub, egy eszköz identitását, és a kód egy szimulált eszköz alkalmazás, amely elküldi az eszköz-felhő üzeneteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a [telemetriai adatok küldése eszközről egy IoT hubra](quickstart-send-telemetry-python.md)épül. Ez megmutatja, hogyan:

* A megoldás háttér-tartalék, felhőből az eszközre üzeneteket küldhet egyetlen eszközre az IoT Hubon keresztül.

* Felhőből az eszközre irányuló üzenetek fogadása az eszközön.

A felhőből az eszközre irányuló üzenetekről az [IoT Hub fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál további információt.

Az oktatóanyag végén két Python konzolalkalmazást futtat:

* **SimulatedDevice.py**, az alkalmazás módosított verziója a [telemetriai adatok küldése az eszközről egy IoT hub,](quickstart-send-telemetry-python.md)amely csatlakozik az IoT hubhoz, és fogadja a felhőből az eszközre üzeneteket.

* **SendCloudToDeviceMessage.py**, amely felhőből az eszközre irányuló üzeneteket küld a szimulált eszközalkalmazásnak az IoT Hubon keresztül.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszközalkalmazásban

Ebben a szakaszban hozzon létre egy Python konzolalkalmazást az eszköz szimulálására és az IoT hubról felhőből az eszközre irányuló üzenetek fogadására.

1. A munkakönyvtár parancssorából telepítse az **Azure IoT Hub Device SDK for Python alkalmazást:**

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Szövegszerkesztő használatával hozzon létre egy **SimulatedDevice.py**nevű fájlt.

1. A `import` **SimulatedDevice.py** fájl elején adja hozzá a következő állításokat és változókat:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Adja hozzá a következő kódot **SimulatedDevice.py** fájlhoz. Cserélje `{deviceConnectionString}` le a helyőrző értéket az eszköz kapcsolati karakterláncára a [telemetriai adatok küldése eszközről egy IoT hub rövid útmutatóba:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adja hozzá a következő függvényt a fogadott üzenetek konzolra történő nyomtatásához:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Adja hozzá a következő kódot az ügyfél inicializálásához, és várja meg a felhőből az eszközre irányuló üzenetet:

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
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Adja hozzá a következő fő funkciót:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Mentse és zárja be a **SimulatedDevice.py** fájlt.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben egy háttérszolgáltatás létrehozása felhőből az eszközre üzenetek küldéséhez az IoT hub on létrehozott [Telemetriai adatok küldése egy eszközről egy IoT hub.](quickstart-send-telemetry-python.md) Felhőből az eszközre irányuló üzenetek küldéséhez a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Alapértelmezés szerint minden IoT Hub jön létre egy megosztott hozzáférési szabályzat nevű **szolgáltatás,** amely megadja ezt az engedélyt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Felhőből eszközre irányuló üzenet küldése

Ebben a szakaszban hozzon létre egy Python-konzolalkalmazást, amely felhőből az eszközre irányuló üzeneteket küld a szimulált eszközalkalmazásnak. Szüksége van az eszköz azonosítóját az eszköz hozzáadott az [eszköz küldése telemetriai adatok](quickstart-send-telemetry-python.md) at egy eszköz egy IoT hub rövid útmutató. Szüksége van az IoT hub kapcsolati karakterláncra is, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben.](#get-the-iot-hub-connection-string)

1. A munkakönyvtárban nyisson meg egy parancssort, és telepítse az **Azure IoT Hub Service SDK pythonhoz című témakört.**

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Szövegszerkesztő használatával hozzon létre egy **SendCloudToDeviceMessage.py**nevű fájlt.

1. A `import` **SendCloudToDeviceMessage.py** fájl elején adja hozzá a következő állításokat és változókat:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Adja hozzá a következő kódot **SendCloudToDeviceMessage.py** fájlhoz. Cserélje `{iot hub connection string}` le `{device id}` a és a helyőrző értékeket a korábban említett IoT hub kapcsolati karakterláncra és eszközazonosítóra:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Adja hozzá a következő kódot az üzenetek eszközre küldéséhez:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Adja hozzá a következő fő funkciót:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Mentse és zárja be **SendCloudToDeviceMessage.py** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A munkakönyvtár parancssorában futtassa a következő parancsot a felhőből az eszközre irányuló üzenetek figyeléséhez:

    ```shell
    python SimulatedDevice.py
    ```

    ![A szimulált eszközalkalmazás futtatása](./media/iot-hub-python-python-c2d/device-1.png)

1. Nyisson meg egy új parancssort a munkakönyvtárban, és futtassa a következő parancsot a felhőből az eszközre irányuló üzenetek küldéséhez:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Az alkalmazás futtatása a felhőből az eszközre parancs elküldéséhez](./media/iot-hub-python-python-c2d/service.png)

1. Jegyezze fel az eszköz által fogadott üzeneteket.

    ![Fogadott üzenet](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan küldhet és fogadhat felhőből az eszközre irányuló üzeneteket.

Az IoT Hubot használó teljes körű megoldások példáinak megtekintéséhez tekintse meg az [Azure IoT Remote Monitoring megoldásgyorsító című témakört.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Ha többet szeretne megtudni a megoldások IoT Hubkal való fejlesztéséről, tekintse meg az [IoT Hub fejlesztői útmutatóját.](iot-hub-devguide.md)
