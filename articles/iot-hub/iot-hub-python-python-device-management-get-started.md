---
title: Ismerkedés az Azure IoT Hub kezelés (Python) |} Microsoft Docs
description: Hogyan használható az IoT-központ kezelés távoli eszköz újraindítás kezdeményezése. Az Azure IoT Python SDK használatával a szimulált eszköz alkalmazást, amely magában foglalja a közvetlen módszer és a service-alkalmazást, amely hívja meg a közvetlen módszer megvalósításához.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 001a5d180e2b97894174ba7c63ec3947c9968364
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-device-management-python"></a>Eszközkezelés (Python) az első lépései

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure-portál használatával létrehoz egy IoT-központot, és az IoT hub hozzon létre egy eszközidentitás.
* Létrehoz egy szimulált eszköz alkalmazást, amely tartalmazza a közvetlen módszer, amely az eszköz újraindul. Közvetlen módszerek a felhőből hívják.
* Hozzon létre egy Python-Konzolalkalmazás, amely behívja az újraindítás közvetlen módszer a szimulált eszköz alkalmazásban az IoT hub keresztül.

Ez az oktatóanyag végén két Python konzol alkalmazások közül választhat:

**dmpatterns_getstarted_device.PY**, amely kapcsolódik az IoT hub korábban létrehozott eszköz identitású kap egy újraindítás közvetlen módszer, szimulálja a fizikai számítógép újraindítása, és jelentést az idő az utolsó újraindítás.

**dmpatterns_getstarted_service.PY**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazás jeleníti meg a választ, és jeleníti meg a frissített jelentett tulajdonságok.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
    * Telepítse a [azure-IOT hubbal-eszközügyfél](https://pypi.org/project/azure-iothub-device-client/) csomag, a parancs használatával   `pip install azure-iothub-device-client`
    * Telepítse a [azure-IOT hubbal-szolgáltatásügyfél](https://pypi.org/project/azure-iothub-service-client/) csomag, a parancs használatával   `pip install azure-iothub-service-client`
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ez a szakasz tartalma:

* Hozzon létre egy Python-konzolalkalmazást, amely válaszol a felhő által meghívott egy közvetlen módszer
* Eszköz újraindítás szimulálásához.
* A jelentésben szereplő tulajdonságok használatával engedélyezhető az eszköz iker lekérdezések azonosíthatja azokat az eszközöket, és ha tartanak újraindítása után

1. Egy szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_device.py** fájlt.

1. Adja hozzá a következő `import` elején utasítások a **dmpatterns_getstarted_device.py** fájlt.
   
    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

1. Adja hozzá a változókat, beleértve a **CONNECTION_STRING** változót és az ügyfél memóriahiba lépett fel.  Cserélje le a kapcsolati karakterlánc az eszköz kapcsolati karakterláncot.  
   
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

1. Adja hozzá a következő függvény visszahívások megvalósításához a közvetlen módszer az eszközön.
   
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

1. Indítsa el a közvetlen módszer figyelő, és várjon, amíg.
   
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

1. Mentse és zárja be a **dmpatterns_getstarted_device.py** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Az eszközön, a közvetlen módszer használatával távoli újraindítás eseményindító
Ebben a szakaszban egy Python-Konzolalkalmazás közvetlen metódussal eszköz távoli újraindítást kezdeményezett hoz létre. Az alkalmazás számára az eszköz legutóbbi újraindítás eszköz iker lekérdezések használ.

1. Egy szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_service.py** fájlt.

1. Adja hozzá a következő `import` elején utasítások a **dmpatterns_getstarted_service.py** fájlt.
   
    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

1. Adja hozzá a következő tartományváltozók deklarációjában. Csak cserélje le a helyőrző értékeket _IoTHubConnectionString_ és _deviceId_.
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Adja hozzá a következő függvény meghívásához indítsa újra a céleszközt, majd az eszköz twins lekérdezése az eszköz metódust, és a legutóbbi újraindítás beolvasása.
   
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

1. Mentse és zárja be a **dmpatterns_getstarted_service.py** fájlt.


## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorba a következő parancsot a rendszer újraindítása közvetlen módszer figyelését.
   
    ```
    python dmpatterns_getstarted_device.py
    ```

1. Egy másik parancssorban futtassa a következő parancsot a távoli újraindítás és a lekérdezés megadása a eszköz iker található az utolsó újraindítás idő elindítani.
   
    ```
    python dmpatterns_getstarted_service.py
    ```

1. A közvetlen módszer a konzolon eszköz válasz megjelenik.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
