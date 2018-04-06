---
title: Az Azure IoT Hub (Python) feladatok ütemezése |} Microsoft Docs
description: How to Schedule a több eszközre közvetlen metódus egy Azure IoT Hub-feladat ütemezése. Az Azure IoT Python SDK segítségével valósítja meg a szimulált eszköz alkalmazások és a service-alkalmazást, a feladat futtatásához.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: bb087d3fce8d663995a3878951477df8343dca00
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="schedule-and-broadcast-jobs-python"></a>Ütemezés és a feladatok (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi egy háttér-alkalmazás létrehozása és nyomon követheti a feladatok ütemezése és eszközök millióira frissítése.  Feladatok is használható a következő műveleteket:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok

Egy feladat fogalmilag, becsomagolja az alábbi műveletek egyikét, és nyomon követi a folyamatot való összevetéssel az eszközök, eszköz két lekérdezést által definiált végrehajtás.  Például egy háttér-alkalmazást, egy feladat 10 000 eszközök, eszköz iker lekérdezés által megadott, és egy későbbi időpontban ütemezett újraindítás metódus használható.  Az alkalmazás egyes eszközök kap, és az újraindítás metódus végrehajtása majd előrehaladásának.

További információ az egyes képességek a cikkeiben:

* A két eszköz és a tulajdonságok: [Ismerkedés az eszköz twins] [ lnk-get-started-twin] és [oktatóanyag: kettős eszköztulajdonságok használata][lnk-twin-props]
* Közvetlen módszerek: [IoT Hub fejlesztői útmutató - közvetlen módszerek] [ lnk-dev-methods] és [oktatóanyag: közvetlen módszer][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Python szimulált eszköz alkalmazást, amely lehetővé teszi, hogy közvetlen metódus **lockDoor**, amely a megoldás háttérrendszeréhez hívható.
* Hozzon létre egy Python-konzolalkalmazást, amely behívja a **lockDoor** közvetlen módszer a alkalmazásban szimulált eszköz egy feladat és a frissítések a kívánt eszköz feladat használatával tulajdonságokkal.

Ez az oktatóanyag végén két Python-alkalmazások közül választhat:

**simDevice.py**, amely az IoT hub eszköz identitással csatlakozik, és megkapja a **lockDoor** közvetlen módszer.

**scheduleJobService.py**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazásban, és frissíti az eszköz iker szükséges tulajdonságok feladat használatával.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

> [!NOTE]
> A **Azure IoT SDK for Python** nem támogatja közvetlenül **feladatok** funkciót. Ez az oktatóanyag helyette aszinkron a szálak és időzítőket okhoz alternatív megoldást kínál. További frissítések, tekintse meg a **szolgáltatás ügyfél SDK** a szolgáltatások listája a [Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) lap. 
> 
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy Python-Konzolalkalmazás, amely válaszol a felhőbe, amely elindítja a szimulált által meghívott közvetlen metódus létrehozása **lockDoor** metódust.

1. A parancssorba a következő parancsot a telepítendő a **azure-iot-eszközügyfél** csomag:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Egy szövegszerkesztő használatával hozzon létre egy új **simDevice.py** fájlt a munkakönyvtárat.

1. Adja hozzá a következő `import` utasítások és változók elején a **simDevice.py** fájlt. Cserélje le `deviceConnectionString` az előbb létrehozott eszköz kapcsolati karakterlánccal:
   
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

1. Adja hozzá a következő függvény visszahívás kezeléséhez a **lockDoor** módszert:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Adja hozzá egy másik függvény visszahívás kezeléséhez twins eszközfrissítésekhez:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Az alábbi kódot a kezelőt regisztrálni a **lockDoor** metódust. Bevonhatja a `main` rutin:
   
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
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Ütemezett feladatok közvetlen metódus hívása, és egy eszköz iker tulajdonságainak frissítése
Ebben a szakaszban egy távoli kezdeményező Python-Konzolalkalmazás létrehozása **lockDoor** közvetlen módszert használ az eszközön, és az eszköz iker tulajdonságainak frissítéséhez.

1. A parancssorba a következő parancsot a telepítendő a **azure-iot-szolgáltatásügyfél** csomag:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Egy szövegszerkesztő használatával hozzon létre egy új **scheduleJobService.py** fájlt a munkakönyvtárat.

1. Adja hozzá a következő `import` utasítások és változók elején a **scheduleJobService.py** fájlt:
   
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

1. Adja hozzá a következő függvény használt eszközök lekérdezéséhez:
   
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

1. A hívó a közvetlen módszer és eszköz iker feladatok futtatása az alábbi módszerek hozzáadása:
   
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

1. Adja hozzá a következő kódot a feladatok ütemezése és a feladat állapotának frissítése. Bevonhatja a `main` rutin:
   
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

1. A parancssorban a munkakönyvtárba, figyeli a rendszer újraindítása közvetlen módszer a kezdéshez a következő parancsot:
   
    ```cmd/sh
    python simDevice.py
    ```

1. Egy másik-parancssorában történő a munkakönyvtárba, zárolhatja az ajtó, és frissíti a kettős feladatok elindítása a következő parancsot:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. A közvetlen módszer az eszköz válaszokat látja, és eszköz twins frissíteni fogja a konzolt.

    ![eszköz kimeneti][1]

    ![szolgáltatás kimeneti][2]


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy feladat ütemezése a közvetlen módszer egy eszköz és a két eszköz tulajdonságok frissítése használt.

A folytatáshoz, a légkondicionáló frissítést keresztül Ismerkedés az IoT-központ és az eszköz felügyeleti minták például távolról, lásd:

[Oktatóanyag: Módjáról a belső vezérlőprogram frissítése][lnk-fwupdate]

Ismerkedés az IoT-központ a folytatáshoz tekintse meg a [Ismerkedés az Azure IoT peremhálózati][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-python-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-python-python-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults

[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
