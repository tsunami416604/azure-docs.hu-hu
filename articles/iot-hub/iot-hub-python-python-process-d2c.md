---
title: "Az Azure IoT Hub (Python) útválasztási üzenetek |} Microsoft Docs"
description: "How Azure IoT Hub eszközről a felhőbe üzenetek feldolgozásához átirányítani más háttérszolgáltatások üzenetek útválasztási szabályokat és az egyéni végpontokat használatával."
services: iot-hub
documentationcenter: python
author: msebolt
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo
ms.openlocfilehash: f467437afb4bf89e77668cfd3e8a824bfbde9e10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>Üzenetek (Python). az IoT hubbal

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Ez az oktatóanyag épít, a [Ismerkedés az IoT-központ] oktatóanyag.  Az oktatóanyag:

* Bemutatja, hogyan könnyen és konfigurációalapú az eszközről a felhőbe üzenetek mennyi útválasztási szabályok használatával.
* Bemutatja, hogyan interaktív üzenetek további feldolgozásra a megoldás háttérből azonnali beavatkozást igénylő elkülönítéséhez.  Például egy eszköz előfordulhat, hogy küldése riasztás, amely elindítja a jegy beszúrása egy CRM-rendszerbe.  Adatpont üzenetek, például hőmérséklet telemetriai ellentétben be egy analytics hírcsatorna.

Ez az oktatóanyag végén három Python konzol alkalmazások futtatása:

