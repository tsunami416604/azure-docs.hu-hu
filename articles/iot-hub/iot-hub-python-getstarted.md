---
title: "Ismerkedés az Azure IoT Hub (Python) szolgáltatással | Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan küldhet üzeneteket egy szimulált eszközről az Azure IoT Hubba a Pythonhoz készült Azure IoT SDK-k használatával."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/22/2017
ms.author: dkshir
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: d020fc6b51f3b20f56fa056e666ba9c7441a3065
ms.lasthandoff: 04/25/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>A szimulált eszköz csatlakoztatása az IoT Hubhoz Pythonnal
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Az oktatóanyag végén kettő Python-alkalmazással fog rendelkezni:

* A **CreateDeviceIdentity.py** egy eszközidentitást, valamint egy társított biztonsági kulcsot hoz létre, amellyel csatlakozhat a szimulált eszközalkalmazáshoz.
* A **SimulatedDevice.py** csatlakozik az IoT Hubhoz a korábban létrehozott eszközidentitással, és az MQTT protokoll használatával rendszeres időközönként telemetriai üzenetet küld.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k][lnk-hub-sdks] című témakörben talál további információt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* [Node.js 4.0 vagy újabb][lnk-node-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Ez az [IoT Hub Explorer eszköz][lnk-iot-hub-explorer] telepítéséhez szükséges.
* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ezzel létrehozta az IoT Hubot. Az oktatóanyag további részében használja az IoT Hub-gazdagépnevet és kapcsolati karakterláncot.

> [!NOTE]
> Az IoT Hubot könnyedén létrehozhatja egy parancssorban a Python- vagy Node.js-alapú Azure CLI használatával. Ennek lépéseit az [IoT Hub Azure CLI 2.0-vel történő létrehozásával][lnk-azure-cli-hub] foglalkozó cikk ismerteti. 
> 

## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása
Ez a szakasz egy Python-konzolalkalmazás létrehozásának lépéseit ismerteti, amely egy új eszközidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért lásd az [IoT Hub fejlesztői útmutatójának][lnk-devguide-identity] **Identitásjegyzék** című szakaszát. A konzolalkalmazás egy egyedi eszközazonosítót állít elő a futtatásakor, valamint egy kulcsot, amellyel az eszköz azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak.

1. Nyisson meg egy parancssort, és telepítse a **Pythonhoz készült Azure IoT Hub szolgáltatási SDK-t**. Az SDK telepítése után zárja be a parancssort.

    ```
    pip install azure-iothub-service-client
    ```

2. Hozzon létre egy Python-fájlt **CreateDeviceIdentity.py** néven. Nyissa meg a fájlt [egy szabadon választott Python-szerkesztőben/IDE-ben][lnk-python-ide-list], például az alapértelmezett [IDLE][lnk-idle] környezetben.

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához a szolgáltatás SDK-jából:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Adja hozzá a következő kódot, és az `[IoTHub Connection String]` helyőrzőjét cserélje le az IoT Hub előző szakaszban létrehozott kapcsolati karakterláncára. Bármilyen nevet használhat `DEVICE_ID` azonosítóként.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
3. Adja hozzá a következő függvényt az eszköz egyes információinak kinyomtatása érdekében.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Adja hozzá a következő függvényt az eszköz azonosítójának létrehozásához a Registry Manager használatával. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Végül adja hozzá a fő függvényt a következők szerint, és mentse a fájlt.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. A parancssorban futtassa a **CreateDeviceIdentity.py** fájlt az alábbiak szerint:

    ```python
    python CreateDeviceIdentity.py
    ```
6. Ekkor létre kell jönnie a szimulált eszköznek. Jegyezze fel az eszköz **deviceId** azonosítóját és **primaryKey** kulcsát. Ezekre az értékekre később szüksége lesz, amikor az IoT Hubhoz eszközként csatlakozó alkalmazást hoz létre.

    ![Eszköz létrehozása sikeres][1]

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információ: [IoT Hub fejlesztői útmutató][lnk-devguide-identity].
> 
> 


## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ez a szakasz egy olyan Python-konzolalkalmazás létrehozásának lépéseit ismerteti, amely egy eszközt szimulál, és az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubra.

1. Nyisson meg egy új parancssort, és telepítse a Pythonhoz készült Azure IoT Hub eszközoldali SDK-t az alábbiak szerint. A telepítés után zárja be a parancssort.

    ```
    pip install azure-iothub-device-client
    ```
2. Hozzon létre egy **SimulatedDevice.py** nevű fájlt. Nyissa meg a fájlt egy szabadon választott Python-szerkesztőben/IDE-ben (például az IDLE környezetben).

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához az eszközoldali SDK-ból.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Adja hozzá a következő kódot, és az `[IoTHub Device Connection String]` helyőrzőjét értékét cserélje le az eszköz kapcsolati karakterláncára. Az eszköz kapcsolati karakterláncának formátuma általában `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Az előző szakaszban létrehozott eszköz **deviceId** azonosítójára és **primaryKey** kulcsára cserélje le a `<deviceId>` és a `<primaryKey>` értékeit. A `<hostName>` nevet cserélje le az IoT Hub gazdagépnevére. Ez általában `<IoT hub name>.azure-devices.net`.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Adja hozzá a következő kódot egy megerősítő visszahívás küldéséhez. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Adja hozzá a következő kódot az eszközügyfél inicializálásához.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        return client
    ```
7. Adja hozzá a következő függvényt egy üzenet formázásához és elküldéséhez a szimulált eszközről az IoT Hubra.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Végül adja hozzá a fő függvényt. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Mentse és zárja be a **SimulatedDevice.py** fájlt. Most már készen áll az alkalmazás futtatására.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Üzenet fogadása a szimulált eszközről
Az eszközről érkező telemetriaüzenetek fogadásához egy, az IoT Hub által feltárt [Event Hubs][lnk-event-hubs-overview]-kompatibilis végpontot kell használnia, amely beolvassa az eszközről a felhőbe irányuló üzeneteket. Az Event Hubstól az IoT Hub Event Hubs-kompatibilis végpontjára érkező üzenetek feldolgozásával kapcsolatos információkért olvassa el [az Event Hubs használatának első lépéseit][lnk-eventhubs-tutorial] ismertető oktatóanyagot. Az Event Hubs egyelőre nem támogatja a telemetriát a Pythonban, így létrehozhat egy [Node.js](iot-hub-node-node-getstarted.md#D2C_node) vagy [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp) Event Hubs-alapú konzolalkalmazást az IoT Hubról érkező, az eszközről a felhőbe irányuló üzenetek olvasásához. Az oktatóanyag bemutatja, hogyan használható az [IoT Hub Explorer eszköz][lnk-iot-hub-explorer] ezeknek az eszközüzeneteknek az olvasására.

1. Nyisson meg egy parancssort, és telepítse az IoT Hub Explorert. 

    ```
    npm install -g iothub-explorer
    ```

2. Futtassa az alábbi parancsot a parancssorban az eszközről érkező, az eszközről a felhőbe irányuló üzenetek megfigyelésének indításához. Használja az IoT Hub kapcsolati karakterláncát a helyőrzőben a `--login` után.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Nyisson egy új parancssort, és lépjen a **SimulatedDevice.py** fájlt tartalmazó könyvtárba.

4. Futtassa a **SimulatedDevice.py** fájlt, amely rendszeres időközönként telemetriaadatokat küld az IoT Hubnak. 
   
    ```
    python SimulatedDevice.py
    ```
5. Vizsgálja meg az eszköz üzeneteit a parancssorban az előző szakaszban ismertetett IoT Hub Explorer futtatásával. 

    ![Eszközről felhőbe irányuló Python-üzenetek][2]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Ennek az eszközidentitásnak a segítségével lehetővé tette a szimulált eszközalkalmazásnak, hogy az eszközről a felhőbe irányuló üzeneteket küldjön az IoT Hubnak. Megfigyelte az IoT Hub által fogadott üzeneteket az IoT Hub Explorer eszköz segítségével. 

A Pythonhoz készült Azure IoT Hub SDK használatának részletesebb megismerése érdekében látogasson el [ebbe a GitHub-adattárba][lnk-python-github]. A Pythonhoz készült Azure IoT Hub szolgáltatási SDK üzenetküldési képességeinek áttekintéséhez töltse le és futtassa az [iothub_messaging_sample.py][lnk-messaging-sample] fájlt. A Pythonhoz készült Azure IoT Hub eszközoldali SDK használatával végzett eszközoldali szimulációkhoz töltse le és futtassa az [iothub_client_sample.py][lnk-client-sample] fájlt.

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Kapcsolódás az eszközhöz][lnk-connect-device]
* [Eszközfelügyelet – első lépések][lnk-device-management]
* [IoT átjáró SDK – első lépések][lnk-gateway-SDK]

Az IoT-megoldás kibővítésével és az eszközről a felhőbe irányuló üzenetek nagy léptékű feldolgozásával kapcsolatban tekintse meg [az eszközről a felhőbe irányuló üzenetek feldolgozását][lnk-process-d2c-tutorial] ismertető oktatóanyagot.

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

