---
title: Az Azure IoT Hub (Python) felhő eszközre üzenetek |} Microsoft Docs
description: Hogyan küldhetők felhő-eszközre küldött üzenetek eszközökre az Azure IoT-központ az Azure IoT SDK-k használata a Python. A szimulált eszköz alkalmazásnak, hogy a felhő-eszközre küldött üzenetek fogadására és módosíthat egy háttér-alkalmazást a felhőből eszközre küldéséhez módosítása.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 236758f1ff83ec37c39a44068879bea80f2ffdb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Az IoT-központ (Python) felhő eszközre-üzenetek
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Bevezetés
Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amellyel eszközök millióira közötti megbízható és biztonságos kétirányú kommunikáció engedélyezése és a megoldás háttérrendszere. A [Ismerkedés az IoT-központ] oktatóanyag bemutatja, hogyan létrehoz egy IoT-központot, azt egy eszközidentitás kiépítéséhez és kód egy szimulált eszköz alkalmazást, amelyet az eszköz a felhőbe küldött üzeneteket küld.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag épül [Ismerkedés az IoT-központ]. Megmutatja, hogyan számára:

* A megoldás háttérrendszeréhez, az IoT-központ keresztül egyetlen eszközt felhő eszközre üzeneteket küldeni.
* Az eszközön a felhőből eszközre üzeneteket fogadni.
* A megoldás háttérrendszeréhez, a kérelem kézbesítési nyugtázási (*visszajelzés*) IoT-központot egy eszközre küldött üzenetek esetében.

Felhő eszközre üzenetek további információt a [IoT Hub fejlesztői útmutató][IoT Hub developer guide - C2D].

Ez az oktatóanyag végén két Python konzol alkalmazások futtatása:

* **SimulatedDevice.py**, az alkalmazás létrehozása a módosított változatát [Ismerkedés az IoT-központ], amely csatlakozik az IoT hub és felhő eszközre üzeneteket fogad.
* **SendCloudToDeviceMessage.py**, melyik felhőalapú eszközre üzenetet küld az IoT-központ szimulált eszköz alkalmazás, és a szállítási nyugtázási majd megkapja.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek (például C, Java és Javascript) keresztül Azure IoT eszközoldali SDK-k SDK támogatása. Csatlakoztassa az eszközt, az oktatóanyag kódot, és általában Azure IoT Hub kapcsolatos lépésenkénti útmutatót lásd: a [Azure IoT fejlesztői központ].
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

> [!NOTE]
> `azure-iothub-service-client` és `azure-iothub-device-client` rendszerhez a *pip*-csomagok jelenleg csak Windows operációs rendszer alatt érhetőek el. A Linux vagy Mac OS tekintse meg a [Felkészülés a fejlesztési környezet a Python] [lnk-python-devbox] post a Linux és Mac OS-specifikus szakaszokat.
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>A szimulált eszköz alkalmazás üzeneteket fogadni
Ebben a szakaszban egy Python-Konzolalkalmazás szimulálja az eszközt, és a felhő-eszközre küldött üzenetek fogadása az IoT hub létrehozása.

1. Egy szövegszerkesztő használatával hozzon létre egy **SimulatedDevice.py** fájlt.

1. Adja hozzá a következő `import` utasítások és változók elején a **SimulatedDevice.py** fájlt:
   
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

1. Adja hozzá a következő kódot a **SimulatedDevice.py** fájlt. A "{deviceConnectionString}" helyőrző értékét lecserélheti az eszköz létrehozta az eszköz kapcsolati karakterlánca a [Ismerkedés az IoT-központ] oktatóanyag:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adja hozzá a következő függvény nyomtatni fogadott üzenetek a konzolhoz:
   
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

1. Adja hozzá a következő kódot az ügyfél inicializálása, és a felhő eszközre üzenet várakozási idő után:
   
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

1. Adja hozzá a következő fő funkciója:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Mentse és zárja be **SimulatedDevice.py** fájlt.


## <a name="send-a-cloud-to-device-message"></a>Felhő eszközre üzenet küldése
Ebben a szakaszban egy Python-Konzolalkalmazás, amely felhő eszközre üzeneteket küld a szimulált eszköz alkalmazást hoz létre. A hozzáadott eszköz az Eszközazonosítót van szüksége a [Ismerkedés az IoT-központ] oktatóanyag. Az IoT-központ kapcsolati karakterlánc, amely megtalálható a központ is kell a [Azure-portálon].

1. Egy szövegszerkesztő használatával hozzon létre egy **SendCloudToDeviceMessage.py** fájlt.

1. Adja hozzá a következő `import` utasítások és változók elején a **SendCloudToDeviceMessage.py** fájlt:
   
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

1. Adja hozzá a következő kódot a **SendCloudToDeviceMessage.py** fájlt. Cserélje le a "{IoTHubConnectionString}" helyőrző értékét az IoT-központ kapcsolati karakterlánccal létrehozott központ a [Ismerkedés az IoT-központ] oktatóanyag. A "{deviceId}" helyőrzőt cserélje le a hozzáadott eszköz az Eszközazonosítót az [Ismerkedés az IoT-központ] oktatóanyag:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Nyomtatni visszajelzés üzenetek a konzolhoz a következő függvény hozzáadása:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Adja hozzá a következő kódot üzenetet küldeni az eszközt, és a visszajelzés üzenet kezelését, amikor az eszköz elfogadja a felhőből eszközre üzenet:
   
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

1. Adja hozzá a következő fő funkciója:
   
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

1. Nyisson meg egy parancssort, és telepítse a **Azure IoT Hub eszköz SDK for Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. A parancssorban futtassa a következő parancsot a felhő-eszközre küldött üzenetek figyelésére:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![A szimulált eszköz alkalmazás futtatása][img-simulated-device]

1. Nyisson meg egy új parancssort, és telepítse a **Azure IoT Hub szolgáltatás SDK for Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. A parancssorba a következő paranccsal felhőből eszközre küldött, és várjon, amíg az üzenet-visszajelzés:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Futtassa az alkalmazásnak, hogy a felhő eszközre vonatkozó parancs küldése][img-send-command]
   
1. Jegyezze fel az üzenetet fogadja az eszköz.

    ![Üzenet érkezett][img-message-recieved]


## <a name="next-steps"></a>További lépések
Ebben az oktatóprogramban megismerte felhő eszközre üzeneteket küldjön és fogadjon. 

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [megoldásgyorsító Azure IoT távoli megfigyelési].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[Ismerkedés az IoT-központ]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[Azure IoT fejlesztői központ]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-portálon]: https://portal.azure.com
[megoldásgyorsító Azure IoT távoli megfigyelési]: https://azure.microsoft.com/documentation/suites/iot-suite/
