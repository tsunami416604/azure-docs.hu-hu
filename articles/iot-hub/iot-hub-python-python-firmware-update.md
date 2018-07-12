---
title: Eszköz belső vezérlőprogramjának frissítése az Azure IoT Hub (Python) szolgáltatással |} A Microsoft Docs
description: Hogyan lehet Azure IoT Hub eszközfelügyeleti használatával kezdeményezheti az eszköz belső vezérlőprogram frissítése. Az Azure IoT SDK Pythonhoz készült használatával valósítható meg egy szimulált eszközalkalmazás és a egy service-alkalmazás, amely elindítja a belső vezérlőprogram frissítését.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: kgremban
ms.openlocfilehash: d2ebdf54e595c2f02464c0c2446a6e5f5feefb9c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38482020"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Eszközfelügyelet használatát kezdeményezéséhez egy eszköz belső vezérlőprogramjának frissítéséhez (Python vagy Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Az a [Eszközfelügyelet – első lépések] [ lnk-dm-getstarted] oktatóanyag bemutatta, hogyan használható a [ikereszköz] [ lnk-devtwin] és [közvetlen metódusok ] [ lnk-c2dmethod] primitívek távolról újraindítja az eszközt. Ebben az oktatóanyagban használja az ugyanazon az IoT Hub primitívek és útmutatást nyújt, és bemutatja, hogyan teheti egy teljes körű szimulált belsővezérlőprogram-frissítést.  Ezt a mintát az Intel Edison eszköz minta a belső vezérlőprogram frissítési végrehajtására szolgál.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Python-Konzolalkalmazás, amely meghívja ezt firmwareUpdate közvetlen metódus a szimulált eszköz alkalmazásban az IoT hub segítségével.
* Egy szimulált eszközalkalmazás létrehozása, amely valósít meg egy **firmwareUpdate** közvetlen metódust. Ez a módszer folyamatot kezdeményez, többlépcsős, a belső vezérlőprogram rendszerképének letöltésére vár, a belső vezérlőprogram rendszerképének letöltése és végül alkalmazza a belsővezérlőprogram-lemezképet. A frissítés minden egyes fázisa során az eszköz jelentett tulajdonságait felhasználva jelenti az állapotot.

Ez az oktatóanyag végén két Python-konzolalkalmazással fog rendelkezni:

**dmpatterns_fwupdate_service.PY**, amely meghívja a közvetlen metódus a szimulált eszközalkalmazásnak, a jeleníti meg a válasz és rendszeres időközönként (minden 500ms) jeleníti meg a frissített jelentett tulajdonságokként.

**dmpatterns_fwupdate_device.PY**, csatlakozik az IoT hubhoz a korábban létrehozott eszközidentitással firmwareUpdate közvetlen metódus kap, szimulálja a belső vezérlőprogram frissítési többek között több állapot folyamaton keresztül futtatja: Várakozás a kép Töltse le, az új lemezkép letöltése, és végül a lemezkép alkalmazása.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Aktiválja az eszközön, a közvetlen metódus használatával távoli belső vezérlőprogram frissítése
Ebben a szakaszban hozzon létre egy Python-Konzolalkalmazás, amely kezdeményezi egy távoli belső vezérlőprogram frissítése egy eszközön. Az alkalmazás közvetlen metódus kezdeményezni a frissítést, és használja az ikereszköz-lekérdezések rendszeres időközönként beolvasni az aktív belső vezérlőprogram frissítésének állapotát.

1. A parancssorban futtassa a telepítéséhez a következő parancsot a **azure-iothub-service-client** csomag:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. A munkakönyvtárban egy szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_service.py** fájlt.

1. Adja hozzá a következő "import" utasításokat és változókat elején a **dmpatterns_getstarted_service.py** fájlt. Cserélje le `IoTHubConnectionString` és `deviceId` a korábban feljegyzett értékekkel:
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. Adja hozzá a következő függvényt hívja meg a közvetlen metódust, és a firmwareUpdate értékének megjelenítése jelentett tulajdonság. Is hozzáadhat a `main` rutin:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
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
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. Mentse és zárja be a **dmpatterns_fwupdate_service.py** fájlt.


## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban:

* Hozzon létre egy Python-Konzolalkalmazás, amely a felhő által meghívott közvetlen metódusra válaszol
* Aktivál egy szimulált belsővezérlőprogram-frissítést
* A jelentett tulajdonságok használatával ikereszköz-lekérdezéseket engedélyez az eszközök azonosítására és utolsó belsővezérlőprogram-frissítésük időpontjának megállapítására

1. A parancssorban futtassa a telepítéséhez a következő parancsot a **azure-iothub-device-client** csomag:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Egy szövegszerkesztővel hozzon létre egy **dmpatterns_fwupdate_device.py** fájlt.

1. Adja hozzá a következő "import" utasításokat és változókat elején a **dmpatterns_fwupdate_device.py** fájlt. Cserélje le `deviceConnectionString` az eszköz kapcsolati karakterlánccal az IoT hubról:
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. Adja hozzá a következő függvényeket, jelentett tulajdonságok frissítések és a közvetlen metódus megvalósításához használt:
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Adja hozzá a vezérlőprogram-rendszerkép letöltését és alkalmazását szimuláló alábbi függvényeket:
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. Adja hozzá a következő függvényt, amely inicializálja az ikereszköz jelentett tulajdonságait, és várjon, amíg meghívandó közvetlen metódus. Is hozzáadhat a `main` rutin:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például egy exponenciális leállítást), az MSDN-cikkben leírtak implementálandó [átmeneti hibák kezelésével](https://msdn.microsoft.com/library/hh675232.aspx).
> 


## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. Parancsot a parancssorba futtassa a következő parancsot, amellyel megkezdheti a újraindítás közvetlen metódus figyel.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. Egy másik parancssorban futtassa a következő parancsot a távoli újraindítás és a lekérdezés az ikereszköz található az utolsó újraindítás időpontja eseményindítóra.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Láthatja, hogy az eszköz válasza a közvetlen metódus a konzolon. Jegyezze fel a módosítás a jelentett tulajdonságok a belső vezérlőprogram frissítése során.

    ![a program kimenete][1]


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban használt közvetlen metódus aktiválhat egy távoli belső vezérlőprogram frissítése egy eszközön, és hajtsa végre a belső vezérlőprogram frissítésének állapotát a jelentett tulajdonságok segítségével.

Ismerje meg, hogyan bővítheti az IoT-megoldás és az ütemezés metódus meghívja a több eszközre, tekintse meg a [feladatok ütemezése és szórása] [ lnk-tutorial-jobs] oktatóanyag.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
