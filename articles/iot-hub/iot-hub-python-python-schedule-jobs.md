---
title: Az Azure IoT Hub (Python) feladatok ütemezéséhez |} A Microsoft Docs
description: Hogyan lehet a több eszközre közvetlen metódus meghívása egy Azure IoT Hub-feladat ütemezése. Az Azure IoT SDK Pythonhoz készült használatával valósítható meg a szimulált eszközalkalmazások és a egy service-alkalmazás a feladat futtatásához.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: fe7c44df57b54fe3a152f4d35a2144fed8413314
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540113"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Feladatok ütemezése és kiküldése (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljesen felügyelt szolgáltatás, amely lehetővé teszi egy háttér-alkalmazást hozhat létre, és nyomon követheti a feladatok ütemezése és több millió eszköz frissítéséhez.  Feladatok is használható a következő műveleteket:

* Eszköz kívánt tulajdonságainak frissítése
* A címkék frissítése
* Közvetlen metódusok meghívása

Elméleti szinten feladat burkolja az alábbi műveletek egyikét, és nyomon követi a folyamatot a végrehajtás egy eszköz ikereszköz-lekérdezés által definiált eszközök készlete alapján.  Például egy háttér-alkalmazás használatával egy feladat újraindítás metódus meghívása az 10 000 eszköz, egy eszköz ikereszköz-lekérdezés által megadott és a egy későbbi időpontra ütemezve.  Az alkalmazás nyomon követésével folyamat ezeknek az eszközöknek mindegyike kap, és hajtsa végre az újraindítási módszert.

További információ az egyes képességek a következő cikkeket:

* Ikereszköz és tulajdonságok: [Ikereszközök – első lépések](iot-hub-python-twin-getstarted.md) és [oktatóanyag: Eszköz-ikertulajdonságok használata](tutorial-device-twins.md)
* Közvetlen metódusok: [Az IoT Hub fejlesztői útmutató – közvetlen metódusok](iot-hub-devguide-direct-methods.md) és [oktatóanyag: közvetlen metódusok](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Python szimulált eszközalkalmazás létrehozása, amely rendelkezik, amely lehetővé teszi a közvetlen metódus **lockDoor**, amelyek meghívhatók a megoldás háttérrendszeréhez.
* Hozzon létre egy Python-Konzolalkalmazás, amely meghívja a **lockDoor** a közvetlen metódus a szimulált eszközalkalmazásnak, feladatok és a frissítések használata a kívánt tulajdonságokkal eszköz feladat használatával.

Ez az oktatóanyag végén kettő Python-alkalmazással rendelkezik:

**simDevice.py**, amely az IoT hub az eszközidentitással csatlakozik, és megkapja a **lockDoor** közvetlen metódust.

**scheduleJobService.py**, amely hívások közvetlen metódus a szimulált eszközalkalmazásnak, és frissíti az ikereszköz kívánt tulajdonságait egy feladat használatával.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x](https://www.python.org/downloads/). Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét](https://pip.pypa.io/en/stable/installing/).
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra](https://www.microsoft.com/download/confirmation.aspx?id=48145) van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

> [!NOTE]
> A **Pythonhoz készült Azure IoT SDK** nem támogatja közvetlenül **feladatok** funkciót. Ebben az oktatóanyagban inkább aszinkron hozzászólásláncok és időzítőket alternatív megoldást kínál. További frissítések, tekintse meg a **szolgáltatás ügyfél-SDK** a szolgáltatások listája a [Pythonhoz készült Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-python) lap. 
> 
> 

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban hoz létre egy Python-Konzolalkalmazás, amely a felhő, amely elindít egy szimulált által meghívott közvetlen metódusra válaszol **lockDoor** metódust.

1. A parancssorban futtassa a telepítéséhez a következő parancsot a **azure-iot-device-client** csomag:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Egy szövegszerkesztővel hozzon létre egy új **simDevice.py** fájlt a munkakönyvtárban.

1. Adja hozzá a következő `import` utasításokat és változókat elején a **simDevice.py** fájlt. Cserélje le `deviceConnectionString` a fent létrehozott eszköz a kapcsolati karakterláncra:
   
    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Adja hozzá a következő függvény visszahívás kezeléséhez a **lockDoor** módszer:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Adjon hozzá egy másik függvény visszahívási device twins frissítések kezelésére:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Adja a következő kódot a kezelő regisztrálni a **lockDoor** metódust. Emellett tartalmazza a `main` rutin:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

1. Mentse és zárja be a **simDevice.py** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), a cikkben leírtak implementálandó [átmeneti hibák kezelésével](/azure/architecture/best-practices/transient-faults).
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezése a közvetlen metódus meghívása és a egy ikereszköz tulajdonságainak frissítése
Ebben a szakaszban hoz létre egy Python-Konzolalkalmazás, amely kezdeményezi egy távoli **lockDoor** egy eszközön a közvetlen metódus használatával, és az ikereszköz tulajdonságainak frissítése.

1. A parancssorban futtassa a telepítéséhez a következő parancsot a **azure-iot-service-client** csomag:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Egy szövegszerkesztővel hozzon létre egy új **scheduleJobService.py** fájlt a munkakönyvtárban.

1. Adja hozzá a következő `import` utasításokat és változókat elején a **scheduleJobService.py** fájlt:
   
    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

1. Adja hozzá a következő függvényt használt eszközök lekérdezéséhez:
   
    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
    
        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)
    
        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

1. Adja hozzá a következő metódusokat, amelyek a közvetlen módszer és eszköz ikereszköz meg a feladatok futtatásához:
   
    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
    
            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
        
            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )
        
    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
    
            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
        
            print ( "" )
            print ( "Device twin updated." )
    ```

1. Adja hozzá a következő kódot a feladatok ütemezése és a feladat állapotának frissítése. Emellett tartalmazza a `main` rutin:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()
        
            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )
        
            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()
        
            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )
        
            while True:
                print ( "" )
            
                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )
            
                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )
                
                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

1. Mentse és zárja be a **scheduleJobService.py** fájlt.


## <a name="run-the-applications"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorban futtassa a következő parancsot, amellyel megkezdheti a újraindítás közvetlen metódus figyeli a munkakönyvtárban:
   
    ```cmd/sh
    python simDevice.py
    ```

1. Egy másik parancs parancssorba a következő parancsot a feladatok a ajtajának, és frissíti az ikereszköz aktiválásához a munkakönyvtárban:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. Az eszköz megjelenniük a közvetlen metódus, és ikereszközök frissítse a konzolon.

    ![az eszközkimenetre][1]

    ![szolgáltatás-kimenet][2]


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy feladat ütemezése és eszköz az ikereszköz tulajdonságok frissítése egy közvetlen metódus használt.

Ismerkedés az IoT Hub és az eszközfelügyeleti minták például távolról keresztül a vezeték nélküli belső vezérlőprogram frissítését a folytatáshoz tekintse meg a [hogyan belső vezérlőprogram frissítése](tutorial-firmware-update.md).

<!-- images -->
[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
