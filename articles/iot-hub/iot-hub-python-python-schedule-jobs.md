---
title: Feladatok ütemezhetnek az Azure IoT Hub (Python) segítségével | Microsoft Docs
description: Azure IoT Hub-feladatok ütemezésének beütemezés közvetlen metódus több eszközön való meghívásához. A Pythonhoz készült Azure IoT SDK-k segítségével implementálhatja a szimulált eszköz alkalmazásait és egy szolgáltatási alkalmazást a feladatok futtatásához.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: bc93dcea74f0660a4f56aec237eaae5a4e3eeb9a
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904525"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Feladatok ütemezett és szórása (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a háttérbeli alkalmazások számára, hogy több millió eszközt ütemezzen és frissítsen feladatok létrehozásához és nyomon követéséhez.  A feladatokat a következő műveletekhez használhatja:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

Elméletileg a feladatok az alábbi műveletek egyikét betakarják, és nyomon követik a végrehajtás előrehaladását egy adott eszközön, amelyet az eszközök kettős lekérdezése határoz meg.  Egy háttérbeli alkalmazás például felhasználhat egy feladatot egy újraindítási módszer meghívására 10 000-eszközökön, egy eszköz kettős lekérdezésével és egy későbbi időpontban ütemezve.  Az alkalmazás ezután nyomon követheti az előrehaladást, mivel az egyes eszközök megkapják és végrehajtják az újraindítási módszert.

További információk a következő cikkekben felsorolt lehetőségekről:

* Eszköz Twin és tulajdonságai: [Ismerkedés az eszközök ikrekkel](iot-hub-python-twin-getstarted.md) és [oktatóanyaggal: Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)

