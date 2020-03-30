---
title: Ismerkedés az Azure IoT Hub-eszközök twins (Python) | Microsoft dokumentumok
description: Az Azure IoT Hub-eszközök twins használata címkék hozzáadásához, majd egy IoT Hub-lekérdezés használatához. Az Azure IoT SDK-k python-hoz a szimulált eszközalkalmazás és egy szolgáltatásalkalmazás, amely hozzáadja a címkéket, és futtatja az IoT Hub-lekérdezést.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.openlocfilehash: c1db7f1a891646ad29f6cae95ddb7e2cf3a42bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409722"
---
# <a name="get-started-with-device-twins-python"></a>Az ikereszközök használata (Python) első lépései

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Az oktatóanyag végén két Python konzolalkalmazás lesz:

* **AddTagsAndQuery.py**, egy Python háttéralkalmazás, amely címkéket és lekérdezéseket ad az eszközök twins.AddTagsAndQuery.py , a Python back-end app, amely hozzáadja a címkéket és lekérdezéseket eszköz twins.

* **ReportConnectivity.py**, egy Python-alkalmazás, amely szimulálja az eszközt, amely csatlakozik az IoT hub az eszköz identitása korábban létrehozott, és jelenti a kapcsolati állapotát.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban olyan Python konzolalkalmazást hoz létre, amely helymetaadatokat ad hozzá **a(z) {Device ID}** eszközhöz társított ikereszközhöz. Ezután lekérdezi az IT hubban tárolt ikereszközök a Redmondban található eszközök kiválasztásával, majd a mobilhálózati kapcsolatot jelző eszközök.

1. A munkakönyvtárban nyisson meg egy parancssort, és telepítse az **Azure IoT Hub Service SDK pythonhoz című témakört.**

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Szövegszerkesztő használatával hozzon létre egy új **AddTagsAndQuery.py** fájlt.

3. Adja hozzá az alábbi kódot a szükséges modulok importálásához a szolgáltatás SDK-jából:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Adja hozzá a következő kódot. Cserélje `[IoTHub Connection String]` le az IoT hub kapcsolati karakterlánc másolt [az IoT hub kapcsolati karakterlánc bekapása.](#get-the-iot-hub-connection-string) Cserélje `[Device Id]` le az [IOt hubban](#register-a-new-device-in-the-iot-hub)regisztrált eszközazonosítót.
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Adja hozzá a következő kódot a **AddTagsAndQuery.py** fájlhoz:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    Az **IoTHubRegistryManager** objektum minden olyan módszert elérhetővé tesz, amely a szolgáltatásból származó ikereszközök kelemi-kapcsolatának kezeléséhez szükséges. A kód először inicializálja az **IoTHubRegistryManager** objektumot, majd frissíti az ikereszközt **DEVICE_ID,** és végül két lekérdezést futtat. Az első csak a **Redmond43-üzemben** található eszközök ikereszközeit választja ki, a második pedig finomítja a lekérdezést, hogy csak azokat az eszközöket válassza ki, amelyek szintén mobilhálózaton keresztül csatlakoznak.

6. A **iothub_service_sample_run** függvény megvalósításához adja hozzá a következő kódot **AddTagsAndQuery.py** végén:

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Futtassa az alkalmazást a következővel:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Meg kell jelennie egy eszköz az eredmények a lekérdezés tanusítandó összes eszköz **redmond43** és nincs a lekérdezés, amely korlátozza az eredményeket a mobilhálózatot használó eszközök.

    ![első lekérdezés, amely az összes eszközt mutatja Redmondban](./media/iot-hub-python-twin-getstarted/service-1.png)

A következő szakaszban létrehoz egy eszközalkalmazást, amely jelenti a kapcsolódási adatokat, és módosítja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Python konzolalkalmazást, amely **{Device ID}** néven csatlakozik a hubhoz, majd frissíti az ikereszköz jelentett tulajdonságait, hogy tartalmazza a mobilhálózaton keresztül csatlakoztatott információkat.

1. A munkakönyvtár parancssorából telepítse az **Azure IoT Hub Device SDK for Python alkalmazást:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Szövegszerkesztő használatával hozzon létre egy új **ReportConnectivity.py** fájlt.

3. Adja hozzá a következő kódot a szükséges modulok importálásához az SDK eszközről:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Adja hozzá a következő kódot. Cserélje `[IoTHub Device Connection String]` le a helyőrző értéket a másolt eszközkapcsolati karakterláncra [az IoT hubúj eszköz regisztrálása című részben.](#register-a-new-device-in-the-iot-hub)

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Adja hozzá a következő kódot a **ReportConnectivity.py** fájlhoz az ikereszközök funkciómegvalósításához:

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
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    Az **IoTHubModuleClient** objektum minden olyan módszert elérhetővé tesz, amely az eszköztwins eszköztwins eszköztwins az eszközről. Az előző kód, miután inicializálja az **IoTHubModuleClient** objektumot, lekéri az eszköz ikereszközjét az eszközhöz, és frissíti a jelentett tulajdonságot a kapcsolódási adatokkal.

6. A **iothub_client_sample_run** függvény megvalósításához adja hozzá a következő kódot **ReportConnectivity.py** végén:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Az eszközalkalmazás futtatása:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Meg kell jelennie annak megerősítését, hogy az ikereszköz jelentett tulajdonságai frissültek.

    ![jelentett tulajdonságok frissítése az eszközalkalmazásból](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Most, hogy az eszköz jelentette a kapcsolódási adatait, mindkét lekérdezésben meg kell jelennie. Lépjen vissza, és futtassa újra a lekérdezéseket:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Ezúttal **a{Device ID}** mindkét lekérdezési eredményben meg kell jelennie.

    ![második lekérdezés a szolgáltatásalkalmazásban](./media/iot-hub-python-twin-getstarted/service-2.png)

    Az eszközalkalmazásban megerősítést kap arról, hogy a szolgáltatásalkalmazás által küldött kívánt tulajdonságok ikerjavítása megérkezett.

    ![a kívánt tulajdonságok fogadása az eszközalkalmazásban](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttéralkalmazásból, és írt egy szimulált eszközalkalmazást az eszközkapcsolati adatok jelentéséhez az ikereszközben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az információkat a beállításjegyzék használatával.

Az alábbi források segítségével megtudhatja, hogyan:

* Telemetriai adatok küldése az eszközökről az [IoT Hub első lépései](quickstart-send-telemetry-python.md) oktatóanyaggal.

* Konfigurálja az eszközöket az ikereszköz kívánt tulajdonságainak használatával a Kívánt tulajdonságok használata az [eszközök oktatóanyagának konfigurálásához.](tutorial-device-twins.md)

* Az eszközök interaktív vezérlése (például egy ventilátor bekapcsolása egy felhasználó által vezérelt alkalmazásból) a [Közvetlen módszerek használata](quickstart-control-device-python.md) oktatóanyaggal.
