---
title: Ismerkedés az Azure IoT Hub Device Twins (Python) szolgáltatással | Microsoft Docs
description: Az Azure IoT Hub-eszközök ikrek használata címkék hozzáadásához és IoT Hub-lekérdezés használatához. A Pythonhoz készült Azure IoT SDK-k segítségével megvalósíthatja a szimulált eszköz alkalmazást és egy olyan szolgáltatási alkalmazást, amely hozzáadja a címkéket, és futtatja a IoT Hub lekérdezést.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 62385f4bd07f4b80dc3d571d409e16c7e0dca205
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667830"
---
# <a name="get-started-with-device-twins-python"></a>Ismerkedés a Device Twins (Python) eszközzel

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Az oktatóanyag végén két Python-konzolos alkalmazás lesz:

* **AddTagsAndQuery.py**, a Python háttér-alkalmazás, amely címkéket és lekérdezéseket tesz az ikreknek.

* **ReportConnectivity.py**, egy Python-alkalmazás, amely egy olyan eszközt szimulál, amely az IoT hubhoz a korábban létrehozott eszköz identitásával csatlakozik, és a kapcsolati feltételét jelzi.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Az előfeltételek a következő telepítési utasításokkal rendelkeznek.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely hely metaadatainak hozzáadását teszi elérhetővé a **{Device ID}** szolgáltatáshoz hozzárendelt eszközökhöz. Ezután lekérdezi az IoT hub eszközben tárolt ikreket, és kiválasztja a Redmondban található eszközöket, majd azokat, amelyek a mobil kapcsolatokat jelentik.

1. Nyisson meg egy parancssort, és telepítse a **Pythonhoz készült Azure IoT Hub szolgáltatási SDK-t**. Az SDK telepítése után zárja be a parancssort.

   ```
   pip install azure-iothub-service-client
   ```

2. Egy szövegszerkesztővel hozzon létre egy új **AddTagsAndQuery.py** -fájlt.

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához a szolgáltatás SDK-jából:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Adja hozzá a következő kódot, `[IoTHub Connection String]` és `[Device Id]` cserélje le a helyőrzőt az IoT hub és az előző szakaszban létrehozott eszköz azonosítójának helyére.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Adja hozzá a következő kódot a **AddTagsAndQuery.py** -fájlhoz:

     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    A **beállításjegyzék** -objektum az összes olyan metódust elérhetővé teszi, amely az eszközökhöz tartozó ikrek szolgáltatással való interakcióhoz szükséges. A kód először inicializálja a **beállításjegyzék** -objektumot, majd frissíti az eszközt a **deviceId**számára, és végül két lekérdezést futtat. Az első kiválasztja a **Redmond43** -üzemben található eszközökből származó ikreket, a második pedig csak azokat az eszközöket választja ki, amelyek a mobil hálózaton keresztül is csatlakoztatva vannak.

6. Adja hozzá a következő kódot a **AddTagsAndQuery.py** végén a **iothub_service_sample_run** függvény megvalósításához:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Futtassa az alkalmazást az alábbiakkal:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    A lekérdezés eredményei között egy eszközt kell látnia, amely a **Redmond43** -ban található összes eszközt kéri, a nem pedig a lekérdezést, amely a mobil hálózatot használó eszközökre korlátozza az eredményeket.

    ![első lekérdezés, amely a Redmondban lévő összes eszközt megjeleníti](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

A következő szakaszban létrehoz egy olyan eszközt, amely jelentést készít a kapcsolati adatokról, és megváltoztatja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszköz-alkalmazás létrehozása

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely a következőhöz csatlakozik a központhoz: **{Device ID}** , majd frissíti az eszköz Twin által jelentett tulajdonságait, hogy tartalmazza a mobil hálózaton keresztül csatlakoztatott adatokat.

1. Nyisson meg egy parancssort, és telepítse a **Pythonhoz készült Azure IoT Hub szolgáltatási SDK-t**. Az SDK telepítése után zárja be a parancssort.

    ```
    pip install azure-iothub-device-client
    ```

2. Egy szövegszerkesztővel hozzon létre egy új **ReportConnectivity.py** -fájlt.

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához a szolgáltatás SDK-jából:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Adja hozzá a következő kódot, és cserélje le `[IoTHub Device Connection String]` a helyőrzőt az előző szakaszban létrehozott IoT hub-eszközhöz tartozó kapcsolatok karakterláncra.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Adja hozzá a következő kódot a **ReportConnectivity.py** fájlhoz az eszköz ikrek funkciójának megvalósításához:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    Az **ügyfél** -objektum minden olyan módszert feltesz, amely az eszközön található ikrekkel való interakcióhoz szükséges. Az előző kód azt követően, hogy inicializálta az **ügyféltanúsítványt** , lekéri az eszközhöz tartozó Twin eszközt, és frissíti a jelentett tulajdonságot a kapcsolati információkkal.

6. Adja hozzá a következő kódot a **ReportConnectivity.py** végén a **iothub_client_sample_run** függvény megvalósításához:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Futtassa az eszköz alkalmazást:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Ekkor meg kell jelennie az eszközhöz tartozó ikrek frissítésének.

    ![ikrek frissítése](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. Most, hogy az eszköz bejelentette a kapcsolati adatait, mindkét lekérdezésben szerepelnie kell. Lépjen vissza, és futtassa újra a lekérdezéseket:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Ekkor a **{Device ID}** értéknek mindkét lekérdezési eredményben szerepelnie kell.

    ![második lekérdezés](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttérbeli alkalmazáshoz, és írt egy szimulált eszközt, amely az eszköz kapcsolati adatait jelenti a Twin-ben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az információkat a beállításjegyzék használatával.

Az alábbi források segítségével megismerheti a következőket:

* Telemetria küldése az eszközökről az első [lépések IoT hub](quickstart-send-telemetry-python.md) oktatóanyaggal.

* Konfigurálja az eszközöket a Device Twin által kívánt tulajdonságokkal, és [használja a kívánt tulajdonságokat az eszközök konfigurálása](tutorial-device-twins.md) oktatóanyaghoz.

* Vezérelheti az eszközöket interaktív módon (például egy felhasználó által vezérelt alkalmazásból való bekapcsolással), a [Direct Methods oktatóanyag használatával](quickstart-control-device-python.md) .