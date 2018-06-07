---
title: Eszköz belső vezérlőprogram frissítése az Azure IoT Hub (Python) |} Microsoft Docs
description: Hogyan használható az eszközkezelés Azure IoT hub eszköz vezérlőprogram-frissítés kezdeményezése. A szimulált eszköz alkalmazásának és a service-alkalmazást, amely elindítja a belső vezérlőprogram-frissítés végrehajtásához használhatja az Azure IoT SDK-k a Python.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: kgremban
ms.openlocfilehash: d2ebdf54e595c2f02464c0c2446a6e5f5feefb9c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634641"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Használjon Eszközkezelés kezdeményezheti a eszköz belső vezérlőprogram frissítése (Python vagy Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Az a [Ismerkedés az eszközkezelés] [ lnk-dm-getstarted] oktatóanyag, megtudhatta, hogyan használható a [eszköz iker] [ lnk-devtwin] és [közvetlen módszerek ] [ lnk-c2dmethod] primitívek távolról az eszköz újraindítását. Ez az oktatóanyag használja az ugyanazon az IoT-központ primitívek és nyújt útmutatást, és bemutatja, hogyan hajtsa végre egy végpontok közötti szimulált belső vezérlőprogram-frissítés.  Ebben a mintában az Intel Edison eszköz minta szolgál a belső vezérlőprogram frissítési megvalósításához.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Python-Konzolalkalmazás, amely behívja a firmwareUpdate közvetlen módszer a szimulált eszköz alkalmazásban az IoT hub keresztül.
* Hozzon létre egy szimulált eszköz alkalmazást, amely egy **firmwareUpdate** közvetlen módszer. Ez a módszer indít el egy többlépcsős folyamat, amely megvárja-e a belső vezérlőprogram lemezképet letölti, letölti a belső vezérlőprogram lemezképet és a belső vezérlőprogram kép végül vonatkozik. A frissítés egyes szakasza alatt az eszköz használatával a jelentésben szereplő tulajdonságok előrehaladásról.

Ez az oktatóanyag végén két Python konzol alkalmazások közül választhat:

**dmpatterns_fwupdate_service.PY**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazás jeleníti meg a választ, és rendszeres időközönként (minden 500ms) jeleníti meg a frissített jelenteni tulajdonságait.

**dmpatterns_fwupdate_device.PY**, amely kapcsolódik az IoT hub, korábban létrehozott eszköz identitású kap egy firmwareUpdate közvetlen módszer esetén az több államot folyamatot, a belső vezérlőprogram frissítési például szimulálásához: Várakozás a kép Töltse le, az új lemezkép letöltése, és végül a kép alkalmazása.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* [Python 2.x vagy 3.x][lnk-python-download]. Mindenképp a rendszernek megfelelő, 32 vagy 64 bites telepítést használja. Amikor a rendszer erre kéri, mindenképp adja hozzá a Pythont a platformspecifikus környezeti változóhoz. Ha a Python 2.x verziót használja, előfordulhat, hogy [telepítenie vagy frissítenie kell a *pip*-et, a Python csomagkezelő rendszerét][lnk-install-pip].
* Ha Windows operációs rendszert használ, a [Visual C++ terjeszthető csomagra][lnk-visual-c-redist] van szükség a Python natív DLL-jei használatához.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Indítás, az eszközön, a közvetlen módszer használatával távoli vezérlőprogram-frissítés
Ebben a szakaszban egy eszköz távoli vezérlőprogram-frissítés kezdeményező Python-Konzolalkalmazás létrehozása. Az alkalmazás közvetlen módszer előtt használja a frissítés, és az eszköz iker lekérdezések rendszeres időközönként a aktív belső vezérlőprogram frissítése a állapot lekérdezése céljából.

1. A parancssorba a következő parancsot a telepítendő a **azure-IOT hubbal-szolgáltatásügyfél** csomag:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. A munkakönyvtár, a egy szövegszerkesztő használatával hozzon létre egy **dmpatterns_getstarted_service.py** fájlt.

1. Adja hozzá a következő "import" utasítások és változók elején a **dmpatterns_getstarted_service.py** fájlt. Cserélje le `IoTHubConnectionString` és `deviceId` azt már korábban említettük a értékekkel:
   
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

1. Adja hozzá a következő függvény közvetlen metódust, és a firmwareUpdate értékének megjelenítése jelentett tulajdonság. Is hozzáadhat a `main` rutin:
   
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

* Hozzon létre egy Python-konzolalkalmazást, amely válaszol a felhő által meghívott egy közvetlen módszer
* Aktivál egy szimulált belsővezérlőprogram-frissítést
* A jelentett tulajdonságok használatával ikereszköz-lekérdezéseket engedélyez az eszközök azonosítására és utolsó belsővezérlőprogram-frissítésük időpontjának megállapítására

1. A parancssorba a következő parancsot a telepítendő a **azure-IOT hubbal-eszközügyfél** csomag:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Egy szövegszerkesztő használatával hozzon létre egy **dmpatterns_fwupdate_device.py** fájlt.

1. Adja hozzá a következő "import" utasítások és változók elején a **dmpatterns_fwupdate_device.py** fájlt. Cserélje le `deviceConnectionString` az IoT hub az eszköz kapcsolati karakterlánccal:
   
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

1. Adja hozzá a következő funkciókat biztosít a Tulajdonságok frissítések jelentett és valósítja meg a közvetlen használt:
   
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

8. Adja hozzá a következő függvény, amely az eszköz iker inicializálja jelentett tulajdonságait, és várja meg, a közvetlen hívandó metódust. Is hozzáadhat a `main` rutin:
   
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
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban, meg kell valósítania újrapróbálkozási házirendek (például az exponenciális leállítási), az MSDN-cikkben leírtak [átmeneti hiba kezelése](https://msdn.microsoft.com/library/hh675232.aspx).
> 


## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancssorba a következő parancsot a rendszer újraindítása közvetlen módszer figyelését.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. Egy másik parancssorban futtassa a következő parancsot a távoli újraindítás és a lekérdezés megadása a eszköz iker található az utolsó újraindítás idő elindítani.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. A közvetlen módszer a konzolon eszköz válasz megjelenik. Jegyezze fel az egész a belső vezérlőprogram frissítési jelentett tulajdonságok módosítása.

    ![Program kimenete][1]


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag elindítása egy távoli belső vezérlőprogram frissítése egy eszközön a közvetlen módszer használatával, és a jelentésben szereplő tulajdonságok segítségével nyomon követheti a belső vezérlőprogram frissítése.

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

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