* **SimulatedDevice.py**, az alkalmazás létrehozása a módosított változatát a [Ismerkedés az IoT-központ] oktatóanyagban adatpont eszközről a felhőbe üzeneteket küld minden második és interaktív eszközről a felhőbe üzenetek véletlenszerű időköz. Ez az alkalmazás kommunikáljon az IoT-központ MQTT protokollt használ.
* **ReadCriticalQueue.py** távolít el a Service Bus-üzenetsorba, az IoT hub csatolva a kritikus üzeneteihez.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek, beleértve a C, Java és JavaScript SDK támogatása. Az eszköz a fizikai eszköz ebben az oktatóanyagban helyettesítése és eszközök csatlakoztatása az IoT-központ, lásd: a [Azure IoT fejlesztői központ].

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A teljes működő verziójához a [Ismerkedés az IoT-központ] oktatóanyag.
* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* [Node.js 4.0 vagy újabb][lnk-node-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Ez az [IoT Hub Explorer eszköz][lnk-iot-hub-explorer] telepítéséhez szükséges.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Javasoljuk továbbá kapcsolatos tájékoztatás [Azure Storage] és [Azure Service Bus].


## <a name="send-interactive-messages-from-a-device-app"></a>Interaktív üzenetek küldése egy eszköz alkalmazásból
Ebben a szakaszban létrehozott eszközalkalmazás módosítja a [Ismerkedés az IoT-központ] alkalmanként küldéséhez azonnali feldolgozást igénylő oktatóanyag.

1. Egy szövegszerkesztővel nyissa meg a **SimulatedDevice.py** fájlt. Ez a fájl tartalmazza a kódját a **SimulatedDevice** létrehozta az alkalmazást a [Ismerkedés az IoT-központ] oktatóanyag.

2. Cserélje le a **iothub_client_telemetry_sample_run** függvény a következő kóddal:

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    Ez a módszer véletlenszerűen hozzáadja a tulajdonság `"level": "critical"` és `"level": "storage"` az eszköz által küldött üzenetek, amely szimulálja egy üzenetet, amely az alkalmazás háttér-vagy, hogy véglegesen kell tárolni, hogy azonnali beavatkozást igényel. Az alkalmazás az üzenet törzse alapuló útválasztási üzeneteket is támogatja.
   
   > [!NOTE]
   > Több, különböző esetekre, beleértve a cold-path feldolgozási mellett a gyakran használt adatok elérési útja az itt bemutatott példában üzenettulajdonságok üzenetek is használhatja.

1. Mentse és zárja be a **SimulatedDevice.py** fájlt.

    > [!NOTE]
    > Az egyszerűség kedvéért ez az oktatóanyag nem valósítja meg az összes újrapróbálkozási házirendje. Az éles kódban, meg kell valósítania egy újrapróbálkozási házirendje, például az exponenciális leállítási, az MSDN-cikkben leírtak [átmeneti hiba kezelése].


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>Az IoT hub és útvonal üzenetek számukra várólisták hozzáadása

Ez a szakasz mind a Service Bus-üzenetsorba, és a Storage-fiók létrehozása, csatlakoztassa őket az IoT hub, és az IoT hub üzenetek küldése az üzenetsorba, annak alapján, hogy az üzenet és minden üzenetet a tárfiók-tulajdonság a beállítása. A Service Bus-üzenetsorok folyamat üzenetek módjáról további információkért lásd: [Ismerkedés a várólisták] [ lnk-sb-queues-node] és felügyelni a tárolást tudnivalókat [Ismerkedés az Azure Storage szolgáltatással] [Az azure Storage].

1. Hozzon létre egy Service Bus-üzenetsorba, a [Ismerkedés a várólisták][lnk-sb-queues-node]. Jegyezze fel az a névtér és a várólista nevét.

    > [!NOTE]
    > Service Bus-üzenetsorok és témakörök használatos az IoT-központok végpontjai nem lehet **munkamenetek** vagy **ismétlődő észlelési** engedélyezve van. Ha ezek a lehetőségek valamelyikét engedélyezve vannak, a végpont megjelenik **Unreachable** az Azure portálon.

1. Az Azure portálon, nyissa meg az IoT hub, és kattintson **végpontok**.

    ![Az IoT-központ végpontok][30]

1. Az a **végpontok** panelen kattintson **Hozzáadás** a sor hozzáadása az IoT hub tetején. A végpont neve **CriticalQueue** majd válassza ki a legördülő listákat **Service Bus-üzenetsorba**, ahol a várólista van a Service Bus-névtér és a várólista nevét. Amikor elkészült, kattintson a **OK** alján.  

    ![A végpont hozzáadása][31]

1. Most kattintson **útvonalak** az IoT Hub a. Kattintson a **Hozzáadás** útválasztási szabály, amely továbbítja az üzeneteket a várólista létrehozása a panel tetején az előzőekben adott hozzá. 

    ![Egy útvonal hozzáadása][34]

    Adjon meg egy nevet, és válassza ki **eszközre küldött üzenetek** a adatforrásként. Válasszon **CriticalQueue** , az útvonal egy egyéni végpontként végpont szabályt, és írja be `level="critical"` lekérdezési karakterláncként kell beállítani. Kattintson a **mentése** alján.

    ![Útvonal részletei][32]

    Győződjön meg arról, hogy a tartalék útvonal értéke **ON**. Ez a beállítás az alapértelmezett beállítása az IoT-központ.

    ![Tartalék útvonal][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Választható) A várólista végpontról olvasása

Ebben a szakaszban hozzon létre egy Python-Konzolalkalmazás, amely olvassa be az IoT Service Bus kritikus üzeneteihez. További információ a [Ismerkedés a várólisták][lnk-sb-queues-node]. 

1. Nyisson meg egy új parancssort, és telepítse a Pythonhoz készült Azure IoT Hub eszközoldali SDK-t az alábbiak szerint. A telepítés után zárja be a parancssort.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > Problémák telepítése a **azure-szolgáltatásbusz** csomag vagy a további telepítési beállítások megtekintéséhez tekintse meg a [Python azure-szolgáltatásbusz csomag][lnk-python-service-bus].

1. Hozzon létre egy fájlt **ReadCriticalQueue.py**. Nyissa meg a fájlt egy szabadon választott Python-szerkesztőben/IDE-ben (például az IDLE környezetben).

1. Adja hozzá a következő kódot a szükséges modulokat importálása az eszközre SDK:

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. Az alábbi kódot, és cserélje le a helyőrzőket a service Bus kapcsolati adatait:

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. Adja hozzá a következő kódot csatlakozzanak, olvassa el a service bus: 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. Végül adja hozzá a fő függvényt. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. Mentse és zárja be a **ReadCriticalQueue.py** fájlt. Most már készen áll az alkalmazások futtatására.


## <a name="run-the-applications"></a>Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

1. Nyisson meg egy parancssort, és telepítse az IoT Hub Explorert. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. Futtassa az alábbi parancsot a parancssorban az eszközről érkező, az eszközről a felhőbe irányuló üzenetek megfigyelésének indításához. Használja az IoT Hub kapcsolati karakterláncát a helyőrzőben a `--login` után.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. Nyisson egy új parancssort, és lépjen a **SimulatedDevice.py** fájlt tartalmazó könyvtárba.

1. Futtassa a **SimulatedDevice.py** fájlt, amely rendszeres időközönként telemetriaadatokat küld az IoT Hubnak. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. Futtatásához a **ReadCriticalQueue** alkalmazás, a parancssor vagy a rendszerhéj navigáljon **ReadCriticalQueue.py** fájlt, és hajtsa végre a következő parancsot:

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. Vizsgálja meg az eszköz üzeneteit a parancssorban az előző szakaszban ismertetett IoT Hub Explorer futtatásával. Figyelje meg a `critical` állapotüzenetek a **ReadCriticalQueue** alkalmazás.

    ![Eszközről felhőbe irányuló Python-üzenetek][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Választható) Az IoT hub és útvonal üzenetek hozzá a tároló hozzáadása

Ebben a szakaszban hozzon létre egy tárfiókot, csatlakoztassa az IoT hub, és konfigurálja az IoT hub üzeneteket küldhet a fiók, az üzenet tulajdonság alapján. Felügyelni a tárolást kapcsolatos további információkért lásd: [Ismerkedés az Azure Storage szolgáltatással][Azure Storage].

 > [!NOTE]
   > Az IoT-központ fiókjainak a _F1 ingyenes_ réteg korlátozva, és egy **végpont**. Ha nem csak egy **végpont**, előfordulhat, hogy a telepítő a **StorageContainer** kívül a **CriticalQueue** , és mindkét simulatneously futtassa.

1. Hozzon létre egy tárfiókot, a [Azure Storage-dokumentációt][lnk-storage]. Jegyezze fel a fiók nevét.

2. Az Azure portálon, nyissa meg az IoT hub, és kattintson **végpontok**.

3. Az a **végpontok** panelen válassza a **CriticalQueue** végpontot, és kattintson **törlése**. Kattintson a **Igen**, és kattintson a **Hozzáadás**. A végpont neve **StorageContainer** majd válassza ki a legördülő listákat **Azure-tárolót**, és hozzon létre egy **tárfiók** és egy **tárolási tároló**.  Jegyezze fel a neveket.  Amikor elkészült, kattintson a **OK** alján. 

 > [!NOTE]
   > Az IoT-központ fiókjainak a _F1 ingyenes_ réteg korlátozva, és egy **végpont**. Ha nem csak egy **végpont**, nem kell törölnie a **CriticalQueue**.

4. Kattintson a **útvonalak** az IoT Hub a. Kattintson a **Hozzáadás** útválasztási szabály, amely továbbítja az üzeneteket a várólista létrehozása a panel tetején az előzőekben adott hozzá. Válassza ki **eszközre küldött üzenetek** a adatforrásként. Adja meg `level="storage"` feltételt, és válassza a **StorageContainer** útválasztási szabály végpontjának egyéni végpontként. Kattintson a **mentése** alján.  

    Győződjön meg arról, hogy a tartalék útvonal értéke **ON**. Ez a beállítás az alapértelmezett beállítása az IoT-központ.

1. Győződjön meg arról, hogy az előző alkalmazás **SimulatedDevice.py** futása. 

1. Az Azure portálon lépjen a tárfiókhoz a **Blob szolgáltatás**, kattintson a **blobok Tallózás...** .  Jelölje ki a tárolót, keresse meg és kattintson a JSON-fájlt, és kattintson **letöltése** adatok megtekintéséhez.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudta, hogyan megbízhatóan átirányítani az eszköz a felhőbe küldött üzeneteket az üzenetet az IoT-központ útválasztási funkcióra használatával.

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [Azure IoT Suite][lnk-suite].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

Az üzenetet az IoT hubon útválasztási kapcsolatos további információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal][lnk-devguide-messaging].

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Ismerkedés az IoT-központ]: iot-hub-python-getstarted.md
[Azure IoT fejlesztői központ]: https://azure.microsoft.com/develop/iot

[átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/en-us/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus