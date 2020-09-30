---
title: Az Azure IoT-megoldáshoz (Python) kapcsolódó IoT Plug and Play-eszköz használata | Microsoft Docs
description: A Python használatával csatlakozhat az Azure IoT-megoldáshoz csatlakoztatott IoT Plug and Play-eszközhöz, és kommunikálhat velük.
author: elhorton
ms.author: elhorton
ms.date: 7/13/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: be5ff3e863752dfc187bd91257425af5e8de85c4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574966"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-python"></a>Rövid útmutató: a megoldáshoz csatlakoztatott IoT Plug and Play eszköz (Python) használata

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

A IoT Plug and Play leegyszerűsíti a IoT azáltal, hogy lehetővé teszi az eszköz modelljének használatát az alapul szolgáló eszköz megvalósításának ismerete nélkül. Ez a rövid útmutató bemutatja, hogyan használható a Python a megoldáshoz csatlakoztatott IoT Plug and Play-eszköz csatlakoztatásához és vezérléséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

A rövid útmutató elvégzéséhez a fejlesztői gépen a Python 3,7 szükséges. A [Python.org](https://www.python.org/)több platformon is letöltheti a legújabb ajánlott verziót. A Python-verziót a következő paranccsal tekintheti meg:  

```cmd/sh
python --version
```

Az **Azure-IOT-Device** csomag pip-ként van közzétéve.

A helyi Python-környezetben a következő módon telepítse a csomagot:

```cmd/sh
pip install azure-iot-device
```

Telepítse az **Azure-IOT-hub** csomagot a következő parancs futtatásával:

```cmd/sh
pip install azure-iot-hub
```

## <a name="run-the-sample-device"></a>A minta eszköz futtatása

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/pnp/README.md)információit.

Ebben a rövid útmutatóban egy Pythonban írt minta termosztát-eszközt használ a IoT Plug and Play eszközként. A minta eszköz futtatása:

1. Nyisson meg egy terminál-ablakot tetszőleges mappában. Futtassa a következő parancsot az [Azure IoT PYTHON SDK](https://github.com/Azure/azure-iot-sdk-python) GitHub-tárház ezen a helyen történő klónozásához:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. A rendszer ezt a terminált használja az **eszköz** -terminálként. Lépjen a klónozott adattár mappájába, és lépjen a */Azure-IOT-SDK-Python/Azure-IOT-Device/Samples/PnP* mappára.

1. Futtassa a minta termosztátos eszközt a következő paranccsal:

    ```cmd/sh
    python simple_thermostat.py
    ```

1. Üzenet jelenik meg, amely közli, hogy az eszköz elküldött néhány információt, és online jelentett. Ezek az üzenetek azt jelzik, hogy az eszköz megkezdte a telemetria-adatok küldését a központba, és készen áll a parancsok és a tulajdonságok frissítéseinek fogadására. Ne zárjuk be ezt a terminált, hogy erősítse meg, hogy a szolgáltatási minta működik-e.

## <a name="run-the-sample-solution"></a>A minta megoldás futtatása

Ebben a rövid útmutatóban egy minta IoT megoldást használunk a Pythonban, hogy együttműködjön az imént beállított eszközzel.

1. Nyisson meg egy másik Terminálablak **szolgáltatást a szolgáltatás** -terminálként való használatra. 

1. Navigáljon a klónozott Python SDK-tárház */Azure-IOT-SDK-Python/Azure-IOT-hub/Samples* mappájához.

1. A Samples (minták) mappában négy minta fájl mutatja be a műveleteket a Digital Twin Manager osztályával: *get_digital_twin_sample., update_digitial_twin_sample., invoke_command_sample. és invoke_component_command_sample*.  Ezek a minták azt mutatják be, hogyan használhatók az egyes API-k az IoT Plug and Play-eszközökkel való interakcióhoz:

### <a name="get-digital-twin"></a>Digitális dupla Letöltés

A [környezet beállítása a IoT Plug and Play rövid útmutatók és oktatóanyagok](set-up-environment.md) létrehozott két környezeti változót a minta konfigurálásához a IoT hub és az eszközhöz való kapcsolódáshoz:

* **IOTHUB_CONNECTION_STRING**: a IoT hub-kapcsolatok karakterlánca korábban már jegyzett készített.
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"` .

Használja a következő parancsot a **szolgáltatás** -terminálon a minta futtatásához:

```cmd/sh
python get_digital_twin_sample.py
```

A kimenet az eszköz digitális ikerét jeleníti meg, és kiírja a modell AZONOSÍTÓját:

```cmd/sh
{'$dtId': 'mySimpleThermostat', '$metadata': {'$model': 'dtmi:com:example:Thermostat;1'}}
Model Id: dtmi:com:example:Thermostat;1
```

A következő kódrészlet a *get_digital_twin_sample.* a (z):

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

Ez a minta bemutatja, hogyan frissítheti *a tulajdonságokat* a tulajdonságok digitális Twin használatával történő frissítéséhez. A következő kódrészlet a *update_digital_twin_sample.* a (z) a javítás összeállítását mutatja be:

```python
# If you already have a component thermostat1:
# patch = [{"op": "replace", "path": "/thermostat1/targetTemperature", "value": 42}]
patch = [{"op": "add", "path": "/targetTemperature", "value": 42}]
iothub_digital_twin_manager.update_digital_twin(device_id, patch)
print("Patch has been succesfully applied")
```

Használja a következő parancsot a **szolgáltatás** -terminálon a minta futtatásához:

```cmd/sh
python update_digital_twin_sample.py
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

Parancs meghívásához futtassa a *invoke_command_sample.* Ez a minta bemutatja, hogyan hívhat meg egy parancsot egy egyszerű termosztátos eszközön. A minta futtatása előtt állítsa be a `IOTHUB_COMMAND_NAME` és a `IOTHUB_COMMAND_PAYLOAD` környezeti változókat a **szolgáltatás** -terminálon:

```cmd/sh
set IOTHUB_COMMAND_NAME="getMaxMinReport" # this is the relevant command for the thermostat sample
set IOTHUB_COMMAND_PAYLOAD="hello world" # this payload doesn't matter for this sample
```

A **szolgáltatás** -terminálon használja a következő parancsot a minta futtatásához:
  
```cmd/sh
python invoke_command_sample.py
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

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztatható egy IoT Plug and Play-eszköz egy IoT-megoldáshoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play modellezési fejlesztői útmutató](concepts-developer-guide-device-csharp.md)
