---
title: Ismerkedés az Azure IoT Hub Device Twins (Python) szolgáltatással | Microsoft Docs
description: Az Azure IoT Hub-eszközök ikrek használata címkék hozzáadásához és IoT Hub-lekérdezés használatához. A Pythonhoz készült Azure IoT SDK-k segítségével megvalósíthatja a szimulált eszköz alkalmazást és egy olyan szolgáltatási alkalmazást, amely hozzáadja a címkéket, és futtatja a IoT Hub lekérdezést.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 729371ede8df3dc21f70bc5742de52f2be874507
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030002"
---
# <a name="get-started-with-device-twins-python"></a>Ismerkedés a Device Twins (Python) eszközzel

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Az oktatóanyag végén két Python-konzolos alkalmazás lesz:

* **AddTagsAndQuery.py**, a Python háttér-alkalmazás, amely címkéket és lekérdezéseket tesz az ikreknek.

* **ReportConnectivity.py**, egy Python-alkalmazás, amely egy olyan eszközt szimulál, amely az IoT hubhoz a korábban létrehozott eszköz identitásával csatlakozik, és a kapcsolati feltételét jelzi.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

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

1. Nyisson meg egy parancssort a munkakönyvtárában, és telepítse a **Pythonhoz készült Azure IoT hub Service SDK**-t.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > Az Azure-iothub-Service-Client pip-csomag jelenleg csak Windows operációs rendszerhez érhető el. Linux/Mac OS esetén tekintse meg a Linux-és Mac OS-specifikus szakaszt a [fejlesztői környezet előkészítése a Pythonhoz](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) című témakörben.
   >

2. Egy szövegszerkesztővel hozzon létre egy új **AddTagsAndQuery.py** -fájlt.

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához a szolgáltatás SDK-jából:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Adja hozzá a következő kódot. Cserélje le a `[IoTHub Connection String]` értéket a IoT hub-beli, az [IoT hub-kapcsolatok karakterláncának beolvasása](#get-the-iot-hub-connection-string)során másolt karakterláncra. Cserélje le a `[Device Id]` értéket az [új eszköz regisztrálása az IoT hub](#register-a-new-device-in-the-iot-hub)-ban regisztrált eszköz azonosítójával.
  
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

    ![első lekérdezés, amely a Redmondban lévő összes eszközt megjeleníti](./media/iot-hub-python-twin-getstarted/service-1.png)

A következő szakaszban létrehoz egy olyan eszközt, amely jelentést készít a kapcsolati adatokról, és megváltoztatja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszköz-alkalmazás létrehozása

Ebben a szakaszban egy olyan Python-konzol alkalmazást hoz létre, amely a következőhöz csatlakozik a központhoz: **{Device ID}** , majd frissíti az eszköz Twin által jelentett tulajdonságait, hogy tartalmazza a mobil hálózaton keresztül csatlakoztatott adatokat.

1. A munkakönyvtárban található parancssorból telepítse a **Pythonhoz készült Azure IoT hub Device SDK**-t:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Egy szövegszerkesztővel hozzon létre egy új **ReportConnectivity.py** -fájlt.

3. Adja hozzá a következő kódot a szükséges modulok importálásához az eszköz SDK-ból:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Adja hozzá a következő kódot. Cserélje le a `[IoTHub Device Connection String]` helyőrző értékét az [új eszköz regisztrálása az IoT hub](#register-a-new-device-in-the-iot-hub)-ban elemre másolt eszköz-összekapcsolási karakterlánccal.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Adja hozzá a következő kódot a **ReportConnectivity.py** fájlhoz az eszköz ikrek funkciójának megvalósításához:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    Az **ügyfél** -objektum minden olyan módszert feltesz, amely az eszközön található ikrekkel való interakcióhoz szükséges. Az előző kód azt követően, hogy inicializálta az **ügyféltanúsítványt** , lekéri az eszközhöz tartozó Twin eszközt, és frissíti a jelentett tulajdonságot a kapcsolati információkkal.

6. Adja hozzá a következő kódot a **ReportConnectivity.py** végén a **iothub_client_sample_run** függvény megvalósításához:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Futtassa az eszköz alkalmazást:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Ekkor meg kell jelennie az eszközhöz tartozó ikrek frissítésének.

    ![ikrek frissítése](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Most, hogy az eszköz bejelentette a kapcsolati adatait, mindkét lekérdezésben szerepelnie kell. Lépjen vissza, és futtassa újra a lekérdezéseket:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Ekkor a **{Device ID}** értéknek mindkét lekérdezési eredményben szerepelnie kell.

    ![második lekérdezés](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttérbeli alkalmazáshoz, és írt egy szimulált eszközt, amely az eszköz kapcsolati adatait jelenti a Twin-ben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az információkat a beállításjegyzék használatával.

Az alábbi források segítségével megismerheti a következőket:

* Telemetria küldése az eszközökről az első [lépések IoT hub](quickstart-send-telemetry-python.md) oktatóanyaggal.

* Konfigurálja az eszközöket a Device Twin által kívánt tulajdonságokkal, és [használja a kívánt tulajdonságokat az eszközök konfigurálása](tutorial-device-twins.md) oktatóanyaghoz.

* Vezérelheti az eszközöket interaktív módon (például egy felhasználó által vezérelt alkalmazásból való bekapcsolással), a [Direct Methods oktatóanyag használatával](quickstart-control-device-python.md) .
