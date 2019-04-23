---
title: Ismerkedés az Azure IoT Hub-Eszközfelügyelet (Python) |} A Microsoft Docs
description: Hogyan használhatja az IoT Hub-Eszközfelügyelet egy távoli eszköz-újraindítás kezdeményezése. A Pythonhoz készült Azure IoT SDK használatával egy szimulált eszközalkalmazás, amely tartalmazza a közvetlen metódus és egy service-alkalmazás, amely a közvetlen metódust hív meg.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 04fc1da04d9da715acfed8ca9d26e9c325afb403
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786784"
---
# <a name="get-started-with-device-management-python"></a>Ismerkedés az eszközfelügyelettel (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure portal használatával hozzon létre egy IoT hubot, és hozzon létre egy új eszközidentitást az IoT hubban.

* Egy szimulált eszközalkalmazás létrehozása, amely közvetlen metódus, amely az eszköz újraindul. Közvetlen metódusok a felhő kerül meghívásra.

* Hozzon létre egy Python-Konzolalkalmazás, amely meghívja ezt az újraindítást közvetlen metódus a szimulált eszközalkalmazásnak, az IoT hub segítségével a.

Ez az oktatóanyag végén két Python-konzolalkalmazással fog rendelkezni:

* **dmpatterns_getstarted_device.PY**, csatlakozik az IoT hubhoz a korábban létrehozott eszközidentitással újraindítás közvetlen metódus kap, szimulálja a fizikai számítógép újraindítása és az utolsó újraindítás időpontja jelenti.

* **dmpatterns_getstarted_service.PY**, közvetlen metódus a szimulált eszközalkalmazásnak, amely hívások jeleníti meg a választ, és megjeleníti a frissített jelentett tulajdonságokként.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét](https://pip.pypa.io/en/stable/installing/).

* Telepítse a [azure-iothub-device-client](https://pypi.org/project/azure-iothub-device-client/) csomag, a parancs használatával       `pip install azure-iothub-device-client`

* Telepítse a [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) csomag, a parancs használatával       `pip install azure-iothub-service-client`

* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra](https://www.microsoft.com/download/confirmation.aspx?id=48145) van szükség a Python natív DLL-jei használatához.

* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ez a szakasz tartalma:

* Hozzon létre egy Python-Konzolalkalmazás, amely a felhő által meghívott közvetlen metódusra válaszol

* Egy eszköz-újraindítás szimulálásához.

* A jelentett tulajdonságok használatával ikereszköz-lekérdezéseket engedélyez az eszközök azonosítására és ha azok utolsó újraindítása

1. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_device.py** fájlt.

2. Adja hozzá a következő `import` elején található utasításokat a **dmpatterns_getstarted_device.py** fájlt.

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

3. Adja hozzá változókat, például egy **connections_string** változót, és az ügyfél inicializálása.  Cserélje le a kapcsolati karakterláncot az eszköz kapcsolati karakterláncát.  

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

4. Adja hozzá a következő függvény visszahívásait, hogy a közvetlen metódus megvalósításához az eszközön.

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

5. Indítsa el a közvetlen metódus figyelőt, és várjon.

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

6. Mentse és zárja be a **dmpatterns_getstarted_device.py** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), a cikkben leírtak implementálandó [átmeneti hibák kezelésével](/azure/architecture/best-practices/transient-faults).


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>A távoli közvetlen metódus az eszközön újra kell indítani az eseményindító

Ebben a szakaszban hozzon létre egy Python-Konzolalkalmazás, amely közvetlen metódus használó eszközök távoli újraindítást kezdeményez. Az alkalmazás számára az eszköz legutóbbi újraindítás ikereszköz-lekérdezések használja.

1. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_getstarted_service.py** fájlt.

2. Adja hozzá a következő `import` elején található utasításokat a **dmpatterns_getstarted_service.py** fájlt.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

3. Adja hozzá a következő változódeklarációkat. Csak cserélje le a helyőrző értékeket _IoTHubConnectionString_ és _deviceId_.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

4. Adja hozzá a következő függvényt indítsa újra a céleszközt, majd az ikereszközök lekérdezése az eszközmetódus meghívása és az utolsó újraindítás időpontja beolvasása.

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

5. Mentse és zárja be a **dmpatterns_getstarted_service.py** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. Parancsot a parancssorba futtassa a következő parancsot, amellyel megkezdheti a újraindítás közvetlen metódus figyel.

    ```
    python dmpatterns_getstarted_device.py
    ```

2. Egy másik parancssorban futtassa a következő parancsot a távoli újraindítás és a lekérdezés az ikereszköz található az utolsó újraindítás időpontja eseményindítóra.

    ```
    python dmpatterns_getstarted_service.py
    ```

3. Láthatja, hogy az eszköz válasza a közvetlen metódus a konzolon.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]