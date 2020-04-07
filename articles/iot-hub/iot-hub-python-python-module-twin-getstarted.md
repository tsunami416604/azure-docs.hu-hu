---
title: Az Azure IoT Hub modul identitása és ikermodul (Python)
description: Ismerje meg, hogyan hozhat létre modulidentitást és frissítheti az ikermodult az IoT SDK-k python-hoz használatával.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756945"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Az IoT Hub modulidentitás és az ikermodul (Python) első lépései

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [A modulidentitások és a modultwins](iot-hub-devguide-module-twins.md) hasonlóaz Azure IoT Hub-eszköz identitások és az ikereszközök, de finomabb részletességet biztosít. Míg az Azure IoT Hub-eszközidentitások és az ikereszközök lehetővé teszik, hogy egy háttéralkalmazás konfiguráljon egy eszközt, és láthatóvá tegye az eszköz feltételeit, a modulidentitások és a modultwins biztosítják ezeket a képességeket az eszköz egyes összetevőihez. A több összetevőből képes eszközökön, például az operációs rendszer alapú eszközökön vagy a belső vezérlőprogram-eszközökön lehetővé teszik az egyes összetevők elkülönített konfigurációját és feltételeit.
>

Az oktatóanyag végén három Python-alkalmazással rendelkezik:

* **CreateModule**, amely létrehoz egy eszköz identitását, modulidentitását és a kapcsolódó biztonsági kulcsokat az eszköz és a modulügyfelek csatlakoztatásához.

* **UpdateModuleTwinDesiredProperties ,** amely elküldi a frissített modul iker kívánt tulajdonságokat az IoT Hub.

* **ReceiveModuleTwinDesiredPropertiesPatch**, amely megkapja a modul iker kívánt tulajdonságok javítás a készüléken.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

Ebben a cikkben hozzon létre egy háttér-szolgáltatás, amely hozzáad egy eszközt az identitás-beállításjegyzékben, majd hozzáad egy modult az eszközhöz. Ehhez a szolgáltatáshoz a **rendszerleíró adatbázis írási** engedélye szükséges (amely magában foglalja **a rendszerleíró adatbázis olvasását**is). Hozzon létre egy szolgáltatást, amely hozzáadja a kívánt tulajdonságokat az újonnan létrehozott modul ikermoduljához. Ennek a szolgáltatásnak szüksége van a **szolgáltatás csatlakozási engedélyére.** Bár vannak olyan alapértelmezett megosztott hozzáférési házirendek, amelyek egyenként adják meg ezeket az engedélyeket, ebben a szakaszban egy egyéni megosztott hozzáférési szabályzatot hoz létre, amely mindkét engedélyt tartalmazza.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Eszközidentitás és modulidentitás létrehozása az IoT Hubban

Ebben a szakaszban hozzon létre egy Python szolgáltatásalkalmazást, amely létrehoz egy eszközidentitást és egy modulidentitást az IoT hub identitás-beállításjegyzékében. Egy eszköz vagy modul nem tud csatlakozni az IoT hubhoz, kivéve, ha rendelkezik egy bejegyzéssel az identitás-beállításjegyzékben. További információ: [Az IoT hub identitásbeállításának megismerése.](iot-hub-devguide-identity-registry.md) A konzolalkalmazás a futtatásakor egy egyedi azonosítót és kulcsot állít elő az eszköz és a modul számára. Ezekkel az értékekkel az eszköz és a modul azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az azonosítók megkülönböztetik a kis- és nagybetűket.

1. A parancssorban futtassa a következő parancsot az **azure-iot-hub** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. A parancssorban futtassa a következő parancsot az **msrest** csomag telepítéséhez. A **HTTPOperationError** kivételek észleléséhez szüksége van erre a csomagra.

    ```cmd/sh
    pip install msrest
    ```

1. Szövegszerkesztő használatával hozzon létre egy **CreateModule.py** nevű fájlt a munkakönyvtárban.

