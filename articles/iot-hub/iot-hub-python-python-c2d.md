---
title: Felhőből az eszközre irányuló üzeneteket az Azure IoT Hub (Python) szolgáltatással |} A Microsoft Docs
description: Annak a felhőből az eszközre irányuló üzeneteket küld egy eszköz, az Azure IoT hubba a Pythonhoz készült Azure IoT SDK-k használatával. Módosítja egy szimulált eszközalkalmazás felhőből az eszközre irányuló üzenetek fogadása és módosíthat egy háttér-alkalmazást a felhőből az eszközre irányuló üzenetek küldéséhez.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: kgremban
ms.openlocfilehash: cab3e5ee64ced59e9eb861e468f7a3c1dc600e8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220738"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Az IoT Hub (Python) felhőből az eszközre irányuló üzenetek küldéséhez
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Bevezetés
Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel engedélyezheti a megbízható és biztonságos kétirányú kommunikációt több millió eszköz között, és megoldást biztosít a háttérrendszer. A [IoT Hub használatának első lépései] az oktatóanyag bemutatja, hogyan hozzon létre egy IoT hubot, azt az eszközidentitás létrehozását és kód az eszköz a felhőbe irányuló üzeneteket küld egy szimulált eszközalkalmazás.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ebben az oktatóanyagban épül [IoT Hub használatának első lépései]. Azt mutatja, hogy:

* A megoldás háttérrendszerének, a felhőből az eszközre irányuló üzenetek küldése IoT hubon keresztül egy adott eszköz.
* Az eszközön a felhőből az eszközre irányuló üzeneteket fogadni.
* A megoldás háttérrendszerének, a kérelmek kézbesítési nyugtázás (*visszajelzés*) az IoT Hub az eszközökre küldött üzenetek.

A felhőből az eszközre irányuló üzenetek további tájékoztatást talál a [IoT Hub fejlesztői útmutatójának][IoT Hub developer guide - C2D].

Ez az oktatóanyag végén két Python-konzolalkalmazással futtassa:

* **SimulatedDevice.py**, a létrehozott alkalmazás egy módosított verziója [IoT Hub használatának első lépései], amely csatlakozik az IoT hubhoz, és megkapja a felhőből az eszközre.
* **SendCloudToDeviceMessage.py**, amely a felhőből az eszközre üzenetet küld az IoT hubon keresztül a szimulált eszközalkalmazásnak, és annak kézbesítési nyugtázási majd kap.

> [!NOTE]
> Az IoT Hub SDK számos eszközplatformok és nyelveken (például a C, Java és Javascript) keresztül az Azure IoT eszközoldali SDK-k támogatással rendelkezik. Az eszköz csatlakoztatása, ebben az oktatóanyagban a kódot, és általában az Azure IoT hubba a részletes útmutatót lásd: a [Azure IoT fejlesztői központ].
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

> [!NOTE]
> `azure-iothub-service-client` és `azure-iothub-device-client` rendszerhez a *pip*-csomagok jelenleg csak Windows operációs rendszer alatt érhetőek el. A Linux/Mac OS tekintse meg a Linux és Mac OS-specifikus szakaszok a [a Python fejlesztőkörnyezet előkészítése] [lnk-python-devbox] bejegyzéshez.
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Üzenetek fogadása a szimulált eszköz alkalmazásban
Ebben a szakaszban hozzon létre egy Python-Konzolalkalmazás, az eszköz szimulálhat, és a felhőből az eszközre irányuló üzenetek fogadása az IoT hubról.

1. Egy szövegszerkesztővel hozzon létre egy **SimulatedDevice.py** fájlt.

1. Adja hozzá a következő `import` utasításokat és változókat elején a **SimulatedDevice.py** fájlt:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

1. Adja hozzá a következő kódot a **SimulatedDevice.py** fájlt. A "{deviceConnectionString}" helyőrző értékét cserélje le az eszköz kapcsolati karakterláncának létrehozott az eszköz a [IoT Hub használatának első lépései] oktatóanyag:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adja hozzá a következő függvényt nyomtassa ki a fogadott üzenetek a konzolhoz:
   
    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

1. Adja hozzá a következő kódot az ügyfél inicializálása, és várjon, amíg a felhőből az eszközre az üzenetet kapja:
   
    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

1. Adja hozzá a következő fő funkciót:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Mentse és zárja be **SimulatedDevice.py** fájlt.


## <a name="send-a-cloud-to-device-message"></a>Felhőből az eszközre irányuló üzenet küldése
Ebben a szakaszban egy Python-Konzolalkalmazás, amely a felhőből az eszközre irányuló üzeneteket küld a szimulált eszközalkalmazás létrehozása. Az eszköz azonosítója, az eszköz a hozzáadott van szüksége a [IoT Hub használatának első lépései] oktatóanyag. Emellett az IoT Hub kapcsolati karakterláncra, amely találhatja meg a hub a [Azure Portal].

1. Egy szövegszerkesztővel hozzon létre egy **SendCloudToDeviceMessage.py** fájlt.

1. Adja hozzá a következő `import` utasításokat és változókat elején a **SendCloudToDeviceMessage.py** fájlt:
   
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

1. Adja hozzá a következő kódot a **SendCloudToDeviceMessage.py** fájlt. A "{IoTHubConnectionString}" helyőrző értékét cserélje le a létrehozott IoT Hub kapcsolati karakterláncára az [IoT Hub használatának első lépései] oktatóanyag. A "{deviceId}" helyőrzőt cserélje le az eszköz azonosítója, az eszköz meg a következő lépésben a [IoT Hub használatának első lépései] oktatóanyag:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Adja hozzá a következő függvényt visszajelzés kiírásához a konzolhoz:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Adja hozzá a következő kódot egy üzenet küldéséhez az eszköz és az eszközre a felhőből az eszközre irányuló üzenet nyugtázza a visszajelzés üzenet kezelését:
   
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

1. Adja hozzá a következő fő funkciót:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. Mentse és zárja be **SendCloudToDeviceMessage.py** fájlt.


## <a name="run-the-applications"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. Nyisson meg egy parancssort, és telepítse a **Pythonhoz készült Azure IoT Hub eszközoldali SDK**.

    ```
    pip install azure-iothub-device-client
    ```

1. A parancssorban futtassa a felhőből az eszközre irányuló üzenetek figyeléséhez a következő parancsot:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![A szimulált eszközalkalmazás futtatása][img-simulated-device]

1. Nyisson meg egy új parancssort, és telepítse a **Pythonhoz készült Azure IoT Hub szolgáltatási SDK**.

    ```
    pip install azure-iothub-service-client
    ```

1. Egy parancssorban futtassa a üzenetküldés felhőből az eszközre, és várjon, amíg az üzenet visszajelzés a következő parancsot:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Futtassa az alkalmazást a felhőből az eszközre irányuló parancs küldése][img-send-command]
   
1. Megjegyzés: az üzenetet fogadja az eszköz.

    ![Üzenet érkezett][img-message-recieved]


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan a felhőből az eszközre irányuló üzenetek küldése és fogadása. 

Példák teljes, végpontok közötti megoldások, amely az IoT Hub használata a megtekintéséhez lásd: [Az Azure IoT távoli figyelési megoldásgyorsító].

Az IoT Hub megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Az IoT Hub fejlesztői útmutató].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[IoT Hub használatának első lépései]: quickstart-send-telemetry-node.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Az IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[Azure IoT fejlesztői központ]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Azure Portal]: https://portal.azure.com
[Az Azure IoT távoli figyelési megoldásgyorsító]: https://azure.microsoft.com/documentation/suites/iot-suite/
