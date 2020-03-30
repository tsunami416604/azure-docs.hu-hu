---
title: Ismerkedés az Azure IoT Hub eszközfelügyelettel (Python) | Microsoft dokumentumok
description: Az IoT Hub eszközfelügyeleti használata távoli eszköz újraindításának kezdeményezéséhez. Az Azure IoT SDK python-hoz egy szimulált eszközalkalmazást valósíthat meg, amely közvetlen metódust és egy közvetlen metódust meghívó szolgáltatásalkalmazást tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.openlocfilehash: 6d6a50db42924d868b57cacc415246ee6990859c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110470"
---
# <a name="get-started-with-device-management-python"></a>Az eszközkezelés első lépései (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure Portalhasználatával hozzon létre egy IoT Hubot, és hozzon létre egy eszközidentitást az IoT-központban.

* Hozzon létre egy szimulált eszközalkalmazást, amely közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy Python konzolalkalmazást, amely meghívja az újraindítás közvetlen metódusa a szimulált eszköz alkalmazás az IoT hubon keresztül.

Az oktatóanyag végén két Python konzolalkalmazás sal rendelkezik:

* **dmpatterns_getstarted_device.py**, amely csatlakozik az IoT hub az eszköz identitása korábban létrehozott, kap egy újraindítás közvetlen metódust, szimulálja a fizikai újraindítás, és jelenti az idő az utolsó újraindítás.

* **dmpatterns_getstarted_service.py**, amely meghívja a szimulált eszközalkalmazásban a közvetlen metódust, megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban:

* Python-konzolalkalmazás létrehozása, amely a felhő által megnevezett közvetlen metódusra reagál

* Eszköz újraindításának szimulálása

* A jelentett tulajdonságok használatával engedélyezheti az ikereszközök lekérdezéseit az eszközök azonosítására és az utolsó újraindításukkor

1. A parancssorban futtassa a következő parancsot az **azure-iot-device** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_device.py** nevű fájlt a munkakönyvtárban.

3. Adja hozzá `import` a következő állításokat a **dmpatterns_getstarted_device.py** fájl elején.

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Adja hozzá a **CONNECTION_STRING** változót. Cserélje `{deviceConnectionString}` le a helyőrző értéket az eszköz kapcsolati karakterláncára. Ezt a kapcsolati karakterláncot korábban másolta az [Új eszköz regisztrálása az IoT hubban](#register-a-new-device-in-the-iot-hub)című részben.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Adja hozzá a következő függvényvisszahívásokat a közvetlen metódus eszközén való megvalósításához.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Indítsa el a közvetlen metódusfigyelőt, és várjon.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Mentse és zárja be a **dmpatterns_getstarted_device.py** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például exponenciális visszamaradást) kell megvalósítania, ahogy azt a cikk, [átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)című cikk ben javasolt.

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás aktiválása az eszközön közvetlen módszerrel

Ebben a szakaszban hozzon létre egy Python konzolalkalmazást, amely elindítja a távoli újraindítás t egy eszközön egy közvetlen metódus használatával. Az alkalmazás az eszköz ikerlekérdezéseit használja az eszköz utolsó újraindítási idejének felderítéséhez.

1. A parancssorban futtassa a következő parancsot az **azure-iot-hub** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_service.py** nevű fájlt a munkakönyvtárban.

3. Adja hozzá `import` a következő állításokat a **dmpatterns_getstarted_service.py** fájl elején.

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Adja hozzá a következő változódeklarációkat. Cserélje `{IoTHubConnectionString}` le a helyőrző értéket az IoT hub kapcsolati karakterláncára, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben.](#get-the-iot-hub-connection-string) Cserélje `{deviceId}` le a helyőrző értéket az [IoT hubban](#register-a-new-device-in-the-iot-hub)regisztrált eszközazonosítóra.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Adja hozzá a következő függvényt az eszközmetódus meghívásához a céleszköz újraindításához, majd kérdezze meg az ikereszközt, és kapja meg az utolsó újraindítási időt.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
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

6. Mentse és zárja be a **dmpatterns_getstarted_service.py** fájlt.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A parancssorban futtassa a következő parancsot az újraindítási közvetlen metódus figyeléséhez.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Egy másik parancssorból futtassa a következő parancsot a távoli újraindítás elindításához, és az ikereszköz lekérdezésével keresse meg az utolsó újraindítási időt.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. A konzolon megjelenik a közvetlen metódusra adott válasz.

   A következőkben az eszköz válasza az újraindítás közvetlen metódusára:

   ![Szimulált eszközalkalmazás-kimenet](./media/iot-hub-python-python-device-management-get-started/device.png)

   A következő azt mutatja, hogy a szolgáltatás hívja az újraindítás közvetlen metódusés a lekérdezés az eszköz iker állapotát:

   ![Újraindítási szolgáltatás kimenetének aktiválása](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]