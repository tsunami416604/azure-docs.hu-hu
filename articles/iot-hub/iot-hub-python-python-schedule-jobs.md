---
title: Feladatok ütemezése az Azure IoT Hub (Python) használatával | Microsoft dokumentumok
description: Egy Azure IoT Hub-feladat ütemezése egy közvetlen metódus meghívására több eszközön. Az Azure IoT SDK-k python-hoz a szimulált eszközalkalmazások és egy szolgáltatásalkalmazás a feladat futtatásához.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 1d721e89534c09a5572e5674796f28355f652165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527401"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Feladatok ütemezése és közvetítése (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi, hogy egy háttéralkalmazás olyan feladatokat hozzon létre és kövessen nyomon, amelyek több millió eszközt ütemeznek és frissítenek.  A feladatok a következő műveletekhez használhatók:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

Fogalmilag a feladat betakarja az egyik ilyen műveleteket, és nyomon követi a végrehajtás előrehaladását egy eszközkészlet, amely egy eszköz iker lekérdezés határozza meg.  Például egy háttéralkalmazás segítségével egy feladat meghívására újraindítási módszer 10 000 eszközön, egy eszköz iker lekérdezés által megadott és egy későbbi időpontban ütemezett.  Ez az alkalmazás ezután nyomon követheti a folyamatot, ahogy az egyes eszközök megkapják és végrehajtják az újraindítási módszert.

Az alábbi cikkekben további információ az egyes funkciókról:

* Ikereszköz és tulajdonságok: [Ismerkedés az ikereszközökkel](iot-hub-python-twin-getstarted.md) és [az oktatóanyaggal: Az ikereszköz tulajdonságainak használata](tutorial-device-twins.md)

