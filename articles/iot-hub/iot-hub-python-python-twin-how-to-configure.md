---
title: Azure IoT Hub eszköz iker tulajdonságait (Python) |} Microsoft Docs
description: Hogyan használható az Azure IoT Hub eszköz twins eszközök konfigurálásához. Az Azure IoT SDK-k a Python egy szimulált eszköz alkalmazást és egy szolgáltatás-alkalmazást, amely módosítja a használatával egy eszközt a két eszköz konfigurációs végrehajtásához használhatja.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: kgremban
ms.openlocfilehash: 0ba614ab26caad31a045849d85a1abcbb5a9f818
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>Használja a kívánt tulajdonságait (Python) eszközök konfigurálása
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Ez az oktatóanyag végén meg kell két Python konzol alkalmazások:

* **SimulateDeviceConfiguration.py**, a szimulált eszköz alkalmazás, amely megvárja-e a szükséges konfiguráció frissítése a jelentést készít egy szimulált konfigurációs frissítési folyamat állapotát.
* **SetDesiredConfigurationAndQuery.py**, egy Python háttér-alkalmazást, amely beállítja a kívánt konfiguráció egy eszközön, és lekérdezi a konfigurációs frissítési folyamat.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Ha követte a [Ismerkedés az eszköz twins] [ lnk-twin-tutorial] oktatóanyagban már rendelkezik egy IoT-központot, és egy eszközidentitás nevű **myDeviceId**; és a ugorjon[a szimulált eszköz-alkalmazás létrehozása] [ lnk-how-to-configure-createapp] szakasz.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>A szimulált eszköz-alkalmazás létrehozása
Ebben a szakaszban egy Python-Konzolalkalmazás, amely kapcsolódik a hub, létrehozhat **myDeviceId**megvárja-e a szükséges konfiguráció frissítése a, majd jelentést készít a frissítések szimulált konfigurációs frissítési folyamat.

1. Telepítse a **Azure IoT Python eszközt SDK** parancsot a parancssorba az alábbi paranccsal:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Egy szövegszerkesztő használatával hozzon létre egy új **SimulateDeviceConfiguration.py** fájlt.

1. Adja hozzá a következő kódot a **SimulateDeviceConfiguration.py** fájlt, és lecserélni a **{eszköz kapcsolati karakterlánc}** helyőrző a létrehozásautánmásoljaeszközkapcsolatikarakterlánccal**myDeviceId** eszközidentitás:

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    A **ügyfél** vezérlőnek az eszközről eszköz twins együttműködhet szükséges összes módszert. További kódban majd a frissítés kívánt tulajdonságainál, a kezelő kapcsolódni fog hozzáadása egy további kezelő annak ellenőrzéséhez, hogy létezik egy tényleges Helykonfiguráció-változtatási kérelem összehasonlítva a configIds, amely majd meghívja a módszere, amely elindítja a konfiguráció módosítása . Az egyszerűség kedvéért, az előző kód egy kódolt alapértelmezett értéket használja, a kezdeti konfiguráció. Egy valós alkalmazás valószínűleg szeretné, hogy a konfigurálás betöltése a helyi tárterület.
   
   > [!IMPORTANT]
   > Kívánt tulajdonság állapotváltozási események mindig kibocsátott egyszer eszköz csatlakozáskor, ügyeljen arra, hogy ellenőrizze, hogy van-e egy tényleges módosítása a kívánt tulajdonságaiban bármilyen művelet végrehajtása előtt.
   > 

1. Adja hozzá a következő kódot végén a **SimulateDeviceConfiguration.py** fájlt:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    A **device_twin_callback** metódus frissítések jelentett, a konfiguráció a helyi eszközön a két objektum tulajdonságainak frissítése a kérelmet, és beállítja a _configId_. Miután sikeresen frissített a eszköz iker, hogy kiírja a frissítési üzenet. Sávszélesség mentéséhez jelentett tulajdonságok frissítése csak a módosítani kívánt tulajdonságok megadásával (nevű **TWIN_PAYLOAD** a fenti kódban), a teljes dokumentum felülírása helyett.
   
   > [!NOTE]
   > Ez az oktatóanyag nem szimulálása egyidejű keresni minden olyan esetben. Néhány konfigurációs frissítési folyamat közben fut-e a frissítés, mások lehet a várólistába helyezni őket, és mások sikerült hibát elutasítása a célként megadott konfigurációs módosítások befogadásához lehet. Ügyeljen arra, hogy fontolja meg a kívánt viselkedés, a konfigurációs folyamat, és adja hozzá a megfelelő logikai kezdeményezése a konfiguráció módosítása előtt.
   > 

1. Adja hozzá a következő kódot végén a **SimulateDeviceConfiguration.py** fájlt: 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

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

1. Az eszköz alkalmazás futtatása:
   
    ```cmd/sh
    node SimulateDeviceConfiguration.py
    ```
   
    Az üzenet `Device twins updated.`. Tartsa meg az alkalmazás futását.


## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása
Ebben a szakaszban egy Python-Konzolalkalmazás, amely frissíti hoz létre a *szükséges tulajdonságok* meg az eszköz a két társított **myDeviceId** új telemetriai configuration objektummal. Ezután lekérdezi az eszköz twins az IoT hub tárolja, és a kívánt és jelentett konfigurációkat, az eszköz közötti különbséget szemlélteti.

1. Telepítse a **Azure IoT Python Service SDK** parancsot a parancssorba az alábbi paranccsal:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Egy szövegszerkesztő használatával hozzon létre egy új **SetDesiredAndQuery.py** fájlt.

1. Adja hozzá a következő kódot a **SetDesiredAndQuery.py** fájlt, és lecserélni az **{IoTHubConnectionString}** helyőrző a hub létrehozása után másolja az IoT-központ kapcsolati karakterlánccal és a **{deviceId}** helyőrző az eszköz nevét:

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Adja hozzá a következő kódot végén a **SetDesiredAndQuery.py** fájlt:

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. A **SimulateDeviceConfiguration.py** fut, futtassa az alkalmazást új parancssort:

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    A jelentésben szereplő konfigurációs Azonosítót, nem kell megjelennie **1** való **2** az új aktív a Küldés 24 óra helyett öt perces gyakoriságot.


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy szabványoskonfiguráció mint beállítása *szükséges tulajdonságok* egy háttér-alkalmazásból, és a szimulált eszköz alkalmazások észleli a változást, és egy frissítési folyamat tag állapotaként reporting szimulálása megírt *jelentett Tulajdonságok* számára az eszköz iker.

A következő források segítségével megtudhatja, hogyan:

* telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ] [ lnk-iothub-getstarted] oktatóanyagban
* ütemezett vagy műveleteket hajtson végre a nagy mennyiségű eszközök lásd: a [ütemezés és a szórásos feladatok] [ lnk-schedule-jobs] oktatóanyag.
* az interaktív (például bekapcsolásával a felhasználó által felügyelt alkalmazásból ventilátor), eszközök szabályozásának a [közvetlen módszerekkel] [ lnk-methods-tutorial] oktatóanyag.

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
