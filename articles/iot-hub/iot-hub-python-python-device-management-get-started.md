---
title: Ismerkedés az Azure IoT Hub-eszközkezelés (Python) szolgáltatással | Microsoft Docs
description: Távoli eszköz újraindításának kezdeményezése IoT Hub-eszközkezelés használatával. A Pythonhoz készült Azure IoT SDK-val egy olyan szimulált eszköz alkalmazást hozhat létre, amely közvetlen metódust és egy közvetlen metódust meghívó szolgáltatási alkalmazást tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: daba6601c0da2bd6d1b76897e7ce1ff75da67b5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667979"
---
# <a name="get-started-with-device-management-python"></a>Ismerkedés az Eszközkezelővel (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A Azure Portal használatával hozzon létre egy IoT Hub, és hozzon létre egy eszköz-identitást az IoT hub-ban.

* Hozzon létre egy szimulált eszköz alkalmazást, amely egy közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy olyan Python-konzol alkalmazást, amely a szimulált eszközön a IoT hub használatával meghívja a közvetlen újraindítási módszert.

Az oktatóanyag végén két Python-konzolos alkalmazás található:

* a korábban létrehozott **dmpatterns_getstarted_device.** , amely a IoT hubhoz csatlakozik, a rendszer újraindítási közvetlen módszert kap, szimulálja a fizikai újraindítást, és az utolsó újraindítás idejét jelenti.

* a **dmpatterns_getstarted_service.** a szimulált eszköz alkalmazásban közvetlen metódust hív meg, a válasz pedig megjeleníti a frissített jelentett tulajdonságokat.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Az előfeltételek a következő telepítési utasításokkal rendelkeznek.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban a következőket fogja megtekinteni:

* Hozzon létre egy olyan Python-konzol alkalmazást, amely válaszol a felhő által meghívott közvetlen metódusra

* Eszköz újraindításának szimulálása

* A jelentett tulajdonságok használatával engedélyezheti az eszköz kettős lekérdezéseit az eszközök azonosításához és a legutóbbi újraindításkor

1. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_device.** a fájlt.

2. Adja hozzá a `import` következő utasításokat az **dmpatterns_getstarted_device.** a fájl elejéhez.

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

3. Adjon hozzá változókat, beleértve a **CONNECTION_STRING** változót és az ügyfél inicializálását.  Cserélje le a kapcsolatok karakterláncát az eszköz-kapcsolatok karakterláncára.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

4. Adja hozzá a következő függvény visszahívásait a közvetlen metódus eszközön való megvalósításához.

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

5. Indítsa el a Direct metódus-figyelőt, és várjon.

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

6. Mentse és zárjuk be a **dmpatterns_getstarted_device.** a fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például egy exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás indítása az eszközön közvetlen módszer használatával

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely egy távoli újraindítást kezdeményez egy eszközön egy közvetlen metódus használatával. Az alkalmazás az eszköz kettős lekérdezéseit használva észleli az adott eszköz utolsó újraindításának idejét.

1. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_service.** a fájlt.

2. Adja hozzá a `import` következő utasításokat az **dmpatterns_getstarted_service.** a fájl elejéhez.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

3. Adja hozzá a következő változó deklarációkat. Csak a _IoTHubConnectionString_ és a _deviceId_helyőrző értékeit cserélje le.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

4. Adja hozzá a következő függvényt az eszköz metódusának meghívásához a céleszköz újraindításához, majd az eszközhöz tartozó ikrek lekérdezéséhez és az utolsó újraindítás időpontjának lekéréséhez.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

5. Mentse és zárjuk be a **dmpatterns_getstarted_service.** a fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A parancssorban futtassa a következő parancsot a közvetlen újraindítási módszer figyelésének megkezdéséhez.

    ```
    python dmpatterns_getstarted_device.py
    ```

2. Egy másik parancssorban futtassa a következő parancsot a Távoli újraindítás és a lekérdezés elindításához a Twin eszközre, hogy megkeresse az utolsó újraindítási időt.

    ```
    python dmpatterns_getstarted_service.py
    ```

3. Megjelenik az eszköz válasza a Direct metódusra a konzolon.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]