* Közvetlen módszerek: [IoT Hub fejlesztői útmutató - közvetlen módszerek](iot-hub-devguide-direct-methods.md) és [oktatóanyag: közvetlen módszerek](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Python szimulált eszközalkalmazást, amely közvetlen metódussal rendelkezik, amely lehetővé teszi **a lockDoor**, amely et a megoldás háttér-kezelője meghívhat.

* Hozzon létre egy Python konzolalkalmazást, amely meghívja a **lockDoor** közvetlen metódust a szimulált eszközalkalmazásban egy feladat használatával, és frissíti a kívánt tulajdonságokat egy eszközfeladat használatával.

Az oktatóanyag végén két Python-alkalmazással rendelkezik:

**simDevice.py**, amely csatlakozik az IoT hub az eszköz identitását, és kap egy **lockDoor** közvetlen metódust.

**scheduleJobService.py**, amely meghívja a szimulált eszközalkalmazásban egy közvetlen metódust, és egy feladat használatával frissíti az ikereszköz kívánt tulajdonságait.

> [!NOTE]
> Az **Azure IoT SDK python-hoz** nem támogatja közvetlenül **a feladatok** funkciót. Ehelyett ez a bemutató kínál egy alternatív megoldás felhasználásával aszinkron szálak és időzítők. További frissítéseket tekintse meg a **Service Client SDK** szolgáltatáslista az [Azure IoT SDK python-hoz](https://github.com/Azure/azure-iot-sdk-python) oldalon.
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Python konzolalkalmazást, amely válaszol a felhő által megnevezett közvetlen metódusra, amely egy szimulált **lockDoor** metódust indít el.

1. A parancssorban futtassa a következő parancsot az **azure-iot-device** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Szövegszerkesztő használatával hozzon létre egy új **simDevice.py** fájlt a munkakönyvtárban.

3. Adja hozzá `import` a következő állításokat és változókat a **simDevice.py** fájl elején. Cserélje `deviceConnectionString` le a fent létrehozott eszköz kapcsolati karakterláncára:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. A **lockDoor** metódus kezeléséhez adja hozzá a következő függvényvisszahívást:

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Adjon hozzá egy másik függvényvisszahívást az eszköztwins-frissítések kezeléséhez:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Adja hozzá a következő kódot a **lockDoor** metódus kezelőjének regisztrálásához. Is tartalmazza `main` a rutin:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Mentse és zárja be a **simDevice.py** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például exponenciális visszamaradást) kell megvalósítania, ahogy azt a cikk, [átmeneti hibakezelés](/azure/architecture/best-practices/transient-faults)című cikk ben javasolt.
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben hozzon létre egy háttérszolgáltatás, amely meghívja a közvetlen metódust egy eszközön, és frissíti az ikereszköz. A szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére egy közvetlen metódus hívásához egy eszközön. A szolgáltatásnak szüksége van a **rendszerleíró adatbázis olvasási** és **beállításjegyzék-írási** engedélyére is az identitásbeállítás-beállításjegyzék olvasásához és írásához. Nincs olyan alapértelmezett megosztott hozzáférési házirend, amely csak ezeket az engedélyeket tartalmazza, ezért létre kell hoznia egyet.

Ha olyan megosztott hozzáférési házirendet szeretne létrehozni, amely **szolgáltatáscsatlakozást**, **rendszerleíró adatbázis olvasási**és **beállításjegyzék-írási** engedélyeket biztosít, valamint a házirendhez kapcsolódó kapcsolati karakterláncot szeretne beolvasni, kövesse az alábbi lépéseket:

1. Nyissa meg az IoT-központot az [Azure Portalon.](https://portal.azure.com) Az IoT hubhoz való leggyorsabb leválasztás: **erőforráscsoportok**kiválasztása, válassza ki azt az erőforráscsoportot, ahol az IoT hub található, majd válassza ki az IoT hubot az erőforrások listájából.

2. Az IoT hub bal oldali ablaktábláján válassza a **Megosztott hozzáférési szabályzatok**lehetőséget.

3. A házirendek listája feletti felső menüben válassza a **Hozzáadás**gombot.

4. A **Megosztott hozzáférésházirend hozzáadása** ablaktáblán adja meg a házirend leíró nevét; például: *serviceAndRegistryReadWrite*. Az **Engedélyek csoportban**válassza a **Szolgáltatáscsatlakozás** és **a Rendszerleíróadatbázis írása** lehetőséget ( A**rendszerleíró adatbázis automatikusan** bevan jelölve, ha a **Rendszerleíróadatbázis írása**lehetőséget választja ). Ezután válassza **a Létrehozás lehetőséget.**

    ![Új megosztott hozzáférési szabályzat hozzáadásának megjelenítése](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. A **Megosztott hozzáférési házirendek** ablaktáblán válassza ki az új szabályzatot a házirendek listájából.

6. A **Megosztott hozzáférési kulcsok csoportban**válassza ki a Kapcsolat karakterlánc másolatikonját - elsődleges **kulcs,** és mentse az értéket.

    ![A kapcsolati karakterlánc beolvasásának megjelenítése](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Az IoT Hub megosztott hozzáférési szabályzatairól és engedélyeiről a [Hozzáférés-vezérlés és -engedélyek című](./iot-hub-devguide-security.md#access-control-and-permissions)témakörben talál további információt.

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezése közvetlen metódus hívásához és az ikereszköz tulajdonságainak frissítéséhez

Ebben a szakaszban hozzon létre egy Python konzolalkalmazást, amely egy távoli **lockDoor** egy eszközön egy közvetlen módszerrel, és frissíti az eszköz iker kívánt tulajdonságait.

1. A parancssorban futtassa a következő parancsot az **azure-iot-hub** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Szövegszerkesztő használatával hozzon létre egy új **scheduleJobService.py** fájlt a munkakönyvtárban.

3. Adja hozzá `import` a következő állításokat és változókat a **scheduleJobService.py** fájl elején. Cserélje `{IoTHubConnectionString}` le a helyőrzőt az IoT hub kapcsolati karakterláncára, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben.](#get-the-iot-hub-connection-string) Cserélje `{deviceId}` le a helyőrzőt az [IoT hubban](#register-a-new-device-in-the-iot-hub)regisztrált eszközazonosítóra:

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Adja hozzá a következő függvényt, amely az eszközök lekérdezéséhez használatos:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Adja hozzá a következő módszereket a közvetlen metódust és az ikereszközt hívó feladatok futtatásához:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Adja hozzá a következő kódot a feladatok ütemezéséhez és a feladat állapotának frissítéséhez. Is tartalmazza `main` a rutin:

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

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
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

7. Mentse és zárja be a **scheduleJobService.py** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A munkakönyvtár parancssorában futtassa a következő parancsot az újraindítási közvetlen módszer figyeléséhez:

    ```cmd/sh
    python simDevice.py
    ```

2. A munkakönyvtár egy másik parancssorában futtassa a következő parancsot az ajtó zárolásához és az ikerhálózat frissítéséhez a feladatok elindításához:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. A konzolon láthatja a közvetlen metódusra adott válaszait és az ikerpár-frissítést.

    ![IoT Hub-feladat minta 1 -- eszköz kimenet](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub-feladat minta 2- eszköz kimenet](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladat segítségével ütemezte a közvetlen metódust egy eszközre, és az ikereszköz tulajdonságainak frissítését.

Az IoT Hub és az eszközfelügyeleti minták , például a távoli belső vezérlőprogram frissítésének megkezdéséhez olvassa el [a Belső vezérlőprogram frissítésének lépéseit.](tutorial-firmware-update.md)