1. Adja hozzá a következő kódot a Python-fájlhoz. Cserélje le *a YourIoTHubConnectionString karakterláncot* az [IoT hub kapcsolati karakterláncának bemásolása](#get-the-iot-hub-connection-string)című helyen másolt kapcsolati karakterláncra.

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. A parancssorban futtassa a következő parancsot:

    ```cmd/sh
    python CreateModule.py
    ```

Ez az alkalmazás létrehoz egy eszköz identitásazonosítóazonosítóazonosítót **myD myFirstDevice** és egy modul identitás id **myFirstModule** eszköz alatt **myFirstDevice**. (Ha az eszköz vagy a modul azonosítója már létezik az identitásjegyzékben, a kód egyszerűen beolvassa a meglévő eszköz- vagy moduladatokat.) Az alkalmazás megjeleníti az azonosítót és az elsődleges kulcsot az egyes identitások.

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszköz- és modulidentitásokat. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. A modulidentitások esetében nincs engedélyezési/letiltási jelző. További információ: [Az IoT hub identitásbeállításának megismerése.](iot-hub-devguide-identity-registry.md)
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Az ikermodul frissítése a Python szolgáltatás SDK-jával

Ebben a szakaszban hozzon létre egy Python szolgáltatásalkalmazást, amely frissíti a modul iker kívánt tulajdonságait.

1. A parancssorban futtassa a következő parancsot az **azure-iot-hub** csomag telepítéséhez. Ezt a lépést kihagyhatja, ha az előző szakaszban telepítette az **azure-iot-hub** csomagot.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Szövegszerkesztő használatával hozzon létre egy **UpdateModuleTwinDesiredProperties.py** nevű fájlt a munkakönyvtárban.

1. Adja hozzá a következő kódot a Python-fájlhoz. Cserélje le *a YourIoTHubConnectionString karakterláncot* az [IoT hub kapcsolati karakterláncának bemásolása](#get-the-iot-hub-connection-string)című helyen másolt kapcsolati karakterláncra.

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Frissítések beszerezni az eszköz oldalán

Ebben a szakaszban hozzon létre egy Python-alkalmazást, hogy a modul iker kívánt tulajdonságok frissítése az eszközön.

1. A modul kapcsolati karakterláncának bekapcsolása. Az [Azure Portalon](https://portal.azure.com/)keresse meg az IoT Hubot, és válassza ki az **IoT-eszközöket** a bal oldali ablaktáblában. Válassza ki a **myFirstDevice-t** az eszközök listájából, és nyissa meg. A **Modulidentitások csoportban**válassza a **MyFirstModule lehetőséget.** Másolja ki a modul kapcsolati sztringjét. Szüksége van rá a következő lépésben.

   ![Az Azure Portal moduladatai](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. A parancssorban futtassa a következő parancsot az **azure-iot-device** csomag telepítéséhez:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Szövegszerkesztő használatával hozzon létre egy **ReceiveModuleTwinDesiredPropertiesPatch.py** nevű fájlt a munkakönyvtárban.

1. Adja hozzá a következő kódot a Python-fájlhoz. Cserélje le a *YourModuleConnectionString karakterláncot* az 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Ebben a szakaszban futtatja a **ReceiveModuleTwinDesiredPropertiesPatch** eszközalkalmazást, majd az **UpdateModuleTwinDesiredProperties** szolgáltatásalkalmazást a modul kívánt tulajdonságainak frissítéséhez.

1. Nyisson meg egy parancssort, és futtassa az eszközalkalmazást:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Az eszközalkalmazás kezdeti kimenete](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Nyisson meg egy külön parancssort, és futtassa a szolgáltatásalkalmazást:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Figyelje meg, hogy a **TelemetryInterval** kívánt tulajdonság a frissített ikermodulban jelenik meg a szolgáltatásalkalmazás kimenetében:

   ![A szolgáltatásalkalmazás kimenete](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Ugyanez a tulajdonság jelenik meg a kívánt tulajdonságjavító javításban, amelyet az eszközalkalmazás kimenetén kapott:

   ![Az eszközalkalmazás kimenete a kívánt tulajdonságok javítását mutatja](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>További lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszközfelügyelet – első lépések](iot-hub-node-node-device-management-get-started.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)