* Közvetlen metódusok: [IoT hub fejlesztői útmutató – közvetlen](iot-hub-devguide-direct-methods.md) metódusok és [oktatóanyag: közvetlen metódusok](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy olyan Python által szimulált eszköz alkalmazást, amely közvetlen metódussal rendelkezik, amely lehetővé teszi a **lockDoor**, amelyet a megoldás hátterében hívhat meg.

* Hozzon létre egy olyan Python-konzol alkalmazást, amely feladatokkal hívja meg a **lockDoor** Direct metódust a szimulált eszköz alkalmazásban, és az eszköz feladataival frissíti a kívánt tulajdonságokat.

Az oktatóanyag végén két Python-alkalmazás áll rendelkezésére:

**simDevice.py**, amely az eszköz identitásával csatlakozik az IoT hubhoz, és egy **lockDoor** Direct metódust kap.

a **scheduleJobService.py**, amely közvetlen metódust hív meg a szimulált eszköz alkalmazásban, és feladatokkal frissíti az eszközhöz tartozó dupla kívánt tulajdonságokat.

> [!NOTE]
> A **Pythonhoz készült Azure IOT SDK** nem támogatja közvetlenül a **feladatok** funkcióit. Ehelyett ez az oktatóanyag egy alternatív megoldást kínál, amely aszinkron szálakat és időzítőket használ. További frissítésekért tekintse meg az [Azure IOT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) -oldal **szolgáltatás ügyféloldali SDK** -szolgáltatásainak listáját.
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Az előfeltételek a következő telepítési utasításokkal rendelkeznek.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely a felhő által meghívott közvetlen metódusra válaszol, amely egy szimulált **lockDoor** metódust indít el.

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-Device-Client** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > Az Azure-iothub-Service-Client és az Azure-iothub-Device-Client pip-csomagjai jelenleg csak Windows operációs rendszer esetén érhetők el. Linux/Mac OS esetén tekintse meg a Linux-és Mac OS-specifikus szakaszt a [fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) a Pythonhoz című témakörben.
   >

2. Egy szövegszerkesztővel hozzon létre egy új **simDevice.py** -fájlt a munkakönyvtárában.

3. Adja hozzá a `import` következő utasításokat és változókat a **simDevice.py** fájl elejéhez. Cserélje `deviceConnectionString` le a szöveget a fent létrehozott eszközhöz tartozó kapcsolatok sztringre:

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

4. Adja hozzá a következő függvény visszahívást a **lockDoor** metódus kezeléséhez:

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Új függvény visszahívás hozzáadása az eszközök ikrek frissítéseinek kezeléséhez:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Adja hozzá a következő kódot a kezelő **lockDoor** metódushoz való regisztrálásához. Adja meg a `main` rutint is:

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

7. Mentse és zárjuk be a **simDevice.py** fájlt.

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például egy exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

Ebben a cikkben egy háttér-szolgáltatást hoz létre, amely egy közvetlen metódust hív meg egy eszközön, és frissíti az eszközt. A szolgáltatásnak szüksége van a **szolgáltatás csatlakozási** engedélyére, hogy közvetlen metódust hívjon az eszközön. A szolgáltatásnak szüksége van a **beállításjegyzék olvasási** és **beállításjegyzék-írási** engedélyeire is az azonosító beállításjegyzékének olvasásához és írásához. Nincs olyan alapértelmezett megosztott hozzáférési szabályzat, amely csak ezeket az engedélyeket tartalmazza, ezért létre kell hoznia egyet.

Ha olyan megosztott hozzáférési szabályzatot szeretne létrehozni, amely a **szolgáltatás kapcsolódását**, a **beállításjegyzék olvasási**és **beállításjegyzék-írási** engedélyeit, valamint a szabályzat kapcsolati karakterláncának lekérését kéri, kövesse az alábbi lépéseket:

1. Nyissa meg az IoT hubot a [Azure Portal](https://portal.azure.com). Az IoT hub kiválasztásának legegyszerűbb módja az **erőforráscsoportok**kiválasztása. Válassza ki azt az erőforráscsoportot, ahol az IoT hub található, majd válassza ki az IoT hubot az erőforrások listájából.

2. Az IoT hub bal oldali ablaktábláján válassza a **megosztott hozzáférési házirendek**elemet.

3. A szabályzatok listájának felső menüjében válassza a **Hozzáadás**lehetőséget.

4. A **megosztott hozzáférési házirend hozzáadása** panelen adjon meg egy leíró nevet a szabályzatnak; például: *serviceAndRegistryReadWrite*. Az **engedélyek**területen válassza a **szolgáltatás kapcsolódása** és a **beállításjegyzék írása** lehetőséget (a**beállításjegyzék olvasása** beállítás automatikusan kiválasztásra kerül a beállításjegyzék **írásakor**). Ezután kattintson a **Létrehozás** elemre.

    ![Új megosztott elérési házirend hozzáadásának megjelenítése](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. A **megosztott hozzáférési szabályzatok** panelen válassza ki az új házirendet a házirendek listájából.

6. A **megosztott elérési kulcsok**területen válassza a **kapcsolati sztring – elsődleges kulcs** másolási ikonját, és mentse az értéket.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

További információ a IoT Hub megosztott hozzáférési szabályzatokról és engedélyekről: [hozzáférés-vezérlés és engedélyek](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Feladatok ütemezhetnek közvetlen metódus hívásához és az eszközök Twin tulajdonságainak frissítéséhez

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely egy közvetlen metódus használatával kezdeményez egy távoli **lockDoor** az eszközön, és frissíti az eszköz Twin tulajdonságait.

1. A parancssorban futtassa a következő parancsot az **Azure-IOT-Service-Client** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Az Azure-iothub-Service-Client és az Azure-iothub-Device-Client pip-csomagjai jelenleg csak Windows operációs rendszer esetén érhetők el. Linux/Mac OS esetén tekintse meg a Linux-és Mac OS-specifikus szakaszt a [fejlesztői környezet előkészítése](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) a Pythonhoz című témakörben.
   >

2. Egy szövegszerkesztővel hozzon létre egy új **scheduleJobService.py** -fájlt a munkakönyvtárában.

3. Adja hozzá a `import` következő utasításokat és változókat a **scheduleJobService.py** fájl elejéhez. Cserélje le `{IoTHubConnectionString}` a helyőrzőt arra a IoT hub-IoT, amelyet korábban átmásolt a beolvasás [az hub-kapcsolatok karakterláncában](#get-the-iot-hub-connection-string). Cserélje le `{deviceId}` a helyőrzőt az [új eszköz regisztrálása az IoT hub](#register-a-new-device-in-the-iot-hub)-ban regisztrált eszköz azonosítójával:

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

4. Adja hozzá a következő függvényt, amely az eszközök lekérdezésére szolgál:

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

5. Adja hozzá a következő metódusokat a közvetlen metódust és az eszköz ikerét meghívó feladatok futtatásához:

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

6. Adja hozzá a következő kódot a feladatok ütemezett és frissítési feladat állapotának megadásához. Adja meg a `main` rutint is:

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

7. Mentse és zárjuk be a **scheduleJobService.py** fájlt.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A munkakönyvtárban a parancssorban futtassa a következő parancsot a közvetlen újraindítási módszer figyelésének megkezdéséhez:

    ```cmd/sh
    python simDevice.py
    ```

2. A munkakönyvtárban egy másik parancssorban futtassa a következő parancsot a feladatok elindításához az ajtó zárolásához és a Twin frissítéséhez:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Megjelenik az eszköz válaszai a Direct metódusra és az eszközök ikrek frissítésére a konzolon.

    ![IoT Hub Job minta 1 – eszköz kimenete](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub Job minta 2 – eszköz kimenete](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy olyan feladatot használt, amely egy közvetlen metódust ütemez egy eszközre, és az eszköz Twin tulajdonságainak frissítését.

Ha továbbra is szeretné megkezdeni a IoT Hub és az eszközkezelés mintáit, például a távoli belső vezérlőprogram frissítését, tekintse meg a [belső vezérlőprogram frissítését](tutorial-firmware-update.md)ismertető témakört.