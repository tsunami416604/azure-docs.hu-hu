---
title: Az Azure IoT-megoldáshoz (Python) kapcsolódó IoT-Plug and Play előnézeti eszköz használata | Microsoft Docs
description: A Python használatával csatlakozhat egy IoT Plug and Play előnézeti eszközhöz, amely az Azure IoT-megoldáshoz csatlakozik.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e3f00bb601cce17721c5247941588be1c2de788d
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352956"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-python"></a>Rövid útmutató: a megoldáshoz csatlakoztatott IoT Plug and Play előnézeti eszköz (Python) használata

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

A IoT Plug and Play Preview leegyszerűsíti a IoT azáltal, hogy lehetővé teszi az eszköz modellel való interakciót a mögöttes eszköz megvalósításának ismerete nélkül. Ez a rövid útmutató bemutatja, hogyan használható a Python a megoldáshoz csatlakoztatott IoT Plug and Play-eszköz csatlakoztatásához és vezérléséhez.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a fejlesztői gépen a Python 3,7 szükséges. A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót. A Python-verziót a következő paranccsal tekintheti meg:  

```cmd/sh
python --version
```

Telepítse a [Python Service SDK előzetes csomagot](https://pypi.org/project/azure-iot-hub/2.2.1rc0/) a következő parancs futtatásával:

```cmd/sh
pip3 install azure-iot-hub==2.2.1rc0
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Futtassa a következő parancsot a hub _IoT hub-kapcsolódási karakterláncának_ lekéréséhez. Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később a rövid útmutatóban fog használni:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

A következő parancs futtatásával lekérheti a hubhoz felvett eszközhöz tartozó _eszköz-kapcsolódási karakterláncot_ . Jegyezze fel ezt a összekapcsolási karakterláncot, amelyet később a rövid útmutatóban fog használni:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

Ebben a rövid útmutatóban egy Pythonban írt minta termosztát-eszközt használ a IoT Plug and Play eszközként. A minta eszköz futtatása:

1. Nyisson meg egy terminál-ablakot tetszőleges mappában. Futtassa a következő parancsot az [Azure IoT PYTHON SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub-tárház ezen a helyen történő klónozásához:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. A rendszer ezt a terminált használja az **eszköz** -terminálként. Lépjen a klónozott adattár mappájába, és lépjen a */Azure-IOT-SDK-Python/Azure-IOT-Device/Samples/PnP* mappára.

1. Az _eszköz-kapcsolatok karakterláncának_konfigurálása:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Futtassa a minta termosztátos eszközt a következő paranccsal:

    ```cmd/sh
    python pnp_thermostat.py
    ```

1. Üzenet jelenik meg, amely közli, hogy az eszköz elküldött néhány információt, és online jelentett. Ezek az üzenetek azt jelzik, hogy az eszköz megkezdte a telemetria-adatok küldését a központba, és készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárjuk be ezt a terminált, hogy erősítse meg, hogy a szolgáltatási minta működik-e.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

Ebben a rövid útmutatóban egy minta IoT megoldást használunk a Pythonban, hogy együttműködjön az imént beállított eszközzel.

1. Nyisson meg egy másik Terminálablak **szolgáltatást a szolgáltatás** -terminálként való használatra. A Service SDK előzetes verzióban érhető el, ezért a [PYTHON SDK előzetes verziójú ágának](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh)kell a mintákat klónozott:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python -b pnp-preview-refresh
    ```

1. Lépjen a klónozott adattár-ág mappájába, és navigáljon a */Azure-IOT-SDK-Python/Azure-IOT-hub/Samples* mappára.

1. Adja meg az eszköz AZONOSÍTÓjának környezeti változóit, és _IoT hub a kapcsolatok karakterláncát_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIOTHubConnectionString>
    set IOTHUB_DEVICE_ID=<Your device ID>
    ```

1. A Samples mappában négy minta fájl található `pnp` előtaggal. Ezek a minták azt mutatják be, hogyan használhatók az egyes API-k az IoT Plug and Play-eszközökkel való interakcióhoz:

### <a name="get-digital-twin"></a>Digitális dupla Letöltés

Használja a következő parancsot a **szolgáltatás** -terminálon a minta futtatásához:

```cmd/sh
python pnp_get_digital_twin_sample.py
```

A kimenet az eszköz digitális ikerét jeleníti meg, és kiírja a modell AZONOSÍTÓját:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

A következő kódrészlet a *pnp_get_digital_twin_sample.* a (z):

```python
    # Get digital twin and retrieve the modelId from it
    digital_twin = iothub_digital_twin_manager.get_digital_twin(device_id)
    if digital_twin:
        print(digital_twin)
        print("Model Id: " + digital_twin["$metadata"]["$model"])
    else:
        print("No digital_twin found")
```

### <a name="update-a-digital-twin"></a>Digitális iker frissítése

Ez a minta bemutatja, hogyan frissítheti *a tulajdonságokat* a tulajdonságok digitális Twin használatával történő frissítéséhez. A következő kódrészlet a *pnp_update_digital_twin_sample.* a (z) a javítás összeállítását mutatja be:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Használja a következő parancsot a **szolgáltatás** -terminálon a minta futtatásához:

```cmd/sh
python pnp_update_digital_twin_sample.py
```

Ellenőrizze, hogy a frissítés az **eszköz** -terminálon van-e alkalmazva, amely a következő kimenetet jeleníti meg:

```cmd/sh
the data in the desired properties patch was: {'targetTemperature': 42, '$version': 2}
previous values
42
```

A **szolgáltatás** -terminál ellenőrzi, hogy a javítás sikeres volt-e:

```cmd/sh
Patch has been successfully applied
```

### <a name="invoke-a-command"></a>Parancs meghívása

Parancs meghívásához futtassa a *pnp_invoke_command_sample.* Ez a minta bemutatja, hogyan hívhat meg egy parancsot egy egyszerű termosztátos eszközön. A minta futtatása előtt állítsa be a `IOTHUB_COMMAND_NAME` és a `IOTHUB_COMMAND_PAYLOAD` környezeti változókat a **szolgáltatás** -terminálon:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

A **szolgáltatás** -terminálon használja a következő parancsot a minta futtatásához:
  
```cmd/sh
python pnp_invoke_command_sample.py
```

A **szolgáltatás** -terminál egy megerősítő üzenetet jelenít meg az eszközről:

```cmd/sh
{"tempReport": {"avgTemp": 34.5, "endTime": "13/07/2020 16:03:38", "maxTemp": 49, "minTemp": 11, "startTime": "13/07/2020 16:02:18"}}
```

Az **eszköz** -terminálon láthatja, hogy az eszköz megkapja a következő parancsot:

```cmd/sh
Command request received with payload
hello world
Will return the max, min and average temperature from the specified time hello to the current time
Done generating
{"tempReport": {"avgTemp": 34.2, "endTime": "09/07/2020 09:58:11", "maxTemp": 49, "minTemp": 10, "startTime": "09/07/2020 09:56:51"}}
Sent message
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play előzetes verzió modellezése – fejlesztői útmutató](concepts-developer-guide.md)
